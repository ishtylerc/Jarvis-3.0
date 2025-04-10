---
title: "Audio and speech - OpenAI API"
source: "https://platform.openai.com/docs/guides/audio"
author:
published:
created: 2025-03-20
description: "Learn how to work with audio and speech in the OpenAI API."
tags:
  - "clippings"
---
Explore audio and speech features in the OpenAI API.

The OpenAI API provides a range of audio capabilities. If you know what you want to build, find your use case below to get started. If you're not sure where to start, read this page as an overview.

## Build with audio

[

![Build voice agents](https://cdn.openai.com/API/docs/images/voice-agents-rounded.png)

Build voice agents

Build interactive voice-driven applications.

](https://platform.openai.com/docs/guides/voice-agents)[

![Transcribe audio](https://cdn.openai.com/API/docs/images/stt-rounded.png)

Transcribe audio

Convert speech to text instantly and accurately.

](https://platform.openai.com/docs/guides/speech-to-text)[

![Speak text](https://cdn.openai.com/API/docs/images/tts-rounded.png)

Speak text

Turn text into natural-sounding speech in real time.

](https://platform.openai.com/docs/guides/text-to-speech)

## A tour of audio use cases

LLMs can process audio by using sound as input, creating sound as output, or both. OpenAI has several API endpoints that help you build audio applications or voice agents.

### Voice agents

Voice agents understand audio to handle tasks and respond back in natural language. There are two main ways to approach voice agents: either with speech-to-speech models and the Realtime API, or by chaining together a speech-to-text model, a text language model to process the request, and a text-to-speech model to respond. Speech-to-speech is lower latency and more natural, but chaining together a voice agent is a reliable way to extend a text-based agent into a voice agent.

### Streaming audio

Process audio in real time to build voice agents and other low-latency applications, including transcription use cases. You can stream audio in and out of a model with the [Realtime API](https://platform.openai.com/docs/guides/realtime). Our advanced speech models provide automatic speech recognition for improved accuracy, low-latency interactions, and multilingual support.

### Text to speech

For turning text into speech, use the [Audio API](https://platform.openai.com/docs/api-reference/audio/) `audio/speech` endpoint. Models compatible with this endpoint are `gpt-4o-mini-tts`, `tts-1`, and `tts-1-hd`. With `gpt-4o-mini-tts`, you can ask the model to speak a certain way or with a certain tone of voice.

### Speech to text

For speech to text, use the [Audio API](https://platform.openai.com/docs/api-reference/audio/) `audio/transcriptions` endpoint. Models compatible with this endpoint are `gpt-4o-transcribe`, `gpt-4o-mini-transcribe`, and `whisper-1`. With streaming, you can continuously pass in audio and get a continuous stream of text back.

## Choosing the right API

There are multiple APIs for transcribing or generating audio:

| API | Supported modalities | Streaming support |
| --- | --- | --- |
| [Realtime API](https://platform.openai.com/docs/api-reference/realtime) | Audio and text inputs and outputs | Audio streaming in and out |
| [Chat Completions API](https://platform.openai.com/docs/api-reference/chat) | Audio and text inputs and outputs | Audio streaming out |
| [Transcription API](https://platform.openai.com/docs/api-reference/audio) | Audio inputs | Audio streaming out |
| [Speech API](https://platform.openai.com/docs/api-reference/audio) | Text inputs and audio outputs | Audio streaming out |

### General use APIs vs. specialized APIs

The main distinction is general use APIs vs. specialized APIs. With the Realtime and Chat Completions APIs, you can use our latest models' native audio understanding and generation capabilities and combine them with other features like function calling. These APIs can be used for a wide range of use cases, and you can select the model you want to use.

On the other hand, the Transcription, Translation and Speech APIs are specialized to work with specific models and only meant for one purpose.

### Talking with a model vs. controlling the script

Another way to select the right API is asking yourself how much control you need. To design conversational interactions, where the model thinks and responds in speech, use the Realtime or Chat Completions API, depending if you need low-latency or not.

You won't know exactly what the model will say ahead of time, as it will generate audio responses directly, but the conversation will feel natural.

For more control and predictability, you can use the Speech-to-text / LLM / Text-to-speech pattern, so you know exactly what the model will say and can control the response. Please note that with this method, there will be added latency.

This is what the Audio APIs are for: pair an LLM with the `audio/transcriptions` and `audio/speech` endpoints to take spoken user input, process and generate a text response, and then convert that to speech that the user can hear.

- If you need [real-time interactions](https://platform.openai.com/docs/guides/realtime-conversations) or [transcription](https://platform.openai.com/docs/guides/realtime-transcription), use the Realtime API.
- If realtime is not a requirement but you're looking to build a [voice agent](https://platform.openai.com/docs/guides/voice-agents) or an audio-based application that requires features such as [function calling](https://platform.openai.com/docs/guides/function-calling), use the Chat Completions API.
- For use cases with one specific purpose, use the Transcription, Translation, or Speech APIs.

## Add audio to your existing application

Models such as GPT-4o or GPT-4o mini are natively multimodal, meaning they can understand and generate multiple modalities as input and output.

If you already have a text-based LLM application with the [Chat Completions endpoint](https://platform.openai.com/docs/api-reference/chat/), you may want to add audio capabilities. For example, if your chat application supports text input, you can add audio input and output—just include `audio` in the `modalities` array and use an audio model, like `gpt-4o-audio-preview`.

Audio is not yet supported in the [Responses API](https://platform.openai.com/docs/api-reference/chat/completions/responses).

Create a human-like audio response to a prompt

```highlighter
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22

import base64
from openai import OpenAI

client = OpenAI()

completion = client.chat.completions.create(
    model="gpt-4o-audio-preview",
    modalities=["text", "audio"],
    audio={"voice": "alloy", "format": "wav"},
    messages=[
        {
            "role": "user",
            "content": "Is a golden retriever a good family dog?"
        }
    ]
)

print(completion.choices[0])

wav_bytes = base64.b64decode(completion.choices[0].message.audio.data)
with open("dog.wav", "wb") as f:
    f.write(wav_bytes)
```