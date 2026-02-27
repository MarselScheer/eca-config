## dspy.Predict

The fundamental DSPy module that takes a signature and produces a callable that invokes the language model. All other modules are built using Predict internally.

```python
import dspy
from typing import Literal

dspy.configure(lm=dspy.LM('openai/gpt-4o-mini'))

# Basic prediction
classify = dspy.Predict('sentence -> sentiment: bool')
response = classify(sentence="This movie was fantastic!")
print(response.sentiment)  # True

# Multiple outputs
extract = dspy.Predict("text -> title, headings: list[str], entities: list[dict[str, str]]")
response = extract(text="Apple Inc. announced iPhone 14. CEO Tim Cook highlighted features.")
print(response.title)     # 'Apple Unveils iPhone 14'
print(response.headings)  # ['Introduction', 'Key Features']
print(response.entities)  # [{'entity': 'Apple Inc.', 'type': 'Organization'}, ...]

# Classification with Literal types
class Classify(dspy.Signature):
    """Classify sentiment of a given sentence."""
    sentence: str = dspy.InputField()
    sentiment: Literal['positive', 'negative', 'neutral'] = dspy.OutputField()
    confidence: float = dspy.OutputField()

classify = dspy.Predict(Classify)
result = classify(sentence="This book was super fun to read, though not the last chapter.")
print(result.sentiment)    # 'positive'
print(result.confidence)   # 0.75

# Request multiple completions
predict = dspy.Predict('question -> answer', n=5, temperature=1.0)
response = predict(question="What's great about Python?")
print(response.completions.answer)  # List of 5 different answers

# Configure at call time
predict = dspy.Predict("question -> answer")
result = predict(question="What is 1 + 52?", config={"temperature": 0.5})
```
