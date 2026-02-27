## dspy.BootstrapFinetune

Optimizer that distills a prompt-based DSPy program into fine-tuned model weights. Creates training data from successful traces and finetunes the underlying LM.

```python
import dspy
from typing import Literal
from datasets import load_dataset
from dspy.datasets import DataLoader

# Load classification dataset
CLASSES = load_dataset("PolyAI/banking77", split="train").features["label"].names
kwargs = {"fields": ("text", "label"), "input_keys": ("text",), "split": "train"}

trainset = [
    dspy.Example(x, hint=CLASSES[x.label], label=CLASSES[x.label]).with_inputs("text", "hint")
    for x in DataLoader().from_huggingface(dataset_name="PolyAI/banking77", **kwargs)[:2000]
]

# Define classifier
lm = dspy.LM('openai/gpt-4o-mini-2024-07-18')
signature = dspy.Signature("text, hint -> label").with_updated_fields('label', type_=Literal[tuple(CLASSES)])
classify = dspy.ChainOfThought(signature)
classify.set_lm(lm)

# Fine-tune
optimizer = dspy.BootstrapFinetune(
    metric=lambda x, y, trace=None: x.label == y.label,
    num_threads=24
)
finetuned = optimizer.compile(classify, trainset=trainset)

# Use finetuned model
result = finetuned(text="What does a pending cash withdrawal mean?")
print(result.label)  # 'pending_cash_withdrawal'
```
