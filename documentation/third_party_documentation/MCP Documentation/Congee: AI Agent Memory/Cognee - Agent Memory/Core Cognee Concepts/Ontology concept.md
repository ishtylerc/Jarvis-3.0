Ontology concept
Cognee optionally uses rdf/xml ontologies to ground the knowledge base with general information about individuals and classes. In order to add your own ontologies you can provide it as a parameter to the main cognify  pipeline in the following way:

 
pipeline_run = await cognee.cognify(ontology_file_path='YOUR_ONTOLOGY_PATH')
 
Parameters
datasets: Union[str, list[str]] = None: A string or list of dataset names to be processed.

user: User = None: The user requesting the processing. If not provided, the default user is retrieved.

ontology_file_path: Optional = None: File path of the rdf/xml ontology file. If not provided, the default ontology is an empty ontology

Ontology matching algorithm logic
Entity Extraction:
The main Cognee pipeline first extracts entities, their types, and connections from the textual input.

Subgraph Matching:
The system compares and matches nodes from the ontology to the extracted entities or entity types based on node similarity.

Mapping Entities and Classes:
Mapping Entities and Classes:
Entities are linked to specific individuals in the ontology. Entity types are associated with corresponding ontology classes.

Knowledge Graph Enrichment:
Once a match is identified, Cognee retrieves the relevant subgraph from the ontology and merges its nodes and edges into the knowledge graph. This process enhances the knowledge graph with a semantically rich, ontology-grounded structure.