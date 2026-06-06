# Reinforcement Learning Analysis: Policy Evaluation & Iteration

## 1. Overview

This document outlines the results of evaluating and improving policies within the FrozenLake-v1 (8x8) environment. The primary objective was to compare sample-based evaluation (Monte Carlo) against model-based evaluation (Dynamic Programming) and observe the impact of hyperparameter tuning, specifically the discount factor (gamma).

## 2. Analysis Techniques

### 2.1 Monte Carlo Policy Evaluation (Sample-Based)

Methodology: The agent learns by interacting with the environment, running full episodes to calculate the expected return for each state.

* Strengths: Does not require prior knowledge of the environment's transition dynamics.
* Observed Weaknesses (The Sparse Reward Trap): When initialized with a zero-policy (always moving Left), the agent became stuck against the wall. Because the reward is strictly 0 everywhere except the goal, the Value Function remained flat at 0, preventing the Q-values from distinguishing between actions.
* Resolution: Implemented a hard step-limit (e.g., 200 steps) to prevent infinite loops during non-terminating episodes.

### 2.2 Dynamic Programming: Policy Iteration (Model-Based)

Methodology: Utilizes the environment's known transition probability matrix to mathematically sweep through states and calculate the Value Function.

* Strengths: Instantly bypasses the blind spot of sparse rewards. It propagates the value of the goal state backward through the grid without needing to accidentally stumble upon it.
* Results: Successfully converged to an optimal policy, effectively mapping a safe route around the holes to the goal.

## 3. Deep Dive: The Impact of the Discount Factor (gamma)

The discount factor (gamma) determines how much the agent cares about rewards in the distant future compared to immediate rewards. In a sparse-reward environment like FrozenLake, tuning this parameter drastically changes the optimal policy.

### Case Study A: The Myopic Agent (gamma = 0.0)

When gamma is set to 0, the agent only cares about the immediate next step.

* Observed Behavior: The agent is completely blind to the overall goal. Unless it is standing exactly one square away from the treasure, all actions look equally worthless (0 reward). The policy fails to converge on a coherent path, resulting in random wandering or wall-bumping.
* Path Length: N/A (Fails to reach goal)
* Risk Tolerance: High

### Case Study B: The Balanced Agent (gamma = 0.90)

When gamma is moderately high, the agent cares about the goal, but the value of that goal degrades by 10 percent for every step it takes to get there.

* Observed Behavior: The agent seeks the shortest possible path. Because taking a longer, safer detour reduces the final value significantly, the agent will hug the edges of the holes to minimize step count.
* Path Length: Minimum
* Risk Tolerance: Moderate to High

### Case Study C: The Far-Sighted Agent (gamma = 0.99 to 1.0)

When gamma is near 1, the agent values a reward 50 steps from now almost as much as a reward tomorrow.

* Observed Behavior: The agent seeks the safest possible path. Because the penalty for taking a longer route is mathematically negligible, the agent is perfectly willing to take a massive detour around the outside edge of the map to avoid walking near any holes.
* Path Length: Often Maximum
* Risk Tolerance: Very Low
