---
draft: false
authors:
  - admin
title: Function Calling in LLMs
date: 2025-03-21
summary: LLMs can extend their capabilities by interacting with external systems
  through tool/function calling. Examples like OpenAI, Ollama and Vercel AI SDK.
categories: []
tags:
  - LLM
  - function calling
  - tool
  - OpenAI
  - Ollama
  - Vercel AI SDK
---

## Design and Workflow

Tool/function calling enables LLMs to extend their capabilities by interacting with external systems. This allows them to perform actions beyond text generation, such as executing code, retrieving data, or triggering specific functions.

1.  **Prompt Reception:**
    * The LLM receives a user's prompt.
2.  **Contextual Understanding (Natural Language Understanding - NLU):**
    * The LLM analyzes the prompt to:
        * Identify the user's intent.
        * Extract key information (entities, concepts, actions).
3.  **Tool Classification (Intent Recognition):**
    * The LLM determines if a tool is required by comparing the user's intent and extracted information with the available tool descriptions.
    * This is essentially a multi-class classification task, where each tool (or the absence of any tool) represents a class.
    * The tool's metadata, consisting of `name`, `description`, and `parameters`, serves as the context for the LLM to understand the tool's capabilities.
        * `name`: Unique identifier for tool invocation.
        * `description`: Human-readable explanation of the tool's purpose.
        * `parameters`: Definitions of required tool inputs.
4.  **Tool Selection (Conditional):**
    * If a tool is deemed necessary, the LLM selects the most appropriate tool based on classification confidence and prompt-tool matching.
5.  **Parameter Extraction:**
    * The LLM extracts parameter values from the user's prompt to populate the selected tool's inputs.
6.  **Structured Output Generation:**
    * The LLM generates a structured output (typically JSON) containing:
        * The selected tool's name.
        * The extracted parameters.
7.  **Execution and Response:**
    * The application executes the selected tool using the extracted parameters.
    * The tool's result is returned to the LLM.
    * The LLM generates a final, coherent response to the user, incorporating the tool's result (if applicable).
    * If no tool was called, the LLM directly responds to the user's prompt.

