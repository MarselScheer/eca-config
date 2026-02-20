## dspy.Signature

Define input/output specifications for DSPy modules using inline strings or class-based definitions. Signatures declare the semantic roles of fields and their types.

```python
import dspy
from typing import Literal

# Inline signatures - simple string format
qa = dspy.Predict('question -> answer')
classify = dspy.Predict('sentence -> sentiment: bool')
summarize = dspy.ChainOfThought('document -> summary')
rag = dspy.ChainOfThought('context: list[str], question: str -> answer: str')

# Inline signature with instructions
toxicity = dspy.Predict(
    dspy.Signature(
        "comment -> toxic: bool",
        instructions="Mark as 'toxic' if the comment includes insults, harassment, or sarcastic remarks.",
    )
)

# Class-based signature for more control
class Emotion(dspy.Signature):
    """Classify emotion of the input sentence."""
    sentence: str = dspy.InputField()
    sentiment: Literal['sadness', 'joy', 'love', 'anger', 'fear', 'surprise'] = dspy.OutputField()

classify = dspy.Predict(Emotion)
result = classify(sentence="I'm feeling great today!")
print(result.sentiment)  # 'joy'

# Signature with field descriptions
class CheckCitationFaithfulness(dspy.Signature):
    """Verify that the text is based on the provided context."""
    context: str = dspy.InputField(desc="facts here are assumed to be true")
    text: str = dspy.InputField()
    faithfulness: bool = dspy.OutputField()
    evidence: dict[str, list[str]] = dspy.OutputField(desc="Supporting evidence for claims")

# Multi-modal signature with images
class DogBreedClassifier(dspy.Signature):
    """Output the dog breed of the dog in the image."""
    image_1: dspy.Image = dspy.InputField(desc="An image of a dog")
    answer: str = dspy.OutputField(desc="The dog breed")

classify = dspy.Predict(DogBreedClassifier)
result = classify(image_1=dspy.Image.from_url("https://example.com/dog.jpg"))
```
