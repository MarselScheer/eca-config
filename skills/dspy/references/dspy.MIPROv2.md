## dspy.MIPROv2

Advanced optimizer that generates optimized instructions and few-shot examples using Bayesian optimization. Tunes prompts to maximize your metric.

```python
import dspy
from dspy.datasets import HotPotQA

dspy.configure(lm=dspy.LM('openai/gpt-4o-mini'))

# Prepare training data
trainset = [x.with_inputs('question') for x in HotPotQA(train_seed=2024, train_size=500).train]

# Define metric
def answer_match(example, pred, trace=None):
    return example.answer.lower() == pred.answer.lower()

# Create your program
class RAG(dspy.Module):
    def __init__(self):
        self.respond = dspy.ChainOfThought('context, question -> response')

    def forward(self, question):
        context = search(question, k=5)
        return self.respond(context=context, question=question)

# Optimize with MIPROv2
optimizer = dspy.MIPROv2(
    metric=answer_match,
    auto="light",  # "light", "medium", or "heavy"
    num_threads=24
)

optimized_program = optimizer.compile(
    RAG(),
    trainset=trainset,
    max_bootstrapped_demos=2,  # Number of generated examples
    max_labeled_demos=2        # Number of labeled examples
)

# Use the optimized program
result = optimized_program(question="What is the capital of France?")

# Optimize ReAct agent
react = dspy.ReAct("question -> answer", tools=[search_wikipedia])
optimizer = dspy.MIPROv2(metric=dspy.evaluate.answer_exact_match, auto="light", num_threads=24)
optimized_react = optimizer.compile(react, trainset=trainset)

# Save and load optimized programs
optimized_program.save("optimized_rag.json")

loaded_program = RAG()
loaded_program.load("optimized_rag.json")
```
