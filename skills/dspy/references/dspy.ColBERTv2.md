## Retrieval with dspy.ColBERTv2

Built-in retrieval using ColBERTv2 for semantic search over document collections.

```python
import dspy

# Use hosted ColBERTv2 endpoint
retriever = dspy.ColBERTv2(url='http://20.102.90.50:2017/wiki17_abstracts')

# Search for relevant documents
results = retriever("What is machine learning?", k=5)
for doc in results:
    print(doc['text'])
    print(doc['score'])

# Use in RAG pipeline
def search_wikipedia(query: str) -> list[str]:
    results = dspy.ColBERTv2(url='http://20.102.90.50:2017/wiki17_abstracts')(query, k=3)
    return [x['text'] for x in results]

rag = dspy.ChainOfThought('context, question -> response')
context = search_wikipedia("Who invented the telephone?")
response = rag(context=context, question="Who invented the telephone?")

# Custom embeddings retriever
embedder = dspy.Embedder('openai/text-embedding-3-small', dimensions=512)
search = dspy.retrievers.Embeddings(embedder=embedder, corpus=documents, k=5)

results = search("query text")
print(results.passages)  # Retrieved document texts
```
