# Nuvolaris Scheduler

Nuvolaris Operator offers the possibility to deploy a simple "scheduler" to invoke repetitive or one-shot Nuvolaris actions. For example, an action executing a SQL script to create a PostgreSQL Database or inserting reference data, or simply an action that sends notifications with an API call every day at the same time.

## How to Activate the Scheduler

Using the `nuv` CLI, you can enable the scheduler with the following command:

```bash
nuv config enable --cron

# if Nuvolaris is not yet deployed
nuv setup devcluster

# alternatively if Nuvolaris is already deployed
nuv update apply

```

By default, the internal scheduler executes a job every minute that starts searching for Nuvolaris actions with special annotations.

## How to Deploy a Repetitive Action

Let's assume we want to deploy a Nuvolaris action to be executed every 30 minutes. The action source code is available in the file `scheduled-action.py`.

Suppose it's an action that simply prints something, like this:

```python
def main(args): 
    print('Hello from a repeated action')
    return {
        'body': 'action invoked'
    }
```

To deploy the action and instruct Nuvolaris to execute it every 30 minutes, issue the following command:

`nuv action create scheduled-action scheduled-action.py -a cron "*/30 * * * *"`

So you can create the action in the classic way and at the end add -a cron yourCronExpression.

## How to Deploy a One-Shot Execution Action

Now suppose we want to execute the same action `scheduled-action.py` only once.

To deploy an action and request a single execution automatically via the Nuvolaris Scheduler, issue the following command:

 `nuv action create scheduled-action scheduled-action.py -a autoexec true`

If we now print activation logs with nuv activation poll, we will see our action execution log:

```bash
Activation: 'scheduled' (ebd532139a464e9d9532139a46ae9d8a)
[
    "2024-03-08T07:28:02.050739962Z stdout: Hello from a scheduled action"
]
```

## Remarks

The scheduler executes the action according to the following rules:

Actions are called in a non-blocking fashion. To verify execution and logs, use the command nuv activation list.
Actions are invoked without any parameters. It is advised to deploy actions with self-contained parameters.
