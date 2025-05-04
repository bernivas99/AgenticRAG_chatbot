# AgenticRAG_chatbot
Chatbot with Agentic RAG

### Introduction 
The project implements an agentic RAG chatbot that uses a combination of LLM reasoning and information retrieval from Wikipedia pages and arXiv.
The utilized framework is  LangGraph.



### System Architecture
The structure of the agentic RAG chatbot can be seen on the following diagram:

The architecture is composed of:

+ Dataset pre-processing: The collection of URLs is made up of URLs of Wikipedia pages, and a fixed list of URLs related to Python, Pytorch and Hugging Face. The Wikipedia pages are retrieved from a Kaggle dataset which can be found here: 'https://www.kaggle.com/datasets/pseudopoo/main-pages-of-wikipedia'.
+ The chatbot: It is an LLM from ChatGroq, the Qwen/QwQ-32B model. It is a reasoning model with 32-billion parameters. QwQ-32B excels at mathematical reasoning, coding, and complex-problem solving with performance comparable to the 20x larger DeepSeek-R1.
+ Tools: The chatbot has access to two types of Tools, the first one is a collection of URLs, the second one is an arXiv searcher.
+ Transitions between the architecture components

The architecture is defined as a graph (StateGraph) with 4 nodes and edges between them. 

The role of the nodes:
+ There are two specific nodes, the START node and END node. The former is the entry point of the workflow, the latter is the finishing point.
+ The Chatbot node realizes the LLM with which the user is interacting.
+ The Tool node contains the specific tools which the LLm has access to.

There are two types of edges in the graph: fixed edges and conditional edges. The fixed edges define a fix route from one node to another, while the conditional edges allows to choose which node to go next based on the current state of the graph. 

In the implemented graph, the graph flow follows the transitions START → chatbot → tools → END. Also, since conditional edges are defined between the LLM and the tools, the LLM can choose between utilizing a tool or not (it is implemented by the function tool_condition). We let the LLM to decide on its own based on the current state. 

The Step-by-step Query flow is the following:

1. The user inputs a query
2. The LLM receives it via chatbot node
3. Based on its own decision the LLM routes to tool(s) 
4. The tool retrieves documents
5. The LLM combines the retrieved information to make a response
6. The response is outputted to the user

### Tools and Data preprocessing

Embedding + VectorStore (e.g. HuggingFace embeddings + FAISS)
(e.g. CSV loading, URL mapping)

Wikipedia:

Source: Main Topics CSV or Hugging Face dataset

Method: Title-to-URL transformation → retrieval

Arxiv:

Tool: ArxivQueryRun()

Logic: On LLM instruction, fetch summaries from Arxiv API

Error Handling: Wrapped to avoid second-call failures

Others (Optional):

Memory: MemorySaver for context accumulation

Future expansion (e.g. TavilySearch, browser tools)

5. Technical Design Decisions
Why use LangGraph vs. vanilla LangChain agents?

Why vectorstore choice (FAISS vs. Chroma)?

Why use structured tools (@tool) over plain functions?

Handling of tool call failures and retries

Streaming vs. blocking inference

✅ Example:

We chose LangGraph to better modularize agent behavior and visualize control flow. Structured tools are used to ensure schema consistency for OpenAI tool-calling.

6. Setup and Installation
Required packages (pip install ...)

Environment variables (e.g. OPENAI_API_KEY, GROQ_API_KEY)

How to run in a Jupyter notebook vs. script

Optional: requirements.txt or conda.yaml

7. How to Extend
Add a new tool (e.g. Semantic Scholar)

Swap LLMs (e.g. use Claude or Mistral)

Use a persistent vectorstore backend

Enable multi-turn memory via MemorySaver or custom storage

8. Known Limitations / Future Work
Arxiv tool state bug (solved via per-call instantiation)

Limited memory or context window

Rate limits on external APIs

Future: integrate citations, full document previews, chat history

9. Appendix
Code snippets for each component

Sample inputs/outputs

Prompt templates (if custom prompts used)

Graph diagram source (e.g. Mermaid, draw.io)
