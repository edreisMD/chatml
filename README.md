# Chat Markup Language for LLMs - Python Implementation

Chat Markup Language is an structured "API" interface between software applications and LLM plain text prompts. It is widely used by applications on the OpenAI ChatGPT API, but it isn't yet addopted by all models (either open or closed-source).

This repository aims to b uild an easy implementation of Chat Markup Language (as defined by OpenAI).

### API Request
An API request to LLMs should look like this [(Example from OpenAI API)](https://openai.com/blog/function-calling-and-other-api-updates):
```json
{
  "messages": [
      {"role": "user", "content": "What is the weather like in Boston?"}
  ],
  "functions": [
    {
      "name": "get_current_weather",
      "description": "Get the current weather in a given location",
      "parameters": {
        "type": "object",
        "properties": {
          "location": {
            "type": "string",
            "description": "The city and state, e.g. San Francisco, CA"
          },
          "unit": {
            "type": "string",
            "enum": ["celsius", "fahrenheit"]
          }
        },
        "required": ["location"]
      }
    }
  ]
}
```

### LLM Prompt

Prompt to be input to the LLM as defined on (OpenAI Github ChatML file)[https://github.com/openai/openai-python/blob/main/chatml.md] on (ChatML.md):

```
<|im_start|>system
You are ChatGPT, a large language model trained by OpenAI. Answer as concisely as possible.
Knowledge cutoff: 2021-09-01
Current date: 2023-03-01<|im_end|>
<|im_start|>user
How are you<|im_end|>
<|im_start|>assistant
I am doing well!<|im_end|>
<|im_start|>user
How are you now?<|im_end|>
```

This is another way to structure the input, but I didn't really understand if this is an intermediary step or this is input to the model (unlikely???).

```
[
 {"token": "<|im_start|>"},
 "system\nYou are ChatGPT, a large language model trained by OpenAI. Answer as concisely as possible.\nKnowledge cutoff: 2021-09-01\nCurrent date: 2023-03-01",
 {"token": "<|im_end|>"}, "\n", {"token": "<|im_start|>"},
 "user\nHow are you",
 {"token": "<|im_end|>"}, "\n", {"token": "<|im_start|>"},
 "assistant\nI am doing well!",
 {"token": "<|im_end|>"}, "\n", {"token": "<|im_start|>"},
 "user\nHow are you now?",
 {"token": "<|im_end|>"}, "\n"
]
```

### API Response
An API request to LLMs should look like this ([Example from OpenAI API](https://openai.com/blog/function-calling-and-other-api-updates))
```json
{
  "choices": [
      {
        "finish_reason": "stop",
        "index": 0,
        "message": {
          "content": "The 2020 World Series was played in Texas at Globe Life Field in Arlington.",
          "role": "assistant"
        }
      }
    ]
}
```

Example response with function call:
```json
{ 
  "choices": [{
      "index": 0,
      "message": {
        "role": "assistant",
        "content": null,
        "function_call": {
          "name": "get_current_weather",
          "arguments": "{ \"location\": \"Boston, MA\"}"
        }
      },
      "finish_reason": "function_call"
    }]
}
```

**Module to add more information to the Response (Similar to OpenAI standards)**

### Best practices
- A message should only be inputed to a model if it finishes by <|im_start|>the_desired_role (???)
  
