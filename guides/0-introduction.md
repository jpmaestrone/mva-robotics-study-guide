# Introduction to robotics

[← All topics](../README.md)

## Learning route: 10 minutes

Read the key ideas below, then optionally watch Kevin Lynch / Northwestern's [Control System Overview](https://modernrobotics.northwestern.edu/nu-gm-book-resource/11-1-control-system-overview/) (**3:24**). Follow the path from sensors through a controller to actuators, and identify where feedback enters. The detailed controller families are not the goal here.

Close the resource and explain a simple example: a robot estimates where it is, chooses where to go, acts, then corrects its motion using new observations. Attempt the three questions below. This is an orientation to the other guides, not a separate long study block. The unofficial notes and practice quiz cover lectures 2–7, so they do not replace this introduction.

## Key ideas

- A robot interacts with the physical world through sensors and actuators. Perception extracts useful information from observations; estimation combines information to infer state.
- Planning chooses a motion or action strategy; control turns objectives and estimated state into commands. Feedback uses new observations to correct deviations.
- Gravity and contact forces make locomotion a physical interaction problem. A motion that is geometrically possible may still be dynamically infeasible.
- Models, optimization and learning provide different ways to construct parts of the perception–decision–action loop; their usefulness depends on the task and available information.

## Course check

Slides are a coverage and terminology reference; they are not required first reading. Consult a selected page if an answer remains unclear.

- [Official introductory lecture slides](https://jcarpent.github.io/teaching/) — PDF viewer pages 55–63 (control loop), 81–88 (gravity and contact), and 123–130 (perception, action and control). Page references correspond to the 2026 local slide version.

## Practice

1. Why does a robot need feedback when acting in a changing physical environment?
2. How do estimation, planning and control contribute different parts of an autonomous task?
3. Why are contact forces relevant to walking?

<details>
<summary>Answer checkpoints</summary>

1. Sensors provide new information about disturbances, uncertainty and deviations; feedback can use it to adjust actions.
2. Estimation infers state, planning selects a motion or strategy, and control produces actions to execute it.
3. Feet exchange forces with the ground; those forces and gravity determine how the body accelerates and balances.

</details>
