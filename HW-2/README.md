# Homework 2: Physics Simulator and ML Surrogate for Tilt Dynamics

## Overview

This project implements a physics-based simulator for vehicle tilt dynamics and trains a machine learning (ML) surrogate model to approximate the simulator output.

The goal is to:
- Model vehicle tilt using control theory
- Generate synthetic data from the simulator
- Train an ML model to predict system behavior
- Compare accuracy and computational efficiency

---

## Problem Description

When a vehicle turns, passengers experience lateral acceleration:

\[
a_{lat} = \frac{v^2}{R}
\]

To improve comfort, the vehicle tilts such that:

\[
\phi_{des} = \tan^{-1}\left(\frac{a_{lat}}{g}\right)
\]

The felt acceleration is:

\[
a_{felt} = \frac{v^2}{R} - g \sin(\phi)
\]

---

## Control Model

A PD controller is used:
φ̈ = kp(φ_des − φ) − krate * φ̇

- `kp` → proportional gain (controls responsiveness)
- `krate` → damping gain (controls smoothness)

---

## Project Structure
HW-2/

├── Simulator_PD_Control.ipynb # Physics-based simulator

├── ML_PD_Control.ipynb # ML surrogate model

├── dataset.csv # Generated dataset

├── ml_vs_simulator.png # Scatter 

├── error_hist.png # Error distribution

├── multi_case.png # Multi-case comparison

├── kp_sensitivity.png # kp analysis

├── krate_sensitivity.png # krate analysis

├── report.tex # Final report (LaTeX)

├── README.md # Project documentation


---

## Workflow

### 1. Physics Simulator
- Solves tilt dynamics using ODE integration
- Computes felt lateral acceleration
- Generates time-series data

---

### 2. Data Generation
- Random sampling of:
    - velocity (v)
    - radius (R)
    - kp
    - krate
- ~10,000 samples generated

---

### 3. ML Surrogate
- Neural network (MLPRegressor)
- Input:(v, R, kp, krate)
- Output:max felt acceleration

- Inputs normalized using StandardScaler

---

### 4. Evaluation

#### Accuracy
- Scatter plot (ML vs Simulator)
- Error histogram
- Multi-case comparison

#### Parameter Analysis
- Effect of kp and krate on output
- Shows limited impact on steady-state behavior

#### Optimization
- ML used to find optimal kp, krate
- Faster than brute-force simulation

#### Computational Efficiency
- ML is ~10–15× faster than simulator

---

## Key Results

- ML model closely matches simulator outputs
- Prediction error is very small
- ML provides significant speed improvement
- Control gains mainly affect transient response

---

## Insights

- Simulator → physics-based, accurate but slower
- ML model → fast approximation of system behavior
- ML can be used for rapid controller tuning

---

## How to Run

1. Run `Simulator_PD_Control.ipynb`
- Generates dataset.csv

2. Run `ML_PD_Control.ipynb`
- Trains ML model
- Generates plots
- Performs comparison and optimization

---

## Requirements

- Python 3.x
- NumPy
- Pandas
- Matplotlib
- Scikit-learn
- SciPy

---

## Conclusion

This project demonstrates how machine learning can effectively approximate a physics-based simulator, enabling faster evaluation and optimization while maintaining high accuracy.

---

## Authors

- Kshitij gupta
- Sabyrzhan llessov