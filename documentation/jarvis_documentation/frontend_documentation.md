# Jarvis Frontend Documentation

This document outlines the architecture, data flow, and components of the Jarvis application's frontend.

## 1. Overview

The frontend is a single-page application (SPA) built using **React** and **Vite**. It utilizes **Tailwind CSS** for styling. The primary function is to establish a real-time, bidirectional communication channel with the OpenAI Realtime API using **WebRTC** for streaming audio input/output and exchanging structured events via a data channel.

## 2. Technology Stack

-   **UI Library:** React 18
-   **Build Tool:** Vite
-   **Styling:** Tailwind CSS (`client/base.css` and inline classes)
-   **Real-time Communication:** WebRTC (specifically `RTCPeerConnection` and `RTCDataChannel`)

## 3. Project Structure (client directory)

```
client/
├── assets/              # Static assets (e.g., logos)
│   └── openai-logomark.svg
├── components/          # React components
│   ├── App.jsx          # Main application component, state management
│   ├── Button.jsx       # Reusable button component
│   ├── EventLog.jsx     # Displays client/server communication events
│   ├── SessionControls.jsx # UI for starting/stopping sessions and sending messages
│   └── ToolPanel.jsx    # UI for demonstrating function calling
├── pages/               # Page components (currently minimal)
│   └── index.jsx
├── base.css             # Base Tailwind CSS setup and custom styles
├── entry-client.jsx     # Client-side entry point (React hydration)
├── entry-server.jsx     # Server-side entry point (SSR rendering function)
├── index.html           # Main HTML template
└── index.js             # Main server file (likely for serving HTML and API proxy) - **Needs confirmation**
```

## 4. Application Flow & Core Concepts

### 4.1. Initialization and Rendering

1.  **Server-Side Rendering (SSR):** When a user first requests the page, the server (likely `server/index.js` or similar, using `client/entry-server.jsx`) renders the initial HTML structure using `ReactDOMServer.renderToString(<App />)`. This HTML includes a `<div id="root"></div>` where the React app will mount.
2.  **Client-Side Hydration:** The browser loads the JavaScript bundle referenced in `client/index.html`. `client/entry-client.jsx` then takes over, using `ReactDOM.hydrateRoot` to attach React to the existing server-rendered DOM within the `#root` element, making the page interactive.

### 4.2. Session Management (`App.jsx`)

-   **State:** The core `App` component manages the application's primary state:
    -   `isSessionActive`: Boolean indicating if a WebRTC connection is active.
    -   `events`: An array storing the history of client and server events exchanged over the data channel.
    -   `dataChannel`: Holds the `RTCDataChannel` instance when active.
    -   `peerConnection`: A `useRef` holding the `RTCPeerConnection` instance.
    -   `audioElement`: A `useRef` holding an `<audio>` element created dynamically to play incoming audio streams from OpenAI.

-   **Starting a Session (`startSession` function):**
    1.  **Token Fetch:** An ephemeral session token is requested from a local backend endpoint (`/token`). This token is necessary to authorize with the OpenAI Realtime API.
    2.  **Peer Connection:** An `RTCPeerConnection` (`pc`) is created.
    3.  **Remote Audio Track:** An event listener (`pc.ontrack`) is set up. When OpenAI adds its audio track, this listener connects the incoming stream to the `audioElement.current.srcObject`, allowing playback.
    4.  **Local Audio Track:** The user's microphone is accessed using `navigator.mediaDevices.getUserMedia({ audio: true })`. The resulting audio track is added to the `pc` using `pc.addTrack()`, sending microphone input to OpenAI.
    5.  **Data Channel:** An `RTCDataChannel` named "oai-events" (`dc`) is created using `pc.createDataChannel()`. This channel is used for sending/receiving JSON-based events. The instance is stored in the `dataChannel` state.
    6.  **SDP Offer/Answer:**
        *   An SDP offer is created (`pc.createOffer()`) and set as the local description (`pc.setLocalDescription(offer)`).
        *   This SDP offer is sent to the OpenAI Realtime API endpoint (`https://api.openai.com/v1/realtime?model=...`) via a POST request, authorized with the ephemeral token.
        *   OpenAI responds with an SDP answer.
        *   The SDP answer is set as the remote description (`pc.setRemoteDescription(answer)`).
    7.  **Connection Established:** The WebRTC connection is now established. The `peerConnection.current` ref is updated.

