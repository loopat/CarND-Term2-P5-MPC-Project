## Overview

In this project, Model Predictive Control to drive the car around the track will be implemented. 

The controller will optimize the actuator inputs to follow the reference trajectory and to minimize the cost of the predicted trjactory.

## Prepare

Ipopt is used in this project to optimize the control input and CapAD is used for automation differentiation.

When installing Ipopt, I followed 'install\_Ipopt_CppAD.md' description to install them on Mac, but encounters a problem.

Fortunately, I found this issue,
https://github.com/udacity/CarND-MPC-Project/issues/34
Here is for a record about this issue.

## MPC

As the lecture presented, MPC mainly contains the following steps:

1. Set N and dt.
2. Fit the polynomial to the waypoints.
3. Calculate initial cross trach error and orientation error values.
4. Define the components of the cost function (state, actuators, etc).
5. Define the model constraints. These are the state update equations defined in the Vehicle Models module.

There is one image from lecture is the main part of MPC.
![avatar](./mpc.png).

#### state, actuators
In order to keep track of the state of the car, the state shall be obtained and also the control input which will impact the next state.

The state mainly contains position x, position y, the orientation psi, the velocity v of the car. The cte and epsi are also contained in the state.

The control inputs are mainly from the steering wheel, throttle and break pedals. These actuators will impact the orientation and the velocity which are refered as delta and a.

#### constraints

The actuators of the cars has some constraints. So the lower and upper bounds shall be set.

#### cost function

As the previous state and control inputs can be obtained, the next state can also be predicted based on the model which is presented as model equations.

Via adjusting the control inputs, the difference between the prediction and the reference trajectory can be minimized. The cost function is defined to evaluate it.

Except the state cte, psi, velocity, the control inputs delta and a, the difference between next control input state and current one are also added into the cost function, which will make a smoother rate change. In order to make a smoother steering transitions, and also the smoother acceleration, these part cost will be multiply a value which is >1.  

#### parameters 

There are some parameters that shall be set. 

N: The number of timesteps
dt: The duration between actuations.
Lf: The length from front to CoG that has a similar radious.
ref_v: the reference velocity.

n\_vars: The number of model varialbes. As there are x, y, psi, v, cte, epsi are in the state vector, and actuators are delta and a, so n_vars = N * 6 + (N - 1) * 2.

n_contraints: It is N * 6.

#### tranfrom coordinates

In the simulator, there are ptsx, ptsy which are the global x and y positions of the waypoints, and x, y which are the global x and y positions of the car.

According to the foumular in the lecture 14, Implementation of a Particle Filter, the observations in car coordinate system can be transformed into map coordinates. 

xm = xp + cos(psi) * xc - sin(psi) * yc
ym = yp + sin(psi) * xc + cos(psi) * yc

In MPC project, the waypoints can be transformed into the vehicle coordinate via the following equations.

