# CarND-Controls-MPC

The purpose of this project is to implement the MPC(Model Predictive Control) code that will be able to drive a vehicle around the track using the Term 2 simulator.

---

### Implementation steps

## The model

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


## Timestep Length and Elapsed Duration 

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

## Polynomial Fitting and MPC Preprocessing

The preprocessing implied shifting the reference angle to 90 degrees and rotating all points about the origin. The purpose of preprocessing was to simplify the polynomial fit and facilitate computations for CTE and transitions. After preprocessing, the x and y coordinates of the vehicle, along with its orientation angle, psi, where set to 0. 

## Model Predictive Control with Latency

On top of the connection latency, in order to resemble more to the real world, an additional matency of 100 miliseconds has been added to the simulator. At low speeds, this latency does not really affect the behaviour of the vehicle, but at high speeds, the vehicle might become instable and even erratic. In order to deal with latency, several actions have been taken:

* Several coefficients are used in order to specify how much importance should the cost function give to certain attributes, like the CTE, epsi, delta, acceleration, etc. For example, the peed is less important than the steering angle and staying aligned with the track. 

The final values for the coefficients have been selected through trial and error. In the table below, a couple of settings are presented, along side with images of their effect on the vehicle's predicted trajectory:

| CTE | epsi | delta | acceleration |  change in delta | change in acceleration | result |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1000 | 1000 | 5 | 5 | 2000 | 100 | ![settings](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/img1.JPG) |
| 1000 | 1000 | 50 | 50 | 2000 | 100 | ![settings](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/img2.JPG) |
| 1000 | 1000 | 50 | 50 | 100 | 100 | ![settings](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/img3.JPG) |
| 1000 | 1000 | 50 | 50 | 3000 | 100 | ![settings](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/img4.JPG) |
| 1000 | 1000 | 50 | 50 | 10000 | 1000 | ![settings](https://raw.githubusercontent.com/sorix6/CarND-MPC-Project/master/images/img5.JPG) |

The following values have been used in the final configuration:
| CTE | epsi | delta | acceleration |  change in delta | change in acceleration |
| 4000 | 4000 | 1 | 1 | 10000 | 50 |

In addition to this, penalties where added for steering and accelerating at the same time and also for high speeds while steering

* The state values are calculated by taking into account the change rate 

## Results

A video of the simulation using the implementation can be found in **/videos/MPC-02-02-2019-22-11-24**

## Resources

During the implementation of this project, the following resources have been used:

* Udacity courses
* Self-Driving Car Project Q&A | MPC Controller(https://www.youtube.com/watch?v=bOQuhpz3YfU&feature=youtu.be)
* Jeremy Shannon CarND-MPC-Project(https://github.com/jeremy-shannon/CarND-MPC-Project)









## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.

## Build with Docker-Compose
The docker-compose can run the project into a container
and exposes the port required by the simulator to run.

1. Clone this repo.
2. Build image: `docker-compose build`
3. Run Container: `docker-compose up`
4. On code changes repeat steps 2 and 3.

## Tips

1. The MPC is recommended to be tested on examples to see if implementation behaves as desired. One possible example
is the vehicle offset of a straight line (reference). If the MPC implementation is correct, it tracks the reference line after some timesteps(not too many).
2. The `lake_track_waypoints.csv` file has waypoints of the lake track. This could fit polynomials and points and see of how well your model tracks curve. NOTE: This file might be not completely in sync with the simulator so your solution should NOT depend on it.
3. For visualization this C++ [matplotlib wrapper](https://github.com/lava/matplotlib-cpp) could be helpful.)
4.  Tips for setting up your environment are available [here](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/23d376c7-0195-4276-bdf0-e02f1f3c665d)
5. **VM Latency:** Some students have reported differences in behavior using VM's ostensibly a result of latency.  Please let us know if issues arise as a result of a VM environment.

## Editor Settings

We have kept editor configuration files out of this repo to
keep it as simple and environment agnostic as possible. However, we recommend
using the following settings:

* indent using spaces
* set tab width to 2 spaces (keeps the matrices in source code aligned)

## Code Style

Please (do your best to) stick to [Google's C++ style guide](https://google.github.io/styleguide/cppguide.html).

## Project Instructions and Rubric

Note: regardless of the changes you make, your project must be buildable using
cmake and make!

More information is only accessible by people who are already enrolled in Term 2
of CarND. If you are enrolled, see [the project page](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/f1820894-8322-4bb3-81aa-b26b3c6dcbaf/lessons/b1ff3be0-c904-438e-aad3-2b5379f0e0c3/concepts/1a2255a0-e23c-44cf-8d41-39b8a3c8264a)
for instructions and the project rubric.

## Hints!

* You don't have to follow this directory structure, but if you do, your work
  will span all of the .cpp files here. Keep an eye out for TODOs.

## Call for IDE Profiles Pull Requests

Help your fellow students!

We decided to create Makefiles with cmake to keep this project as platform
agnostic as possible. We omitted IDE profiles to ensure
students don't feel pressured to use one IDE or another.

However! I'd love to help people get up and running with their IDEs of choice.
If you've created a profile for an IDE you think other students would
appreciate, we'd love to have you add the requisite profile files and
instructions to ide_profiles/. For example if you wanted to add a VS Code
profile, you'd add:

* /ide_profiles/vscode/.vscode
* /ide_profiles/vscode/README.md

The README should explain what the profile does, how to take advantage of it,
and how to install it.

Frankly, I've never been involved in a project with multiple IDE profiles
before. I believe the best way to handle this would be to keep them out of the
repo root to avoid clutter. Most profiles will include
instructions to copy files to a new location to get picked up by the IDE, but
that's just a guess.

One last note here: regardless of the IDE used, every submitted project must
still be compilable with cmake and make./

## How to write a README
A well written README file can enhance your project and portfolio and develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777).
