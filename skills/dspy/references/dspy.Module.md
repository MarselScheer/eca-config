## dspy.Module

Base class for building custom DSPy programs that compose multiple modules together. Enables complex multi-step pipelines.

```python
import dspy

dspy.configure(lm=dspy.LM('openai/gpt-4o-mini'))

# Simple RAG module
class RAG(dspy.Module):
    def __init__(self, num_docs=5):
        self.num_docs = num_docs
        self.respond = dspy.ChainOfThought('context, question -> response')

    def forward(self, question):
        # search() is your retrieval function
        context = search(question, k=self.num_docs)
        return self.respond(context=context, question=question)

rag = RAG()
result = rag(question="What is machine learning?")
print(result.response)

# Multi-hop search module
class MultiHopSearch(dspy.Module):
    def __init__(self, num_docs=10, num_hops=4):
        self.num_docs = num_docs
        self.num_hops = num_hops
        self.generate_query = dspy.ChainOfThought('claim, notes -> query')
        self.append_notes = dspy.ChainOfThought('claim, notes, context -> new_notes: list[str], titles: list[str]')

    def forward(self, claim: str) -> dspy.Prediction:
        notes, titles = [], []
        for _ in range(self.num_hops):
            query = self.generate_query(claim=claim, notes=notes).query
            context = search(query, k=self.num_docs)
            prediction = self.append_notes(claim=claim, notes=notes, context=context)
            notes.extend(prediction.new_notes)
            titles.extend(prediction.titles)
        return dspy.Prediction(notes=notes, titles=list(set(titles)))

# Article drafting pipeline
class DraftArticle(dspy.Module):
    def __init__(self):
        self.build_outline = dspy.ChainOfThought("topic -> title, sections: list[str], section_subheadings: dict[str, list[str]]")
        self.draft_section = dspy.ChainOfThought("topic, section_heading, section_subheadings: list[str] -> content")

    def forward(self, topic):
        outline = self.build_outline(topic=topic)
        sections = []
        for heading, subheadings in outline.section_subheadings.items():
            section = self.draft_section(
                topic=outline.title,
                section_heading=f"## {heading}",
                section_subheadings=[f"### {s}" for s in subheadings]
            )
            sections.append(section.content)
        return dspy.Prediction(title=outline.title, sections=sections)

draft = DraftArticle()
article = draft(topic="World Cup 2002")

# Track LM usage
dspy.configure(track_usage=True)
result = rag(question="What is AI?")
print(result.get_lm_usage())  # {'openai/gpt-4o-mini': {'completion_tokens': 61, 'prompt_tokens': 260, ...}}
```
