---
name: dspy
description: Helps design, implement, and optimize AI systems using the DSPy framework. Use this skill when building RAG pipelines, multi-step reasoning tasks, or modular AI programs that require structured signatures and optimizers instead of manual prompting.
compatibility: Requires dspy.
---

# DSPy Programming Skill

Use this skill to transition from "prompt engineering" to "programming" with Language Models.

## Core Workflow

1.  **Define Signatures**: Specify the input and output fields with type hints.
    ```python
    # ✅ Good: Always use type hints in signatures
    class RAG(dspy.Signature):
        """Answer questions with given context."""
        context: list[str] = dspy.InputField()
        question: str = dspy.InputField()
        answer: str = dspy.OutputField()
    ```

    For complex types (nested structures, validation, or complex objects), define a dedicated Pydantic model:

    ```python
    # ✅ Good: Use Pydantic for complex types in signatures
    from pydantic import BaseModel

    class Citation(BaseModel):
        text: str
        source: str
        page: int

    class ResearchAnswer(dspy.Signature):
        """Answer with citations from the provided sources."""
        context: list[str] = dspy.InputField()
        question: str = dspy.InputField()
        citations: list[Citation] = dspy.OutputField()
    ```

2.  **Select Modules**: Use `dspy.Predict`, `dspy.ChainOfThought`, or `dspy.ReAct`.
3.  **Build the Program**: Inherit from `dspy.Module` and define `__init__` and `forward`.
4.  **Optimize**: Use `dspy.MIPROv2` or `BootstrapFewShot` to tune the program based on data.

## Key Components

- **dspy.LM**: Configure the model (e.g., `dspy.LM('openai/gpt-4o')`).
- **dspy.Signature**: The declarative "what" of the task.
- **dspy.Module**: The structural "how" of the task.
- **dspy.Optimizer**: Algorithms to improve prompts/weights automatically.

## Best Practices

- **Avoid String Formatting**: Never manually build prompts; let DSPy compile them.
- **Small Signatures**: Keep signatures focused. Use multiple modules for complex tasks.
- **Metrics Matter**: Always define a clear metric (e.g., `exact_match` or `semantic_similarity`) before optimizing.
- **Data-Driven**: Provide at least 10-20 examples for `BootstrapFewShot` to be effective.
- **Always Use Type Hints**: Always define type hints for all input and output fields in your `dspy.Signature`. This improves:
  - **Type safety**: Catches errors early at runtime
  - **Documentation**: Makes the signature self-documenting
  - **DSPy optimization**: Helps the compiler generate better prompts
  - **IDE support**: Enables better autocomplete and refactoring

  ```python
  # ✅ Good: Always use type hints
  class RAG(dspy.Signature):
      """Answer questions with given context."""
      context: list[str] = dspy.InputField()
      question: str = dspy.InputField()
      answer: str = dspy.OutputField()
  ```

- **Use Pydantic for Complex Types**: If Python's standard types aren't sufficient (e.g., for nested structures, validation, or complex objects), define a dedicated `pydantic.BaseModel` and use it in your signature:

  ```python
  # ✅ Good: Use Pydantic for complex/validated types
  from pydantic import BaseModel

  class Citation(BaseModel):
      text: str
      source: str
      page: int

  class ResearchAnswer(dspy.Signature):
      """Answer with citations from the provided sources."""
      context: list[str] = dspy.InputField()
      question: str = dspy.InputField()
      citations: list[Citation] = dspy.OutputField()
  ```

  This approach provides:
  - Automatic validation of LLM outputs
  - Clear schema definition for complex data
  - Better error handling and parsing

## Common Patterns

### Simple RAG
```python
class SimpleRAG(dspy.Module):
    def __init__(self, num_passages=3):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=num_passages)
        self.generate_answer = dspy.ChainOfThought(RAG)

    def forward(self, question):
        context = self.retrieve(question).passages
        return self.generate_answer(context=context, question=question)
```

### Agent with Tools
```python
search_tool = dspy.Tool(search_func, name="Search", description="Search the web")
agent = dspy.ReAct(MySignature, tools=[search_tool])
```

## References


[Language model configuration for various providers](references/dspy.LM.md)
[Input/output specifications](references/dspy.Signature.md)
[Basic prediction module](references/dspy.Predict.md)
[Reasoning with step-by-step output](references/dspy.ChainOfThought.md)
[Agent pattern with tool use](references/dspy.ReAct.md)
[Custom program composition](references/dspy.Module.md)
[Function wrapping for agents](references/dspy.Tool.md)
[Program evaluation](references/dspy.Evaluate.md)
[Bayesian optimization for prompts](references/dspy.MIPROv2.md)
[Few-shot demonstration generation](references/dspy.BootstrapFewShot.md)
[Fine-tuning from traces](references/dspy.BootstrapFinetune.md)
[Retrieval integration](references/dspy.ColBERTv2.md)
[Save and Load DSPy Programs](references/dspy-save-and-load-program.md)
