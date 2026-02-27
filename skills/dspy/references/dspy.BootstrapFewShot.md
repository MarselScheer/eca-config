## dspy.BootstrapFewShot

Optimizer that generates few-shot demonstrations by running your program and collecting successful traces. Simpler than MIPROv2 but effective for smaller datasets.

```python
import dspy
from dspy.teleprompt import BootstrapFewShot, BootstrapFewShotWithRandomSearch

dspy.configure(lm=dspy.LM('openai/gpt-4o-mini'))

# Prepare data and metric
trainset = [dspy.Example(question="...", answer="...").with_inputs('question') for _ in range(50)]

def metric(example, pred, trace=None):
    return example.answer.lower() == pred.answer.lower()

# Basic BootstrapFewShot
optimizer = BootstrapFewShot(
    metric=metric,
    max_bootstrapped_demos=4,  # Generated demonstrations
    max_labeled_demos=4        # Labeled examples from trainset
)
optimized = optimizer.compile(my_program, trainset=trainset)

# BootstrapFewShot with random search (better quality)
optimizer = BootstrapFewShotWithRandomSearch(
    metric=metric,
    max_bootstrapped_demos=4,
    max_labeled_demos=4,
    num_candidate_programs=10,  # Number of candidates to try
    num_threads=4
)
optimized = optimizer.compile(my_program, trainset=trainset)

# Use a teacher model for bootstrapping
teacher = dspy.ChainOfThought('question -> answer')
teacher.set_lm(dspy.LM('openai/gpt-4'))  # Use stronger model as teacher

optimizer = BootstrapFewShot(metric=metric, teacher_settings={'lm': dspy.LM('openai/gpt-4')})
optimized = optimizer.compile(my_program, trainset=trainset)
```