## 2. OpenAI SDK
[OpenAI Function Calling](https://platform.openai.com/docs/guides/function-calling)
### Python
* The `function` parameter in `Completions.create()` defines available tools.  
* The LLM's response contains a `tool_calls` object when a tool should be invoked.  
* The application should then executes the function and provides the result in a subsequent `Completions.create()` call.

The following is get weather example:
```python
from openai import OpenAI

def get_weather(location: str) -> str:
    # This is a mock function - in real world, you'd call a weather API
    return f"The weather in {location} is currently sunny and 22°C"

client = OpenAI()

tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "Get current temperature for a given location.",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": "City and country e.g. Bogotá, Colombia"
                }
            },
            "required": [
                "location"
            ],
            "additionalProperties": False
        },
        "strict": True
    }
}]

messages = [{"role": "user", "content": "What is the weather like in Paris today?"}]

# First completion to get the function call
completion = client.chat.completions.create(
    model="gpt-4o",
    messages=messages,
    tools=tools
)

assistant_message = completion.choices[0].message
print("First response (function call):")
print(assistant_message)

# Get the function call details
tool_call = assistant_message.tool_calls[0]
function_name = tool_call.function.name
function_args = eval(tool_call.function.arguments)

# Execute the function
function_response = get_weather(**function_args)

# Add both the assistant's message and function response to messages
messages.append(assistant_message)
messages.append({
    "role": "tool",
    "tool_call_id": tool_call.id,
    "name": function_name,
    "content": function_response
})

# Second completion to get the final response
completion = client.chat.completions.create(
    model="gpt-4o",
    messages=messages
)

print("\nFinal response (with content):")
print(completion.choices[0].message)
```

```bash title:"output"
First response (function call):
ChatCompletionMessage(content=None, refusal=None, role='assistant', annotations=[], audio=None, function_call=None, tool_calls=[ChatCompletionMessageToolCall(id='call_LFykyebfbsUuTYbUs4NtF8v5', function=Function(arguments='{"location":"Paris, France"}', name='get_weather'), type='function')])

Final response (with content):
ChatCompletionMessage(content='The weather in Paris today is sunny with a temperature of 22°C.', refusal=None, role='assistant', annotations=[], audio=None, function_call=None, tool_calls=None)
```

### JavaScript

Mirrors the Python implementation, using the functions parameter in `chat.completions.create()`.

```javascript
import { OpenAI } from "openai";

// Mock weather function - in real world, you'd call a weather API
function getWeather(location) {
  return `The weather in ${location} is currently sunny and 22°C`;
}

const openai = new OpenAI();

const tools = [{
  "type": "function",
  "function": {
    "name": "get_weather",
    "description": "Get current temperature for a given location.",
    "parameters": {
      "type": "object",
      "properties": {
        "location": {
          "type": "string",
          "description": "City and country e.g. Bogotá, Colombia"
        }
      },
      "required": [
        "location"
      ],
      "additionalProperties": false
    },
    "strict": true
  }
}];

async function main() {
  const messages = [{ role: "user", content: "What is the weather like in Paris today?" }];

  // First completion to get the function call
  const completion = await openai.chat.completions.create({
    model: "gpt-4o",
    messages: messages,
    tools
  });

  const assistantMessage = completion.choices[0].message;
  console.log("First response (function call):");
  console.log(JSON.stringify(assistantMessage, null, 2));

  // Get the function call details
  const toolCall = assistantMessage.tool_calls[0];
  const functionName = toolCall.function.name;
  const functionArgs = JSON.parse(toolCall.function.arguments);

  // Execute the function
  const functionResponse = getWeather(functionArgs.location);

  // Add both the assistant's message and function response to messages
  messages.push(assistantMessage);
  messages.push({
    role: "tool",
    tool_call_id: toolCall.id,
    name: functionName,
    content: functionResponse
  });

  // Second completion to get the final response
  const finalCompletion = await openai.chat.completions.create({
    model: "gpt-4o",
    messages: messages
  });

  console.log("\nFinal response (with content):");
  console.log(JSON.stringify(finalCompletion.choices[0].message, null, 2));
}

main().catch(console.error);
```

```bash
First response (function call):
{
  "role": "assistant",
  "content": null,
  "tool_calls": [
    {
      "id": "call_6MX1RG9XGrLrhsatzypkRqTt",
      "type": "function",
      "function": {
        "name": "get_weather",
        "arguments": "{\"location\":\"Paris, France\"}"
      }
    }
  ],
  "refusal": null,
  "annotations": []
}

Final response (with content):
{
  "role": "assistant",
  "content": "The weather in Paris today is sunny, with a temperature of 22°C.",
  "refusal": null,
  "annotations": []
}
```

## 3. Ollama SDK

### Python 
[Unofficial Reference on ollama Python library](https://leanpub.com/ollama/read#llm-tool-calling-with-ollama)

The Ollama Python SDK relies heavily on <mark>docstrings</mark> as a fundamental component of its runtime function invocation mechanism. When defining functions intended for invocation by the Large Language Model (LLM), docstrings serve as structured metadata that undergoes parsing and conversion into a JSON schema format. This schema elucidates the function's parameters, their respective types, and anticipated behavior, which is subsequently utilized by the model to accurately determine how to invoke the function.

The formatting of these docstrings adheres to a standardized protocol that encompasses parameter descriptions, type hints, and return value specifications. Consequently, the SDK can automatically generate requisite function signatures that are intelligible and actionable for the LLM. 

Upon runtime execution, when the LLM necessitates invoking a function, it first consults the schemas derived from these docstrings to comprehend the function's interface. The SDK employs Python's introspection capabilities (via the inspect module) to parse these docstrings and match the LLM's intended function call with the corresponding implementation. This arrangement facilitates a clean distinction between the function's implementation and its interface description, while maintaining human-readable documentation that serves as both API documentation and runtime function calling specifications.

The docstring parsing is performed lazily at runtime upon initial access to the function, and the resultant schema is typically cached to enhance performance in subsequent calls.

```python
import ollama

# Define your tool function
def get_current_weather(location):
    """
    Gets the current weather in a given location.

    Args:
        location (str): The city and state, e.g., "San Francisco, CA".

    Returns:
        dict: A dictionary containing the weather information,
              or an error message if the location is invalid.
    """
    # Simulate API call
    if location == "San Francisco, CA":
        return {"temperature": 15, "unit": "celsius", "condition": "Cloudy"}
    elif location == "London, UK":
        return {"temperature": 10, "unit": "celsius", "condition": "Rainy"}
    else:
        return {"error": "Location not found"}

# User prompt
user_prompt = "What is the weather like in London, UK?"

# Initiate chat with the model
response = ollama.chat(
    model="llama3.2:latest",
    messages=[{"role": "user", "content": user_prompt}],
    tools=[get_current_weather],
)

# Process the model's response
for tool_call in response.message.tool_calls or []:
    print(f"{tool_call=}")
```

```bash title:"output"
tool_call=ToolCall(function=Function(name='get_current_weather', arguments={'location': 'London, UK'}))
```
### JavaScript/curl

The Ollama JavaScript library's API is designed around the [Ollama REST API](https://github.com/ollama/ollama/blob/main/docs/api.md).  `ollama.chat(request)`.
```js
import ollama from 'ollama'

function getCurrentWeather(location) {
    // Simulate API call
    if (location === "San Francisco, CA") {
        return { temperature: 15, unit: "celsius", condition: "Cloudy" };
    } else if (location === "London, UK") {
        return { temperature: 10, unit: "celsius", condition: "Rainy" };
    } else {
        return { error: "Location not found" };
    }
}

// User prompt
const userPrompt = "What is the weather like in London, UK?";

async function main() {
    try {
        // Initiate chat with the model
        const response = await ollama.chat({
            model: "llama3.2:latest",
            messages: [{ role: "user", content: userPrompt }],
            "tools": [
                {
                    "type": "function",
                    "function": {
                        "name": "getCurrentWeather",
                        "description": "Get the current weather for a location",
                        "parameters": {
                            "type": "object",
                            "properties": {
                                "location": {
                                    "type": "string",
                                    "description": "The location to get the weather for, e.g. San Francisco, CA"
                                },
                                "format": {
                                    "type": "string",
                                    "description": "The format to return the weather in, e.g. 'celsius' or 'fahrenheit'",
                                    "enum": ["celsius", "fahrenheit"]
                                }
                            },
                            "required": ["location"]
                        }
                    }
                }
            ],
        });
        console.log('Initial response with tool call:', response.message.tool_calls);
        // Process the model's response
        const toolCalls = response.message?.tool_calls || [];

        // Handle tool calls and prepare the results
        const toolResults = [];

        for (const toolCall of toolCalls) {
            if (toolCall.function?.name === 'getCurrentWeather') {
                try {
                    // Handle both string and object arguments
                    let args;
                    if (typeof toolCall.function.arguments === 'string') {
                        args = JSON.parse(toolCall.function.arguments);
                    } else {
                        args = toolCall.function.arguments;
                    }

                    const weatherData = getCurrentWeather(args.location);

                    toolResults.push({
                        tool_call_id: toolCall.id,
                        function_name: toolCall.function.name,
                        result: JSON.stringify(weatherData)
                    });
                } catch (error) {
                    console.error('Error executing tool call:', error);
                    toolResults.push({
                        tool_call_id: toolCall.id,
                        function_name: toolCall.function.name,
                        error: error.message
                    });
                }
            }
        }

        // If there were tool calls, send their results back to the model
        if (toolResults.length > 0) {
            const finalResponse = await ollama.chat({
                model: "llama3.2:latest",
                messages: [
                    { role: "user", content: userPrompt },
                    { role: "assistant", content: response.message.content, tool_calls: response.message.tool_calls },
                    { role: "tool", tool_call_id: toolResults[0].tool_call_id, content: toolResults[0].result }
                ]
            });

            console.log('Final response:', finalResponse.message.content);
        } else {
            console.log('Model response:', response.message.content);
        }
    } catch (error) {
        console.error('Error:', error);
    }
}

main();

```

```bash title:"output"
Initial response with tool call: [ { function: { name: 'getCurrentWeather', arguments: [Object] } } ]
Final response: The current temperature in London, UK is around 10°C (50°F), and the condition is rainy.
```

The following <mark>curl</mark> example will also illustrate the logic for initial response with tool call:
```bash
curl http://localhost:11434/api/chat -d '{
  "model": "llama3.2",
  "messages": [
    {
      "role": "user",
      "content": "What is the weather today in Paris?"
    }
  ],
  "stream": false,
  "tools": [
    {
      "type": "function",
      "function": {
        "name": "get_current_weather",
        "description": "Get the current weather for a location",
        "parameters": {
          "type": "object",
          "properties": {
            "location": {
              "type": "string",
              "description": "The location to get the weather for, e.g. San Francisco, CA"
            },
            "format": {
              "type": "string",
              "description": "The format to return the weather in, e.g. 'celsius' or 'fahrenheit'",
              "enum": ["celsius", "fahrenheit"]
            }
          },
          "required": ["location", "format"]
        }
      }
    }
  ]
}'
```

```json title:"response"
{
    "model": "llama3.2",
    "created_at": "2025-03-21T03:49:49.95906Z",
    "message": {
        "role": "assistant",
        "content": "",
        "tool_calls": [
            {
                "function": {
                    "name": "get_current_weather",
                    "arguments": {
                        "format": "celsius",
                        "location": "Paris"
                    }
                }
            }
        ]
    },
    "done_reason": "stop",
    "done": true,
    "total_duration": 1067304584,
    "load_duration": 40793750,
    "prompt_eval_count": 217,
    "prompt_eval_duration": 531020708,
    "eval_count": 25,
    "eval_duration": 494243542
}
```

## 4. Vercel AI SDK

* The [Vercel AI SDK](https://sdk.vercel.ai/docs/introduction) focuses on streamlining the integration of different LLMs providers with user interfaces, particularly for streaming responses.  
* When used with OpenAI, Vercel AI SDK uses the function call implementation of OpenAI SDK.

The following gives a pseudo-code example of chat api with registerred tools:
```ts
export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = streamText({
    model: openai('gpt-4o'),
    messages,
    system: `You are a helpful assistant. Check your knowledge base before answering any questions.
    Only respond to questions using information from tool calls.
    if no relevant information is found in the tool calls, respond, "Sorry, I don't know."`,
    tools: {
      addResource: tool({
        description: `add a resource to your knowledge base.
          If the user provides a random piece of knowledge unprompted, use this tool without asking for confirmation.`,
        parameters: z.object({
          content: z
            .string()
            .describe('the content or resource to add to the knowledge base'),
        }),
        execute: async ({ content }) => createResource({ content }),
      }),
      getInformation: tool({
        description: `get information from your knowledge base to answer questions.`,
        parameters: z.object({
          question: z.string().describe('the users question'),
        }),
        execute: async ({ question }) => findRelevantContent(question),
      }),
    },
  });

  return result.toDataStreamResponse();
}
```