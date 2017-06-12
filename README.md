# CarND-Controls-PID
Self-Driving Car Engineer Nanodegree Program

---

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `./install-mac.sh` or `./install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.
* Simulator. You can download these from the [project intro page](https://github.com/udacity/self-driving-car-sim/releases) in the classroom.

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./pid`. 

## Editor Settings

We've purposefully kept editor configuration files out of this repo in order to
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
of CarND. If you are enrolled, see [the project page](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/f1820894-8322-4bb3-81aa-b26b3c6dcbaf/lessons/e8235395-22dd-4b87-88e0-d108c5e5bbf4/concepts/6a4d8d42-6a04-4aa6-b284-1697c0fd6562)
for instructions and the project rubric.

## Hints!

* You don't have to follow this directory structure, but if you do, your work
  will span all of the .cpp files here. Keep an eye out for TODOs.

## Call for IDE Profiles Pull Requests

Help your fellow students!

We decided to create Makefiles with cmake to keep this project as platform
agnostic as possible. Similarly, we omitted IDE profiles in order to we ensure
that students don't feel pressured to use one IDE or another.

However! I'd love to help people get up and running with their IDEs of choice.
If you've created a profile for an IDE that you think other students would
appreciate, we'd love to have you add the requisite profile files and
instructions to ide_profiles/. For example if you wanted to add a VS Code
profile, you'd add:

* /ide_profiles/vscode/.vscode
* /ide_profiles/vscode/README.md

The README should explain what the profile does, how to take advantage of it,
and how to install it.

Frankly, I've never been involved in a project with multiple IDE profiles
before. I believe the best way to handle this would be to keep them out of the
repo root to avoid clutter. My expectation is that most profiles will include
instructions to copy files to a new location to get picked up by the IDE, but
that's just a guess.

One last note here: regardless of the IDE used, every submitted project must
still be compilable with cmake and make./


# Project Analysis

## Effect of PID Controller

* A PID (Proportional, Integral, Derivative) controller is a control loop feedback controller which is widely used in different control systems.
* Error is an input variable for the controller:
* cte = desired_state - measured_state
* P --> With the proportional band (P) only, the PID controller output is proportional to the cte. It takes into account only the present value of cte. Thanks to this part of controller, it is able to steer in the correct direction.
* I --> Integral term (I) takes into account the integral of cte over the past time. It is used to reduce systematic bias. From my experianse of PID tuning on real-world systems and some experiments in the simulator, it was found out that we do not need to compute the integral for all time, but calculat it over only last n frames. In other case, we can accumulate errors because of left turns prevalens which can result in different behaviour of the controller during multi lap drive and even going off the track.
* D --> With derivative (D) part, the controller output is proportional to the rate of change of cte (its derivative). The parameter is used to reduce overshooting and dump oscillations caused by the P.

## Tuning Approach

* While the PID controller is easy to implement, but it is not so easy to tune.
* Initially, the controller was tuned with so-called Ziegler–Nichols method. Generally speaking, it requires to set Kd and Ki to 0 and gradually increase Kp before the car runs with stable and consistent oscillations. This value of Kp and the oscillation period can be used to calculate optimal pid controller parameters by the method. Unfortunatly, the controller with resulted parameters was able to drive car around the track but with a lot of wobbling. That is why, parameters were further tuned manually by try-and-error process.
* The same process was applied for differnt speed, so different PID parameters were found for different speed. The results were linearized The same process was applied for different speed, so different PID parameters were found for different speed. The results were linearized in order to make the parameters automatically tune with the car speed variation. It also takes into account different time intervals between data frames.

