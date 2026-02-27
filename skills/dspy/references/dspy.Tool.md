## dspy.Tool

Wrap Python functions as tools for use with agents and manual tool handling. Provides schema extraction and execution capabilities.

```python
import dspy

# Define tool functions
def weather(city: str) -> str:
    """Get weather information for a city."""
    return f"The weather in {city} is sunny"

def calculator(expression: str) -> str:
    """Evaluate a mathematical expression."""
    try:
        result = eval(expression)
        return f"The result is {result}"
    except Exception:
        return "Invalid expression"

# Create tool instances
weather_tool = dspy.Tool(weather)
calc_tool = dspy.Tool(calculator)

# Tool properties
print(weather_tool.name)  # 'weather'
print(weather_tool.desc)  # 'Get weather information for a city.'
print(weather_tool.args)  # Parameter schema

# Manual tool handling with signatures
class ToolSignature(dspy.Signature):
    """Signature for manual tool handling."""
    question: str = dspy.InputField()
    tools: list[dspy.Tool] = dspy.InputField()
    outputs: dspy.ToolCalls = dspy.OutputField()

predictor = dspy.Predict(ToolSignature)
response = predictor(
    question="What's the weather in New York?",
    tools=[weather_tool, calc_tool]
)

# Execute tool calls
for call in response.outputs.tool_calls:
    result = call.execute()  # Automatic function discovery
    # Or explicit: result = call.execute(functions={"weather": weather})
    print(f"Tool: {call.name}, Args: {call.args}, Result: {result}")

# Async tools
import asyncio

async def async_weather(city: str) -> str:
    """Get weather information asynchronously."""
    await asyncio.sleep(0.1)
    return f"Weather in {city}: sunny"

async_tool = dspy.Tool(async_weather)
result = await async_tool.acall(city="Tokyo")

# Run async tools synchronously
with dspy.context(allow_tool_async_sync_conversion=True):
    result = async_tool(city="Tokyo")
```
