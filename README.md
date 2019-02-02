# CarND-Controls-MPC

The purpose of this project is to implement the MPC(Model Predictive Control) code that will be able to drive a vehicle around the track using the Term 2 simulator.

---

## Implementation steps

### The model

The kinematic model corresponds to the one presented during the lectures and includes the following parameters:

![Kinematic model](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/model.JPG)

* x - x coordinate
* y - y coordinate
* psi - orientation angle
* v - velocity
* cte - Cross Track Error
* epsi - Psi Error

The actuators are:
![Actuators](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/actuators.JPG)
* delta - steering angle
* a - acceleration

In order to calculate the state at a time t+1, we use the following equations:

![Equations](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/equations.JPG)


### Timestep Length and Elapsed Duration 

The prediction horizon is the duration over which future predictions are made and it is the product of two variables:
* N - the number of timesteps in the horizon
* dt - the time elapsed between actuations

The value for dt has been selected as 0.1, as suggested in the classes.

Various values have been tested out for N with the following results:

| N | result |
| --- | --- |
| 5 | the trajectory line was pointing in the opposite direction of the reference line |
| 8 | the trajectory line tended to not diverge from the reference line but it was pointing in the opposite direction |
| 10 | the trajectory line does not diverge from the reference line |
| >10 | increased the number of calculations without real gain for the MPC |

In the simulator, the yellow line corresponds to the reference line and the green line to the MPC predicted trajectory.

### Polynomial Fitting and MPC Preprocessing

The preprocessing implied shifting the reference angle to 90 degrees and rotating all points about the origin. The purpose of preprocessing was to simplify the polynomial fit and facilitate computations for CTE and transitions. After preprocessing, the x and y coordinates of the vehicle, along with its orientation angle, psi, where set to 0. 

### Model Predictive Control with Latency

On top of the connection latency, in order to resemble more to the real world, an additional matency of 100 miliseconds has been added to the simulator. At low speeds, this latency does not really affect the behaviour of the vehicle, but at high speeds, the vehicle might become instable and even erratic. In order to deal with latency, several actions have been taken:

* Several coefficients are used in order to specify how much importance should the cost function give to certain attributes, like the CTE, epsi, delta, acceleration, etc. For example, the peed is less important than the steering angle and staying aligned with the track. 

The final values for the coefficients have been selected through trial and error. In the table below, a couple of settings are presented, along side with images of their effect on the vehicle's predicted trajectory:

| CTE | epsi | delta | acceleration |  change in delta | change in acceleration | result |
| --- | --- | --- | --- | --- | --- | --- |
| 1000 | 1000 | 5 | 5 | 2000 | 100 | ![settings](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/img1.JPG) |
| 1000 | 1000 | 50 | 50 | 2000 | 100 | ![settings](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/img2.JPG) |
| 1000 | 1000 | 50 | 50 | 100 | 100 | ![settings](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/img3.JPG) |
| 1000 | 1000 | 50 | 50 | 3000 | 100 | ![settings](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/img4.JPG) |
| 1000 | 1000 | 50 | 50 | 10000 | 1000 | ![settings](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/img5.JPG) |

The following values have been used in the final configuration:

| CTE | epsi | delta | acceleration |  change in delta | change in acceleration |
| --- | --- | --- | --- | --- | --- |
| 4000 | 4000 | 1 | 1 | 10000 | 50 |

In addition to this, penalties where added for steering and accelerating at the same time and also for high speeds while steering

* The state values are calculated by taking into account the change rate 

### Results

A video of the simulation using the implementation can be found in **/videos/MPC-02-02-2019-22-11-24**

### Resources

During the implementation of this project, the following resources have been used:

* Udacity courses
* Self-Driving Car Project Q&A | MPC Controller(https://www.youtube.com/watch?v=bOQuhpz3YfU&feature=youtu.be)
* Jeremy Shannon CarND-MPC-Project(https://github.com/jeremy-shannon/CarND-MPC-Project)


