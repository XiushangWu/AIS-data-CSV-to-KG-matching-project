
### README: CSV to Knowledge Graph Matching System
Author: Xiushang Wu

---

#### Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Data Preparation](#data-preparation)
4. [Knowledge Graph Schema](#knowledge-graph-schema)
5. [Data Transformation](#data-transformation)
6. [Graph Construction](#graph-construction)
7. [Output](#output)
8. [Running the System](#running-the-system)
9. [Future Enhancements](#future-enhancements)

---

### Introduction

This document provides a detailed guide to creating a system that transforms a CSV file containing maritime vessel data into a knowledge graph. The system reads the CSV file, processes the data, and constructs a knowledge graph with nodes and edges representing vessels and their locations.

### Prerequisites

- Python 3.x
- Required libraries: `pandas`, `networkx`, `json`

To install the required libraries, run:
```bash
pip install pandas networkx
```

### Data Preparation

1. **Load the CSV File**:
   The CSV file is loaded into a DataFrame using `pandas`.

```python
import pandas as pd

file_path = '/path/to/your/file.csv'
df = pd.read_csv(file_path)
```

2. **Inspect the Data**:
   Display the first few rows of the DataFrame to understand its structure.

```python
df.head()
```

### Knowledge Graph Schema

The schema for our knowledge graph is defined as follows:
- **Nodes**:
  - Each vessel is represented as a node.
  - Nodes have properties such as `MMSI`, `Latitude`, `Longitude`, `Navigational status`, `SOG` (Speed Over Ground), `COG` (Course Over Ground), `Heading`, etc.
- **Edges**:
  - Edges represent relationships such as "located at" with coordinates.

### Data Transformation

Transform the CSV data into nodes and edges suitable for a knowledge graph.

### Graph Construction

1. **Define Function to Create Nodes and Edges**:

```python
import networkx as nx
from networkx.readwrite import json_graph
import json

def create_knowledge_graph(dataframe):
    G = nx.DiGraph()

    for index, row in dataframe.iterrows():
        # Create a node for each vessel
        vessel_id = row['MMSI']
        G.add_node(vessel_id, **{
            'timestamp': row['# Timestamp'],
            'type_of_mobile': row['Type of mobile'],
            'latitude': row['Latitude'],
            'longitude': row['Longitude'],
            'navigational_status': row['Navigational status'],
            'ROT': row['ROT'],
            'SOG': row['SOG'],
            'COG': row['COG'],
            'heading': row['Heading'],
            'type_of_position_fixing_device': row['Type of position fixing device'],
            'draught': row['Draught'],
            'destination': row['Destination'],
            'ETA': row['ETA'],
            'data_source_type': row['Data source type'],
            'A': row['A'],
            'B': row['B'],
            'C': row['C'],
            'D': row['D']
        })
        
        # Add edges representing the location relationship
        location_node = f"location_{row['Latitude']}_{row['Longitude']}"
        if location_node not in G:
            G.add_node(location_node, latitude=row['Latitude'], longitude=row['Longitude'])
        
        G.add_edge(vessel_id, location_node, relationship="located at")
    
    return G
```

2. **Create the Knowledge Graph**:

```python
# Create the knowledge graph
kg = create_knowledge_graph(df)
```

3. **Convert and Save the Graph**:
   Convert the graph to a JSON serializable format and save it to a file.

```python
# Convert to a JSON serializable format
kg_data = json_graph.node_link_data(kg)

# Save the knowledge graph to a JSON file
with open('/path/to/save/knowledge_graph.json', 'w') as f:
    json.dump(kg_data, f, indent=2)
```

### Output

The output is a JSON file representing the knowledge graph. This file contains nodes and edges with all the properties and relationships derived from the CSV data.

### Running the System

1. **Ensure all prerequisites are installed**.
2. **Update the file paths** in the provided code snippets.
3. **Run the provided code** step-by-step to transform the CSV data into a knowledge graph.

### Future Enhancements

- **Add more relationships**: Extend the graph schema to include more complex relationships between nodes.
- **Data Validation**: Implement data validation checks to ensure data quality before processing.
- **Visualization**: Integrate with graph visualization tools to visually inspect the knowledge graph.
- **Scalability**: Optimize the system to handle larger datasets efficiently.

---

This document provides a comprehensive guide to creating a basic system for transforming CSV data into a knowledge graph. 
Feel free to extend and adapt the system to fit more complex use cases and datasets.
