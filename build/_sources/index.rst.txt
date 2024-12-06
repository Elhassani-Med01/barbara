Langchain-Ollama RetrievalQA Project
====================================

This project demonstrates how to use Langchain with Ollama's language models to build a Retrieval-based Question Answering (RetrievalQA) system. The code includes steps for embedding documents, storing them in a vector database, and retrieving the most relevant information to answer user queries.

Code Explanation
----------------
The following sections break down the code into its components, explaining their purpose and usage.

1. **Importing Libraries**:

   .. code-block:: python

       import os
       from langchain_community.llms import Ollama
       from dotenv import load_dotenv
       from langchain_community.embeddings import OllamaEmbeddings
       from langchain.document_loaders import TextLoader
       from langchain.text_splitter import RecursiveCharacterTextSplitter
       from langchain.vectorstores import Chroma
       from langchain.chains import RetrievalQA
       from langchain.prompts import PromptTemplate

   - `os`: Provides functionality for interacting with the operating system.
   - `langchain_community.llms`: Contains integration with Ollama LLMs for inference.
   - `dotenv`: Loads environment variables from a `.env` file.
   - `RecursiveCharacterTextSplitter`: Splits text into smaller chunks.
   - `Chroma`: A vector database for storing and querying embeddings.
   - `RetrievalQA`: A Langchain chain for question answering with retrieval support.

2. **Loading Environment Variables**:

   .. code-block:: python

       load_dotenv()

   The `dotenv` library reads key-value pairs from a `.env` file and loads them into the environment. This allows you to configure variables like API endpoints or secret keys outside the code.

3. **Initializing the LLM and Embedding Models**:

   .. code-block:: python

       llm = Ollama(model="mistral", base_url="http://127.0.0.1:11434")
       embed_model = OllamaEmbeddings(
           model="mistral",
           base_url="http://127.0.0.1:11434"
       )

   - `Ollama`: Initializes the language model (here, `mistral`) with a local server endpoint.
   - `OllamaEmbeddings`: Generates vector representations of text using the same model.

4. **Input Text for Processing**:

   .. code-block:: python

       text = """
       Depuis sa création en 1997, 
       sous les hautes directives  de  feu  Sa  Majesté le Roi Hassan II, l’Ecole Nationale Supérieure  des  Arts  et  Métiers de Meknès, première école de ce type en Afrique, a formé des in-
       génieurs polyvalents capables de s’intégrer et de s’implanter dans le marché du travail, grâce à une solide formation qui ne se con-tente  pas  seulement  de  la  part-
       ie  théorique  et  technique  mais qui  permet  le  développement 
       et l’enrichissement du savoir-
       faire  et  du  savoir-être  chez  les étudiants.  De  plus,  cette  école prestigieuse offre à ses élèves-in-
       génieurs  plusieurs  opportunités de bénéficier de stages et de se-
       mestres d’études à l’étranger ou 
       encore  de  suivre  des  formations  en double diplôme.
       """

   This is the sample text to be processed by the RetrievalQA system. Replace this with your own document or text data.

5. **Splitting the Text into Chunks**:

   .. code-block:: python

       text_splitter = RecursiveCharacterTextSplitter(chunk_size=512, chunk_overlap=128)
       chunks = text_splitter.split_text(text)

   - `chunk_size`: Defines the maximum size of each text chunk.
   - `chunk_overlap`: Ensures there’s some overlap between chunks to maintain context continuity.

   For example, if the `chunk_size` is 512 characters and `chunk_overlap` is 128, consecutive chunks will overlap by 128 characters.

6. **Creating a Vector Store**:

   .. code-block:: python

       vector_store = Chroma.from_texts(chunks, embed_model)

   - `Chroma`: A vector database that stores the embeddings generated from the text chunks. It uses `embed_model` (OllamaEmbeddings) to generate these vectors.

7. **Setting up the Retriever**:

   .. code-block:: python

       retriever = vector_store.as_retriever()

   The retriever allows querying the vector store to fetch the most relevant chunks based on similarity to the user query.

8. **Creating the RetrievalQA Chain**:

   .. code-block:: python

       qa_chain = RetrievalQA.from_chain_type(llm=llm, chain_type="stuff", retriever=retriever)

   - `llm`: Specifies the LLM to be used for generating answers.
   - `chain_type`: Specifies how the retrieved chunks will be processed. The `stuff` method combines all retrieved documents into a single input for the LLM.
   - `retriever`: Connects the vector store to the QA chain.

9. **Running the QA Chain**:

   .. code-block:: python

       response = qa_chain.run("quand l'ensam a été crée?")
       print(response)

   - The `run` method takes a query as input, retrieves the most relevant documents, and uses the LLM to generate an answer.
   - In this example, the query is *"quand l'ensam a été crée?"* (When was ENSAM created?). The expected response is *"1997"*.

Project Usage
-------------
To use this project:
1. Replace the `text` variable with your own document or data source.
2. Customize the query passed to `qa_chain.run()` based on your needs.
3. Ensure the Ollama server is running at the specified `base_url`.

File Structure
--------------
.. code-block::

    .
    ├── main.py                # Main script containing the code
    ├── .env                   # Environment variables file
    ├── requirements.txt       # List of dependencies
    └── README.rst             # Project documentation

Dependencies
------------
Add the following dependencies to `requirements.txt`:

.. code-block:: text

    langchain
    chromadb
    python-dotenv

License
-------
This project is licensed under the MIT License.

Acknowledgments
---------------
Special thanks to the teams behind Langchain and Ollama for their tools and APIs.
