# Kinematics

[← All topics](../README.md)

## Learning route

**Main reading:** The unofficial *MVA Robotics 2026 Notes*, PDF viewer pp. **6–12**. Read frames and placements, FK, the Jacobian, IK and the hierarchy example; skim the exponential-map and Gauss–Newton derivations. See [references](../references/README.md) for access.

**Practice:** The companion *MVA Robotics 2026 Practice Quiz*, questions **1–30**, has one answer slide after each question. Select 10–15 conceptual questions after reading; review mistakes before doing more. It is unofficial and omits lecture 1.

**Correction:** For Q18, the expression `(JᵀJ)⁻¹Jᵀ` requires full column rank; `J⁺` remains defined for singular or redundant cases.

Use the resources below when you need a clearer explanation; they are targeted supplements. Suggested times apply to the supplemental route alone; add time for the main reading and quiz. Adapt the 30-minute fallback to your weakest points.

For a visual explanation, use these in order; reading the slides first is unnecessary. These optional videos are by **Kevin Lynch / Northwestern**.

1. **Frames and transforms — 6:22:** [Homogeneous Transformation Matrices](https://modernrobotics.northwestern.edu/nu-gm-book-resource/3-3-1-homogeneous-transformation-matrices/). Follow how one point gets different coordinates in different frames and how transforms compose. Keep `p_A = R_AB p_B + t_AB`; skip memorizing matrix identities.
2. **Joint motion to hand motion — 8:28:** [Velocity Kinematics and Statics](https://modernrobotics.northwestern.edu/nu-gm-book-resource/velocity-kinematics-and-statics/). Focus on the arm example, Jacobian columns and loss of motion directions at a singularity. The force/manipulability discussion is secondary.
3. **Solving IK locally — 5:04:** [Numerical Inverse Kinematics, Part 1](https://modernrobotics.northwestern.edu/nu-gm-book-resource/6-2-numerical-inverse-kinematics-part-1-of-2/). You already know local optimization: connect its root-finding picture to correcting a robot's pose error with a Jacobian. Understand initial-guess dependence and least squares; skip the derivation.
4. **Consolidate, 15–25 min:** read the key ideas, explain FK versus IK aloud, then attempt the practice questions. Spend extra time on redundancy and task hierarchy: the videos do not directly teach the course's hierarchy formulation. Use the arm example below.

**Optional only if FK is still abstract:** [Forward Kinematics Example](https://modernrobotics.northwestern.edu/nu-gm-book-resource/forward-kinematics-example/) (**3:28**). Watch how fixed joint values determine the end-effector pose; the product-of-exponentials calculation is unnecessary here. Only its description was checked, not a transcript.

**30-minute version:** Read the main reading’s intuition and examples, review the key ideas below, and try 4–6 relevant practice questions. Use one targeted video only if a concept remains unclear.

## Key ideas

- **Frame / pose / configuration:** a frame has an origin and axes; a body's pose is position plus orientation; configuration `q` describes the robot's joints and, when applicable, its floating base.
- **Transform:** `p_A = R_AB p_B + t_AB` expresses one point in another frame. Composition follows the frame chain: `T_AC = T_AB T_BC`. Order matters.
- **FK:** robot model + `q` -> body poses. **IK:** desired task pose -> joint configuration(s); targets can be unreachable and solutions nonunique.
- **Jacobian:** at a fixed `q`, `V = J(q) q_dot` maps joint velocity to task twist/velocity in a stated frame. Each column describes one joint's instantaneous contribution. `J` changes with configuration.
- **Singularity:** loss of task-Jacobian rank can remove motion directions. A pseudoinverse solves least squares; it cannot make an impossible direction feasible.
- **Redundancy and hierarchy:** hold the hand fixed while moving the elbow. Lower-priority motion uses the primary task's null space, preserving that task locally.
- **Twist / exp / log:** velocity combines linear and angular motion; exp/log connect geometric configurations with local increments/errors. Know their purpose, not closed-form formulas.

**Hierarchy example:** keep your hand fixed while moving your elbow. A joint-velocity change in the hand Jacobian's null space satisfies `J q_dot = 0`, so it preserves the hand task to first order. As posture changes, recompute the Jacobian; redundancy is usable freedom, whereas singularity is loss of task directions.

**Connection:** planning supplies a desired motion; IK converts task-space requirements into joint commands; dynamics/control determines how forces execute them. IK alone does not guarantee collision avoidance or torque feasibility unless those constraints are included.

**Do not confuse:** a point with its coordinates; pose with velocity; redundancy with singularity; local differential tracking with a global IK solution. Skip long Lie-group and pseudoinverse derivations.

## Course check

Slides are a coverage and terminology reference; they are not required first reading. Consult a selected page if an answer remains unclear.

- [Official lecture slides](https://jcarpent.github.io/teaching/) — Kinematics, PDF viewer pages 13–16, 30, 42, 47, 50–51, 63. Page numbers refer to the local 2026 slide versions and may differ if the course files change.
- **Exercise:** [Official notebook](https://github.com/TheotimeLH/2026_MVA_Robotics_Exercises/blob/main/1_differential_IK.ipynb). Associated exercise concepts are in scope: read the problem statement and explanatory text after learning the topic. Running code is unnecessary for this route.

## Practice

Explain each in one or two sentences, then check the answers. These practice prompts are not official quiz questions.

1. **K01.** What is a reference frame, and why must coordinates be tied to one?
2. **K02.** What is the difference between position, orientation and pose?
3. **K03.** Why do rotations or rigid transformations generally depend on composition order?
4. **K04.** What are the inputs and outputs of forward kinematics?
5. **K05.** Why can inverse kinematics have no solution, one solution or multiple solutions?
6. **K06.** What does the Jacobian relate in robot kinematics?
7. **K07.** Does the same Jacobian generally apply at every robot configuration?
8. **K08.** What is a kinematic singularity, and why can it matter for tracking?
9. **K09.** Does using a pseudoinverse guarantee an exact solution to any requested task velocity?
10. **K10.** What does redundancy mean, and how can it help a robot?
11. **K11.** How can a lower-priority task avoid disturbing a higher-priority task?
12. **K12.** How does differential IK differ from solving for a complete target configuration?
13. **K13.** What does a rigid-body twist describe?
14. **K14.** Why are exponential/logarithm maps useful for rotations and poses?
15. **K15.** What distinguishes kinematics from dynamics?

## Answers

<details>
<summary>Open after attempting the questions</summary>

- **K01:** A frame supplies an origin and axes. Coordinates describe a geometric point relative to that choice; the point itself does not change when the frame changes.
- **K02:** Position locates a point; orientation describes the body's axes; pose combines position and orientation.
- **K03:** Each operation changes the frame or geometry in which the next acts. Matrix multiplication is generally noncommutative.
- **K04:** A robot model and joint configuration determine body/end-effector poses relative to a stated frame. It does not infer required torques.
- **K05:** Reachability, joint limits and geometry determine existence; different joint configurations may realize the same task pose.
- **K06:** At a given configuration it maps joint velocities to task velocity or a frame twist, expressed consistently. It is a local differential map.
- **K07:** No. Changing joint positions changes how each joint's motion affects the task, so J generally depends on configuration.
- **K08:** The task Jacobian loses rank relative to its maximal rank; some instantaneous task directions become unavailable and near-singular tracking can require large joint velocities.
- **K09:** No. It gives a least-squares solution, conventionally the minimum-norm one; an unattainable component leaves residual error.
- **K10:** The task leaves joint freedom. The robot may use that freedom for posture or another task while maintaining the primary task locally.
- **K11:** In the linearized hierarchy, use directions in the higher-priority task's null space. Constraints and changing configuration still need attention.
- **K12:** It computes joint velocity corrections for local tracking, rather than directly solving the full nonlinear pose problem from scratch.
- **K13:** Instantaneous angular and linear motion in a specified convention/frame. A twist is a velocity representation, not a pose.
- **K14:** They connect geometric configurations with local tangent increments/errors, allowing meaningful updates and comparisons while respecting the geometry.
- **K15:** Kinematics describes geometric motion relations; dynamics relates motion and acceleration to forces, torques and inertia.

</details>
