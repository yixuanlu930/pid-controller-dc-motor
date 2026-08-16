# DC Motor PID Control

A control-systems project focused on the **implementation, simulation, tuning, and performance evaluation of a PID controller for a DC motor**.

The project analyzes how the proportional, integral, and derivative gains affect the dynamic response of the motor and evaluates several controller configurations according to key performance indicators such as:

* Overshoot
* Steady-state error
* Settling time
* Delay time
* Stability
* Control effort

The final tuning achieves a fast and stable response with approximately **1.14% overshoot**, very low steady-state error, and a settling time of approximately **3.9 seconds**.

---

## Overview

A PID controller determines the control signal according to:

[
u(t) =
K_p e(t)
+
K_i \int e(t),dt
+
K_d \frac{de(t)}{dt}
]

where:

* (K_p) is the proportional gain
* (K_i) is the integral gain
* (K_d) is the derivative gain
* (e(t)) is the difference between the desired reference and the measured motor speed

The objective of the project is to understand experimentally how these three parameters affect the behavior of a simulated DC motor.

The workflow is:

```text
Speed Reference
      │
      ▼
Compute Error
      │
      ▼
PID Controller
      │
      ▼
Control Signal u(t)
      │
      ▼
DC Motor Simulator
      │
      ▼
Motor Speed
      │
      └──────────────► Feedback
```

---

# Project Objectives

The practical work is divided into three main exercises.

## Exercise 1 — PID Controller Implementation

The first objective is to implement:

```python
Exec_controller_cycle()
```

inside the `Controller` class.

This function performs one complete control iteration:

1. Reads the current motor speed.
2. Calculates the control error.
3. Computes the proportional contribution.
4. Updates the integral contribution.
5. Calculates the derivative contribution.
6. Generates the new control signal.
7. Sends the control action to the DC motor simulator.
8. Updates the motor state.

This function represents the core feedback-control loop.

---

# PID Controller

The controller contains three fundamental contributions.

## Proportional Term

[
P = K_p e(t)
]

The proportional component reacts directly to the current error.

Increasing (K_p):

* Accelerates the response
* Reduces rise time
* Can increase overshoot
* Can produce stronger oscillations

---

## Integral Term

[
I = K_i \int e(t),dt
]

The integral component accumulates past errors.

Its main purpose is to eliminate persistent steady-state error.

Increasing (K_i):

* Reduces steady-state error
* Can increase overshoot
* Can increase oscillation
* Can increase settling time

---

## Derivative Term

[
D = K_d \frac{de(t)}{dt}
]

The derivative contribution responds to changes in the error.

Increasing (K_d):

* Adds damping
* Reduces oscillation
* Can reduce overshoot
* Improves transient stability

---

# DC Motor Simulator

The motor is represented through the:

```python
DC_motor_sim
```

class.

The simulator models the dynamic evolution of the DC motor and provides methods including:

```python
Set_ea()
Give_me_speed()
Exec_cycle()
```

The controller sends a voltage/control command to the motor and receives the updated speed as feedback.

---

# Closed-Loop Control

The complete feedback system can be represented as:

```text
              ┌─────────────────┐
              │    Reference    │
              └────────┬────────┘
                       │
                       ▼
                 ┌───────────┐
                 │   Error   │◀───────────────┐
                 └─────┬─────┘                │
                       │                      │
                       ▼                      │
                 ┌───────────┐                │
                 │    PID    │                │
                 │Controller │                │
                 └─────┬─────┘                │
                       │                      │
                       ▼                      │
                 ┌───────────┐                │
                 │ DC Motor  │                │
                 └─────┬─────┘                │
                       │                      │
                       ▼                      │
                  Motor Speed ────────────────┘
```

---

# Exercise 2 — Manual PID Tuning

Several PID configurations are evaluated according to different control objectives.

The reference motor speed is:

```text
50 km/h
```

The objective is not to find one universally optimal controller, but to understand how different PID parameters influence different performance characteristics.

---

# Configuration 1 — Approximately 10% Overshoot

Parameters:

```text
Kp = 0.1
Ki = 3.0
Kd = 0.6
```

Measured performance:

| Metric             |      Result |
| ------------------ | ----------: |
| Overshoot          |      10.33% |
| Delay              |   17 cycles |
| Steady-state error | 0.0004 km/h |
| Settling time      |  142 cycles |

This configuration successfully achieves approximately the desired 10% overshoot.

The final error is almost zero, although the response requires a relatively long time to stabilize.

---

