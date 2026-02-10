# HW1 Problem 1 — Lean-Into-Corner Controller
Gain Scheduling and ML-Based Comparison (Notebook Implementation)

This repository contains a comfort-first lean-into-corner controller for a passenger vehicle equipped with strong roll actuators.

The goal is to reduce passenger discomfort during turns by leaning the vehicle into the corner and minimizing the passenger felt lateral acceleration.

Two approaches are implemented and compared:

- A classical gain-scheduled controller
- An ML-based gain scheduler trained offline

All implementations are provided as Jupyter notebooks.

---

## Repository Contents

AV_Assignments/
└── Assignment-1/
├── Lean into the corner gain scheduling.ipynb
├── ML model.ipynb
└── README.md

---

## Notebook Descriptions

### Lean into the corner gain scheduling.ipynb

This notebook implements the baseline controller using classical control techniques.

Features:
- Roll dynamics model
- Smooth curvature ramp-in and ramp-out
- Cascaded PI controller with inner rate servo
- Feedforward roll torque
- Passenger comfort metric
- Gain scheduling of Kp and Krate using a 2D lookup table
- Bilinear interpolation to avoid gain jumps
- Plots for roll angle, roll rate, torque, curvature, and comfort

---

### ML model.ipynb

This notebook implements an ML-based gain scheduler.

Features:
- Small neural network mapping (v, |kappa|) to (Kp, Krate)
- Offline training using the Cross-Entropy Method (CEM)
- Training across multiple scenarios:
    - v in {5, 10, 20} m/s
    - R in {±50, ±100, ±200, ±400} m
- Evaluation and plotting of:
    - Learned gains
    - Roll response
    - Passenger comfort metric
    - Actuator effort
- Comparison with the gain-scheduled baseline

---

## How to Run (Notebook Workflow)

### Step 1 — Launch Jupyter

Run:
jupyter notebook

Navigate to:
Assignment-1/

---

### Step 2 — Run the gain-scheduled baseline

Open:
Lean into the corner gain scheduling.ipynb


Run all cells from top to bottom.

This will generate plots for:
- Roll angle and reference
- Roll rate and roll-rate command
- Actuator torque
- Road curvature and lateral acceleration
- Passenger felt lateral acceleration
- Scheduled gains

---

### Step 3 — Run the ML-based controller

Open:
ML model.ipynb

Run all cells from top to bottom.

This notebook:
- Trains the ML gain scheduler offline
- Evaluates the learned controller across multiple speed and radius scenarios
- Produces plots for comfort, tracking, torque, and gain behavior

---

## Controller Overview

### Roll dynamics model

The vehicle roll dynamics are modeled as:

I * phi_ddot + c * phi_dot + k * phi = tau_act

Where:
- I is the roll inertia
- c is the roll damping
- k is the roll stiffness
- tau_act is the actuator torque

---

### Reference generation (comfort-first)

Road curvature:
- kappa = 1 / R

Lateral acceleration:
- a_y = v^2 * kappa

Desired lean angle:
- phi_des = -atan(a_y / g)

The lean angle is limited to ±10 degrees for safety.

Curvature is ramped in and out smoothly to reduce jerk and improve comfort.

---

## Control Architecture

### Outer loop (PI controller)

The outer loop computes a roll-rate command:

phi_dot_cmd = Kp * (phi_des - phi) + Ki * integral(phi_des - phi)

The roll-rate command is limited to ±6 degrees per second for comfort.
Anti-windup logic is used when saturation occurs.

---

### Inner loop (rate servo)

The inner loop computes a feedback torque:

tau_fb = Krate * (phi_dot_cmd - phi_dot)

---

### Feedforward torque

A feedforward torque is applied to hold the vehicle at the desired lean angle against roll stiffness:

tau_ff = k * phi_des

---

### Total actuator torque

tau_act = tau_ff + tau_fb

---

## Passenger Comfort Metric

Passenger felt lateral acceleration is approximated as:

a_lat_feel = g * (phi_des - phi)

This metric:
- Is zero when lean perfectly cancels lateral acceleration
- Peaks during turn-in and turn-out
- Is the primary performance objective

The optimization goal is to minimize:

mean over time of |a_lat_feel|

---

## Gain Scheduling vs ML

### Classical gain scheduling

- Gains Kp and Krate are scheduled using a 2D lookup table
- Scheduling variables:
    - vehicle speed v
    - curvature magnitude |kappa|
- Bilinear interpolation ensures smooth gain transitions
- Fully deterministic and interpretable

---

### ML-based gain scheduling

- A neural network learns the gain surface
- Inputs: v and |kappa|
- Outputs: Kp and Krate
- Trained offline using CEM
- Evaluated online with no retraining

Both controllers:
- Use the same plant and reference model
- Respect roll angle and roll-rate limits
- Prioritize passenger comfort

---

## Test Scenarios Covered

- Straight road (R approaches infinity)
- Nominal turn (v = 10 m/s, R = 200 m)
- Tight turn (v = 20 m/s, R = 50 m)
- Left and right turns (positive and negative R)
- Smooth turn-in and turn-out
- Saturation and anti-windup behavior

---

## Performance Profiling (Discussion)

Profiling focuses on:
- Main simulation loop
- Gain computation per timestep
- ML inference versus bilinear interpolation

Observations:
- Classical gain scheduling is extremely efficient and predictable
- ML inference is competitive but requires offline training
- Classical scheduling remains preferable for safety-critical deployment

---

## ML vs Classical Control — Discussion

Classical gain scheduling:
- Simple
- Interpretable
- Easy to certify

ML-based scheduling:
- Automatically adapts gains
- Handles nonlinear interactions
- Can outperform hand-tuned tables if trained carefully

Conclusion:
ML can compete in performance, but classical gain scheduling is preferred unless ML is tightly constrained and validated.

---

## Notes

- Scheduled gains may appear constant during steady turns; this is expected because v and |kappa| become constant after ramp-in.
- Gain variation is most visible during turn entry and exit.
- All results are fully reproducible by re-running the notebooks.

---

## Attribution

This project was completed by:
- Student: kshitij gupta
- LLM Assistant: ChatGPT 5.2 (used for derivations, discussion, and code structuring)

As permitted by the assignment instructions.
