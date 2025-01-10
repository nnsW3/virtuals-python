# GAME SDK

## Overview

This library enables the creation of AI agents, in which actions are defined by the creator/developer. These agents can be used:
- Worker Mode: to autonomously execute tasks through interaction with the agent. User interaction and tasks is required for the agent to execute tasks. For example, "Bring me some fruits and then go sit on the chair".
- Agent Mode: to autonomously function in an open-ended manner by just providing a general goal. The agent independently and continously decides tasks for itself in an open-ended manner, and attempts to complete them.


## Core Features

### 1. Functions and Executables

Functions define available actions for the agent:

```python
my_function = Function(
    fn_name="action_name",
    fn_description="Description of action",
    args=[Argument(name="param", type="type", description="param description")],
    executable=function_implementation
)
```
Executable functions must return a tuple of:
- FunctionResultStatus
- Message string
- Additional info dictionary


### 2. State Management

Easy and flexible wayto define the state management, what the agent sees and how that changes.

```python
def get_state_fn(function_result: FunctionResult, current_state: dict) -> dict:
    """
    Updates state based on function execution results
    
    Args:
        function_result: Result from last executed function
        current_state: Current state dictionary or None for initialization
        
    Returns:
        Updated state dictionary
    """
    if current_state is None:
        return initial_state
    
    # Update state based on function_result.info
    new_state = update_state(current_state, function_result)
    return new_state
```

Key features:
- Can be shared or unique per worker
- Processes function execution results to update state

### 3. Workers
Workers are simple interactiable agents that exectue the tasks defined by the user. They can be specialized agents with defined capabilities:

```python
worker = Worker(
    api_key="your_api_key",
    description="Worker description",
    instruction="Default instructions",
    get_state_fn=state_function,
    action_space=[list_of_functions]
)

worker.run("Bring me some fruits")
```



### 4. Agents

Agents are used to autonomously function in an open-ended manner by just providing a general goal. Tasks are generated by the agent itself continuously, and the agent will attempt to complete them. You can provide many workers to the agent, and they will be used to execute the tasks.

```python
agent = Agent(
    api_key="your_api_key",
    name="Agent Name",
    agent_goal="Primary goal",
    agent_description="Description",
    get_agent_state_fn=agent_state_function,
    workers=[worker1, worker2]
)

# Compile and run
agent.compile()
agent.run()
```

Use WorkerConfig for agent composition:

```python
worker_config = WorkerConfig(
    id="worker_id",
    worker_description="Description",
    get_state_fn=state_function,
    action_space=[function1, function2]
)
```

You can also access and obtain an individual worker from the agent:

```python
worker = agent.get_worker("worker_id")

worker.run("Bring me some fruits")
```
