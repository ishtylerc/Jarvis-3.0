---
title: "Built-in tools - OpenAI API"
source: "https://platform.openai.com/docs/guides/tools?api-mode=responses"
author:
published:
created: 2025-03-20
description: "Use built-in tools like web search and file search to extend the model's capabilities."
tags:
  - "clippings"
---
Use built-in tools like web search and file search to extend the model's capabilities.

When generating model responses, you can extend model capabilities using built-in **tools**. These tools help models access additional context and information from the web or your files. The example below uses the [web search tool](https://platform.openai.com/docs/guides/tools-web-search) to use the latest information from the web to generate a model response.

Include web search results for the model response

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

from openai import OpenAI
client = OpenAI()

response = client.responses.create(
    model="gpt-4o",
    tools=[{"type": "web_search_preview"}],
    input="What was a positive news story from today?"
)

print(response.output_text)
```

## Available tools

Here's an overview of the tools available in the OpenAI platform—select one of them for further guidance on usage.

[

Web search

Include data from the Internet in model response generation.

](https://platform.openai.com/docs/guides/tools-web-search)[

File search

Search the contents of uploaded files for context when generating a response.

](https://platform.openai.com/docs/guides/tools-file-search)[

Computer use

Create agentic workflows that enable a model to control a computer interface.

](https://platform.openai.com/docs/guides/tools-computer-use)[

Function calling

Enable the model to call custom code that you define, giving it access to additional data and capabilities.

](https://platform.openai.com/docs/guides/function-calling)

## Usage in the API

When making a request to generate a [model response](https://platform.openai.com/docs/api-reference/responses/create), you can enable tool access by specifying configurations in the `tools` parameter. Each tool has its own unique configuration requirements—see the [Available tools](https://platform.openai.com/docs/guides/?api-mode=responses#available-tools) section for detailed instructions.

Based on the provided [prompt](https://platform.openai.com/docs/guides/text), the model automatically decides whether to use a configured tool. For instance, if your prompt requests information beyond the model's training cutoff date and web search is enabled, the model will typically invoke the web search tool to retrieve relevant, up-to-date information.

You can explicitly control or guide this behavior by setting the `tool_choice` parameter [in the API request](https://platform.openai.com/docs/api-reference/responses/create).

### Function calling

In addition to built-in tools, you can define custom functions using the `tools` array. These custom functions allow the model to call your application's code, enabling access to specific data or capabilities not directly available within the model.

Learn more in the [function calling guide](https://platform.openai.com/docs/guides/function-calling).