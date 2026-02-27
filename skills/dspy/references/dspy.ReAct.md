## dspy.ReAct

An agent module that implements the Reasoning and Acting pattern, allowing the LM to use tools iteratively to solve tasks.

```python
import dspy

dspy.configure(lm=dspy.LM('openai/gpt-4o-mini'))

# Define tools as functions
def evaluate_math(expression: str) -> float:
    """Evaluate a mathematical expression."""
    return dspy.PythonInterpreter({}).execute(expression)

def search_wikipedia(query: str) -> list[str]:
    """Search Wikipedia for information."""
    results = dspy.ColBERTv2(url='http://20.102.90.50:2017/wiki17_abstracts')(query, k=3)
    return [x['text'] for x in results]

def get_weather(city: str) -> str:
    """Get the current weather for a city."""
    return f"The weather in {city} is sunny and 75F"

# Create ReAct agent with tools
react = dspy.ReAct(
    signature="question -> answer: float",
    tools=[evaluate_math, search_wikipedia],
    max_iters=5
)

# Agent combines search and calculation
result = react(question="What is 9362158 divided by the year of birth of David Gregory of Kinnairdy castle?")
print(result.answer)      # 5761.328
print(result.trajectory)  # Full history of reasoning and tool calls

# Simple ReAct agent for Q&A
react_qa = dspy.ReAct("question -> answer", tools=[search_wikipedia])
result = react_qa(question="Who invented the telephone?")
print(result.answer)

# Multi-tool agent
agent = dspy.ReAct(
    signature="question -> answer",
    tools=[get_weather, search_wikipedia, evaluate_math],
    max_iters=10
)
result = agent(question="What's the weather in Tokyo and what's 25 * 4?")
```
