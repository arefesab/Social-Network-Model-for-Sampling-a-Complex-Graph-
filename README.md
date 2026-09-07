# **Graph Sampling & Social Network Analysis**
### **Sampling Complex Social Networks While Preserving Their Behavior**

**Graph Sampling · Network Science · Centrality · Diffusion Models · Social Networks**

## **Overview**
Large-scale social networks can contain millions of nodes and edges, making complete graph analysis computationally expensive.

**Graph Sampling** explores how a complex social network can be reduced to a significantly smaller graph while preserving as much of the original network's structural and behavioral characteristics as possible.

The project investigates two sampling strategies:

- **HYB-based random edge sampling**
- **Shortest-path sampling using Dijkstra's algorithm**

The resulting graphs are evaluated against the original network using **degree centrality and the Independent Cascade diffusion model** to determine how closely the sampled networks resemble the original.

## **Research Question**
**How much can a social network be reduced while preserving its important structural and diffusion behavior?**

The project compares different sampling strategies and examines whether a smaller graph can reproduce characteristics of the original network.
### **Core objective**
```
Original Social Network
          │
          ▼
   ┌───────────────┐
   │ Graph Sampling│
   └───────┬───────┘
           │
     ┌─────┴─────┐
     ▼           ▼
  HYB Edge    Shortest
  Sampling     Path
     │           │
     └─────┬─────┘
           ▼
     Sampled Graphs
           │
           ▼
 ┌───────────────────────┐
 │ Structural Evaluation │
 │ + Diffusion Behavior  │
 └───────────┬───────────┘
             ▼
       D-Statistic
             │
             ▼
    Similarity to Original
```
# **Dataset**
The experiments use a **LastFM social network dataset**, representing relationships between users in a social network.

The graph is treated as a complex network in which:

- **Nodes** represent users **(7,624 nodes**)
- **Edges** represent relationships between users **(27,806 edges)**
- Network structure is analyzed before and after sampling
- Sampled graphs are compared with the original network

The dataset provides a realistic environment for investigating whether network behavior can be preserved after substantial graph reduction.

<img width="491" height="330" alt="Aspose Words 86ff8177-4167-4fd5-944c-810b9b697e8b 001" src="https://github.com/user-attachments/assets/12045072-670b-4a5e-b8fd-98f221ceb078" />

*The original LastFM network graph* 

# **Sampling Strategies**
## **1. HYB Random Edge Sampling**
The first approach uses the **HYB sampling algorithm** to select edges from the original network.

The objective is to reduce the graph while maintaining important structural characteristics.

```
Original Graph
      │
      ▼
 HYB Sampling
      │
      ▼
Reduced Graph
      │
      ▼
Behavioral Evaluation
```
The sampled graph is subsequently analyzed using network metrics and diffusion simulations.

<img width="407" height="335" alt="Aspose Words 86ff8177-4167-4fd5-944c-810b9b697e8b 002" src="https://github.com/user-attachments/assets/96e1563c-8422-41ec-b858-27a8e26b21c6" />


*Sample graph produced by the HYB method* 

## **2. Shortest-Path Sampling**
The second approach builds a sampled graph using shortest paths between randomly selected nodes.

**Dijkstra's algorithm** is used to calculate shortest paths within the original network.

```
Random Node A
      │
      │
      │  Shortest Path
      ▼
   Network
      │
      ▼
Random Node B
      │
      ▼
Selected Paths
      │
      ▼
Sampled Graph
```

This approach attempts to preserve important connectivity patterns by retaining paths that connect randomly selected nodes. In practice, the project used NetworkX's  all\_shortest\_paths (rather than a raw Dijkstra implementation) so that, when multiple shortest paths exist between two nodes, all of them are kept — not just one. As with the HYB method, sampling continued until the sample graph reached **20% of the original graph's edges**.

<img width="453" height="347" alt="Aspose Words 86ff8177-4167-4fd5-944c-810b9b697e8b 003" src="https://github.com/user-attachments/assets/7cf47ddb-1210-49b1-bc3e-484d790127e5" />

*Sample graph produced by the shortest-path*

# **Sampling Approaches**
The project investigates two perspectives for reducing the original graph:

|Approach|Concept|
| :- | :- |
|**Scale-Down**|Reduce the network while attempting to preserve its structural properties|
|**Back-in-Time**|Reconstruct a smaller representation based on previously observed network structure|

