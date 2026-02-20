## Saving and Loading Programs

Save optimized programs to disk and reload them for inference without re-optimization.

```python
import dspy

# After optimization
optimized_program.save("my_optimized_program.json")

# Later, load the program
class MyProgram(dspy.Module):
    def __init__(self):
        self.predict = dspy.ChainOfThought('question -> answer')

    def forward(self, question):
        return self.predict(question=question)

loaded = MyProgram()
loaded.load("my_optimized_program.json")

# Use loaded program
result = loaded(question="What is DSPy?")

# Inspect the saved state (plain-text JSON)
import json
with open("my_optimized_program.json") as f:
    state = json.load(f)
    print(state)  # Contains all parameters, demonstrations, and instructions
```
