---
title: "Reasoning models - OpenAI API"
source: "https://platform.openai.com/docs/guides/reasoning?api-mode=responses"
author:
published:
created: 2025-03-20
description: "Explore the capabilities of OpenAI's o1 series for complex reasoning and problem-solving. Learn about their features and how they compare to GPT-4o models."
tags:
  - "clippings"
---
Explore advanced reasoning and problem-solving models.

**Reasoning models**, like OpenAI o1 and o3-mini, are new large language models trained with reinforcement learning to perform complex reasoning. Reasoning models [think before they answer](https://openai.com/index/introducing-openai-o1-preview/), producing a long internal chain of thought before responding to the user. Reasoning models excel in complex problem solving, coding, scientific reasoning, and multi-step planning for agentic workflows.

As with our GPT models, we provide both a smaller, faster model (`o3-mini`) that is less expensive per token, and a larger model (`o1`) that is somewhat slower and more expensive, but can often generate better responses for complex tasks, and generalize better across domains.

The new [o1-pro model](https://platform.openai.com/docs/models/o1-pro) calls built-in tools, uses additional compute, and may take multiple model generation turns before returning a response to API requests. It is available in the [Responses API only](https://platform.openai.com/docs/api-reference/responses) to enable these unique features.

## Get started with reasoning

Reasoning models can be used through the [Responses API](https://platform.openai.com/docs/api-reference/responses/create) as seen here.

Using a reasoning model in the Responses API

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

from openai import OpenAI

client = OpenAI()

prompt = """
Write a bash script that takes a matrix represented as a string with 
format '[1,2],[3,4],[5,6]' and prints the transpose in the same format.
"""

response = client.responses.create(
    model="o3-mini",
    reasoning={"effort": "medium"},
    input=[
        {
            "role": "user", 
            "content": prompt
        }
    ]
)

print(response.output_text)
```

### Reasoning effort

In the example above, the `reasoning.effort` parameter (lovingly referred to as the "juice" during the development of these models) is used to give the model guidance on how many reasoning tokens it should generate before creating a response to the prompt.

You can specify one of `low`, `medium`, or `high` for this parameter, where `low` will favor speed and economical token usage, and `high` will favor more complete reasoning at the cost of more tokens generated and slower responses. The default value is `medium`, which is a balance between speed and reasoning accuracy.

## How reasoning works

Reasoning models introduce **reasoning tokens** in addition to input and output tokens. The models use these reasoning tokens to "think", breaking down their understanding of the prompt and considering multiple approaches to generating a response. After generating reasoning tokens, the model produces an answer as visible completion tokens, and discards the reasoning tokens from its context.

Here is an example of a multi-step conversation between a user and an assistant. Input and output tokens from each step are carried over, while reasoning tokens are discarded.

![Reasoning tokens aren't retained in context](https://cdn.openai.com/API/images/guides/reasoning_tokens.png)

While reasoning tokens are not visible via the API, they still occupy space in the model's context window and are billed as [output tokens](https://openai.com/api/pricing).

### Managing the context window

It's important to ensure there's enough space in the context window for reasoning tokens when creating responses. Depending on the problem's complexity, the models may generate anywhere from a few hundred to tens of thousands of reasoning tokens. The exact number of reasoning tokens used is visible in the [usage object of the response object](https://platform.openai.com/docs/api-reference/responses/object), under `output_tokens_details`:

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

{
    "usage": {
        "input_tokens": 75,
        "input_tokens_details": {
            "cached_tokens": 0
        },
        "output_tokens": 1186,
        "output_tokens_details": {
            "reasoning_tokens": 1024
        },
        "total_tokens": 1261
    }
}
```

Context window lengths are found on the [model reference page](https://platform.openai.com/docs/models), and will differ across model snapshots.

### Controlling costs

To manage costs with reasoning models, you can limit the total number of tokens the model generates (including both reasoning and final output tokens) by using the [`max_output_tokens`](https://platform.openai.com/docs/api-reference/responses/create#responses-create-max_output_tokens) parameter.

### Allocating space for reasoning

If the generated tokens reach the context window limit or the `max_output_tokens` value you've set, you'll receive a response with a `status` of `incomplete` and `incomplete_details` with `reason` set to `max_output_tokens`. This might occur before any visible output tokens are produced, meaning you could incur costs for input and reasoning tokens without receiving a visible response.

To prevent this, ensure there's sufficient space in the context window or adjust the `max_output_tokens` value to a higher number. OpenAI recommends reserving at least 25,000 tokens for reasoning and outputs when you start experimenting with these models. As you become familiar with the number of reasoning tokens your prompts require, you can adjust this buffer accordingly.

Handling incomplete responses

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
23
24
25
26
27

from openai import OpenAI

client = OpenAI()

prompt = """
Write a bash script that takes a matrix represented as a string with 
format '[1,2],[3,4],[5,6]' and prints the transpose in the same format.
"""

response = client.responses.create(
    model="o3-mini",
    reasoning={"effort": "medium"},
    input=[
        {
            "role": "user", 
            "content": prompt
        }
    ],
    max_output_tokens=300,
)

if response.status == "incomplete" and response.incomplete_details.reason == "max_output_tokens":
    print("Ran out of tokens")
    if response.output_text:
        print("Partial output:", response.output_text)
    else: 
        print("Ran out of tokens during reasoning")
```

## Advice on prompting

There are some differences to consider when prompting a reasoning model versus prompting a GPT model. Generally speaking, reasoning models will provide better results on tasks with only high-level guidance. This differs somewhat from GPT models, which often benefit from very precise instructions.

- A reasoning model is like a senior co-worker—you can give them a goal to achieve and trust them to work out the details.
- A GPT model is like a junior coworker—they'll perform best with explicit instructions to create a specific output.

For more information on best practices when using reasoning models, [refer to this guide](https://platform.openai.com/docs/guides/reasoning-best-practices).

### Prompt examples

OpenAI o-series models are able to implement complex algorithms and produce code. This prompt asks o1 to refactor a React component based on some specific criteria.

Refactor code

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
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43

from openai import OpenAI

client = OpenAI()

prompt = """
Instructions:
- Given the React component below, change it so that nonfiction books have red
  text. 
- Return only the code in your reply
- Do not include any additional formatting, such as markdown code blocks
- For formatting, use four space tabs, and do not allow any lines of code to 
  exceed 80 columns

const books = [
  { title: 'Dune', category: 'fiction', id: 1 },
  { title: 'Frankenstein', category: 'fiction', id: 2 },
  { title: 'Moneyball', category: 'nonfiction', id: 3 },
];

export default function BookList() {
  const listItems = books.map(book =>
    <li>
      {book.title}
    </li>
  );

  return (
    <ul>{listItems}</ul>
  );
}
"""

response = client.responses.create(
    model="o3-mini",
    input=[
        {
            "role": "user",
            "content": prompt,
        }
    ]
)

print(response.output_text)
```

## Use case examples

Some examples of using reasoning models for real-world use cases can be found in [the cookbook](https://cookbook.openai.com/).[Using reasoning for data validation](https://cookbook.openai.com/examples/o1/using_reasoning_for_data_validation)

[Evaluate a synthetic medical data set for discrepancies.](https://cookbook.openai.com/examples/o1/using_reasoning_for_data_validation)[Using reasoning for routine generation](https://cookbook.openai.com/examples/o1/using_reasoning_for_routine_generation)

[Use help center articles to generate actions that an agent could perform.](https://cookbook.openai.com/examples/o1/using_reasoning_for_routine_generation)