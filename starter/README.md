Claro — substitui seu README por este aqui. Ele já deixa explícito que você corrigiu os dois pontos da revisão: **LLM judge em `evaluate_retrieval`** e **memória multi-turn real no agente**.

````markdown
# UdaPlay - AI Game Research Agent Project

## Project Overview

UdaPlay is an AI-powered research agent for the video game industry. The agent answers questions about video games using a local Retrieval-Augmented Generation (RAG) pipeline, evaluates the quality of retrieved documents with an LLM judge, and falls back to web search when the local knowledge base is not enough.

The project is divided into two main parts:

1. **Offline RAG with ChromaDB**
2. **AI Agent Development with tools, structured outputs, retrieval evaluation, web search, and conversation memory**

---

## Project Structure

### Part 1: Offline RAG

In Part 1, the project builds a local vector database using ChromaDB. The database stores and retrieves video game information from local JSON files.

Key tasks:

- Set up ChromaDB as a persistent client
- Create a ChromaDB collection with an embedding function
- Process and index local video game JSON files
- Store each game as a searchable document

Each game document includes information such as:

- Name
- Platform
- Genre
- Publisher
- Description
- Year of release

---

### Part 2: AI Agent Development

In Part 2, the project implements an AI research agent that combines local RAG retrieval, retrieval evaluation, web search, structured responses, and short-term conversation memory.

The agent can:

1. Retrieve relevant game information from the local ChromaDB database
2. Evaluate retrieved results using an LLM-based judge
3. Use web search when local retrieval is not sufficient
4. Maintain conversation history across multiple turns
5. Use previous user interactions to understand follow-up questions
6. Return structured responses with answer text, source information, confidence, and tool trace

---

## Implemented Agent Tools

### 1. `retrieve_game`

Searches the local ChromaDB vector database for relevant game information.

This tool is used first when the user asks a question about video games. It returns matching documents from the local dataset, including metadata and retrieval distance.

---

### 2. `evaluate_retrieval`

Evaluates whether the documents retrieved by `retrieve_game` are useful for answering the user's question.

This implementation uses an **LLM judge**, not only the ChromaDB distance score. The LLM receives the user question and the retrieved documents, then decides whether the retrieved context is relevant enough.

The evaluation returns structured information such as:

- Whether the retrieved documents are useful
- A relevance score
- A short explanation
- Whether web search is needed

This addresses the project requirement that retrieval quality should be assessed by an LLM evaluator.

---

### 3. `game_web_search`

Performs a web search when the local RAG results are not enough.

This tool is used as a fallback when:

- The retrieved local documents are not relevant
- The LLM judge determines that more information is needed
- The question asks for information that may not exist in the local dataset

---

## Conversation Memory

The agent maintains short-term conversation memory using `conversation_history`.

The `run` method uses the previous conversation turns when processing a new user question. This allows the agent to correctly answer follow-up questions that depend on earlier context.

Example:

```python
agent = UdaPlayAgent(session_id="demo_two_turns")

first_response = agent.run("Which Mario game was the first 3D platformer?")
second_response = agent.run("When was it released?")
```
````

In this example, the second question depends on the first one. The agent uses the previous interaction to understand that "it" refers to the first 3D Mario platformer.

This demonstrates real multi-turn behavior, not only saving the conversation history for display.

---

## Structured Output

The agent returns responses using a structured format.

The response includes:

- `answer`: the final answer to the user
- `sources`: sources used to answer the question
- `confidence`: confidence level
- `used_web`: whether web search was used
- `tool_trace`: trace of the tools used during execution

This makes the agent behavior easier to inspect and evaluate.

---

## Requirements

### Environment Setup

Create a `.env` file with the required API keys:

```env
OPENAI_API_KEY="YOUR_OPENAI_API_KEY"
TAVILY_API_KEY="YOUR_TAVILY_API_KEY"
```

Optional, depending on the embedding setup used in Part 1:

```env
CHROMA_OPENAI_API_KEY="YOUR_OPENAI_API_KEY"
```

Do not commit your `.env` file to GitHub.

---

## Project Dependencies

- Python 3.11+
- ChromaDB
- OpenAI
- Tavily
- python-dotenv
- Pydantic

---

## Directory Structure

```text
project/
├── starter/
│   ├── games/                           # JSON files with game data
│   ├── lib/                             # Custom library implementations
│   │   ├── llm.py                       # LLM abstractions
│   │   ├── messages.py                  # Message handling
│   │   └── tooling.py                   # Tool implementations
│   ├── Udaplay_01_starter_project.ipynb # Part 1: Offline RAG
│   └── Udaplay_02_starter_project.ipynb # Part 2: AI agent
```

---

## Getting Started

1. Create and activate a virtual environment
2. Install the required dependencies
3. Create a `.env` file with the required API keys
4. Run `Udaplay_01_starter_project.ipynb` to build the ChromaDB vector database
5. Run `Udaplay_02_starter_project.ipynb` to test the AI agent

---

## Testing the Agent

After completing both parts, test the agent with questions such as:

```text
When was Pokémon Gold and Silver released?
```

```text
Which one was the first 3D platformer Mario game?
```

```text
Was Mortal Kombat X released for PlayStation 5?
```

Also test a multi-turn interaction:

```python
agent = UdaPlayAgent(session_id="demo_two_turns")

response_1 = agent.run("Which Mario game was the first 3D platformer?")
response_2 = agent.run("When was it released?")

print(response_1)
print(response_2)
```

The second question should use the context from the first question.

---

## Evaluation Notes

This project demonstrates:

- Local RAG retrieval with ChromaDB
- LLM-based retrieval evaluation
- Web search fallback
- Structured agent responses
- Tool trace visibility
- Short-term memory for multi-turn conversations

The retrieval evaluation is performed by an LLM judge instead of relying only on ChromaDB distance thresholds. The agent also uses previous conversation turns when answering follow-up questions.

---

## Notes

- Keep API keys in the `.env` file
- Do not upload API keys to GitHub
- Run the notebooks in order
- Save notebook outputs before submission
- Include examples showing both direct questions and multi-turn follow-up questions
