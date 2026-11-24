# Perception and estimation

[← All topics](../README.md)

## Learning route

**Main reading:** The unofficial *MVA Robotics 2026 Notes*, PDF viewer pp. **34–38**. Read the sensor, Kalman, EKF and SLAM explanations; work through the position/velocity example for covariance intuition. See [references](../references/README.md) for access.

**Practice:** The companion *MVA Robotics 2026 Practice Quiz*, questions **121–150**, has one answer slide after each question. Select 10–15 conceptual questions after reading; review mistakes before doing more. It is unofficial and omits lecture 1.

**Correction:** For Q127, prediction uses `FPFᵀ + Q`, but covariance need not increase relative to its previous value for every dynamics matrix. The example on p. 36 omits Q in its stated gain; that gain assumes zero relevant process noise.

Use the resources below when you need a clearer explanation; they are targeted supplements. Suggested times apply to the supplemental route alone; add time for the main reading and quiz. Adapt the 30-minute fallback to your weakest points.

This topic deserves a little more time: confidence and correlations are often harder to interpret than the equations. The videos are by **Melda Ulusoy / MathWorks**; no MATLAB work is needed.

1. **Why estimation? — 6:42:** [An Optimal State Estimator, Part 3](https://uk.mathworks.com/videos/understanding-kalman-filters-part-3-optimal-state-estimator--1490710645421.html). Focus on combining a motion model with noisy measurements and estimating quantities not directly measured.
2. **How does the filter work? — 8:36:** [Optimal State Estimator Algorithm, Part 4](https://www.mathworks.com/videos/understanding-kalman-filters-part-4-optimal-state-estimator-algorithm--1493129749201.html). Follow prediction → innovation → correction; interpret `P`, `Q`, `R` and the gain. Then work the scalar example below before memorizing any matrix update.
3. **Why an EKF? — 7:21:** [Nonlinear State Estimators, Part 5](https://ch.mathworks.com/videos/understanding-kalman-filters-part-5-nonlinear-state-estimators-1495052905460.html). Focus on nonlinear transformation of uncertainty and local linearization. UKF/particle-filter comparisons provide context; they do not add required topics to this guide.
4. **Consolidate, 15–25 min:** read the key ideas, explain why increasing measurement noise reduces trust in that measurement, then attempt the questions. Keep bias, SLAM and invariant-EKF intuition on the checklist: the linear-KF videos alone do not cover them.

**Targeted SLAM reading if needed, about 5–8 min:** Dellaert & Hutchinson, [SLAM, §6.4](https://www.roboticsbook.org/S64_driving_perception.html). Read the introduction and **§6.4.3 PoseSLAM**; find **§6.4.8 Using GTSAM to optimize** and read only its prose explaining loop closure; finish with the opening of **§6.4.9 SLAM with Landmarks**. Skip code, ICP and factor-graph derivations. This is a graph-based illustration, not an invariant-EKF explanation. Reading time is an estimate.

**30-minute version:** Read the main reading’s intuition and examples, review the key ideas below, and try 4–6 relevant practice questions. Use one targeted video only if a concept remains unclear.

## Key ideas

- **State versus observation:** position, velocity and orientation may be needed for control, but sensors measure only part of them, indirectly and noisily. Encoders/IMUs provide internal-motion information; cameras/lidar observe the environment.
- **Bias versus noise:** bias is a systematic offset; zero-mean random noise fluctuates. Repeating or averaging a biased measurement does not automatically remove its offset.
- **Belief:** a distribution over possible states. KF retains a mean `x_hat` and covariance `P`, including correlations between errors in different state components.
- **Prediction:** use dynamics and the previous belief to predict the current belief; process-noise covariance `Q` accounts for uncertain evolution.
- **Update:** compare measurement `z` with its prediction; the **innovation** is the discrepancy. The **Kalman gain** weights its correction using prior and measurement uncertainty `R`.
- **KF versus EKF:** linear-Gaussian assumptions make the standard KF exact. EKF uses local Jacobians for nonlinear dynamics/observations and retains a Gaussian approximation; poor linearization, initialization or noise models can cause failure.
- **SLAM:** estimate robot location/trajectory and map jointly. Loop closure recognizes an old place; correlations let a new observation correct other map/trajectory estimates too.
- **Invariant EKF:** represent errors using the motion's Lie-group geometry. The heading/position example produces curved uncertainty; favorable propagation for structured systems does not make every nonlinear inference problem exact.
- **Learning connection:** a network can adapt measurement confidence, detect stops or model bias while a filter retains structured estimation.

### One small KF example

Consider a scalar position estimate with unbiased, independent Gaussian measurement noise: prior mean **10 m**, prior variance `P = 4 m^2`; measured position **14 m**, measurement variance `R = 1 m^2`.

`K = P / (P + R) = 0.8`

`x_updated = 10 + 0.8 * (14 - 10) = 13.2 m`

The more precise measurement gets more influence; updated variance is `(1-K)P = 0.8 m^2`. A larger `R`, with the same prior, gives less measurement influence. This scalar weighted-average picture illustrates the idea; a multivariable filter also exploits correlations. That is how position measurements can improve an unmeasured velocity estimate.

**Connection:** controllers act on estimated state, not ground truth. A confident but wrong estimate can produce bad actions even when the controller itself is correct.

**Do not confuse:** covariance with error itself; low variance with absence of bias; process noise `Q` with sensor noise `R`; Gaussian approximation with exact nonlinear inference. Prediction adds process noise but does not universally increase every variance. Under the ideal model, a measurement update cannot increase posterior covariance; it need not reduce every component strictly.

## Course check

Slides are a coverage and terminology reference; they are not required first reading. Consult a selected page if an answer remains unclear.

- [Official lecture slides](https://jcarpent.github.io/teaching/) — Perception and estimation, PDF viewer pages 9–11, 55, 57, 60, 75, 78–79, 98–99. Page numbers refer to the local 2026 slide versions and may differ if the course files change.

## Practice

Explain each in one or two sentences, then check the answers. These practice prompts are not official quiz questions.

1. **E01.** Why does a robot need state estimation rather than just forwarding sensor readings to its controller?
2. **E02.** How do proprioceptive and exteroceptive sensors differ?
3. **E03.** What is the difference between random measurement noise and sensor bias?
4. **E04.** What is a belief, and what information does the Kalman filter retain about it?
5. **E05.** What happens in the Kalman prediction step?
6. **E06.** What happens in the Kalman update step?
7. **E07.** How should increasing measurement uncertainty affect its influence on the estimate?
8. **E08.** How can position observations improve an estimate of velocity that is not directly measured?
9. **E09.** Under which broad assumptions is the standard KF an exact Gaussian Bayesian filter?
10. **E10.** What does the EKF change to address nonlinear models?
11. **E11.** Why can an EKF be inaccurate or diverge?
12. **E12.** What must SLAM estimate jointly?
13. **E13.** Why can loop closure improve more than the current robot position?
14. **E14.** What is the intuition behind invariant-EKF methods?
15. **E15.** How can learning complement a model-based estimator?

## Answers

<details>
<summary>Open after attempting the questions</summary>

- **E01:** Sensors are noisy and incomplete; an estimator combines a motion model and measurements to infer the state needed for decisions/control.
- **E02:** Proprioceptive sensors measure internal/motion-related quantities; exteroceptive sensors observe the environment. Encoders/IMUs and cameras/lidar illustrate the distinction.
- **E03:** Noise varies unpredictably around a modelled mean; bias is a systematic offset. Treating persistent bias as zero-mean noise can give a systematically wrong estimate.
- **E04:** A belief is a probability distribution over possible states. The Gaussian KF representation retains mean and covariance.
- **E05:** Propagate the estimated state and uncertainty through the dynamics, accounting for process noise. Prediction does not incorporate a new sensor observation.
- **E06:** Compare an observation with its predicted measurement and use uncertainty-weighted correction to update the estimate and covariance.
- **E07:** For a fixed prior/model, the estimator generally gives that measurement less weight relative to the prior prediction.
- **E08:** The dynamics couple position and velocity and create error correlations; conditioning on position then also informs velocity.
- **E09:** Linear dynamics/observations, Gaussian initial belief and compatible Gaussian noise assumptions. Outside this setting, its exact optimality claims do not transfer automatically.
- **E10:** It propagates through nonlinear functions and linearizes their uncertainty/measurement relations using Jacobians, retaining an approximate Gaussian belief.
- **E11:** Linearization and Gaussian approximation can be poor for large uncertainty/nonlinearity, and wrong models, noise assumptions or initialization can worsen the error.
- **E12:** The robot's localization/trajectory and a map of an initially unknown environment, with uncertainties and correlations between them.
- **E13:** Recognizing a previously observed place provides a shared constraint; correlations allow corrections to affect earlier trajectory and map estimates too.
- **E14:** Represent errors/uncertainty using the problem's Lie-group geometry. Suitable dynamics have favorable error propagation; this is not a guarantee of exact inference for every nonlinear system.
- **E15:** A learned component can adapt noise confidence, detect stops or model biases while a filter still performs structured state/uncertainty estimation.

</details>