Both approaches are evaluated to determine how effectively they preserve the behavior of the original network.

# **Network Analysis**
Sampling quality is not evaluated simply by comparing the number of nodes or edges.

Instead, the sampled graphs are evaluated through several dimensions of network behavior.
## **Centrality Analysis**
Centrality measures help determine whether important nodes in the original network remain important after sampling.

The analysis considers network centrality characteristics such as:

- Degree centrality
- Betweenness-related structural behavior
- Node importance
- Distribution of centrality values

The objective is to determine whether the sampled network preserves the original network's structural hierarchy.

# **Diffusion Models**
A major part of the evaluation focuses on **information diffusion**.
## **Independent Cascade**
The **Independent Cascade (IC)** model represents information spreading through probabilistic activation.

```
Seed
 │
 ├──► Neighbor
 │      │
 │      └──► Neighbor
 │
 └──► Neighbor
        │
        └──► ...

```
The sampled graph is compared with the original graph to determine whether information spreads in a similar way. In the implementation, each node gets a Try flag (has it already attempted to activate its neighbors?) and an Infected flag (is it active?); activation probability was set to **0.1** based on prior evaluation, and the final result is the average number of activated nodes over repeated simulation runs.

# **Similarity Evaluation**
To quantify how closely the sampled networks resemble the original network, the project uses the **D-statistic**.

The D-statistic provides a measure of the difference between distributions.

Conceptually:

```
Original Distribution
        │
        │
        │  D-statistic
        ▼
Sampled Distribution

```

A **smaller difference** indicates that the sampled graph more closely resembles the original graph for the evaluated property.

This makes it possible to compare the effectiveness of different sampling strategies quantitatively rather than relying only on visual inspection. In this project, the D-statistic was **not** the metric used. Instead, the two methods were compared by taking the top 5, 10, and 15 highest-degree nodes (degree centrality) from each sampled graph, treating them as seed/infected nodes, and measuring the average number of nodes activated in the *original* graph via the Independent Cascade model. Under that comparison, the shortest-path method showed **greater influence/diffusion** than HYB.


# **Experimental Pipeline**
The complete workflow can be summarized as:
```
                    ┌──────────────────┐
                    │  LastFM Dataset  │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Original Graph   │
                    └────────┬─────────┘
                             │
                 ┌───────────┴───────────┐
                 ▼                       ▼
        ┌────────────────┐      ┌─────────────────┐
        │ HYB Sampling   │      │ Shortest Path   │
        │                │      │ + Dijkstra      │
        └───────┬────────┘      └────────┬────────┘
                │                        │
                ▼                        ▼
        ┌────────────────┐      ┌─────────────────┐
        │ Sampled Graph  │      │ Sampled Graph   │
        └───────┬────────┘      └────────┬────────┘
                │                        │
                └────────────┬───────────┘
                             ▼
                 ┌──────────────────────┐
                 │ Network Evaluation   │
                 ├──────────────────────┤
                 │ • Centrality         │
                 │ • Independent       │
                 │   Cascade            │
                 │ • Linear Threshold   │
                 └───────────┬──────────┘
                             │
                             ▼
                 ┌──────────────────────┐
                 │ D-Statistic          │
                 │ Similarity Analysis  │
                 └──────────────────────┘

```
# **Technology Stack**

|Technology|Purpose|
| :- | :- |
|**Python**|Data processing and experimentation|
|**NetworkX**|Graph construction and network analysis|
|**Pandas**|Data manipulation and analysis|
|**Matplotlib**|Visualization|
|**Little Ball of Fur**|Graph sampling and network analysis|
|**Google Colab**|Experimental environment|
|**Dijkstra**|Shortest-path computation|

# **Project Structure**
```
graph-sampling/
│
├── final/
│   └── New folder/
│       └── ...
│
├── README.md
│
└── ...

```

The main experiments are implemented as Python/Google Colab-based notebooks.

# **Getting Started**
## **Requirements**
Python 3.x with the following libraries:

pip install networkx pandas matplotlib little-ball-of-fur

Depending on the notebook environment, additional dependencies may be required.

