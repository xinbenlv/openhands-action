# OpenHands AI Action

A GitHub Action that enables AI-powered task automation using headless mode of 
[OpenHands](https://github.com/All-Hands-AI/OpenHands). 
Execute complex tasks using natural language prompts in your GitHub workflows.

## Features

- Execute AI-powered tasks using natural language prompts
- Customizable LLM models (default: Claude 3 Sonnet)
- Configurable runtime environment
- Detailed logging and output capture
- Support for additional environment variables

## Usage

```yaml
name: Run OpenHands Task
on: [push, pull_request]

jobs:
  run-openhands:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Execute OpenHands Task
        uses: namefi/openhands-action@v1
        with:
          prompt: "Your natural language task description here"
          llm_api_key: ${{ secrets.LLM_API_KEY }}
          # Optional parameters
          llm_model: "anthropic/claude-3-7-sonnet-20250219"
          log_all_events: "true"
          additional_env: |
            {
              "CUSTOM_VAR": "value"
            }
```

## Inputs

| Input | Required | Description | Default |
|-------|----------|-------------|---------|
| `prompt` | Yes | The natural language prompt or task to execute | - |
| `llm_api_key` | Yes | API key for the LLM service | - |
| `llm_model` | No | The LLM model to use | `anthropic/claude-3-7-sonnet-20250219` |
| `log_all_events` | No | Enable detailed logging | `true` |
| `runtime_image` | No | Docker image for runtime | `docker.all-hands.dev/all-hands-ai/runtime:0.32-nikolaik` |
| `openhands_image` | No | Docker image for OpenHands | `docker.all-hands.dev/all-hands-ai/openhands:0.32` |
| `additional_env` | No | JSON string of additional environment variables | `{}` |

## Outputs

| Output | Description |
|--------|-------------|
| `task_id` | The unique identifier of the executed task |
| `status` | The final status of the task execution |
| `result` | The result output from the task execution |

## License

MIT License

## Support

For support, please open an issue in the [GitHub repository](https://github.com/namefi/openhands-action).
