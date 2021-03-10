---
layout: post
title:  "More Models"
categories: update
---
# Extended C Interface
This week the first thing I did was to support models with 1, 2 and 4 end effectors.
To make it possible, I extended the interface a bit.

```c++
// Choose the model type when creating the session.
int create_session(int model);

// Information needed to initialize the optimization.
struct Bound {
    double initial_base_linear_position[3];
    double initial_base_linear_velocity[3];
    double initial_base_angular_position[3];
    double initial_base_angular_velocity[3];

    double final_base_linear_position[3];
    double final_base_linear_velocity[3];
    double final_base_angular_position[3];
    double final_base_angular_velocity[3];

    double initial_ee_positions[4][3];

    double duration;

    double max_cpu_time;
    int max_iter;
    bool optimize_phase_durations;
};

// The solution at a given time point.
struct State {
    double base_linear_position[3];
    double base_linear_velocity[3];
    double base_angular_position[3];
    double base_angular_velocity[3];

    // For at most 4 end effectors.
    double ee_motions[4][3];
    double ee_forces[4][3];
    bool contacts[4];
};
```

Note that I decide to use fixed-size arrays which can store at most 4 3D-vectors no matter what the actual number of end effector is,
because it is much easier to manage the memory than using size-variable arrays.

One limit of the current interface is that user cannot define custom models from C#.
To make it easier to set up models with multiple end effectors, another two functions are added to the interface to retrieve the kinematic information about the created model.

```c++
struct ModelInfo {
    // The displacement of each end effector from the base.
    double nominal_stance[4][3];
    // The range each end effector can move.
    double max_deviation[3];
};

void get_model_info(int session, ModelInfo* output);
int get_ee_count(int session);
```

This information is useful as the user doesn't have to adjust the positions of the base and the end effectors manually in the editor.

# Extended Unity Interface
Now that the library supports multiple models, the unity interface should be able to switch from them freely.
So I make it into a single component.
By changing only the `model` from the inspector, the user can change from models with 1, 2 or 4 end effectors.

Also, the range of motion of end effectors are visible in the editor.

<div style="text-align: center;"><iframe width="560" height="315" src="https://www.youtube.com/embed/F-rzrh0QiXQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

# Custom Trajectory
In order to let users create their own trajectories using path points, I can think of two approaches:
1. Optimize multiple short trajectories separated by path points.
2. Optimize one long trajectory.

The first one is easy to be thought of: having path points decide the start and the end pose and connecting them with short trajectories.
However, the situations (in terms of discrete variables like gait and contact) on those connection points can be complex and need to be fixed ahead.
This may cause artifacts on the result and may also lead to loss of freedom on choosing the solutions.

The second one exploit one feature of this trajectory optimization algorithm: there is no cost functions, so that the algorithm will generate trajectories that most similar to the initialization.
This gives the optimizer the freedom to change the gait continuously along the path.
Note that there are still drawbacks.
For example, the trajectory has one non-separable property: whether the phase durations should be optimized.
So both values shall be used, separated trajectories must be used.

Currently the trajectories are initialized by linear interpolation.
I have been looking for ways to hack into this initialization process but failed.
Then I found that there is only one way to do that: directly set the values of the node variables after initialization but before optimization.
Since node variables used in optimization don't contain time information, I have to re-implement the spline in Unity and then convert user-defined splines to node variable values.
This is the work I am still working on and I will finish it in the next week.

# Builds
- [Linux Build](https://drive.google.com/file/d/1dWk0FtpIyt72BIKF64Lz5CmeSVrc1_EC/view?usp=sharing)