# OpenHands Docker CLI Usage

You can run the OpenHands agent via Docker with the following command and options:

```sh
# Example Docker run command

docker run -it \
    --pull=always \
    -e SANDBOX_RUNTIME_CONTAINER_IMAGE=docker.all-hands.dev/all-hands-ai/runtime:0.33-nikolaik \
    -e WORKSPACE_MOUNT_PATH=$WORKSPACE_BASE \
    -e LLM_API_KEY=$LLM_API_KEY \
    -e LLM_MODEL=$LLM_MODEL \
    -e LOG_ALL_EVENTS=true \
    -v $WORKSPACE_BASE:/opt/workspace_base \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v ~/.openhands-state:/.openhands-state \
    --add-host host.docker.internal:host-gateway \
    --name openhands-app-$(date +%Y%m%d%H%M%S) \
    docker.all-hands.dev/all-hands-ai/openhands:0.33 \
    python -m openhands.core.main --help
```

## OpenHands CLI Flags

```
usage: main.py [-h] [-v] [--config-file CONFIG_FILE] [-d DIRECTORY] [-t TASK] [-f FILE] [-c AGENT_CLS] [-i MAX_ITERATIONS] [-b MAX_BUDGET_PER_TASK] [--eval-output-dir EVAL_OUTPUT_DIR] [--eval-n-limit EVAL_N_LIMIT] [--eval-num-workers EVAL_NUM_WORKERS]
               [--eval-note EVAL_NOTE] [-l LLM_CONFIG] [--agent-config AGENT_CONFIG] [-n NAME] [--eval-ids EVAL_IDS] [--no-auto-continue] [--selected-repo SELECTED_REPO]

Run the agent via CLI

options:
  -h, --help            show this help message and exit
  -v, --version         Show version information
  --config-file CONFIG_FILE
                        Path to the config file (default: config.toml in the current directory)
  -d DIRECTORY, --directory DIRECTORY
                        The working directory for the agent
  -t TASK, --task TASK  The task for the agent to perform
  -f FILE, --file FILE  Path to a file containing the task. Overrides -t if both are provided.
  -c AGENT_CLS, --agent-cls AGENT_CLS
                        Name of the default agent to use
  -i MAX_ITERATIONS, --max-iterations MAX_ITERATIONS
                        The maximum number of iterations to run the agent
  -b MAX_BUDGET_PER_TASK, --max-budget-per-task MAX_BUDGET_PER_TASK
                        The maximum budget allowed per task, beyond which the agent will stop.
  --eval-output-dir EVAL_OUTPUT_DIR
                        The directory to save evaluation output
  --eval-n-limit EVAL_N_LIMIT
                        The number of instances to evaluate
  --eval-num-workers EVAL_NUM_WORKERS
                        The number of workers to use for evaluation
  --eval-note EVAL_NOTE
                        The note to add to the evaluation directory
  -l LLM_CONFIG, --llm-config LLM_CONFIG
                        Replace default LLM ([llm] section in config.toml) config with the specified LLM config, e.g. "llama3" for [llm.llama3] section in config.toml
  --agent-config AGENT_CONFIG
                        Replace default Agent ([agent] section in config.toml) config with the specified Agent config, e.g. "CodeAct" for [agent.CodeAct] section in config.toml
  -n NAME, --name NAME  Session name
  --eval-ids EVAL_IDS   The comma-separated list (in quotes) of IDs of the instances to evaluate
  --no-auto-continue    Disable auto-continue responses in headless mode (i.e. headless will read from stdin instead of auto-continuing)
  --selected-repo SELECTED_REPO
                        GitHub repository to clone (format: owner/repo) 