# Simulation and dynamics

[← All topics](../README.md)

## Learning route

**Main reading:** The unofficial *MVA Robotics 2026 Notes*, PDF viewer pp. **13–18**. Focus on the simulation pipeline, forward/inverse dynamics, the friction cone and the box-on-a-slope example; skim solver internals. See [references](../references/README.md) for access.

**Practice:** The companion *MVA Robotics 2026 Practice Quiz*, questions **31–60**, has one answer slide after each question. Select 10–15 conceptual questions after reading; review mistakes before doing more. It is unofficial and omits lecture 1.

**Correction:** For Q50, sticking can occur at the friction-cone boundary as well as inside it; sliding also requires relative tangential motion.

Use the resources below when you need a clearer explanation; they are targeted supplements. Suggested times apply to the supplemental route alone; add time for the main reading and quiz. Adapt the 30-minute fallback to your weakest points.

These optional clips are by **Kevin Lynch / Northwestern**. They address different gaps: motion under force, contact motion, and friction forces.

1. **Dynamics and simulation — 3:43:** [Forward Dynamics of Open Chains](https://modernrobotics.northwestern.edu/nu-gm-book-resource/8-5-forward-dynamics-of-open-chains/). Identify the inputs and output, then distinguish acceleration computation from integration. The recursive algorithm and mass-matrix construction are not study targets.
2. **Contact modes — 5:41:** [Contact Types: Rolling, Sliding, and Breaking](https://modernrobotics.northwestern.edu/nu-gm-book-resource/12-1-2-contact-types-rolling-sliding-and-breaking/). Track relative normal/tangential motion. Its “rolling” label means no slip at the instantaneous contact; this connects to the course's sticking condition. Skip wrench-coordinate algebra.
3. **Contact forces — 5:38:** [Friction](https://modernrobotics.northwestern.edu/nu-gm-book-resource/12-2-1-friction/). Focus on the block example and friction cone: static friction adjusts up to a bound; sliding friction opposes slip. Composite wrench cones are secondary.
4. **Consolidate, 15–25 min:** read the key ideas and contrast a planted foot, a slipping foot and a lifted foot. Then attempt the questions. Collision detection, GJK and numerical tradeoffs are covered briefly below; these videos are not a complete simulator tutorial.

**30-minute version:** Read the main reading’s intuition and examples, review the key ideas below, and try 4–6 relevant practice questions. Use one targeted video only if a concept remains unclear.

## Key ideas

- **Simulation step:** detect contacts -> resolve contact forces -> integrate dynamics to update the state. Detection finds where interaction occurs; resolution determines its physical effect.
- **Forward dynamics:** configuration, velocity and forces/torques -> acceleration. **Inverse dynamics:** configuration, velocity and desired acceleration, with specified external/contact forces -> required torques. Floating-base robots also need feasible contacts/actuation.
- Read the equation `M(q) q_ddot + C(q,q_dot) + G(q) = tau + J_c(q)^T lambda_c`: inertia, velocity effects and gravity balance motor and contact forces. For floating-base coordinates, motor actuation has unactuated components; do not assume every coordinate has a motor.
- **Broad phase** rejects implausible collision pairs cheaply; **narrow phase** checks the remaining geometry. GJK exploits convex geometry/support queries to solve distance/intersection efficiently; no iteration rules required.
- **Contact modes:** breaking = separating, no contact force; sticking = zero relative contact velocity, possibly nonzero force; sliding = tangential motion with opposing friction at its ideal Coulomb bound.
- **Unilateral contact and friction:** the normal force between nonadhesive surfaces is compressive, not attractive. At an established non-impact contact, normal force and separation velocity are complementary. Tangential force is bounded by normal force times friction coefficient.
- **Model/compute choices:** compliance allows deformation; ideal rigid contact does not. A patch captures distributed support better than one point. Smaller integration steps cost more; GPU batch throughput is different from low latency for one step.

**Example:** lifting a foot, standing on it, and slipping on ice are three different contact states even when collision geometry looks similar.

**Connection:** simulation predicts control consequences and generates RL experience. Correct kinematics or fast computation cannot compensate for a poor contact/actuator model.

**Do not confuse:** breaking with braking; zero velocity with zero force; detecting contact with solving its force; a smooth numerical approximation with exact physical behavior. Skip ADMM/GJK derivations and benchmark numbers.

## Course check

Slides are a coverage and terminology reference; they are not required first reading. Consult a selected page if an answer remains unclear.

- [Official lecture slides](https://jcarpent.github.io/teaching/) — Simulation and dynamics, PDF viewer pages 42, 46–47, 87–88, 95, 100. Page numbers refer to the local 2026 slide versions and may differ if the course files change.
- **Exercise:** [Official notebook](https://github.com/TheotimeLH/2026_MVA_Robotics_Exercises/blob/main/2_simulation.ipynb). Associated exercise concepts are in scope: read the problem statement and explanatory text after learning the topic. Running code is unnecessary for this route.

## Practice

Explain each in one or two sentences, then check the answers. These practice prompts are not official quiz questions.

1. **S01.** What are the three main stages in the simulation pipeline presented in class?
2. **S02.** What is the difference between broad-phase and narrow-phase collision detection?
3. **S03.** What geometric problem does GJK address, and why is convexity useful?
4. **S04.** What are the inputs and outputs of forward dynamics?
5. **S05.** What are the inputs and outputs of inverse dynamics?
6. **S06.** What does the J-transpose contact-force term represent in the dynamics equation?
7. **S07.** What does breaking contact mean in the ideal unilateral contact model?
8. **S08.** Can a sticking contact exert a nonzero force even though its relative contact velocity is zero?
9. **S09.** What characterizes sliding contact with Coulomb friction?
10. **S10.** What does a friction cone constrain?
11. **S11.** Why are separation velocity and normal force complementary in the simplified non-impact contact formulation?
12. **S12.** How does a compliant contact model differ from an ideal rigid-contact model?
13. **S13.** Why can a contact patch represent interaction better than one contact point?
14. **S14.** Why does numerical time integration involve an accuracy/computation tradeoff?
15. **S15.** Why is 'GPU simulation is always faster' an unreliable statement?

## Answers

<details>
<summary>Open after attempting the questions</summary>

- **S01:** Detect geometric contacts, resolve interaction forces using a physical model, and integrate to update the state.
- **S02:** Broad phase cheaply rejects impossible collision pairs; narrow phase examines remaining geometry to determine contact/proximity information.
- **S03:** Distance/intersection for convex geometries can be treated as a minimum-norm problem on their Minkowski difference. Do not memorize its iteration rules.
- **S04:** Given configuration, velocity and applied/generalized forces with the relevant contact assumptions, compute accelerations. Integration then updates motion.
- **S05:** Given configuration, velocity and desired acceleration with specified external/contact forces, compute required joint torques in the modeled actuated system. Floating-base feasibility needs additional care.
- **S06:** It maps forces at contact points into generalized joint forces/torques; it connects a workspace interaction with joint dynamics.
- **S07:** The surfaces separate, with no contact force at that released interface. It means losing contact, not braking/slowing down.
- **S08:** Yes. A planted foot can transmit normal and tangential forces while the contacting surfaces remain stationary relative to each other.
- **S09:** Relative tangential motion is nonzero; friction opposes sliding and, in the ideal model, its magnitude reaches the friction bound.
- **S10:** The admissible tangential contact force relative to the compressive normal force and friction coefficient. It is a force constraint, not a set of body poses.
- **S11:** An active compressive contact does not separate, while a separating contact carries no normal contact force. Unilateral contact pushes rather than attracts.
- **S12:** Compliance permits deformation, often represented by a spring-damper. Ideal rigid contact imposes nonpenetration without that deformation; numerical relaxation is a modeling choice.
- **S13:** Distributed support can transmit different moments and improve stability/realism; a single point may miss the geometry of the supporting area.
- **S14:** Discretization introduces errors. Smaller steps or different integration schemes can improve behavior at additional computational cost; they do not repair a wrong physical model.
- **S15:** Batch throughput and latency of one simulation/control step are different measures. The useful hardware depends on workload and numerical requirements.

</details>
