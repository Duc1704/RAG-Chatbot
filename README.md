# Simple RAG Chatbot

A minimal Retrieval-Augmented Generation (RAG) chatbot that answers questions using **only** the PDF documents in the `papers/` folder. Built with LangChain, OpenAI, and FAISS.

## How it works

1. **Load:** reads every PDF in `papers/` with `UnstructuredFileLoader`.
2. **Split:** chunks the text (1200 characters, 200 overlap) with `RecursiveCharacterTextSplitter`.
3. **Embed:** embeds the chunks with OpenAI `text-embedding-3-large` and stores them in an in-memory FAISS index (cosine distance).
4. **Retrieve:** fetches up to 5 chunks with a similarity score of at least 0.2.
5. **Answer:** `gpt-5-mini` answers from the retrieved context only. If the answer isn't in the documents, it replies *"I don't know based on the provided documents."*

## Requirements

- Python 3.12
- An OpenAI API key

## Setup

```bash
git clone https://github.com/Duc1704/RAG-Chatbot.git
cd RAG-Chatbot

python3.12 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Create a `.env` file in the project root:

```
OPENAI_API_KEY=sk-...
```

## Usage

Put your PDFs in `papers/`, then run:

```bash
python main.py
```

```
Question: what is microservices?
```

The index is rebuilt on every run, so each run re-embeds all documents.

## Project structure

```
.
├── main.py            # the whole RAG pipeline
├── papers/            # PDFs used as the knowledge base
├── requirements.txt
└── .env               # your API key (not committed)
```

## Troubleshooting

| Problem | Fix |
|---|---|
| `partition_pdf() is not available` | `pip install "unstructured[pdf]"` inside the activated `.venv` |
| `OMP: Error #15 ... libomp.dylib already initialized` (macOS) | torch and faiss each ship their own `libomp`. `main.py` sets `KMP_DUPLICATE_LIB_OK=TRUE` to work around it. |
| OpenAI API key error | The variable must be named `OPENAI_API_KEY` (not `OPEN_API_KEY`). |
| `which pip` points to the system Python | Run `source .venv/bin/activate` first. If you moved or renamed the project folder, recreate `.venv`. |

## License

MIT, see [LICENSE](LICENSE).
