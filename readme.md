# Fair Online Resource Allocation with Multi-Attribute Constraints

## Overview

This project explores how resources can be allocated efficiently while maintaining fairness across different groups of jobs.

In systems such as cloud platforms and data centers, jobs arrive continuously, and the system has to decide whether to accept or reject each job without knowing what will arrive next. Since resources are limited, accepting every job is not possible.

The main goal of this project is to study the trade-off between maximizing the total value of accepted jobs and ensuring that resources are distributed fairly across different groups.

We implemented several existing resource allocation algorithms and developed an **Adaptive FairAllocator** that considers both job value and fairness when making allocation decisions.

## Problem Statement

We consider an online resource allocation problem where jobs arrive one at a time. Each job has a value and belongs to multiple groups based on attributes such as priority, scheduling class, and resource usage.

The system has a fixed capacity \(B\), which limits the number of jobs that can be accepted.

Our objective is to:

- Maximize the total value of accepted jobs.
- Maintain reasonable acceptance rates across different groups.
- Make allocation decisions online, without knowing future job arrivals.

Once a job is accepted or rejected, the decision cannot be changed.

## Fairness Measurement

We measure fairness using the acceptance rate of each group.

For a group \(c\):

\[
\text{Acceptance Rate}(c) = \frac{\text{Accepted jobs in group }c}{\text{Total jobs in group }c}
\]

The fairness gap is calculated as:

\[
\text{Fairness Gap} = \max_c(\text{Acceptance Rate}(c)) - \min_c(\text{Acceptance Rate}(c))
\]

A smaller fairness gap indicates that the acceptance rates across groups are more balanced.

However, improving fairness can sometimes reduce the total value obtained. Our experiments focus on understanding this trade-off.

## Algorithms Implemented

We implemented five allocation algorithms to compare their performance.

### 1. Greedy

The Greedy algorithm accepts jobs as they arrive until the available capacity is exhausted.

It is simple to implement but does not consider fairness between groups.

### 2. D-SETASIDE

D-SETASIDE reserves capacity for different groups using predefined quotas.

This helps maintain fairness, but strict reservations can reduce overall resource utilization and total value.

### 3. R-SETASIDE

R-SETASIDE extends the quota-based approach by introducing randomized acceptance decisions.

After accounting for group quotas, jobs are accepted probabilistically based on their value and a threshold.

This allows the algorithm to make more flexible allocation decisions.

### 4. Proportional Fairness (PF)

Proportional Fairness tracks the utility received by different groups and uses it to guide allocation decisions.

The algorithm attempts to balance overall efficiency with fairness, although handling multiple overlapping group attributes makes the allocation process more complicated.

### 5. Adaptive FairAllocator (Proposed Approach)

We developed an Adaptive FairAllocator that uses both job value and group acceptance rates when deciding whether to accept a job.

The algorithm assigns a score using:

\[
\text{Score} = \alpha \times \text{Value} + (1-\alpha) \times \text{Fairness Score}
\]

Here, \(\alpha\) controls the importance of job value relative to fairness.

The algorithm also estimates a value threshold dynamically based on recently observed jobs.

Unlike approaches that depend entirely on fixed quotas, our method adjusts its decisions according to the jobs observed so far.

## Online Estimation of Theta

An important parameter in the allocation process is \(\theta\), which represents the variation in job values.

\[
\theta = \frac{\text{Maximum Job Value}}{\text{Minimum Job Value}}
\]

Since future job values are unknown, we estimate this parameter using a sliding window of recently observed jobs.

This allows the allocator to adapt to changes in the incoming workload.

## Dataset

We used a sample of the **Google Cluster workload traces** available on Kaggle.

Dataset: https://www.kaggle.com/datasets/derrickmwiti/google-2019-cluster-sample

The following attributes were used:

- CPU usage to represent job value.
- Priority to categorize jobs.
- Scheduling class as another grouping attribute.
- CPU-based classification to divide jobs into small and large groups.

These attributes allow us to evaluate fairness across multiple job categories.

## Experimental Setup

We evaluated the algorithms by processing jobs sequentially under a fixed resource budget.

To study how arrival order affects performance, we repeated the experiments with different shuffled job sequences and averaged the results.

The main experimental parameters are:

- **Budget (B):** Maximum number of jobs that can be accepted.
- **Runs:** Number of times each experiment is repeated.
- **Alpha (α):** Controls the trade-off between efficiency and fairness.

For each algorithm, we measured the total value of accepted jobs and the fairness gap across groups.

## Results and Observations

Our experiments compare the algorithms based on total accepted value and fairness gap.

The main observations are:

| Algorithm | Total Value | Fairness |
|---|---|---|
| Greedy | Moderate | Poor |
| D-SETASIDE | Low | Good |
| R-SETASIDE | High | Moderate |
| Proportional Fairness | High | Moderate |
| Adaptive FairAllocator | High | Good |

Greedy focuses on accepting jobs without considering group-level fairness, which can lead to uneven acceptance rates.

D-SETASIDE maintains better fairness through quotas, but this can come at the cost of lower total value.

R-SETASIDE and Proportional Fairness provide more flexibility and generally achieve better efficiency.

In our experiments, the Adaptive FairAllocator provided a good balance between total value and fairness compared with the baseline algorithms.

These results are based on the workloads and parameter settings used in our evaluation.

## Visualizations

We generated the following plots to compare the algorithms:

1. Total value obtained by each algorithm.
2. Fairness gap across different algorithms.
3. Trade-off between total value and fairness.

These plots help illustrate how different allocation strategies behave under the same workload.


## How to Run

**1. Install the required libraries**

```bash
pip install pandas matplotlib
```

**2. Run the experiment**

```bash
python src/main.py
```

## Team Members

- Utkarsh Agarwal
- Manan Ashokkumar Solanki
- Arjun Reju

## Reference

**Fair Online Resource Allocation with Multi-Attribute Constraints**

Paper: https://arxiv.org/pdf/2510.21055

## Future Work

There are several possible extensions to this project:

- Dynamically adjusting the value of α based on the observed workload.
- Extending the allocation model to handle multiple resources, such as CPU and memory.
- Studying theoretical guarantees for the proposed algorithm.
- Testing the approach on larger workloads and more realistic resource constraints.

## Conclusion

This project helped us explore the challenges of allocating limited resources in an online setting while maintaining fairness across multiple groups.

By implementing and comparing different allocation strategies, we observed that improving fairness often involves a trade-off with total accepted value.

Our Adaptive FairAllocator attempts to balance these objectives using acceptance rates and dynamically estimated thresholds. The experimental results suggest that this approach can provide a useful balance between fairness and efficiency under the tested conditions.
