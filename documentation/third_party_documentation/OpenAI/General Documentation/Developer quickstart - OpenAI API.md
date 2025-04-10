---
title: "Developer quickstart - OpenAI API"
source: "https://platform.openai.com/docs/quickstart?api-mode=responses&lang=python"
author:
published:
created: 2025-03-20
description: "Learn how to use the OpenAI API to generate human-like responses to natural language prompts, analyze images with computer vision, use powerful built-in tools, and more."
tags:
  - "clippings"
---
Take your first steps with the OpenAI API.

The OpenAI API provides a simple interface to state-of-the-art AI [models](https://platform.openai.com/docs/models) for text generation, natural language processing, computer vision, and more. This example generates [text output](https://platform.openai.com/docs/guides/text) from a prompt, as you might using [ChatGPT](https://chatgpt.com/).

Generate text from a model

```highlighter

from openai import OpenAI
client = OpenAI()

response = client.responses.create(
    model="gpt-4o",
    input="Write a one-sentence bedtime story about a unicorn."
)

print(response.output_text)
```

Data retention for model responses

[

Configure your development environment

Install and configure an official OpenAI SDK to run the code above.

](https://platform.openai.com/docs/libraries)[

Responses starter app

Start building with the Responses API

](https://github.com/openai/openai-responses-starter-app)[

Text generation and prompting

Learn more about prompting, message roles, and building conversational apps.

](https://platform.openai.com/docs/guides/text)  
  

## Analyze image inputs

You can provide image inputs to the model as well. Scan receipts, analyze screenshots, or find objects in the real world with [computer vision](https://platform.openai.com/docs/guides/images).

Analyze the content of an image

```highlighter


from openai import OpenAI
client = OpenAI()

response = client.responses.create(
    model="gpt-4o",
    input=[
        {"role": "user", "content": "what teams are playing in this image?"},
        {
            "role": "user",
            "content": [
                {
                    "type": "input_image",
                    "image_url": "https://upload.wikimedia.org/wikipedia/commons/3/3b/LeBron_James_Layup_%28Cleveland_vs_Brooklyn_2018%29.jpg"
                }
            ]
        }
    ]
)

print(response.output_text)
```

[

Computer vision guide

Learn to use image inputs to the model and extract meaning from images.

](https://platform.openai.com/docs/guides/images)  
  

## Extend the model with tools

Give the model access to new data and capabilities using [tools](https://platform.openai.com/docs/guides/tools). You can either call your own [custom code](https://platform.openai.com/docs/guides/function-calling), or use one of OpenAI's [powerful built-in tools](https://platform.openai.com/docs/guides/tools). This example uses [web search](https://platform.openai.com/docs/guides/tools-web-search) to give the model access to the latest information on the Internet.

Get information for the response from the Internet

```highlighter


from openai import OpenAI
client = OpenAI()

response = client.responses.create(
    model="gpt-4o",
    tools=[{"type": "web_search_preview"}],
    input="What was a positive news story from today?"
)

print(response.output_text)
```

[

Use built-in tools

Learn about powerful built-in tools like web search and file search.

](https://platform.openai.com/docs/guides/tools)[

Function calling guide

Learn to enable the model to call your own custom code.

](https://platform.openai.com/docs/guides/function-calling)  
  

## Deliver blazing fast AI experiences

Using either the new [Realtime API](https://platform.openai.com/docs/guides/realtime) or server-sent [streaming events](https://platform.openai.com/docs/guides/streaming-responses), you can build high performance, low-latency experiences for your users.

Stream server-sent events from the API

```highlighter


from openai import OpenAI
client = OpenAI()

stream = client.responses.create(
    model="gpt-4o",
    input=[
        {
            "role": "user",
            "content": "Say 'double bubble bath' ten times fast.",
        },
    ],
    stream=True,
)

for event in stream:
    print(event)
```

[

Use streaming events

Use server-sent events to stream model responses to users fast.

](https://platform.openai.com/docs/guides/streaming-responses)[

Get started with the Realtime API

Use WebRTC or WebSockets for super fast speech-to-speech AI apps.

](https://platform.openai.com/docs/guides/realtime)  
  

## Build agents

Use the OpenAI platform to build [agents](https://platform.openai.com/docs/guides/agents) capable of taking action—like [controlling computers](https://platform.openai.com/docs/guides/tools-computer-use)—on behalf of your users. Use the [Agent SDK for Python](https://platform.openai.com/docs/guides/agents-sdk) to create orchestration logic on the backend.

```highlighter


from agents import Agent, Runner
import asyncio

spanish_agent = Agent(
    name="Spanish agent",
    instructions="You only speak Spanish.",
)

english_agent = Agent(
    name="English agent",
    instructions="You only speak English",
)

triage_agent = Agent(
    name="Triage agent",
    instructions="Handoff to the appropriate agent based on the language of the request.",
    handoffs=[spanish_agent, english_agent],
)

async def main():
    result = await Runner.run(triage_agent, input="Hola, ¿cómo estás?")
    print(result.final_output)

if __name__ == "__main__":
    asyncio.run(main())

# ¡Hola! Estoy bien, gracias por preguntar. ¿Y tú, cómo estás?
```

[

Build agents that can take action

Learn how to use the OpenAI platform to build powerful, capable AI agents.

](https://platform.openai.com/docs/guides/agents)  
  

## Explore further

We've barely scratched the surface of what's possible with the OpenAI platform. Here are some resources you might want to explore next.

[

Go deeper with prompting and text generation

Learn more about prompting, message roles, and building conversational apps like chat bots.

](https://platform.openai.com/docs/guides/text)[

Analyze the content of images

Learn to use image inputs to the model and extract meaning from images.

](https://platform.openai.com/docs/guides/images)[

Generate structured JSON data from the model

Generate JSON data from the model that conforms to a JSON schema you specify.

](https://platform.openai.com/docs/guides/structured-outputs)[

Call custom code to help generate a response

Empower the model to invoke your own custom code to help generate a response. Do this to give the model access to data or systems it wouldn't be able to access otherwise.

](https://platform.openai.com/docs/guides/function-calling)[

Search the web or use your own data in responses

Try out powerful built-in tools to extend the capabilities of the models. Search the web or your own data for up-to-date information the model can use to generate responses.

](https://platform.openai.com/docs/guides/tools)[

Responses starter app

Start building with the Responses API

](https://github.com/openai/openai-responses-starter-app)[

Build agents

Explore interfaces to build powerful AI agents that can take action on behalf of users. Control a computer to take action on behalf of a user, or orchestrate multi-agent flows with the Agents SDK.

](https://platform.openai.com/docs/guides/agents)[

Full API Reference

View the full API reference for the OpenAI platform.

](https://platform.openai.com/docs/api-reference)