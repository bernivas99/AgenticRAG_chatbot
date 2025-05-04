# AgenticRAG_chatbot

### Introduction 
The project implements an agentic RAG chatbot that uses a combination of LLM reasoning and information retrieval from Wikipedia pages and arXiv.
The utilized framework is  LangGraph.


### System Architecture

The architecture is composed of:

+ Dataset pre-processing: The collection of URLs is made up of URLs of Wikipedia pages, and a fixed list of URLs related to Python, Pytorch and Hugging Face. 
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
4. If yes, the LLM retrieves documents from the tool
5. The LLM combines the retrieved information to make a response
6. The response is outputted to the user

### Tools and Data preprocessing

Collection of URLs:
+ fixed URLs related to Python, Pytorch and Hugging Face, which gives opportunity to more topic-specific information retrieval.
+ URLs of Wikipedia pages. The Wikipedia pages are retrieved from a Kaggle dataset which can be found here: 'https://www.kaggle.com/datasets/pseudopoo/main-pages-of-wikipedia'. Tha Kaggle dataset is saved as a CSV file, loaded and then the URLs are created from the Wikipedia page titles with the function load_wikipedia_urls. The first 10 URLs is chosen to be included in the retrieve_docs_from_urls Tool. This is mainly because in my own laptop it takes much time to repsonse for the chatbot if more URLs are added. Needless to say, if more resources are available, the more URLs can be added and the more information the chatbot can have access to.
+ The retrieved information from the URLs is splitted into chunks with RecursiveCharacterTextSplitter. The embeddings of the documents are created with HuggingFaceEmbeddings with one of the Sentence Transformer model: all-MiniLM-L6-v2. The vectorstore for the document chunks and their embeddings is created with FAISS.

Arxiv:
+ For retrieving data from arXiv, the ArxivQueryRun() tool is used.


Others elements of the architecture:

Persistent checkpointing: MemorySaver() for context accumulation

Future expansion could include web browser tools, human in the loop method.


6. Setup and Installation

+ API keys are saved in the 'apikeys_agenticRAG_chatbot.yaml' file. Here we only use the API key for Groq models.
+ The Kaggle dataset is saved as a csv file under the name 'WikiMainPage.csv'
+ To interact with the chatbot, the last cell of the notebook can be used after running the whole notebook. The chatbot asks for new user input after every generated answer. In order to end the conversation with the chatbot, type 'exit' or 'quit'.
+ The required packagescan be installed with requirements.txt






