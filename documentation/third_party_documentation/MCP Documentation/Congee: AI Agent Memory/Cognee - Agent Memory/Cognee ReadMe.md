

  cognee - memory layer for AI apps and Agents


  AI Agent responses you can rely on.



Build dynamic Agent memory using scalable, modular ECL (Extract, Cognify, Load) pipelines.


## Features

- Interconnect and retrieve your past conversations, documents, images and audio transcriptions
- Reduce hallucinations, developer effort, and cost.
- Load data to graph and vector databases using only Pydantic
- Manipulate your data while ingesting from 30+ data sources

## Get Started

Get started quickly with a Google Colab  <a href="https://colab.research.google.com/drive/1g-Qnx6l_ecHZi0IOw23rg0qC4TYvEvWZ?usp=sharing">notebook</a>  or  <a href="https://github.com/topoteretes/cognee-starter">starter repo</a>

## Contributing
Your contributions are at the core of making this a true open source project. Any contributions you make are **greatly appreciated**. See [`CONTRIBUTING.md`](CONTRIBUTING.md) for more information.





## 📦 Installation

You can install Cognee using either **pip**, **poetry**, **uv** or any other python package manager.

### With pip

```bash
pip install cognee
```

## 💻 Basic Usage

### Setup

```
import os
os.environ["LLM_API_KEY"] = "YOUR OPENAI_API_KEY"

```

You can also set the variables by creating .env file, using our <a href="https://github.com/topoteretes/cognee/blob/main/.env.template">template.</a>
To use different LLM providers, for more info check out our <a href="https://docs.cognee.ai">documentation</a>


### Simple example

This script will run the default pipeline:

```python
import cognee
import asyncio


async def main():
    # Add text to cognee
    await cognee.add("Natural language processing (NLP) is an interdisciplinary subfield of computer science and information retrieval.")

    # Generate the knowledge graph
    await cognee.cognify()

    # Query the knowledge graph
    results = await cognee.search("Tell me about NLP")

    # Display the results
    for result in results:
        print(result)


if __name__ == '__main__':
    asyncio.run(main())

```
Example output:
```
  Natural Language Processing (NLP) is a cross-disciplinary and interdisciplinary field that involves computer science and information retrieval. It focuses on the interaction between computers and human language, enabling machines to understand and process natural language.
  
```
Graph visualization:
<a href="https://rawcdn.githack.com/topoteretes/cognee/refs/heads/add-visualization-readme/assets/graph_visualization.html"><img src="assets/graph_visualization.png" width="100%" alt="Graph Visualization"></a>
Open in [browser](https://rawcdn.githack.com/topoteretes/cognee/refs/heads/add-visualization-readme/assets/graph_visualization.html).

For more advanced usage, have a look at our <a href="https://docs.cognee.ai"> documentation</a>.


## Understand our architecture

<div style="text-align: center">
  <img src="assets/cognee_diagram.png" alt="cognee concept diagram" width="100%" />
</div>



## Demos

1. What is AI memory:

[Learn about cognee](https://github.com/user-attachments/assets/8b2a0050-5ec4-424c-b417-8269971503f0)

2. Simple GraphRAG demo

[Simple GraphRAG demo](https://github.com/user-attachments/assets/d80b0776-4eb9-4b8e-aa22-3691e2d44b8f)

3. cognee with Ollama

[cognee with local models](https://github.com/user-attachments/assets/8621d3e8-ecb8-4860-afb2-5594f2ee17db)


## Code of Conduct

We are committed to making open source an enjoyable and respectful experience for our community. See <a href="https://github.com/topoteretes/cognee/blob/main/CODE_OF_CONDUCT.md"><code>CODE_OF_CONDUCT</code></a> for more information.

## 💫 Contributors

<a href="https://github.com/topoteretes/cognee/graphs/contributors">
  <img alt="contributors" src="https://contrib.rocks/image?repo=topoteretes/cognee"/>
</a>


## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=topoteretes/cognee&type=Date)](https://star-history.com/#topoteretes/cognee&Date)