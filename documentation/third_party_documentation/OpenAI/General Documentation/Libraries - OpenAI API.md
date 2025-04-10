---
title: "Libraries - OpenAI API"
source: "https://platform.openai.com/docs/libraries?language=python"
author:
published:
created: 2025-03-20
description: "Discover language-specific libraries for using the OpenAI API, including Python, Node.js, .NET, and more."
tags:
  - "clippings"
---
Set up your development environment to use the OpenAI API with an SDK in your preferred language.

This page covers setting up your local development environment to use the [OpenAI API](https://platform.openai.com/docs/api-reference). You can use one of our officially supported SDKs, a community library, or your own preferred HTTP client.

## Create and export an API key

Before you begin, [create an API key in the dashboard](https://platform.openai.com/api-keys), which you'll use to securely [access the API](https://platform.openai.com/docs/api-reference/authentication). Store the key in a safe location, like a [`.zshrc` file](https://www.freecodecamp.org/news/how-do-zsh-configuration-files-work/) or another text file on your computer. Once you've generated an API key, export it as an [environment variable](https://en.wikipedia.org/wiki/Environment_variable) in your terminal.

Export an environment variable on macOS or Linux systems

```highlighter
1

export OPENAI_API_KEY="your_api_key_here"
```

OpenAI SDKs are configured to automatically read your API key from the system environment.

## Install an official SDK

To use the OpenAI API in Python, you can use the official [OpenAI SDK for Python](https://github.com/openai/openai-python). Get started by installing the SDK using [pip](https://pypi.org/project/pip/):

Install the OpenAI SDK with pip

```highlighter
1

pip install openai
```

With the OpenAI SDK installed, create a file called `example.py` and copy the example code into it:

Test a basic API request

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

from openai import OpenAI
client = OpenAI()

completion = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {
            "role": "user",
            "content": "Write a one-sentence bedtime story about a unicorn."
        }
    ]
)

print(completion.choices[0].message.content)
```

Execute the code with `python example.py`. In a few moments, you should see the output of your API request.

[

Learn more on GitHub

Discover more SDK capabilities and options on the library's GitHub README.

](https://github.com/openai/openai-python)

