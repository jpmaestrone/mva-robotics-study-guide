# Reinforcement learning

[← All topics](../README.md)

## Learning route

**Main reading:** The unofficial *MVA Robotics 2026 Notes*, PDF viewer pp. **39–43**. Focus on states and observations, rewards and values, actor–critic, privileged information and sim-to-real; skim policy-gradient derivations. See [references](../references/README.md) for access.

**Practice:** The companion *MVA Robotics 2026 Practice Quiz*, questions **151–180**, has one answer slide after each question. Select 10–15 conceptual questions after reading; review mistakes before doing more. It is unofficial and omits lecture 1.

**Correction:** For Q166, PPO clipping discourages large policy changes but does not impose a hard bound on them.

Use the resources below when you need a clearer explanation; they are targeted supplements. Suggested times apply to the supplemental route alone; add time for the main reading and quiz. Adapt the 30-minute fallback to your weakest points.

Prefer selected explanatory reading to a long algorithm survey. You do not need to derive losses or implement training.

1. **Vocabulary and objective, about 10–15 min:** OpenAI's [Spinning Up — Part 1: Key Concepts in RL](https://spinningup.openai.com/en/latest/spinningup/rl_intro.html). In **Key Concepts and Terminology**, read **States and Observations**, **Action Spaces**, **Policies**, **Reward and Return**, **The RL Problem** and **Value Functions**. Identify policy inputs/outputs and distinguish reward, return, `V` and `Q`. Skip the full formalism and Bellman derivations.
2. **Actor–critic and experience reuse, about 5–8 min:** [Spinning Up — Part 2: Kinds of RL Algorithms](https://spinningup.openai.com/en/latest/spinningup/rl_intro2.html), **Model-Free vs Model-Based RL** and the **Policy Optimization**, **Q-Learning**, **Trade-offs Between Policy Optimization and Q-Learning**, and **Interpolating Between Policy Optimization and Q-Learning** subsections. Connect an actor's action choice with a critic's evaluation. Treat this as a foundational taxonomy, not a current algorithm ranking; use the key ideas below for PPO/SAC and online/offline distinctions.
3. **Why robotics changes the problem, about 5–8 min:** Miki et al. / ETH and collaborators, [Learning robust perceptive locomotion for quadrupedal robots in the wild](https://leggedrobotics.github.io/rl-perceptiveloco/). Read **Abstract**, **Robustness against unreliable perception** and **Training overview**. Trace privileged simulated information → teacher → student using deployable observations. This is an illustrative research example, not a claim that the course used this exact paper. Skip benchmark results and embedded videos.
4. **Consolidate, 15–20 min:** read the key ideas and answer the questions. Explain what is available during training versus deployment, how actions reach motors, and why a high reward can still produce unwanted behavior. The external readings do not replace those robotics-specific checks.

**Optional video alternative for basic vocabulary — 14:05:** Brian Douglas / MathWorks, [What Is Reinforcement Learning?](https://www.mathworks.com/videos/reinforcement-learning-part-1-what-is-reinforcement-learning-1551974943006.html). Use it instead of the first reading if you prefer a narrated introduction; it does not replace the actor–critic or robotics material.

**30-minute version:** Read the main reading’s intuition and examples, review the key ideas below, and try 4–6 relevant practice questions. Use one targeted video only if a concept remains unclear.

## Key ideas

- **Objective:** learn a policy mapping observations/state information to actions, maximizing expected cumulative discounted reward. Immediate reward, long-term return and value are different.
- **Value / actor / critic:** `V(s)` evaluates a state under a policy; `Q(s,a)` evaluates a chosen first action and subsequent policy. Actor chooses actions; critic estimates value. Advantage compares an action with a state baseline.
- **Two data distinctions:** online versus offline = new interactions versus fixed data; on-policy versus off-policy = relation between the learning policy and the data-generating policy. They are not synonyms.
- **Algorithm purpose:** Q-learning estimates action values; continuous-action maximization motivates actor methods. PPO discourages excessively large changes using a surrogate objective on recent on-policy data. SAC reuses experience off-policy and optimizes reward plus entropy. Know the tradeoffs, not losses or update rules.
- **Deployment information:** the actor must use available sensor observations. A critic can use privileged simulator information during training; a privileged teacher policy can separately train a restricted-observation student.
- **Actions and models:** a policy may output torque, joint targets tracked by PD, or higher-level commands. A learned actuator model can reduce mismatch between requested targets and real applied torques.
- **Initial conditions:** the initial-state distribution determines where experience begins; choosing informative starting states can support learning.
- **Robustness:** randomize plausible physics and apply perturbations to train variation/recovery. Curriculum gradually increases difficulty. Neither removes the sim-to-real gap or guarantees safety/generalization.
- **Reward and imitation:** optimizing forward speed alone need not teach safe stopping. Tracking RL uses a reference-motion reward; behavior cloning directly fits demonstrated state/observation-action pairs. IK/retargeting adapts human motions to a different robot body.

**Connection:** RL still depends on sensors/estimation, physical simulation, actuator behavior and fast control. Its output is one component in that loop.

**Do not confuse:** off-policy with offline; privilege during training with usable deployment inputs; reward maximization with the designer's unstated intent. PPO clipping is not a hard bound on every probability or a monotonic-improvement guarantee. Skip code, paper dates, training-time claims and benchmark rankings.

## Course check

Slides are a coverage and terminology reference; they are not required first reading. Consult a selected page if an answer remains unclear.

- [Official lecture slides](https://jcarpent.github.io/teaching/) — Reinforcement learning, PDF viewer pages 18, 21, 30, 47, 57, 62, 66, 70–71. Page numbers refer to the local 2026 slide versions and may differ if the course files change.

## Practice

Explain each in one or two sentences, then check the answers. These practice prompts are not official quiz questions.

1. **R01.** What elements define the RL control problem, and what is the policy trying to maximize?
2. **R02.** How do policy, state value V and action value Q differ?
3. **R03.** How are online/offline RL and on/off-policy learning different distinctions?
4. **R04.** What is the basic Q-learning idea, and why can direct action maximization be awkward for continuous robot controls?
5. **R05.** What are the roles of the actor, critic and advantage in actor-critic learning?
6. **R06.** What problems are PPO and SAC intended to address at a conceptual level?
7. **R07.** Why can a simulator's full state differ from the observations available to a deployed robot?
8. **R08.** What is the difference between a privileged critic and a privileged teacher policy?
9. **R09.** Must an RL robot policy output motor torques directly?
10. **R10.** Why might a learned actuator model improve sim-to-real transfer?
11. **R11.** How do domain randomization and random perturbations help, and what do they fail to guarantee?
12. **R12.** Why can a poorly designed reward produce behavior that looks wrong despite a high return?
13. **R13.** What is curriculum learning, and how can it affect robot training?
14. **R14.** How does RL with a reference-motion tracking reward differ from behavior cloning?
15. **R15.** How can retargeting and kinematics remain useful inside a learning-based robotics pipeline?

## Answers

<details>
<summary>Open after attempting the questions</summary>

- **R01:** States/observations, actions, transitions, rewards, initial/terminal conditions and discounting define the task. A policy seeks high expected cumulative return, not necessarily the largest immediate reward.
- **R02:** A policy chooses an action/distribution; V evaluates future return from a state under a policy; Q evaluates taking a particular first action and then following a policy.
- **R03:** Online/offline concerns new environment interaction versus a fixed dataset; on/off-policy concerns which policy generated the data used for learning. Off-policy does not mean offline.
- **R04:** Learn action values from reward plus future value and favor high-value actions. Continuous action spaces do not permit a simple finite enumeration of all choices.
- **R05:** The actor chooses actions; the critic estimates value; advantage compares an action's value with a state baseline to guide relative policy improvement.
- **R06:** PPO uses recent on-policy data and a conservative surrogate update to discourage excessive policy changes. SAC is an off-policy actor-critic method combining reward and entropy, enabling experience reuse. Neither is universally best.
- **R07:** Simulation exposes hidden quantities such as exact contact/environment parameters; physical sensors provide only partial, noisy information. A deployable policy must use available inputs.
- **R08:** A privileged critic helps evaluate actions during actor training; a teacher is a policy trained with extra information whose behavior can then be distilled to a restricted-observation student.
- **R09:** No. Actions can be torques, joint position targets or commands to another controller/gait generator; the choice affects learning and stability.
- **R10:** Real actuators may not implement a requested target as the simulator assumes. Modeling target/history-to-torque behavior can reduce that mismatch while retaining rigid-body/contact simulation.
- **R11:** Varying plausible physics and applying disturbances exposes the policy to variation/recovery during training. Robustness outside that training coverage is not guaranteed.
- **R12:** The agent optimizes the specified objective, not the designer's unstated intent. For example, forward speed alone need not reward stopping safely or using an acceptable gait.
- **R13:** Increase difficulty or change task/reward conditions progressively as ability improves. Starting too hard or penalizing all early attempts can prevent useful exploration.
- **R14:** Tracking RL learns actions through interaction to follow a reference while respecting other objectives; behavior cloning fits demonstrated observation/state-action pairs directly.
- **R15:** Human and robot bodies differ; IK/retargeting can produce robot-compatible motion references, which a learned controller then tries to execute robustly.

</details>