-   **Data Channel Events (`useEffect` hook watching `dataChannel`):**
    1.  **`open` Event:** When the data channel becomes active:
        *   `isSessionActive` state is set to `true`.
        *   The `events` log is cleared.
        *   A `conversation.item.create` event containing the system prompt (defining Jarvis's persona) is sent via `sendClientEvent`.
        *   If the `ToolPanel` is active, a `session.update` event might be sent to register available tools (like `display_color_palette`).
    2.  **`message` Event:** When a message (event) is received from OpenAI:
        *   The JSON data is parsed.
        *   A timestamp is added if not present.
        *   The event is prepended to the `events` state array, updating the `EventLog`.
        *   Specific event types (`response.done` with `function_call`) might trigger actions in the `ToolPanel`.
    3.  **`close` Event:** (Implicitly handled) When the channel closes, the session should ideally be cleaned up.

-   **Stopping a Session (`stopSession` function):**
    1.  Closes the `dataChannel` if it exists.
    2.  Stops all local media tracks being sent (`sender.track.stop()`).
    3.  Closes the `peerConnection`.
    4.  Resets state: `isSessionActive` to `false`, `dataChannel` to `null`, `peerConnection.current` to `null`.

### 4.3. Sending Events (`sendClientEvent`, `sendTextMessage`)

-   `sendClientEvent(message)`:
    *   Takes a message (JSON object) as input.
    *   Adds a timestamp (primarily for local logging) and a unique `event_id` if missing.
    *   Sends the stringified message over the `dataChannel`.
    *   Adds the timestamped message to the local `events` state for display in `EventLog`.
-   `sendTextMessage(text)`:
    *   Constructs a standard `conversation.item.create` event object with the user's text input.
    *   Calls `sendClientEvent` to send the text message event.
    *   **Immediately calls `sendClientEvent({ type: "response.create" })`** to signal to the OpenAI backend that it should generate and send back a response.

### 4.4. User Utterance to Response Flow

1.  User clicks "Start Session" -> `startSession` executes, WebRTC connection established.
2.  Data channel opens, `systemPrompt` is sent.
3.  User types "What is the weather?" into the input field (`SessionControls.jsx`).
4.  User presses Enter or clicks "Send Text".
5.  `SessionControls.jsx` calls `sendTextMessage("What is the weather?")`.
6.  `sendTextMessage` calls `sendClientEvent` with the `conversation.item.create` event.
7.  `sendTextMessage` calls `sendClientEvent` with the `response.create` event.
8.  The `conversation.item.create` event is sent via the data channel. The user's microphone audio is streamed via the audio track.
9.  The `response.create` event is sent via the data channel.
10. OpenAI processes the audio and text input.
11. OpenAI sends events back via the data channel (e.g., `transcription.update`, `response.chunk`, `response.done`).
12. OpenAI streams synthesized audio response via the audio track established in step 1 (`pc.ontrack`).
13. The `useEffect` hook in `App.jsx` receives the data channel messages, parses them, adds timestamps, and updates the `events` state.
14. `EventLog.jsx` re-renders, displaying the new server events.
15. The `<audio>` element plays the incoming audio stream.
16. If OpenAI decides to call a function (e.g., based on the prompt "give me a color palette"), it sends a `response.done` event containing an `output` array with a `function_call` object.
17. `ToolPanel.jsx` detects this specific function call (`display_color_palette`) in its `useEffect` hook.
18. `ToolPanel.jsx` updates its state to display the results (e.g., color boxes) based on the arguments in the `function_call`.
19. `ToolPanel.jsx` then sends a *new* `response.create` event (after a short delay) with instructions like "ask for feedback about the color palette", prompting the AI to continue the conversation based on the tool's output.

## 5. Components Breakdown

-   **`App.jsx`:**
    -   **Responsibilities:** Main application container, overall layout (nav, main content area), state management (session status, events, WebRTC objects), coordinating session start/stop, handling data channel events, passing state and functions down to child components.
    -   **Key Functions:** `startSession`, `stopSession`, `sendClientEvent`, `sendTextMessage`.
-   **`SessionControls.jsx`:**
    -   **Responsibilities:** Renders UI controls based on `isSessionActive`. Shows "Start Session" button (`SessionStopped` internal component) or input field/send button/disconnect button (`SessionActive` internal component). Handles user input for text messages and triggers the relevant functions passed down from `App.jsx`.
    -   **Interacts with:** `App.jsx` (receives `isSessionActive`, `startSession`, `stopSession`, `sendTextMessage`). Uses `Button.jsx`.
-   **`EventLog.jsx`:**
    -   **Responsibilities:** Renders the list of client/server events passed via the `events` prop. Provides basic formatting (client vs. server origin), timestamping, and an expandable view to see the full JSON payload of each event. It attempts to condense sequences of `*.delta` events.
    -   **Interacts with:** `App.jsx` (receives `events` prop).
-   **`ToolPanel.jsx`:**
    -   **Responsibilities:** Demonstrates function calling capabilities.
        -   Sends a `session.update` event after session creation to inform the backend about the available `display_color_palette` function.
        -   Listens for `response.done` events. If a `function_call` for `display_color_palette` is detected, it parses the arguments and renders the output (color boxes).
        -   Sends a follow-up `response.create` event to prompt the AI for feedback after displaying the tool output.
        -   Manages its own state (`functionAdded`, `functionCallOutput`).
    -   **Interacts with:** `App.jsx` (receives `isSessionActive`, `sendClientEvent`, `events`).
-   **`Button.jsx`:**
    -   **Responsibilities:** Simple, reusable button component accepting children, an icon, and standard button props (like `onClick`, `className`).

## 6. Styling

-   Styling is primarily handled by **Tailwind CSS** utility classes applied directly in the JSX of the components.
-   `client/base.css` contains the basic Tailwind setup (`@tailwind base;`, `@tailwind components;`, `@tailwind utilities;`) and potentially some global base styles or custom component styles if needed.

## 7. Potential Improvements / Areas for Clarification

-   **Error Handling:** Robust error handling for API calls (token fetch, SDP exchange), WebRTC connection issues, and data channel errors should be implemented.
-   **Backend Interaction:** The exact nature of the backend server (`index.js`?) needs clarification – primarily its role in serving the HTML and providing the `/token` endpoint.
-   **State Management:** For larger applications, consider a dedicated state management library (like Zustand, Redux Toolkit, or Jotai) instead of prop drilling.
-   **Component Reusability:** Further decomposition of components might be beneficial as complexity grows.
-   **Accessibility:** Ensure proper ARIA attributes and semantic HTML are used for accessibility.
-   **Testing:** Implement unit and integration tests. 