# Configuration 2 — Minimum Settling Time

Parameters:

```text
Kp = 1.0
Ki = 3.0
Kd = 0.6
```

Measured performance:

| Metric             |      Result |
| ------------------ | ----------: |
| Overshoot          |      32.80% |
| Delay              |    6 cycles |
| Steady-state error | 0.4385 km/h |
| Settling time      |   96 cycles |

This is a much more aggressive configuration.

Advantages:

* Very fast initial response
* Short delay
* Lower settling time

Disadvantages:

* Large overshoot
* Increased oscillation
* Higher final error

This illustrates the classic trade-off between speed and stability.

---

# Configuration 3 — Minimum Steady-State Error

Parameters:

```text
Kp = 0.1
Ki = 5.0
Kd = 0.6
```

Measured performance:

| Metric             |      Result |
| ------------------ | ----------: |
| Overshoot          |      20.59% |
| Delay              |   15 cycles |
| Steady-state error | 0.0121 km/h |
| Settling time      |  152 cycles |

The stronger integral contribution effectively eliminates persistent error.

However, this increases both overshoot and settling time.

---

# Configuration 4 — No Overshoot

Parameters:

```text
Kp = 0.1
Ki = 1.0
Kd = 1.0
```

Measured performance:

| Metric             |      Result |
| ------------------ | ----------: |
| Overshoot          |       0.00% |
| Delay              |   22 cycles |
| Steady-state error | 0.2896 km/h |
| Settling time      |  187 cycles |

This configuration completely eliminates overshoot.

The trade-off is a significantly slower response.

It demonstrates that minimizing one performance metric can negatively affect others.

---

# Configuration 5 — Initial Compromise

An initial attempt was made to obtain a balanced controller using:

```text
Kp = 0.7
Ki = 0.8
Kd = 0.5
```

The experimental analysis showed that this configuration did not provide the expected global compromise.

Although the controller reacted quickly, its overshoot and overall dynamic behavior indicated that further tuning was necessary.

---

# Final Improved Configuration

After analyzing the previous experiments, the controller was retuned to:

```text
Kp = 0.2
Ki = 3.0
Kd = 0.8
```

This configuration produced the best overall compromise among the tested manual configurations.

Measured performance:

| Metric             |          Result |
| ------------------ | --------------: |
| Overshoot          |     **1.1449%** |
| Delay              |   **10 cycles** |
| Steady-state error | **0.0157 km/h** |
| Settling time      |   **39 cycles** |

Assuming one control cycle corresponds to approximately 0.1 seconds:

```text
Settling time ≈ 3.9 seconds
Delay ≈ 1 second
```

The resulting response is:

* Fast
* Stable
* Almost free of overshoot
* Characterized by negligible steady-state error

---

# Performance Evaluation

The third exercise introduces a dedicated:

```python
Performance
```

class.

Its purpose is to objectively quantify the quality of each PID response.

Instead of relying only on visual interpretation of the plots, the controller configurations are compared numerically.

---

# Performance Metrics

## Overshoot

Overshoot measures how much the response exceeds the desired reference.

[
Overshoot =
\frac{y_{max} - y_{ref}}
{y_{ref}}
\times 100
]

A high overshoot may indicate an excessively aggressive or insufficiently damped controller.

---

## Steady-State Error

The steady-state error measures the remaining difference between the desired reference and the final motor speed.

[
E_{ss} =
|r - y_{final}|
]

Ideally:

```text
Ess → 0
```

The integral contribution of the PID controller is especially important for reducing this error.

---

## Settling Time

The settling time (T_s) measures how long the motor requires to enter and remain within a specified tolerance around the reference.

The project uses a:

```text
±2% band
```

around the desired speed.

Conceptually:

```text
Upper limit = Reference × 1.02
Lower limit = Reference × 0.98
```

The controller is considered settled when the response enters this interval and remains within it.

---

## Delay Time

The project also calculates a delay metric representing how quickly the response begins reaching a meaningful fraction of the requested reference.

This provides another measure of controller responsiveness.

---

# Response Visualization

For every PID configuration, two main signals are plotted.

## Motor Speed

The first plot shows:

```text
Motor speed vs. time
```

This makes it possible to visually identify:

* Rise time
* Overshoot
* Oscillation
* Settling behavior
* Steady-state error

---

## Control Signal

The second plot shows:

```text
u(t) vs. time
```

The control signal reveals how aggressively the controller drives the motor.

Typical behavior includes:

