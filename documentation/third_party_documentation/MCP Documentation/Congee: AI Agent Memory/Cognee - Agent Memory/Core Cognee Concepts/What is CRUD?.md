What is CRUD?
CRUD stands for:

Create: Add new records or data entries.
Read: Retrieve existing data (search, filter, or simply fetch details).
Update: Modify existing data, such as changing a record’s content or updating settings.
Delete: Remove data entries that are no longer needed or are incorrect.
These operations form the backbone of data management. In cognee, you’ll use CRUD concepts to add data to your datasets, retrieve them for inspection or processing, update system settings or dataset details, and remove data or entire datasets as needed.

Cognee provides a flexible way to manage data and knowledge graphs through a set of CRUD operations, aligning with standard RESTful API conventions. Now, we will walk you through how CRUD works within cognee and offer practical examples on how to integrate these operations into your workflow.

RESTful Alignment
In a RESTful context, each HTTP method typically corresponds to one of these CRUD actions. Cognee’s API follows these conventions, making it straightforward for developers to integrate cognee into their applications or data pipelines.

CRUD Actions	RESTful Context	How Cognee Handles
Create	POST	Upload new datasets or documents for analysis
Read	GET	Retrieve the current state of datasets, graphs, and system settings
Update	PUT/PATCH	Refine configurations and reprocess data to improve AI-driven results
Delete	DELETE	Clean Up when a dataset is no longer needed or to remove specific data entries
Cognee’s Endpoints
Below is a quick reference for how these CRUD operations map to cognee’s API endpoints. You can find detailed information in cognee’s API Reference .

1. Create
Add Data
POST /add
Upload new data (e.g., files, documents) to a specified dataset. The uploaded files will be stored and available for further processing (e.g., chunk extraction, graph population).

2. Read
Get Datasets
GET /datasets
Retrieve a list of available datasets.

Get Dataset Data
GET /datasets/{dataset_id}/data
Retrieves the data entries (documents, files, etc.) associated with a specific dataset.

Get Dataset Graph
GET /datasets/{dataset_id}/graph
Obtain the graph visualization URL for a particular dataset.

Get Dataset Status
GET /datasets/status
Check the status of one or more datasets.

Get Raw Data
GET /datasets/{dataset_id}/data/{data_id}/raw
Downloads the original file for a specific data entry.

Get Settings
GET /settings
Retrieves current system configurations (LLM settings, vector DB configurations, etc.).

3. Update
Save or Update Settings
POST /settings
Updates system configurations. This does not modify datasets directly but affects how data is processed and stored.
4. Delete
Delete a Dataset
DELETE /datasets/{dataset_id}
Removes as specific dataset by its ID, including all associated data from cognee’s storage.
Deleting a Single Document from a Dataset
Currently, cognee does not offer a single “delete document” endpoint for partially removing files from a dataset’s graph. However, you can remove a file from the dataset’s metastore using a script provided in the codebase, ensuring it will not be processed in subsequent runs:

delete_data.py
Once removed from the metastore:

The file will no longer appear in the dataset listing.
If you re-run cognify on that dataset, the removed file will not be included in the new graph build.
Note: A feature is cooking to remove a single document from the knowledge graph itself. Until that is released, manually removing the file from the metastore is the best workaround if you do not want to delete the entire dataset.

Example CRUD Workflow in cognee
A typical sequence using cognee’s RESTful API might look like this:

1. Create - upload your documents or data to a new dataset

POST /add
2. Read - verify the dataset was created and inspect its contents

GET /datasets
GET /datasets/{dataset_id}/data
3. Cognify - once the dataset is verified, trigger cognitive processing (e.g., generate embeddings, extract knowledge graphs, etc.)

POST /cognify
{
  "datasets": ["..."]
}
4. Read (Graph Insights) - check the resulting graph and insights

GET /datasets/{dataset_id}/graph
GET /datasets/status
5. Update - adjust system settings if necessary

POST /settings
{
  "llm_provider": "...",
  "vector_db": "..."
}
6. Search - now that the dataset is processed, run queries to discover insights

POST /search
{
  "dataset_id": "{dataset_id}",
  "query": "...?"
}
7. Delete - remove the dataset entirely if no longer needed

DELETE /datasets/{dataset_id}
See an example with code.

Cognee SDK Overview: CRUD in Practice
The cognee SDK offers a structured approach to data management through its core components, which align with CRUD operations. Here’s a summary from the CRUD perspective:

Create -> Data Ingestion: cognee.add(text: str) adds new text data to the metastore for future graph and embedding generation.

Read -> Data Retrieval: cognee.search(...) searches the knowledge graph or embeddings, enabling quick data exploration.

Update -> Data Processing: cognee.cognify() processes ingested data, including generating embeddings and building knowledge graphs.

Delete -> Data Pruning: cognee.prune.prune_data() removes stored documents, embeddings, or graph elements when they are no longer relevant.

These functions let you manage the full data lifecycle within cognee, making it easier to create, read, update, and delete data elements programmatically.