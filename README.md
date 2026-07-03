# Modeling Information Diffusion on Bipartite Graphs

This project is devoted to modeling information diffusion processes on bipartite graphs. The nodes of the two parts are interpreted as users and groups, and the diffusion dynamics are described by a continuous-time SI model.

The main objective of the project is to compare stochastic modeling using the Gillespie algorithm with deterministic mean-field approximations of different levels of detail: IBMF and DBA.

## Project Description

In many social and communication systems, interactions have a bipartite structure. Users belong to groups, channels, or communities, interacting with videos, posts, and other information objects. In this setting, information diffusion occurs not within a single homogeneous population, but between two types of nodes.

The project considers a bipartite graph:

`G = (U ∪ V, E)`,

where:

- `U` — users;
- `V` — groups, channels, or communities; - `E` — connections between users and groups.

Direct connections within a single share are not considered. This allows us to focus on cross-level diffusion: from groups to users and from users to groups.

## Diffusion Model

The dynamics are described by the SI model:

- `S` — receptive node;
- `I` — informed node.

The transition `S` → `I` is irreversible. This formulation is suitable for modeling information processes where the fact of exposure to information is preserved within the cascade under consideration: watching a video, receiving a message, participating in an event, or initially learning about a news item or memetic object.

The model allows for asymmetry in transmission rates:

- `β_VU` — transmission rate from groups to users;
- `β_UV` — transmission rate from users to groups.

It is generally assumed that β_VU > β_UV, since publishing information in a group reaches participants faster than an individual user can share information with other groups.

## Implemented Methods

The project compares three approaches.

### 1. Gillespie Algorithm

Stochastic modeling of a continuous-time Markov process. The algorithm is used as a reference model because it generates trajectories without time discretization.

For each mode, an ensemble of independent runs is performed, after which the following are calculated:

- average trajectory;
- ensemble variance;
- standard deviation;
- error of deterministic approximations relative to the stochastic reference.

### 2. IBMF — Individual-Based Mean Field

Individual-based mean field approximation. For each graph vertex, a separate differential equation for the awareness probability is integrated.

IBMF preserves the individual graph structure and takes into account the specific neighborhood of each vertex, but uses first-order mean-field closure.

### 3. DBA — Degree-Block Approximation

Degree-block approximation. Instead of individual vertices, classes of vertices with the same degree are considered.

DBA is significantly faster than IBMF and the Gillespie algorithm, since the system dimension is determined by the number of distinct degrees, not the number of vertices. The method is especially effective in hub-dominated graphs, where the macrodynamics of diffusion are determined by high-degree vertices.

## Synthetic Graphs

The project uses three types of bipartite graphs:

1. **Power-law / Power-law**
Power-law degree distribution in both parts.

2. **Binomial / Power-law**
Binomial user degree distribution and power-law group degree distribution.

3. **Exponential / Power-law**
Exponential distribution of user degrees and power-law distribution of group degrees.

In all cases, the upper fraction `V` corresponding to groups has a power-law distribution with the exponent `α_V < 2`. This allows us to study the hub-dominated regime characteristic of social platforms, where a small number of groups concentrate a significant portion of connections.

## Quality Metrics

The following metrics are used to compare models:

- `MSE` — root mean square error relative to the average Gillespie trajectory;
- `nMSE` — normalized MSE, taking into account the variance of the Gillespie ensemble;
- proportion of time points within the `±1σ` band;
- method execution time.

The normalized error is calculated as the ratio of the MSE to the time-averaged variance of the stochastic ensemble. If nMSE < 1, the error of the approximate model is less than the natural stochastic spread of Gillespie trajectories.

## Main Results

The results show that:

- in hub-dominated graphs, DBA can be as accurate as IBMF;
- for graphs with a power-law distribution in both shares, DBA reproduces the mean trajectory of the stochastic process well;
- with a binomial distribution of user shares, DBA is less accurate in describing group dynamics, since the specific neighborhood structure begins to play a larger role;
- with an exponential distribution of users, an intermediate regime is observed;
- IBMF is generally more robust than DBA, but requires more computation.

