---
layout: post
title:  "Goal Oriented Action Planning Instructions"
categories: update
---
## Objective
The purpose of this instruction is to train Unity game developers to build extendable AI agents using GOAP (Goal Oriented Action Planning) systems. Proper usage of GOAP systems can vastly simplify the Finite State Machines used in AI agents. Readers are assumed to have basic knowledge of Unity Engine, C# programming and game AI. This instruction presents the steps of creating a general GOAP agent by using a GOAP implementation called `ReGoap`. These steps include environment setup, sensor implementation, action implementation, goal implementation and debugging.

## Background
### Concepts
One common practice to implement AI agents used in games is to use FSMs (Finite State Machines). However, as the behavior of AI agents becomes more complicated, the states and transitions needed increase significantly.

GOAP (Goal Oriented Action Planning) is a type of method called AI planning which can simplify the FSMs and make the behavior of agents more flexible and extendable. GOAP has four main components: the planner, the memory, actions and goals.
- The planner calculates the action sequences needed to achieve a certain goal
- The memory represents the current feeling and memory of the agent
- An Action defines the preconditions and the effects of a certain behavior of the agent
- A goal indicates the states the agent needs to be at after performing the calculated action sequences

The data structure used for representing world states, memory states, goal states, preconditions and effects is a key-value pair, in which the key is of string type and the value is of the most generic object type.

The following figure shows an example of a planning algorithm generating a patrolling-searching-chasing behavior.
All available actions are listed with their preconditions and effects, and two different plans are calculated based on the current memory state.

<img src="{{site.baseurl}}/assets/GOAP/planner.png">
Figure 1. An illustration showing an example of a planning system that generates a patrolling-searching-chasing behavior

### Tools
- Unity Engine (version 5 or higher)
- Text editor
- `ReGoap` package

### Warnings
- Think twice before using GOAP in project prototypes, as it takes lots of time to set up and its code can be intrusive
- Design all goals and actions on paper before implementing them, otherwise it may cause unwanted behaviors
- Disable impossible goals under any circumstances, since impossible goals, if not being disabled, will keep the system replanning for every frame, which may cause severe lagging

## Procedure
### Set up the Agent
This stage imports `ReGoap` into an existing Unity project and creates an agent to which GOAP is applied. 

`ReGoap` is an open-source implementation of GOAP which will be used in this instruction. Its algorithm is backward A* planning, i.e., starting from the goal and searching for precedent actions whose effects cover the goal state. Note that it may have difficulties when an action’s preconditions or effects depend on its previous one, because at a certain point in planning only actions behind it are known.
1. Clone or download the `ReGoap` repository from https://github.com/luxkun/ReGoap into an existing Unity project
2. Create a planner manager component inherited from `ReGoapPlannerManager`; attach the component to an object in the scene
3. Create an empty game object, which is the agent
4. Create a `ReGoap`'s agent component inherited from `ReGoapAgentAdvanced`; attach the component to the agent game object

### Implement the Memory
This stage implements the agent's memory and sensors.

Memory is a record of real-time state of the agent. It contains all the information an agent feels and remembers. Memory is used to determine whether an action is valid right now so that the plan (action sequence) starting from it can be chosen.

Sensors are memory helpers which sense the outside world and modify the memory constantly. `UpdateSensor` is called when it's a sensor's turn to update the memory.

1. Create a `ReGoap`'s memory component inherited from `ReGoapMemoryAdvanced`; attach the component to the agent game object
2. Create a `ReGoap`'s sensor component inherited from `ReGoapSensor` or implemented the `IReGoapSensor` interface
3. Modify the memory in `UpdateSensor` method by the `Set` method of the memory state
4. Repeat steps 2 and 3 until all sensors are implemented
5. Attach the sensors to the agent game object

#### Notes
Sensors are usually updated once a few milliseconds, which are much slower than the game's framerate. This is intended to save the computational resources. 
So in principle, sensors should only put memory modifying code in `UpdateSensor`.
However there is an exception: if the sensor is event-based, i.e., it reacts to specific events, it can be error-prone to synchronize the update with `UpdateSensor`. In that case, just update the memory as soon as the event is received.

### Implement Actions
This stage implements actions for the agent.

