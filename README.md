# UdaPlay - AI Game Research Agent

UdaPlay is an AI-powered research agent for the video game industry. The project uses a local vector database with ChromaDB for Retrieval-Augmented Generation (RAG) and an agent workflow that can fall back to web search when local information is not enough.

## Project Overview

This project is divided into two main parts:

1. **Offline RAG Pipeline**
   - Loads local JSON files containing video game data.
   - Formats each game into searchable documents.
   - Stores the documents in a persistent ChromaDB vector database.
   - Demonstrates semantic search over the local game database.

2. **AI Agent Development**
   - Implements tools for local retrieval, retrieval evaluation, and web search.
   - Uses a stateful agent workflow.
   - Attempts to answer questions using internal knowledge first.
   - Evaluates whether retrieved documents are useful.
   - Falls back to Tavily web search when local retrieval is not sufficient.
   - Returns structured answers with tool traces and citations.

## Project Structure

```text
project/
├── starter/
│   ├── games/
│   │   └── JSON files with video game data
│   ├── lib/
│   │   └── helper files provided by the starter project
│   ├── chroma_db/
│   │   └── persistent ChromaDB database
│   ├── Udaplay_01_solution_project.ipynb
│   ├── Udaplay_02_solution_project.ipynb
│   ├── README.md
│   └── requirements.txt
```

## Dependencies

The project uses:

```text
Python 3.11+
chromadb
sentence-transformers
openai
tavily-python
python-dotenv
pydantic
```

Install dependencies with:

```bash
pip install -r requirements.txt
```

## Environment Setup

Create a `.env` file in the project directory with the following variables:

```env
OPENAI_API_KEY="YOUR_OPENAI_API_KEY"
TAVILY_API_KEY="YOUR_TAVILY_API_KEY"
CHROMA_OPENAI_API_KEY="YOUR_CHROMA_OPENAI_API_KEY"
```

Note: this implementation uses `SentenceTransformerEmbeddingFunction` for ChromaDB embeddings, so the OpenAI key is not required for local vector search.

## Part 1: Offline RAG Pipeline

Notebook:

```text
Udaplay_01_solution_project.ipynb
```

This notebook:

- Loads game data from JSON files in the `games/` folder.
- Creates searchable text documents from each game.
- Stores the documents in a persistent ChromaDB collection.
- Demonstrates that the vector database can be queried using semantic search.

Example query:

```text
Which game was released for Nintendo 64?
```

Example result:

```text
Super Mario 64 - Nintendo 64 - 1996
```

## Part 2: AI Agent

Notebook:

```text
Udaplay_02_solution_project.ipynb
```

This notebook implements three main tools:

### 1. retrieve_game

Searches the local ChromaDB vector database for relevant game information.

### 2. evaluate_retrieval

Evaluates whether the retrieved documents are useful enough to answer the user question.

This implementation uses the semantic distance returned by ChromaDB. A lower distance means the retrieved document is more similar to the user query. If the best result is below the selected threshold, the result is considered useful. Otherwise, the agent falls back to web search.

### 3. game_web_search

Uses Tavily to search the web for additional information when the local database does not provide enough evidence.

## Agent Workflow

The agent follows this workflow:

```text
START
→ RETRIEVE
→ EVALUATE
→ RESPOND using local database
```

If the retrieved documents are not useful:

```text
START
→ RETRIEVE
→ EVALUATE
→ WEB_SEARCH
→ RESPOND using web results
```

The agent also maintains conversation history during a session.

## Testing

The agent was tested with at least three example questions:

```text
When Pokémon Gold and Silver was released?
Which one was the first 3D platformer Mario game?
Was Mortal Kombat X released for PlayStation 5?
```

For each question, the notebook displays:

- the original question;
- the final answer;
- the source used;
- citation or supporting document;
- tool trace showing the agent workflow.

## Notes

- API keys should not be committed to version control.
- The `.env` file should stay private.
- The ChromaDB database is stored persistently in the `chroma_db/` folder.
- The retrieval evaluation tool uses ChromaDB distance instead of an LLM judge to avoid dependency on LLM quota during development.

## Built With

- Python
- ChromaDB
- Sentence Transformers
- Tavily
- OpenAI
- Pydantic
- python-dotenv

## License

This project was completed as part of the Udacity UdaPlay AI Agent project.
