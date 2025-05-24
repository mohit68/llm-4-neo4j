# Neo4j + LLaMA 3 Chatbot

This project is an interactive command-line chatbot that converts natural language questions into Cypher queries using the LLaMA 3 language model via the Groq API, and runs them against a local Neo4j graph database.

## Features

- Converts natural language questions to **Cypher queries**.
- Utilizes **LLaMA 3 (70B)** model through the **Groq API**.
- Connects to a **Neo4j** instance and runs generated queries.
- Extracts and includes schema context (labels & relationships).
- Provides a command-line interface for user interaction.

---

## Project Structure

```bash
├── chatbot.ipynb       # Main script containing the chatbot logic
├── README.md        # Project documentation
```

---

## Requirements

- Python 3.7+
- Neo4j running locally (`bolt://localhost:7687`)
- Groq API Key

---

## Installation

1. **Clone the repository**:

```bash
git clone https://github.com/yourusername/neo4j-llama3-chatbot.git
cd neo4j-llama3-chatbot
```

2. **Install dependencies**:

```bash
pip install neo4j requests
```

3. **Configure environment variable** (optional but recommended):

```bash
export GROQ_API_KEY="your_actual_groq_api_key"
```

> ⚠️ If not set, a default key is hardcoded in the script (for testing).

4. **Ensure Neo4j is running** locally at `bolt://localhost:7687` using: `https://ifs.dfki.de/industrial-edge-cloud/hannover-messe/aas-repository-neo4j-kafka-plugin` and is accessible with:
   - **Username**: `neo4j`
   - **Password**: *(leave blank or update in the script)*

---

## Usage

Run the chatbot:

```bash
python chatbot.ipynb
```

You’ll be greeted with:

```
🔗 Connected to Neo4j.
🤖 Chatbot powered by LLaMA 3 (Groq API). Type 'exit' to quit.
```

Then start asking your graph-related questions in plain English:

```
🧠 Ask your question (or type 'exit'):
```

---

## How It Works

### 1. **Schema Extraction** (`get_graph_schema`)
Fetches all node labels and relationship types from the Neo4j database to inform the language model.

```cypher
MATCH (n) RETURN DISTINCT labels(n) AS labels
CALL db.relationshipTypes()
```

This schema is injected into the system prompt to provide grounding for query generation.

---

### 2. **Prompt Engineering**
The assistant prompt is structured like this:

```
You are an assistant that converts natural language questions into Cypher queries for a Neo4j knowledge graph.

Schema details:
Labels: [...]
Relationship Types: [...]

Only return Cypher queries. Do not explain.
```

---

### 3. **Query Generation** (`generate_cypher_query`)
Makes an HTTP request to the Groq API, using the `llama3-70b-8192` model to generate the Cypher query.

```python
response = requests.post(GROQ_API_URL, headers=headers, json=payload)
```

---

### 4. **Execution Loop** (`chat_bot`)
- Accepts user input.
- Sends input (with prior context) to Groq API.
- Displays the generated Cypher query.
- Optionally executes it on Neo4j and prints results.

---

## 📌 Example

```text
🧠 Ask your question: Find the submodel element with idShort 'TypeOfEmailAddress'

💬 Generating Cypher query...

📄 Cypher Query:
MATCH (se:SubmodelElement {idShort: 'TypeOfEmailAddress'}) RETURN se;

▶️  Run this query? (y/n): y

📊 Query Results:
{'se': {'smId': 'https://smartfactory.de/submodels/1b8b8375-73e6-4f37-9d7c-6d08e21b066f', 'idShort': 'TypeOfEmailAddress', 'idShortPath': 'ContactInformation.Email.TypeOfEmailAddress', 'value': '0173-1#07-AAS754#001'}}
{'se': {'smId': 'https://smartfactory.de/submodels/1b8b8375-73e6-4f37-9d7c-6d08e21b066f', 'idShort': 'TypeOfEmailAddress', 'idShortPath': 'Address.Address.Email01.TypeOfEmailAddress', 'value': 'Office'}}
...
```

---

## 🔐 Authentication Notes

Currently, the Neo4j connection assumes no password (blank). If your Neo4j instance is secured:

- Update these variables:

```python
NEO4J_USER = "your_username"
NEO4J_PASSWORD = "your_password"
```

---

## Tech Stack

| Component        | Tool/Service       |
|------------------|--------------------|
| LLM Backend      | Groq API (LLaMA 3) |
| Graph Database   | Neo4j              |
| Programming Lang | Python             |

---

## 🧑‍💻 Author

Built by [Mohit Raj Lokwani]