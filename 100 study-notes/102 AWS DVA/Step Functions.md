---
created: 2022-05-30 12:29
updated: 2023-02-16 08:32
---
---
**Links**: [[102 AWS DVA Index]]
**Keywords**: *state machines*, *multiple lambdas*, *inventory management*, order *tracking*, *decisions*, *steps*, *workflows*.

---
## Step Functions
- It allows us to **model our workflow as state machines**.
- Use cases:
	- Order fulfilment, Data processing
	- Web applications, Any workflow 
- Written in **JSON**
	- **Amazon states language**.
- Visualisation of the workflow and the execution of the workflow, as well as **history** (useful for debugging)
- Start workflow with *SDK call*, *API Gateway*, *Event Bridge* (CloudWatch Event)

### Task State
- **Do some work** in your state machine
- *Invoke one AWS service*
	- Can invoke a *Lambda function*
		- ![[attachments/Pasted image 20220530123640.png]]
	- Run an AWS Batch job
	- Run an *ECS task* and wait for it to complete
	- *Insert an item from DynamoDB*
	- Publish message to *SNS, SQS*
	- Launch another Step Function workflow
	- ![[attachments/Pasted image 20220530123451.png]]
- *Run an one Activity*
	- *EC2, Amazon ECS, on-premises*
	- Activities poll the Step functions for work
	- Activities send results back to Step Functions
	- ![[attachments/Pasted image 20220530123526.png]]

> [!note]- A Task state (`"Type": "Task"`) *represents a single unit of work performed by a state machine*.
> - **All work in your state machine is done by the type Task**.

#### Information Flow
- A *Step Functions execution receives a JSON text as input* and passes that input to the first state in the workflow. 
- **Individual states receive JSON as input and usually pass JSON as output to the next state**. 
	- ![[attachments/Pasted image 20220610152407.png]]
- In the Amazon States Language, these fields filter and control the flow of JSON from state to state:
	- InputPath
	- OutputPath
	- ResultPath
	- Parameters

- Both the `InputPath` and `Parameters` fields provide a way to manipulate JSON as it moves through your workflow. 
	- `InputPath` can limit the input that is passed by *filtering the JSON notation* by using a path. 
	- The `Parameters` field enables you to *pass a collection of key-value pairs*, where the values are either static values that you define in your state machine definition, or that are selected from the input using a path.
	- AWS Step Functions **applies the InputPath field first, and then the Parameters field**. You can first filter your raw input to a selection you want using `InputPath`, and then apply `Parameters` to manipulate that input further, or add new values.
-  The *output of a state* can be 
	- A copy of its input, 
	- The result it produces (for example, the output from a Task state’s Lambda function), 
	- Or a combination of its input and result. 
- Use `ResultPath` to *control which combination of these is passed to the state output*.
- `OutputPath` enables you to **select a portion of the state output to pass to the next state**. 
	- This enables you to filter out unwanted information, and pass only the portion of JSON that you care about.
- Out of these field filters, the **`ResultPath` field filter is the only one that can control input values and its previous results to be passed to the state output**. 

#### States
- *Choice State*: *Test for a condition* to send to a branch (or default branch)
- *Fail or Succeed State*: Stop execution with failure or success
- Pass State: Simply pass its input to its output or inject some fixed data, without performing work.
- Wait State: Provide a delay for a certain amount of time or until a specified time/date.
- Map State: Dynamically iterate steps
- **Parallel State**: Begin *parallel branches of execution*.

> [!question]- You want to orchestrate multiple Lambda functions and *wait for the result of all of them before making a final decision*. What do you recommend?
> Step Functions *Parallel States* and then one final Task State

### Error Handling
- Whole power of step functions
- **All the errors should be handled in the state machine outside of the application code**.
- Any state can encounter *runtime errors* for various reasons:
	- State *machine definition issues* (for example, no matching rule in a Choice state)
	- *Task failures* (for example, an exception in a Lambda function)
	- Transient issues (for example, network partition events)
- Use **Retry** (to retry failed state) and **Catch** (transition to failure path) in the State Machine to handle the errors instead of inside the Application Code.
- Predefined error codes:
	- `States.ALL` : matches any error name
	- `States.Timeout`: Task *ran longer* than `TimeoutSeconds` or no heartbeat received
	- `States.TaskFailed`: *execution failure*
	- `States.Permissions`: insufficient privileges to execute code
- The state may report its own errors

#### Retry 
- **Evaluated from top to bottom**
	- ![[attachments/Pasted image 20220530125551.png]]
- *ErrorEquals*: match a specific kind of error
- *IntervalSeconds*: initial delay before retrying
- *BackoffRate*: multiple the delay after each retry
- *MaxAttempts*: default to 3, set to 0 for never retried
- When **max attempts are reached, the Catch kicks in**
- In the above example we see that changing the *error handling logic is very easy*.
	- If all these steps would have been inside the lambda function it might have timed out.

#### Catch
- **Evaluated from top to bottom**
	- ![[attachments/Pasted image 20220530125903.png]]
- *ErrorEquals*: match a specific kind of error
- *Next*: State to send to
- **ResultPath**: A path that determines what input is sent to the state specified in the Next field.
	- ResultPath is how you pass errors from the input to the output 
	- ![[attachments/Pasted image 20220530130227.png]]

### Standard vs Express Workflow
- **Express workflows** are **cheaper**, **run for shorter duration**(**5 minutes**) and *don't have an execution history*.
	- **Standard workflows** can run upto **1 year**.
	- ![[attachments/Pasted image 20220530141934.png]]

> [!note] If you need **human approval** then go for **Standard workflows**.