```text
Large initial command
       ↓
Rapid acceleration
       ↓
Reduced command after overshoot
       ↓
Small corrective actions
       ↓
Stable control level
```

Analyzing both plots together provides a more complete understanding of the controller.

---

# Effect of PID Gains

The experiments illustrate the characteristic effect of each parameter.

| Parameter | Increasing it generally...                              |
| --------- | ------------------------------------------------------- |
| (K_p)     | Makes response faster but can increase overshoot        |
| (K_i)     | Reduces steady-state error but can increase oscillation |
| (K_d)     | Adds damping and can reduce overshoot                   |

The project demonstrates why PID tuning is fundamentally a **multi-objective trade-off**.

Optimizing only one metric can worsen another.

---

# Experimental Workflow

The complete workflow is:

```text
Define PID gains
      │
      ▼
Initialize motor
      │
      ▼
Set speed reference
      │
      ▼
Run PID simulation
      │
      ▼
Store motor speed
      │
      ├───────────────► Plot speed response
      │
      └───────────────► Plot control signal
                              │
                              ▼
                      Performance class
                              │
             ┌────────────────┼───────────────┐
             │                │               │
             ▼                ▼               ▼
         Overshoot           Ess              Ts
                              │
                              ▼
                     Compare configurations
                              │
                              ▼
                        Retune PID
```

---

# Project Structure

```text
dc-motor-pid-control/
│
├── Práctica PID-DEF.ipynb
└── README.md
```

The notebook contains:

* DC motor simulation
* PID controller implementation
* Multiple controller configurations
* Response visualization
* Performance metric implementation
* Automated evaluation
* Comparative analysis
* Final controller retuning

---

# Installation

A Python environment with Jupyter is recommended.

Install the required dependencies:

```bash
pip install numpy matplotlib jupyter
```

The notebook also uses Python's built-in `statistics` module.

---

# Running the Project

Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/dc-motor-pid-control.git
cd dc-motor-pid-control
```

Start Jupyter:

```bash
jupyter notebook
```

Then open:

```text
Práctica PID-DEF.ipynb
```

Run the notebook sequentially to reproduce the experiments.

---

# Technologies

* Python
* NumPy
* Matplotlib
* Jupyter Notebook

---

# Control Engineering Concepts

This project explores:

* PID control
* Feedback control
* DC motor simulation
* Closed-loop systems
* Proportional control
* Integral control
* Derivative control
* Controller tuning
* Transient response
* Steady-state response
* Overshoot
* Settling time
* Steady-state error
* Control effort
* Performance metrics

---

# Main Findings

The experiments illustrate several fundamental control-system principles.

### Faster does not necessarily mean better

Increasing proportional action can significantly improve response speed while introducing excessive overshoot and oscillation.

### Integral action is effective against steady-state error

Increasing (K_i) helps eliminate persistent tracking error, although excessive integral action can reduce transient performance.

### Derivative action improves damping

Derivative feedback can help reduce oscillations and stabilize aggressive responses.

### PID tuning requires balancing multiple objectives

No individual parameter setting simultaneously minimizes every performance metric.

The final configuration:

```text
Kp = 0.2
Ki = 3.0
Kd = 0.8
```

provides the strongest overall compromise found in the experiments.

---

# Relationship to Evolutionary PID Tuning

This project focuses on **manual PID design and control-system analysis**.

It can also serve as a baseline for more advanced automatic controller optimization techniques such as:

* Genetic algorithms
* Particle Swarm Optimization
* Differential Evolution
* Bayesian optimization
* Reinforcement Learning

The performance metrics developed here can naturally be incorporated into optimization objective functions.

---

# Possible Extensions

Future improvements could include:

* Automatic PID tuning
* Genetic algorithm optimization
* Anti-windup
* Actuator saturation analysis
* Measurement noise
* Disturbance rejection
* Variable references
* Position control
* PID vs. PI comparison
* Ziegler–Nichols tuning
* Cohen–Coon tuning
* Frequency-response analysis
* Real DC motor experiments

---

# Academic Context

This project was developed as an educational exercise focused on **PID control and control-system performance analysis**.

The objective was to implement a complete feedback controller, understand the role of the proportional, integral, and derivative components, and quantitatively evaluate different tuning strategies.

---

# Disclaimer

This project uses a simulated DC motor for educational purposes.

The PID gains and performance results are specific to the implemented simulation and should not be transferred directly to physical hardware without additional modeling, identification, safety constraints, and experimental validation.

---

# License

See the repository license for applicable terms.