In `ReGoap` context, an action is a record of preconditions and effects. An action can also have dynamical preconditions and effects depending on the actions in the current plan the planner is evaluation.

The behavior of an action can also take usage of information got when the planner is chaining it to the plan. A typical usage is in the `Go To` action in figure 1. This action depends dynamically on a vector-typed variable called `Objective Position`. The value of this variable is retrieved from one of the preconditions (`At Position`) of its successor actions.
This information can be easily got since `ReGoap` uses backward planning, and all preconditions of the successor actions are stored in `stackData.goalState`.
One may store the values get from there to a directory called `settings`, and the behavior has the access to it when running.

`ReGoap` does not care about the actual behavior of the action. The behavior is usually implemented by FSMs. When executing a plan, `ReGoap` will call the `Run` method of the first action and it will continue to the next action only if the `done` callback is called from the behavior, or it will replan if `fail` callback is called.

#### Cautions
If a behavior never calls either `done` or `fail`, the system has no way to know when it ends.
This will cause the agent to be stuck.

1. Implement an FSM class, in whose constructor `done` and `fail` should be passed in as arguments
2. Implement the concrete logic of the FSM; call `done` when the behavior is completed successfully or call `fail` if not
3. Create a `ReGoap`'s action component inherited from `ReGoapAction` or implemented the `IReGoapAction` interface
4. Override the protected `Awake` method; Set static preconditions and effects in there by calling `preconditions`'s and `effects`'s `Set` method

Steps 5 - 7 set dynamic preconditions:

5. Override `GetPreconditions`
2. Find the goal state, which is the state required by the other actions behind this action in the plan, in `stackData.goalState`
3. Set dynamical preconditions based on the the goal state
4. Store useful values need when running the action later to `settings`

Steps 8 - 11 set dynamic effects:

8. Override `GetEffects`
9. Find the goal state as in step 6
10. Set dynamical effects based on the the goal state
4. Store useful values need when running the action later to `settings`

Steps 12 - 15 set the behaviors when an action starts to run and when it finishes running

12.  Override `Run`
2.  Start behaviors related to this action
3.  If dynamic values got from planning are needed, retrieve them from `settings`
4.  Override `Exit`; stop all coroutines in this method

#### Notes
An action can be also interrupted by the planner when a goal with higher priority is possible.
To disable this, override `IsInterruptable` and return false.

### Implement Goals
This stage implements goals for the agent.

A goal is the ultimate motivation of the agent. It is a record indicating the final states an agent wants to achieve. 
When planning, The planner will search for a chain of actions where the preconditions of the first one are confined with the agent's current memory state, and the effects of the last one contain all the goal states.

`ReGoap` will keep replanning if it cannot find a chain of actions as described above, and it wastes a lot of computational resources. So a possibility check must be provided so that the system can fall back to lower priority goals when the current one is impossible.

1. Create a goal class inherited from `ReGoapGoal`
2. Override its `Awake` method; set goal states by calling the `Set` method of `goal`
3. Override `IsGoalPossible`; return true if it is possible under current situation, or return false if not

After these stages the agent should start working. An overview of the resulting project structure is showing below.

<img src="{{site.baseurl}}/assets/GOAP/structure.png">
Figure 2: Overview of the resulting project structure

### Debugging
This stage describes how to use the debugging tool provided by `ReGoap` to make sense why an agent is choosing a certain sequence of actions.

Sometimes the agent can behave wield or be idle when it is supposed to do a job. This is usually caused by two main reasons:
- There are mistakes in preconditions and effects of some actions so that the planner cannot find a solution
- The behavior is running forever without calling `done` or `fail` so that the agent stucks

The debugger provided by `ReGoap` lists out all actions available, their preconditions and post effects, and which ones are satisfied in the context.

<img src="{{site.baseurl}}/assets/GOAP/debugger.png">
Figure 3. Overview of `ReGoap`'s debugger

1. Select an agent to investigate into
2. Open the debugger window
3. Enter play mode, then a vision similar to Figure 2 should appear
4. If the action chain is empty, then it's likely that there is no action available; check the memory state to see why no action is chosen
5. If there is an action chain but the agent is not moving, it's likely that the behavior of the current action is blocking