# de_contingency_analysis
### README - Explanation for the CFB (Current Flow Betweenness) Project

## Introduction

This README provides an in-depth explanation of the CFB project, which includes a set of Julia scripts to compute the Current Flow Betweenness (CFB) centrality for a given graph. The CFB is a metric that evaluates the importance of edges in a network by considering how the removal of certain edges affects the overall connectivity of the graph.

### Project Files

The project consists of three main files:

1. **`cfb.jl`**: Contains the core functions for calculating Current Flow Betweenness (CFB) using different algorithms: Exhaustive Search, Differential Evolution (DE), and a Greedy Algorithm.
2. **`de.jl`**: Provides a command-line interface for configuring and executing the Differential Evolution (DE) algorithm.
3. **`script.jl`**: A script to run multiple CFB calculations on various graph datasets using different parameter configurations.

### Core Concepts

#### Current Flow Betweenness (CFB)

CFB is a metric that evaluates the significance of edges in maintaining the overall connectivity of a graph. It is based on the concept of electrical current flow in a circuit, where each edge represents a resistor. The CFB value is computed for each edge, indicating how the removal of that edge would disrupt the flow across the network.

#### Algorithms Overview

The project uses three main algorithms to compute CFB:

1. **Exhaustive Search (`cfb_exaustivo`)**: 
   This method systematically removes combinations of `k` edges from the graph and calculates the CFB for each scenario. It checks the impact on network connectivity and identifies the most critical edges.
   
2. **Differential Evolution (DE) (`cfb_de`)**:
   An optimization algorithm inspired by natural evolution that aims to find the most critical edges by evolving a population of candidate solutions. It uses operations like mutation, crossover, and selection to explore different combinations of edge removals efficiently.
   
3. **Greedy Algorithm (`cfb_guloso`)**:
   A heuristic approach that iteratively removes the edge with the highest CFB impact, recalculating after each removal until `k` edges are removed. It is faster than exhaustive search but may not always find the optimal solution.

## File Descriptions

### 1. `cfb.jl`

#### Key Functions

- **`read_edgelist(s::String)`**: 
  Reads a graph from an edge list file and constructs a `SimpleGraph` object. The file should contain pairs of integers representing the source and destination of each edge.

- **`current_flow_betweenness(g::Graph)`**:
  Computes the CFB for all nodes in the graph using the algorithm of Brandes. It involves matrix operations on the graph's Laplacian matrix and the incidence matrix to determine how current would flow through each edge.

- **`cfb_exaustivo(g::Graph, k::Integer, arquivo_saida::String = "result")`**:
  Performs an exhaustive search to calculate CFB by removing all combinations of `k` edges. The function:
  1. Calculates a reference CFB for the original graph.
  2. Generates combinations of `k` edges to be removed.
  3. Filters out combinations that disconnect the graph.
  4. Computes CFB for valid combinations and calculates the impact of each removal.

- **`cfb_de(g::Graph, k::Integer, pop_size::Integer, crossover_rate::Float64, beta_min::Float64, beta_max::Float64, iter_num::Integer = 10, arquivo_saida::String = "result")`**:
  Runs the Differential Evolution algorithm to find critical edges:
  1. Calculates reference CFB.
  2. Initializes a population of edge combinations.
  3. Evolves the population through several iterations using mutation, crossover, and selection.
  4. Exports the final results.

- **`cfb_guloso(g::Graph, k::Integer, arquivo_saida::String = "result")`**:
  Implements the greedy algorithm to remove `k` edges:
  1. Iteratively removes the edge with the highest CFB impact.
  2. Exports the results after `k` removals.

### 2. `de.jl`

#### Key Functions

- **`parse_commandline()`**:
  Parses command-line arguments to configure and run the Differential Evolution algorithm. The arguments include:
  - `--iter_num`: Number of iterations.
  - `--beta_min` and `--beta_max`: Range for mutation parameters.
  - `grafo`: Input file containing the graph in edge list format.
  - `k`: Number of edges to remove simultaneously.
  - `pop`: Population size for the DE algorithm.
  - `crossover`: Crossover rate for the DE algorithm.

#### Execution Flow

- Reads command-line arguments and echoes them.
- Loads the graph from the specified file.
- Runs the Differential Evolution algorithm with the given parameters.

### 3. `script.jl`

#### Purpose

- Automates the execution of CFB calculations on different graph datasets (`ieee14` to `ieee300`).
- Executes multiple calls to the `cfb_de` function with varying parameters to analyze different graph sizes and configurations.

#### Execution Flow

1. Selects the input file for the graph (e.g., `ieee14.txt`).
2. Reads the graph using `read_edgelist`.
3. Runs the `cfb_de` function with different values of `k` (number of edges to be removed) and other parameters, timing each run.

### Input File Format

The input graph file (`ieee14.txt`, `ieee300.txt`, etc.) is expected to be in the form of an edge list, where each line represents an edge between two nodes in the format:

```
1 2
1 5
2 3
...
```

Each line contains two integers representing the source and destination of an edge.

## How to Run the Project

### Running from Command Line

To run the Differential Evolution algorithm with specific parameters:

```bash
julia de.jl --iter_num 10 --beta_min 0.2 --beta_max 0.8 grafo ieee14.txt k 2 pop 100 crossover 0.7
```

### Running the Script

To run the predefined calculations on multiple datasets:

```bash
julia script.jl
```

### Results

The outputs are saved in CSV files for different configurations, including the valid edge combinations (`valid_tuples.csv`), disconnected combinations (`disconnects.csv`), local and global delta impacts, and other metrics.

## Conclusion

The CFB project provides a robust set of tools for analyzing network connectivity using Current Flow Betweenness. By leveraging exhaustive search, differential evolution, and greedy algorithms, the project offers multiple approaches to identifying critical edges in a network. This analysis can be applied to various fields, including power grids, communication networks, and social networks, to understand vulnerabilities and optimize resilience.
