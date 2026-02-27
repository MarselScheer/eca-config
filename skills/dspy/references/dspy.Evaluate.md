## dspy.Evaluate

Evaluate DSPy programs against a dataset using a specified metric. Supports parallel evaluation and progress display.

```python
import dspy
from dspy.evaluate import Evaluate, SemanticF1

dspy.configure(lm=dspy.LM('openai/gpt-4o-mini'))

# Create dataset of examples
trainset = [
    dspy.Example(question="What is Python?", answer="A programming language").with_inputs('question'),
    dspy.Example(question="What is AI?", answer="Artificial Intelligence").with_inputs('question'),
]

# Define a simple metric
def validate_answer(example, pred, trace=None):
    return example.answer.lower() == pred.answer.lower()

# Or use built-in metrics
metric = SemanticF1(decompositional=True)  # For long-form answers

# Simple evaluation loop
scores = []
for x in trainset:
    pred = program(**x.inputs())
    score = metric(x, pred)
    scores.append(score)
print(f"Average: {sum(scores)/len(scores)}")

# Using Evaluate utility with parallelism
evaluator = Evaluate(
    devset=trainset,
    metric=metric,
    num_threads=24,
    display_progress=True,
    display_table=5  # Show 5 example results
)
score = evaluator(my_program)
print(f"Score: {score}")

# Complex metric checking multiple properties
def validate_context_and_answer(example, pred, trace=None):
    answer_match = example.answer.lower() == pred.answer.lower()
    context_match = any((pred.answer.lower() in c) for c in pred.context)

    if trace is None:  # Evaluation mode
        return (answer_match + context_match) / 2.0
    else:  # Bootstrapping mode
        return answer_match and context_match

# AI-powered metric
class Assess(dspy.Signature):
    """Assess the quality of a response."""
    assessed_text: str = dspy.InputField()
    assessment_question: str = dspy.InputField()
    assessment_answer: bool = dspy.OutputField()

def quality_metric(gold, pred, trace=None):
    question = "Is this response accurate and helpful?"
    result = dspy.Predict(Assess)(assessed_text=pred.response, assessment_question=question)
    return float(result.assessment_answer)
```
