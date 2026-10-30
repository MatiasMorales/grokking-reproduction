# Grokking reproduction

A small reproduction of [Grokking: Generalization Beyond Overfitting on Small Algorithmic Datasets](https://arxiv.org/abs/2201.02177) (Power et al., 2022).

## What this is

Grokking is when a neural network first memorizes its training examples and only much later learns the actual rule behind them. Early in training, the model gets the training questions right but does no better than guessing on questions it has not seen. If you keep training long after that, test accuracy can suddenly jump to near 100%. This notebook tries to make that happen on a simple math task and plots it.

## Setup

- Task: (a + b) mod 97, for every pair of numbers a and b from 0 to 96.
- Data: 50% of all equations are used for training. The other 50% are held out for testing.
- Model: 2-layer transformer, width 128, 4 attention heads.
- Optimizer: AdamW, learning rate 1e-3, weight decay 0.1.
- Batch size: 512.
- Training length: 20,000 steps.

## How to run

Open `grokking.ipynb` in [Google Colab](https://colab.research.google.com/), switch to a GPU runtime (Runtime > Change runtime type > GPU), and run all cells.

## Results

See EXPERIMENTS.md for all runs. First result: with 50% training data,
weight decay 1 groks in 1,100 steps, weight decay 0.1 in 15,700 steps,
and weight decay 0 did not grok within 20,000 steps (test accuracy stuck
at about 30%).

## Next experiments

- Training data fraction: try less training data (for example 30% or 40%) and see how it changes when, or whether, grokking happens.
- Weight decay: try different values to see how much it affects the jump in test accuracy.
- Subtraction: switch the task to (a - b) mod 97.
