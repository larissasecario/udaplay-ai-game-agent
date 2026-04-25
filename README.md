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
   - Implements tools for local retrieval, LLM-based retrieval evaluation, and web search.
   - Uses a stateful agent workflow.
   - Attempts to answer questions using internal knowledge first.
   - Evaluates whether retrieved documents are useful with an LLM judge.
   - Falls back to Tavily web search when local retrieval is not sufficient.
   - Returns structured answers with tool traces and citations.
   - Uses conversation history to support multi-turn follow-up questions.

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

Note: this implementation uses `SentenceTransformerEmbeddingFunction` for ChromaDB embeddings, so the OpenAI key is not required for local vector search. The `OPENAI_API_KEY` is used by the LLM judge in the retrieval evaluation step.

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

### 1. `retrieve_game`

Searches the local ChromaDB vector database for relevant game information.

This tool returns candidate documents from the local game dataset, including the document text, metadata, and retrieval distance.

### 2. `evaluate_retrieval`

Evaluates whether the retrieved documents are useful enough to answer the user question.

This implementation uses an **LLM judge**. The judge receives:

- the user question;
- the retrieved documents from ChromaDB;
- the relevant metadata from those documents.

The LLM then decides whether the retrieved context is useful for answering the question. The evaluation returns structured information such as:

- whether the retrieved documents are useful;
- a relevance score;
- a short explanation;
- whether web search is needed.

This satisfies the project requirement that retrieval quality should be evaluated by an LLM rather than only by a ChromaDB distance threshold.

### 3. `game_web_search`

Uses Tavily to search the web for additional information when the local database does not provide enough evidence.

The agent uses this tool when the LLM judge determines that the retrieved local documents are not sufficient to answer the question.

## Agent Workflow

The agent follows this workflow when local retrieval is useful:

```text
START
→ RETRIEVE
→ LLM_EVALUATE_RETRIEVAL
→ RESPOND using local database
```

If the LLM judge determines that the retrieved documents are not useful enough:

```text
START
→ RETRIEVE
→ LLM_EVALUATE_RETRIEVAL
→ WEB_SEARCH
→ RESPOND using web results
```

## Conversation Memory

The agent maintains conversation history during a session.

The `run` method uses previous conversation turns when processing a new question. This allows the agent to understand follow-up questions that depend on earlier context.

Example:

```python
agent = UdaPlayAgent(session_id="demo_two_turns")

response_1 = agent.run("Which Mario game was the first 3D platformer?")
response_2 = agent.run("When was it released?")
```

In this example, the second question depends on the first one. The agent uses the previous interaction to understand that "it" refers to the Mario game discussed in the first turn.

This demonstrates real multi-turn behavior, not only storing conversation history for display.

## Structured Output

The agent returns structured responses with:

- `answer`: final answer to the user;
- `sources`: local or web sources used;
- `confidence`: confidence level;
- `used_web`: whether web search was used;
- `tool_trace`: tools used during the workflow.

The `tool_trace` makes the agent behavior easier to inspect and evaluate.

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

The notebook also includes a multi-turn test:

```python
agent = UdaPlayAgent(session_id="demo_two_turns")

response_1 = agent.run("Which Mario game was the first 3D platformer?")
response_2 = agent.run("When was it released?")
```

This test verifies that the agent uses conversation history when answering a follow-up question.

## Notes

- API keys should not be committed to version control.
- The `.env` file should stay private.
- The ChromaDB database is stored persistently in the `chroma_db/` folder.
- ChromaDB distance is used only as retrieval metadata.
- Retrieval quality is evaluated by an LLM judge.
- The agent uses conversation history to support multi-turn interactions.

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
