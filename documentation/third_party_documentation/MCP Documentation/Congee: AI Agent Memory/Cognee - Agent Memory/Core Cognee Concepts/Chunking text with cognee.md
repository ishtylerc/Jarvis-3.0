Chunking text with cognee
Chunking is the process of splitting text into smaller, more manageable units (referred to as “chunks”). These chunks form the foundation for downstream tasks such as embedding, classification, and ultimately the construction of a knowledge graph. Cognee’s flexible chunking approach ensures that no matter the data source—documents, code snippets, or web content—you can tailor chunking logic to fit your needs.

Why Chunking Matters
Manageability: Large documents are hard to handle as single units. Splitting them into smaller pieces allows more granular analysis.

Improved Quality of Embeddings and Classification: LLMs and vector databases often perform better when dealing with shorter, more focused pieces of text. Chunked data ensures embeddings capture more specific semantic information.

Contextual Relevance: By working with smaller data units, Cognee can more accurately identify relevant facts and relationships, improving the overall quality of retrieval-augmented generation (RAG) results.

How Cognee Handles Chunking
In Cognee, chunking can be implemented as a Task within a Pipeline. The chunking task receives a Datapoint (e.g., DocumentData containing raw text) and returns another Datapoint (e.g., ChunkData with a list of text chunks).

Key Concepts
Tasks: A ChunkingTask is responsible for performing the actual splitting of text. Different tasks can implement different splitting logic—by paragraphs, sentences, tokens, or custom delimiters.

Datapoints: A pydantic-based model (e.g., ChunkData) defines the schema for the output of the chunking process, ensuring all subsequent tasks receive the data in a known, structured format.

Integration in Pipelines: Chunking typically appears early in a pipeline, after text ingestion but before embedding and entity extraction. By chunking first, you ensure that all downstream tasks process consistently sized units of information.

Example Code Snippet
Below is a simplified example of a chunking task and how it might be integrated into a pipeline.

import cognee
import asyncio
from pydantic import BaseModel
from cognee import Task, Pipeline
 
# Define Datapoints
class DocumentData(BaseModel):
    content: str
 
class ChunkData(BaseModel):
    chunks: list[str]
 
# Define a simple chunking task
class ChunkingTask(Task):
    def run(self, doc: DocumentData) -> ChunkData:
        # Example: split by double newlines
        chunks = doc.content.split("\n\n")
        return ChunkData(chunks=chunks)
 
async def main():
    # Reset Cognee state
    await cognee.prune.prune_data()
    await cognee.prune.prune_system(metadata=True)
 
    text = """
    Natural language processing (NLP) is an interdisciplinary
    subfield of computer science and information retrieval.
 
    NLP techniques are used to analyze text, allowing machines to
    understand human language.
    """
 
    # Add text to Cognee
    await cognee.add(text)
 
    # Create a pipeline with just the chunking task for demonstration
    chunking_pipeline = Pipeline([ChunkingTask()])
 
    # Retrieve the added document from Cognee as DocumentData
    # (In practice, this might be part of a pipeline or a retrieval task.)
    doc_data = DocumentData(content=text.strip())
 
    # Run the pipeline to chunk the document
    chunk_data = chunking_pipeline.run(doc_data)
    print("Chunks:", chunk_data.chunks)
 
if __name__ == '__main__':
    asyncio.run(main())