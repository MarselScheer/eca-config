## dspy.ChainOfThought

A module that teaches the LM to reason step-by-step before producing the final output. Automatically adds a `reasoning` field to the output.

```python
import dspy

dspy.configure(lm=dspy.LM('openai/gpt-4o-mini'))

# Basic chain of thought
math = dspy.ChainOfThought("question -> answer: float")
result = math(question="Two dice are tossed. What is the probability that the sum equals two?")
print(result.reasoning)  # Step-by-step explanation
print(result.answer)     # 0.0277776

# Summarization with reasoning
summarize = dspy.ChainOfThought('document -> summary')
document = """The 21-year-old made seven appearances for the Hammers and netted his only goal
for them in a Europa League qualification round match against Andorran side FC Lustrains
last season. Lee had two loan spells in League One last term, with Blackpool and then
Colchester United. He scored twice for the U's but was unable to save them from relegation."""

response = summarize(document=document)
print(response.reasoning)  # How the LM analyzed the document
print(response.summary)    # Concise summary

# RAG with chain of thought
def search_wikipedia(query: str) -> list[str]:
    results = dspy.ColBERTv2(url='http://20.102.90.50:2017/wiki17_abstracts')(query, k=3)
    return [x['text'] for x in results]

rag = dspy.ChainOfThought('context, question -> response')
question = "What's the name of the castle that David Gregory inherited?"
response = rag(context=search_wikipedia(question), question=question)
print(response.reasoning)  # Analysis of context
print(response.response)   # 'Kinnairdy Castle'

# Citation faithfulness check
class CheckCitation(dspy.Signature):
    """Verify that the text is based on the provided context."""
    context: str = dspy.InputField(desc="facts assumed to be true")
    text: str = dspy.InputField()
    faithfulness: bool = dspy.OutputField()

check = dspy.ChainOfThought(CheckCitation)
result = check(context="He scored twice for Colchester.", text="Lee scored 3 goals for Colchester.")
print(result.reasoning)     # Analysis of the claim
print(result.faithfulness)  # False
```
