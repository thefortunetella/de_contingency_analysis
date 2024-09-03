### README - Explanation for the CFB Project Using Differential Evolution (DE)

## Introduction

This README provides a comprehensive explanation of the CFB project, which uses the Differential Evolution (DE) algorithm to compute the Current Flow Betweenness (CFB) centrality for a given graph. CFB is a metric that evaluates the importance of edges in a network based on their role in maintaining overall connectivity. The Differential Evolution algorithm is applied to efficiently identify the most critical edges whose removal would have the most significant impact on the network's structure.

### Project Files

The project consists of three main files:

1. **`cfb.jl`**: Contains the core functions for calculating Current Flow Betweenness (CFB) using the Differential Evolution (DE) algorithm.
2. **`de.jl`**: Provides a command-line interface for configuring and running the DE algorithm.
3. **`script.jl`**: A script to run multiple CFB calculations on various graph datasets using different parameter configurations.

### Core Concept: Current Flow Betweenness (CFB)

Current Flow Betweenness (CFB) centrality is a measure used to determine the importance of edges in a graph based on the concept of electrical current flow. This approach differs from classic betweenness centrality, which focuses on the shortest paths; instead, CFB considers all paths and how the removal of specific edges affects the overall flow through the network.

### Algorithm Overview: Differential Evolution (DE)

The Differential Evolution (DE) algorithm is an optimization technique inspired by natural evolution. It operates by evolving a population of candidate solutions over several iterations to find the optimal edges to remove that maximize the disruption to the network's flow.

### Detailed Explanation of Files

#### 1. `cfb.jl`

This file contains the core logic for calculating the CFB using the DE algorithm.

##### Key Functions

- **`read_edgelist(s::String)`**:
  - Reads a graph from an edge list file and constructs a `SimpleGraph` object. The input file should contain pairs of integers representing the source and destination of each edge.
  - The function scans through each line, identifies nodes and edges, and adds them to the graph structure.

- **`current_flow_betweenness(g::Graph)`**:
  - Computes the CFB for all nodes in the graph using the algorithm of Brandes. 
  - This involves matrix operations on the graph's Laplacian matrix and incidence matrix to determine how current flows through each edge and how its removal would impact the flow.

- **`sample_initial_pop(m::Integer, n_pop::Integer, k::Integer)`**:
  - Generates an initial population of edge sets to be removed. 
  - The function randomly samples `k` edges from the total `m` edges of the graph to form each candidate solution.

- **`de_cost_function(deltas::Matrix{Float64})`**:
  - Calculates the cost associated with each candidate solution based on the change in CFB when the edges in the solution are removed.
  - The function returns a vector of costs, with higher values indicating greater impact on the network.

- **`de_mutation(...)`**:
  - Applies the mutation step in the DE algorithm, which generates new candidate solutions by perturbing existing ones.
  - It uses random coefficients within a specified range (`beta_min` to `beta_max`) to adjust the positions of edges in the solution space.

- **`de_crossover(...)`**:
  - Executes the crossover operation, where new solutions are created by combining parts of two different candidates.
  - The function probabilistically selects which parts of each solution to retain, based on the crossover rate.

- **`de_iter!(...)`**:
  - Executes a single iteration of the DE algorithm:
    1. Generates initial edge sets (candidate solutions).
    2. Filters out invalid solutions that disconnect the graph.
    3. Computes the betweenness centrality changes for valid solutions.
    4. Sorts and updates the population based on the calculated cost function.
  - If the current iteration is the last one (`ultima = true`), it computes and returns the normalized deltas (changes in CFB) for each candidate solution.

- **`cfb_de(g::Graph, k::Integer, pop_size::Integer, crossover_rate::Float64, beta_min::Float64, beta_max::Float64, iter_num::Integer = 10, arquivo_saida::String = "result")`**:
  - Main function to perform the DE-based CFB analysis:
    1. Calculates the reference CFB for the original graph.
    2. Initializes a population of edge sets to be removed.
    3. Iteratively evolves the population using the DE algorithm, applying mutation, crossover, and selection to refine the solutions.
    4. After the specified number of iterations (`iter_num`), computes and exports the final results, including the most critical edges and their impact on the network.

#### 2. `de.jl`

This file sets up the command-line interface for configuring and running the DE algorithm.

##### Key Functions

- **`parse_commandline()`**:
  - Parses command-line arguments to configure the DE algorithm. The arguments include:
    - `--iter_num`: Number of iterations to run the DE algorithm.
    - `--beta_min` and `--beta_max`: Range for the mutation coefficients in the DE algorithm.
    - `grafo`: Input file containing the graph as an edge list.
    - `k`: Number of edges to remove simultaneously.
    - `pop`: Population size for the DE algorithm.
    - `crossover`: Crossover rate for the DE algorithm.

#### Execution Flow

- Reads and validates the command-line arguments.
- Loads the graph from the specified input file.
- Calls the `cfb_de` function from `cfb.jl` with the provided parameters to execute the DE algorithm.

#### 3. `script.jl`

This script automates the execution of the DE-based CFB analysis on different graph datasets.

##### Purpose

- Automates the process of running the DE algorithm on multiple datasets (`ieee14`, `ieee30`, `ieee300`, etc.) with varying parameters.

##### Execution Flow

1. Selects an input graph file (e.g., `ieee14.txt`).
2. Reads the graph using `read_edgelist` from `cfb.jl`.
3. Runs the `cfb_de` function with different parameter values for `k` (the number of edges to be removed) and other DE settings, measuring the execution time for each run.

### Input File Format

The input graph files (e.g., `ieee14.txt`) are expected to be in an edge list format, where each line represents an edge between two nodes, like this:

```
1 2
1 5
2 3
...
```

Each line contains two integers representing the source and destination of an edge in the graph.

## How to Run the Project

### Running from the Command Line

To run the DE algorithm with specific parameters, use:

```bash
julia de.jl --iter_num 10 --beta_min 0.2 --beta_max 0.8 grafo ieee14.txt k 2 pop 100 crossover 0.7
```

### Running the Script

To execute the predefined calculations on multiple datasets:

```bash
julia script.jl
```

### Results

The outputs will be saved in CSV files named according to the configuration used (`valid_tuples.csv`, `disconnects.csv`, `local_deltas.csv`, `vertex_global_deltas.csv`, `edge_global_deltas.csv`), containing:
- Valid edge combinations that do not disconnect the graph.
- Combinations that do disconnect the graph.
- Local and global delta impacts for each combination, indicating how critical each edge is to the network's connectivity.

## Conclusion

The CFB project leverages the Differential Evolution (DE) algorithm to efficiently identify the most critical edges in a network by calculating the Current Flow Betweenness (CFB). This approach helps in understanding the resilience of the network and can be applied in various fields like power grid management, communication networks, and social network analysis.


 


  
  
