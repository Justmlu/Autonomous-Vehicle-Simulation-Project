# Autonomous Vehicle Simulation

## Overview
This project focuses on developing a simulated vehicle sensing system for autonomous driving. The core objective is to track other cars on a two-dimensional rectangular grid by computing belief distributions based on noisy sensor readings. 

---

To visualize the code and test different behaviors, use the `drive.py` script. 
* **Python Requirement:** `drive.py` requires Python 2 to run, while all other code relies on Python 3.

### Manual Driving
To drive the vehicle manually, run the following command:
`python drive.py -l lombardi -i none`

* **Controls:** Steer using the arrow keys or w, a, and d. 
* **Objective:** Drive from the start location to the green finish box without getting into an accident.
* **Note:** You cannot reverse the car or turn in place, and you can quit the simulation by pressing q.

---

## Mathematical Model
The world is discretised into tiles represented by `(row, col)` pairs. At each time step $t$, your car receives a noisy distance estimate to other independently moving cars.

* Your car's position is $a_t \in \mathbb{R}^2$ and the actual location of another car is $C_t \in \mathbb{R}^2$.
* The microphone sensor outputs a distance measurement $D_t$, which is a Gaussian random variable.
* The mean of this distribution is the true distance between the cars, and the standard deviation is defined by `Const.SONAR_STD`.
* This relationship is modeled as $D_t \sim \mathcal{N}(||a_t - C_t||, \sigma^2)$.

---

## Core Features to Implement

### 1. Emission Probabilities 
This component assumes the other car is stationary ($C_t = C_{t-1}$). 
* Implement the `observe` method in the `ExactInference` class within `submission.py`.
* This method updates the current posterior probability based on a new distance measurement $D_t = d_t$.
* The update follows this proportionality: $P(C_t|D_1=d_1,...,D_t=d_t) \propto P(C_t|D_1=d_1,...,D_{t-1}=d_{t-1})p(d_t|c_t)$.
* You must remember to normalise the posterior probability after the update is applied.
* **Test Command:** `python drive.py -a -p -d -k 1 -i exactInference`.

### 2. Transition Probabilities 
This component assumes the other car is moving according to the transition probabilities $p(c_{t+1}|c_t)$ provided in `self.transProb`.
* Implement the `elapseTime` method in the `ExactInference` class.
* This method updates the posterior probability for the next time step $t+1$.
* The update utilizes the recurrence: $P(C_{t+1}=c_{t+1}|D_1=d_1,...,D_t=d_t) \propto \sum_{c_t} P(C_t=c_t|D_1=d_1,...,D_t=d_t)p(c_{t+1}|c_t)$.
* **Test Command:** `python drive.py -a -d -k 1 -i exactInference`.
$