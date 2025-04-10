Working with Graphs in Cognee
Cognee enables you to create, query, and manipulate knowledge graphs derived from your data. Through a combination of structured pipelines, tasks, and typed DataPoints, you can transform raw textual input into rich semantic graphs that LLMs can use to produce contextually relevant responses.

This page provides a more in-depth look at how tasks and pipelines power the graph creation process. For an introductory example, see the Getting Started guide.

Key Concepts
Tasks
Tasks are the fundamental units of logic within Cognee’s processing model. Each task:

Inputs and Outputs: Takes in specific data, processes it, and outputs a transformed result.
Typed Datapoints: Uses pydantic-based models to ensure the input and output schemas are well-defined and validated.
Single Responsibility: Typically, a task focuses on a single step of the workflow—such as extracting text chunks, embedding them, or classifying their content.
Because tasks are self-contained, it’s easy to reuse them in multiple pipelines, swap them out for improved or alternate implementations, and maintain them as independent modules within your codebase.

Example: A ChunkingTask might accept raw document text as input and return an array of ChunkData objects representing smaller units of the original text.

Pipelines
Pipelines are orchestrations of tasks designed to perform complex operations end-to-end. Instead of handling the entire process in a monolithic script, pipelines let you compose several tasks into a coherent sequence or graph of operations.

Features of Pipelines:

Composable: By combining tasks that are each focused on a single responsibility, you can create pipelines that perform complex transformations, such as:

Reading documents from a store.
Chunking and embedding text.
Classifying entities and relationships.
Building a final knowledge graph.
Scalable: As your data and complexity grow, you can easily add more tasks or rearrange existing ones within the pipeline without refactoring large portions of code.

Parallel & Distributed Execution: Some parts of a pipeline can be parallelized or distributed across multiple compute resources, improving performance and scalability.

