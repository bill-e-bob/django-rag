# Django RAG Assistant

A local RAG (Retrieval-Augmented Generation) assistant for searching Django repository code. This MVP implementation uses AST parsing to extract top-level functions and class methods, generates embeddings with sentence-transformers, and uses FAISS for efficient similarity search.

## Features

- **AST Parsing**: Extracts top-level functions and class methods from Python files
- **Embeddings**: Uses sentence-transformers for semantic search
- **FAISS Index**: Fast similarity search with FAISS
- **Lexical Reranking**: Simple keyword-based reranking for better results
- **Metadata**: Tracks filepath, line ranges, and symbol names for citations
- **No External APIs**: Fully local, no OpenAI or external services required

## Installation

1. Install Python 3.11 or higher

2. Install dependencies:
```bash
pip install -r requirements.txt
```

## Usage

### Build Index

Parse a Django repository and build the FAISS index:

```bash
python main.py build-index --repo <path-to-django-repo> --out data/
```

**Example:**
```bash
python main.py build-index --repo /path/to/django --out data/
```

This will:
- Parse all Python files in the repository
- Extract top-level functions and class methods
- Generate embeddings using sentence-transformers
- Build and save a FAISS index to `data/`

### Query Index

Search the index with a question:

```bash
python main.py query --index data/ --question "<your question>" --topk 8 --show-context
```

**Example:**
```bash
python main.py query --index data/ --question "How does authentication work?" --topk 8 --show-context
```

**Options:**
- `--index`: Path to the index directory (default: `data/`)
- `--question`: Your search query/question
- `--topk`: Number of results to return (default: 8)
- `--show-context`: Show full code context for each result (optional)

**Example without context:**
```bash
python main.py query --index data/ --question "database connection" --topk 5
```

This will display:
- A summary of results
- Citations with file paths, line ranges, and symbol names
- (Optional) Full code context for each result

## Output Format

### Without `--show-context`:
- Summary panel with key symbols and files found
- Citations with metadata (filepath, line ranges, distances)

### With `--show-context`:
- All of the above, plus full code snippets for each result

## Technical Details

- **Parser**: AST-based extraction of top-level functions and class methods
- **Embeddings**: `all-MiniLM-L6-v2` model (384 dimensions)
- **Index**: FAISS L2 distance index
- **Reranking**: Lexical matching based on keyword overlap and frequency
- **Storage**: FAISS index + JSON metadata files

## Project Structure

```
django-rag/
├── main.py                 # CLI entry point
├── requirements.txt        # Python dependencies
├── rag_assistant/         # Main package
│   ├── __init__.py
│   ├── parser.py          # AST parsing
│   ├── embeddings.py      # Embedding generation
│   ├── index.py           # FAISS index management
│   ├── reranker.py        # Lexical reranking
│   └── query.py           # Query processing and formatting
└── README.md
```

## Limitations (MVP)

- Only parses top-level functions and class methods (not nested functions)
- No LLM integration for answer generation (simple summary only)
- Lexical reranking is basic (keyword matching)
- Single embedding model (not optimized for code)

## Future Enhancements

- Support for nested functions and other code structures
- LLM integration for answer synthesis
- Advanced reranking techniques
- Code-specific embedding models
- Incremental index updates