## **Running the Project**
The experiments are designed to run in **Google Colab**.
### **1. Clone the repository**
git clone https://github.com/arefesab/graph-sampling.git\
cd graph-sampling
### **2. Open the notebook**
Upload/open the relevant notebook in Google Colab.
### **3. Install dependencies**
Run the required package installation cells.
### **4. Load the LastFM dataset**
Provide the dataset used by the experiment.
### **5. Run the sampling experiments**
Execute the sampling algorithms and generate the corresponding sampled graphs.
### **6. Evaluate the results**
- Compare the original and sampled graphs using: Degree centrality (top 5/10/15 highest-degree nodes)
- Independent Cascade simulation (activation probability ≈ 0.1)


# **Experimental Comparison**
The project is structured around comparing the behavior of the sampled graphs against the original graph.

|Evaluation|Original Graph|HYB Sampling|Shortest-Path Sampling|
| :- | :- | :- | :- |
|Graph structure|✓|✓|✓|
|Centrality|✓|✓|✓|
|Independent Cascade|✓|✓|✓|
|Linear Threshold|theory only|-|-|
|D-statistic|theory only|-|-|

This allows the sampling strategies to be evaluated from multiple perspectives rather than using graph size alone.

# **Key Concepts**
### **Graph Sampling**
Reducing a large network to a smaller representative network while attempting to preserve important properties.
### **Centrality**
Measures the importance or structural position of nodes within a network.
### **Shortest Path**
The minimum-cost route between two nodes. In this project, shortest paths are calculated using Dijkstra's algorithm.
### **Diffusion**
The process through which information, influence, or activity propagates through a network.
### **Independent Cascade**
A probabilistic diffusion model where activated nodes attempt to activate neighboring nodes.
### **Linear Threshold**
A diffusion model where nodes become active after accumulated influence exceeds a threshold.
### **D-statistic**
A distribution-based metric used to quantify differences between the original and sampled networks.

# **Why Graph Sampling Matters**
Graph sampling is particularly useful when the original network is too large or computationally expensive to analyze directly.

A successful sampling strategy can provide:

```
Large Network
      │
      ▼
┌──────────────┐
│ Graph        │
│ Sampling     │
└──────┬───────┘
       │
       ▼
Smaller Network
       │
       ├──► Faster Analysis
       ├──► Lower Computation
       ├──► Easier Visualization
       └──► Similar Network Behavior

```

The central challenge is therefore not simply to create a smaller graph, but to create a **smaller graph that behaves like the original**.

# **Results & Analysis**
The experiments compare two sampled representations of the LastFM network and investigate how well each approach preserves the original network.

The analysis focuses on:

- Structural similarity
- Centrality distributions
- Independent Cascade diffusion behavior

**Result:** Comparing the two sampled graphs by taking their top 5/10/15 highest-degree nodes and running Independent Cascade on the *original* graph, the **shortest-path sampling method outperformed HYB**, producing greater influence/diffusion overall. As the number of seed (infected) nodes increased, the gap between the two methods narrowed — at 10 seed nodes their results converged closely.

<img width="462" height="224" alt="Aspose Words 86ff8177-4167-4fd5-944c-810b9b697e8b 004" src="https://github.com/user-attachments/assets/1f045edd-8a5d-4e12-8da8-b0a8ee5dc424" />


A useful final results section could contain:
```
                 Sampling Quality
                       │
        ┌──────────────┴──────────────┐
        ▼                             ▼
   HYB Sampling                 Shortest-Path
        │                         Sampling
        ▼                             ▼
   Centrality                     Centrality
        │                             │
 Diffusion Models              Diffusion Models
        │                             │
        └──────────────┬──────────────┘
                       ▼
                 D-statistic
                       │
                       ▼
              Best Representation

```

# **Project Goals**
The project investigates whether graph sampling can achieve the following:

- Reduce graph size significantly
- Preserve important network structures
- Maintain similar node centrality characteristics
- Preserve diffusion behavior
- Compare different sampling methodologies
- Quantitatively evaluate sampling quality

The ultimate goal is to identify a sampling strategy that provides the best balance between **graph reduction and behavioral similarity**.

# **Research Areas**
This project sits at the intersection of several areas of computer science:

```
                   Graph Theory
                         │
                         ▼
                 Network Science
                         │
             ┌───────────┴───────────┐
             ▼                       ▼
       Graph Sampling          Social Networks
             │                       │
             └───────────┬───────────┘
                         ▼
                  Diffusion Models
                         │
                         ▼
                   Data Analysis

```

# **References & Related Concepts**
The implementation builds on established concepts in:

- Social network analysis
- Graph sampling
- Shortest-path algorithms
- Network centrality
- Information diffusion
- Statistical distribution comparison


