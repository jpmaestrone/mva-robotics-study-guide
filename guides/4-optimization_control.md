# Optimization and control

[← All topics](../README.md)

## Learning route

**Main reading:** The unofficial *MVA Robotics 2026 Notes*, PDF viewer pp. **24–33**. Prioritize differential IK as a QP, LQR/PD, trajectory optimization and MPC; skim familiar foundations, derivations and solver names. See [references](../references/README.md) for access.

**Practice:** The companion *MVA Robotics 2026 Practice Quiz*, questions **91–120**, has one answer slide after each question. Select 10–15 conceptual questions after reading; review mistakes before doing more. It is unofficial and omits lecture 1.

**Class emphasis:** The class transcript suggests MPPI was only briefly introduced because time ran short. Know its basic purpose before its detailed weights.

Use the resources below when you need a clearer explanation; they are targeted supplements. Suggested times apply to the supplemental route alone; add time for the main reading and quiz. Adapt the 30-minute fallback to your weakest points.

Given an optimization background, focus on what makes a controller useful on a physical robot.

1. **Read, about 5 min:** Russ Tedrake / MIT, [Trajectory Optimization](https://underactuated.mit.edu/trajopt.html), only the opening and **Problem Formulation**, stopping before **Convex Formulations for Linear Systems**. Identify the state/control trajectory, dynamics, initial condition, cost and physical constraints. Note why one optimized trajectory differs from a feedback policy over possible states.
2. **Watch — 6:06:** Melda Ulusoy / MathWorks, [What Is MPC?](https://www.mathworks.com/videos/understanding-model-predictive-control-part-2-what-is-mpc--1528106359076.html). Follow its lane-keeping example: predict, optimize, apply the first action, update the state and repeat. Explain what changes after a disturbance.
3. **Consolidate, 15–25 min:** use the method comparison below, then attempt the questions. Be able to choose between PD, LQR, an offline optimized trajectory and MPC for a simple situation. Real-time computation and constraints matter as much as the objective.

**Targeted supplement if LQR is unclear, about 5–8 min:** Tedrake's [Linear Quadratic Regulators](https://underactuated.mit.edu/lqr.html), opening and the setup at the start of **Basic Derivation**. Recognize linear dynamics, quadratic state/control costs and feedback `u = -Kx`; stop before the HJB/Riccati derivation. No need to read the chapter.

**30-minute version:** Read the main reading’s intuition and examples, review the key ideas below, and try 4–6 relevant practice questions. Use one targeted video only if a concept remains unclear.

## Key ideas

| Method | Core idea | Main assumption/limitation |
|---|---|---|
| PD | React to position and velocity error; the derivative term provides damping in the usual setting | Basic PD does not explicitly optimize a future trajectory or enforce hard constraints |
| LQR | Choose feedback to trade state error against effort | Linear dynamics, quadratic objective; standard form has no hard inequality constraints |
| Trajectory optimization | Optimize states/controls over time subject to dynamics and constraints | Robot problems are often nonconvex; a local optimum is not a global guarantee |
| MPC | Estimate -> optimize a finite horizon -> apply first action -> repeat | Needs a useful model, state estimate and timely feasible solution |

- **State/control:** often `x = (q, v)` and `u = tau`. Dynamics, torque/joint limits, collision avoidance and friction constraints distinguish an executable motion from a cheap objective value.
- **Feedback:** an offline sequence alone cannot react to an unexpected push. MPC replanning from the updated state makes its action depend on what actually happened.
- **Nested loops:** MPC or RL can output joint targets while faster PD/torque loops track them. Learning does not eliminate conventional control.
- **Computation:** a lower-cost solution arriving after the deadline can be worse than a timely usable one. Warm starts and temporal sparsity help; no need to reproduce Riccati recursion.
- **Method families:** DP reasons through value functions but suffers from state dimension; indirect methods optimize then discretize; direct methods discretize then optimize. Single shooting rolls out controls; multiple shooting also optimizes intermediate states with continuity constraints.
- **Physical optimization:** contact-constraint multipliers can represent forces. MPPI samples and scores candidate control sequences without requiring derivatives.

**Connection:** the estimator supplies the current state, planning supplies goals/routes, and control turns them into motor commands under physical limits.

**Do not confuse:** predicted motion with measured motion; a soft penalty with a guaranteed hard constraint; PD's LQR interpretation for a suitable model with a universal equivalence. Skip optimization basics you already know and long proofs.

## Course check

Slides are a coverage and terminology reference; they are not required first reading. Consult a selected page if an answer remains unclear.

- [Official lecture slides](https://jcarpent.github.io/teaching/) — Optimization and control, PDF viewer pages 21–24, 66, 78, 96, 131, 135, 154. Page numbers refer to the local 2026 slide versions and may differ if the course files change.
- **Exercise:** [Official notebook](https://github.com/TheotimeLH/2026_MVA_Robotics_Exercises/blob/main/4_optim_and_ocp.ipynb). Associated exercise concepts are in scope: read the problem statement and explanatory text after learning the topic. Running code is unnecessary for this route.

## Practice

Explain each in one or two sentences, then check the answers. These practice prompts are not official quiz questions.

1. **O01.** What is optimized in trajectory optimization, and which constraints matter?
2. **O02.** What roles do the proportional and derivative terms play in joint PD control?
3. **O03.** Why might an integral term be added to a controller?
4. **O04.** What are the defining assumptions behind the standard LQR problem?
5. **O05.** What tradeoff do the state and control weights encode in LQR?
6. **O06.** Why does generic dynamic programming suffer from the curse of dimensionality?
7. **O07.** What is the conceptual difference between direct and indirect optimal-control methods?
8. **O08.** What is a key difference between single and multiple shooting?
9. **O09.** What does MPC do at each control cycle?
10. **O10.** Why is MPC a feedback controller rather than simply an offline plan?
11. **O11.** Why can MPC or RL coexist with a faster low-level PD controller?
12. **O12.** Why can a more accurate optimization result still be unusable for a robot?
13. **O13.** How are contact forces related to constraints and Lagrange multipliers?
14. **O14.** Does formulating robot motion as a nonlinear optimization problem guarantee a global optimum?
15. **O15.** What is the central idea of sampling-based control optimization such as MPPI?

## Answers

<details>
<summary>Open after attempting the questions</summary>

- **O01:** A sequence or function of states/controls minimizing task/effort cost, subject to dynamics, initial/terminal conditions and relevant physical/geometric limits.
- **O02:** The proportional term responds to position error; the derivative term responds to velocity error and provides damping in the usual setting.
- **O03:** It accumulates error and can reduce steady-state offsets in suitable closed-loop conditions. It is not a universal stability or constraint guarantee.
- **O04:** Linear dynamics and a quadratic objective, with suitable definiteness/solvability assumptions; the basic formulation does not impose hard inequality constraints.
- **O05:** Tracking/regulation quality versus control effort. The resulting feedback gain depends on the model and chosen weights.
- **O06:** Representing a value/policy over a high-dimensional state space can require prohibitively many states, even though the Bellman recursion is conceptually simple.
- **O07:** Direct methods discretize then optimize; indirect methods first derive continuous optimality conditions and then solve/discretize the resulting equations.
- **O08:** Single shooting obtains states by rolling out controls; multiple shooting also optimizes intermediate states and enforces continuity. More variables can improve numerical handling of unstable dynamics.
- **O09:** Estimate the current state, optimize over a finite future horizon, apply the first action, shift the horizon and repeat.
- **O10:** It replans using the updated measured/estimated state, so its chosen action changes in response to disturbances and deviations.
- **O11:** A higher-level method provides targets or feedforward actions; the fast local loop tracks them and corrects disturbances between higher-level updates.
- **O12:** It may arrive after the control deadline or violate required feasibility/reliability. Physical systems need timely usable control, not merely a low objective value.
- **O13:** For the contact-dynamics formulation shown in class, multipliers enforcing contact constraints have the physical interpretation of contact forces.
- **O14:** No. Many robot problems are nonconvex; local solvers depend on initialization and may converge locally or fail.
- **O15:** Sample candidate control sequences, simulate/evaluate their cost and favor low-cost candidates; derivatives are not required. Details were only briefly introduced in class.

</details>
