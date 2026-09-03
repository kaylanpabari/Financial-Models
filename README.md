# Financial-Models

# Monte Carlo Portfolio Stress Tester

Simulate thousands of possible portfolio outcomes over time and stress-test them against custom market scenarios (recessions, crashes, stagflation, etc.) using geometric Brownian motion.

---

## Table of Contents

- [About](#about)
- [How It Works](#how-it-works)
- [Installation](#installation)
- [Usage](#usage)
- [Parameters](#parameters)
- [Understanding the Output](#understanding-the-output)
- [Customizing Scenarios](#customizing-scenarios)
- [Disclaimer](#disclaimer)

---

## About

Traditional projections show a single "expected" outcome, which can be misleading — real portfolios are volatile. This tool runs thousands of randomized simulations to show the *range* of possible outcomes, then compares a base-case market against user-defined stress scenarios.

It outputs:
- Summary statistics (median, 5th/95th percentile outcomes)
- Risk metrics (Value at Risk and Conditional Value at Risk)
- A fan chart visualizing all scenarios side by side

## How It Works

Each simulated path uses **geometric Brownian motion**, a common model for asset prices:

```
log_return = (μ - 0.5σ²) * dt + σ * √dt * Z
```

Where:
- `μ` (mu) — expected annual return (drift)
- `σ` (sigma) — annual volatility
- `Z` — random noise drawn from a standard normal distribution
- `dt` — time step (1/12 for monthly steps)

Returns are compounded monthly across the full time horizon, generating one possible "path" per simulation. Running this thousands of times produces a distribution of outcomes rather than a single number.

## Installation

```bash
pip install numpy matplotlib
```

## Usage

Run directly:

```bash
python stress_test.py
```

This will print a summary of each scenario to the console and open a fan chart comparing them visually.

Example output:

```
Stress test results:

Base case:
  Median ending value: $339,966
  5th percentile (downside): $106,625
  95th percentile (upside): $870,049
  VaR (95% confidence): $-6,625
  CVaR (95% confidence): $21,789
  Probability of loss: 4.0%

Mild recession:
  Median ending value: $89,903
  5th percentile (downside): $18,937
  95th percentile (upside): $320,782
  VaR (95% confidence): $81,063
  CVaR (95% confidence): $89,452
  Probability of loss: 55.0%

Severe crash:
  Median ending value: $3,703
  5th percentile (downside): $425
  95th percentile (upside): $45,307
  VaR (95% confidence): $99,575
  CVaR (95% confidence): $99,809
  Probability of loss: 99.0%

Stagflation:
  Median ending value: $54,647
  5th percentile (downside): $8,421
  95th percentile (upside): $316,709
  VaR (95% confidence): $91,579
  CVaR (95% confidence): $93,301
  Probability of loss: 75.0%
```

## Parameters

| Parameter         | Description                                      | Example  |
|-------------------|---------------------------------------------------|----------|
| `principal`       | Starting portfolio value                          | `100000` |
| `mu`               | Expected annual return (base case)                | `0.07`   |
| `sigma`            | Annual volatility (base case)                     | `0.15`   |
| `years`            | Investment horizon in years                       | `20`     |
| `num_simulations`  | Number of random paths to simulate                | `1000`   |

## Understanding the Output

- **Median ending value** — the middle outcome across all simulations
- **5th / 95th percentile** — the range covering 90% of outcomes (downside vs upside)
- **VaR (Value at Risk)** — the loss threshold you'd expect to exceed only 5% of the time
- **CVaR (Conditional VaR)** — the *average* loss in that worst 5% of cases (a more conservative "tail risk" measure)
- **Probability of loss** — the fraction of simulations that ended below the starting principal

## Customizing Scenarios

Scenarios are just `(mu, sigma)` pairs. Add your own in the `scenarios` dictionary:

```python
scenarios = {
    "Mild recession": (0.02, 0.20),
    "Stagflation": (0.00, 0.25),
    "Severe crash": (-0.10, 0.35),
}
```

## Disclaimer

This project is for educational and illustrative purposes only. It is **not financial advice**. Real markets do not perfectly follow log-normal distributions, and past volatility is not a reliable predictor of future risk.
