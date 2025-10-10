---
title: "Recursive Update Filtering: A New Approach"
excerpt: "Maximum _a posteriori_ estimation via homotopy<br/><img src='/images/ode-bruf.png' width='300'>"
collection: portfolio
---
Consider the _data assimilation_ (or _measurement update_ problem): given a prior state estimate with an associated uncertainty distribution, incorporate new information from a measurement. This new information should nudge the state estimate closer to the true state and reduce uncertainty.

If the measurement is a _nonlinear function_ of the state, the state update can be computed using the Extended Kalman Filter (EKF) equations.

$$ \hat{x} = \bar{x} + K*(y - h(\bar{x})) $$

$$ \hat{P} = (I - KH)\bar{P}$$

$$ K = \bar{P}H^T(HPH^T + R)^{-1}$$

where $H$ is the measurement Jacobian

$$ H = \frac{\partial h}{\partial x}. $$

$H$ contains the set of derivatives of the measurement model $y = h(x) + \eta$ with respect to the states. The measurement has its own uncertainty, $\eta \sim \mathcal{N}(0,R)$. The figure below shows the result of the EKF update for a range measurement.

![EKF](/images/portfolio-2/ekf.png){: .align-center width="300px"}

In this figure, the prior state estimate is $\hat{x} = \begin{bmatrix} -2.5 & 0 \end{bmatrix}$. The prior distribution is Gaussian and centered at the prior state estimate. The measurement model is,

$$ h(x) = \sqrt{x_1^2 + x_2^2}, $$

the distance of the state from the origin. The measurement uncertainty is $R = 0.1^2$. This forms a _measurement likelihood distribution_ in the shape of a ring (red) with maxima at the measurement value, $y = 1$. The crescent-shaped _posterior distribution_, the distribution we're hoping to capture with our EKF update, is shown in yellow.

The updated state estimate lies close to the posterior distribution. But we can do better!

Consider the Bayesian Recursive Update Filter (BRUF), in which we apply the EKF update $N$ times with inflated measurement noise covariance $NR$. 

$$ \hat{x}_{i+1} = \hat{x}_i + K_i*(y - h(\hat{x}_i)) $$

$$ \hat{P}_{i+1} = (I - KH_i)\hat{P}_{i+1}$$

$$ K_i = \hat{P}_iH_i^T(H_iP_iH_i^T + NR)^{-1}$$

where $\hat{x}_{0} = \bar{x}$, $\hat{P}_0 = \bar{P}$, and $H_i = \partial h / \partial (\hat{x}_i)$. The intuition behind the BRUF is that by inflating the measurement noise covariance to $NR$, we _trust_ the measurement less than we would have in a single EKF update. The figure below shows the result for the range measurment. Interestingly 
