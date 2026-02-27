## dspy.LM

Configure and use language models from various providers (OpenAI, Anthropic, Google, local models) through a unified interface. The LM class handles authentication, caching, and provides utilities for inspecting history and usage.

```python
import dspy

# Configure OpenAI model (authenticate via OPENAI_API_KEY env var)
lm = dspy.LM('openai/gpt-4o-mini', api_key='YOUR_API_KEY')
dspy.configure(lm=lm)

# Configure Anthropic model
lm = dspy.LM('anthropic/claude-sonnet-4-5-20250929', api_key='YOUR_ANTHROPIC_API_KEY')

# Configure Google Gemini
lm = dspy.LM('gemini/gemini-2.5-flash', api_key='YOUR_GEMINI_API_KEY')

# Configure local model via Ollama
lm = dspy.LM('ollama_chat/llama3.2', api_base='http://localhost:11434', api_key='')

# Configure local model via SGLang
lm = dspy.LM('openai/meta-llama/Llama-3.1-8B-Instruct',
             api_base='http://localhost:7501/v1', api_key='local', model_type='chat')

# Call LM directly
response = lm("Say this is a test!", temperature=0.7)  # => ['This is a test!']
response = lm(messages=[{"role": "user", "content": "Say this is a test!"}])

# Configure generation parameters
lm = dspy.LM('openai/gpt-4o-mini', temperature=0.9, max_tokens=3000, cache=False)

# Use different LMs in different contexts (thread-safe)
dspy.configure(lm=dspy.LM('openai/gpt-4o-mini'))
with dspy.context(lm=dspy.LM('openai/gpt-3.5-turbo')):
    response = qa(question="What is DSPy?")

# Inspect LM history
print(len(lm.history))  # Number of calls
print(lm.history[-1].keys())  # ['prompt', 'messages', 'kwargs', 'response', 'outputs', 'usage', 'cost', ...]
```
