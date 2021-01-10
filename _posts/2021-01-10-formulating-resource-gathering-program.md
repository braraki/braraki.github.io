---
layout: post
title:  "Formulating and Implementing a Resource Gathering Graph Optimization Problem"
date: 2021-01-10 12:00:00
categories: programming, optimization
---

# The Problem

Let's say you have a robot that needs to move around an area to collect a resource. For example, maybe you have a berry-picking robot roaming a field collecting blueberries. The catch is that the robot can only hold so many blueberries, and it also has kinematic constraints so that it can't turn too quickly. Let's also say that it has a base station where it starts and where it can return to dump the blueberries it has collected. How can you find a path through the blueberry field such that the robot will fill its bin and return to its base station?

There are many ways to approach this problem, and I decided to try solving it using a mixed integer linear program (MILP). It turns out that this approach isn't very useful - MILPs take too long to solve to be used on a real robot. In the end, I believe that using fast, non-optimal heuristics is a better approach than using an MILP in this case. However, I think that the formulation and implementation of the MILP are interesting.

# The Formulation

Formulating optimization problems is always a fun exercise in converting an idea in your mind into a mathematical form that an optimizer can solve. Let us first take the fuzzy problem statement above and attempt to state it more precisely:

We have a directed graph $$G = (V, E)$$, where $$V$$ are the nodes and $$E$$ are edges. The robot has a set starting node and ending node. The robot has kinematic constraints -- for example, a turning radius of 2m. Every edge is associated with a distance cost and a certain number of blueberries. The robot should minimize the distance cost while also collecting as many blueberries as possible. However, it cannot collect more blueberries than its collection bin can hold.

How can we convert this slightly less fuzzy problem statement into an MILP?

## Encoding kinematic constrains in the graph structure

I'm pretty sure that defining the graph to be a simple grid and then adding kinematic constraints as optimization constraints would make the optimization problem really hard. I therefore built the kinematic constraints directly into the graph. By this I mean that I assume that the robot has a state $$(x, y, \theta)$$, and that $$theta$$ is discretized into $$n_\theta$$ steps. Therefore the graph is an $$nX \times nY \times n\theta$$ grid with edges between two points $$((x_1, y_1, \theta_1)$$ and $$(x_{2}, y_{2}, \theta_2))$$ only when the kinematic constraints are met. In this case, that means whenever $$\theta_2 = \theta_1 + \{-1, 0, 1\}$$ and whenever $$(x_2, y_2) = (x_1 + \{-1, 0, 1\}, y_1 + \{-1, 0, 1\})$$. It's not quite this simple unfortunately -- in addition to these constraints, there are further constraints on which direction the robot can move in based on the current value of $$\theta$$.

Even if my explanation doesn't make sense, there's only one important take away here -- the kinematic constraints are built into the graph so that the optimization problem doesn't have to take them into account.

## Linear programming formulation of the shortest path problem

What we have on our hands seems to be a variant of a shortest path problem. There are tons of algorithms for solving shortest path problems, even with weighted edges, including Djikstra and Bellman-Ford. However, we need to use some sort of linear program formulation because our problem statement has constraints, such as a blueberry capacity, that normal algorithms don't account for. That's the beauty of optimization programs versus set algorithms -- once you have the formulation down, it's easy to add new constraints or costs and still get an optimal solution.

Here is the linear programming formulation of the shortest path problem from [Wikipedia](https://en.wikipedia.org/wiki/Shortest_path_problem#Linear_programming_formulation), where $$w_{ij}$$ are the weights of edges between nodes $$i$$ and $$j$$, and $$x_{ij}$$ are nonnegative continuous variables indicating whether or not edge $$ij$$ is part of the path. $$s$$ is the number of the source node, and $$t$$ is the number of the target node:

$$
\begin{aligned}
\min_{x_{ij}} \quad & \sum_{ij \in A} w_{ij}x_{ij} \\ 
\text{s.t.} \quad & x_{ij} \geq 0 \forall i, j \\
& \sum_j x_{ij} - \sum_j x_{ji} = \begin{cases}
      1,  & \text{if           $i=s$}\\
      -1, & \text{if           $i=t$}\\
      0,  & \text{otherwise}
    \end{cases}
\end{aligned}
$$

The beauty of this formulation is that even though this is a linear program, which is extremely efficient to solve, the $$x_{ij}$$ are guaranteed to converge to be either 0 or 1. If we had to specify the $$x_{ij}$$ to be binary/integer-valued variables, then this problem would become an integer program, which would take much more time to solve.

## Formulation of the resource gathering problem

Unfortunately, we need to add some constraints to the shortest path formulation that will require us to treat the $$x_{ij}$$ variables as binary variables. Let $$b_{ij}$$ be the number of blueberries the robot will collect by traversing edge $$ij$$. Let $$B$$ be the blueberry capacity of the robot. My first naive formulation of this problem was

$$
\begin{aligned}
\min_{x_{ij}} \quad & \sum_{ij \in A} (w_{ij} - b_{ij}) x_{ij} \\ 
\text{s.t.} \quad & x_{ij} \in \{0, 1\} \\
& \sum_j x_{ij} - \sum_j x_{ji} = \begin{cases}
      1,  & \text{if           $i=s$}\\
      -1, & \text{if           $i=t$}\\
      0,  & \text{otherwise}
    \end{cases} \\
& \sum_{ij} b_{ij} x_{ij} \leq B
\end{aligned}
$$

In this formulation, the objective makes the robot try to minimize cost but maximize blueberries while enforcing a blueberry capacity. However, this formulation allows the formation of cycles, or "subtours". In other other words, the optimizer "cheats" by finding cycles in the graph that are not part of the main path. The cycles obey the graph constraints that every node must have an equal number of paths entering and leaving it and maximizes the number of blueberries collected, but they are obviously not what we want.

## Eliminating cycles

How can we eliminate the cycles? Mosek has a [very helpful page](https://docs.mosek.com/6.0/capi/node015.html) on the Travelling Salesman Problem, in which they discuss constraints for preventing "subtours" or cycles. The MTZ formulation of the TSP is apparently a "very weak formulation" for preventing cycles, but I found that it worked for me. A benefit of MTZ is that it only adds a linear number of new constraints, as opposed to "stronger" formulations that add more constraints exponentially in the number of nodes, which is really bad.

Given that there are $$n$$ nodes, the MTZ constraints define $$n$$ new variables $$u_i$$ such that

$$
\begin{aligned}
u_s &= 1 &&\\
2 \leq &u_i \leq n &&\forall i \neq s \\
u_i - u_j + 1 &\leq (n - 1)(1 - x_{ij})  &&\forall i \neq s, j \neq s
\end{aligned}
$$

The website I linked goes into detail about why these constraints are the way they are, and also about alternative anti-cycle formulations.

Adding these constraints resulted in my program finding paths that at least look good. 

# Implementation

I implemented everything in Python. I used the graph package ```networkx``` and the optimization package ```pulp```. ```pulp``` does not actually implement a solver -- it is a convenient Python interface for defining programs, and it then converts the program that you define into the format of a specific optimizer that you want to use.

```
import numpy as np
import networkx as nx
import pulp

from collections import OrderedDict
from scipy import interpolate
from scipy.stats import multivariate_normal
```

## Defining the graph
Initialize the networkx directed graph:
```
G = nx.DiGraph()
```
The directions in the graph, in clockwise order:
```
graph_steps = OrderedDict({'N': (0, 1), 'NE': (1, 1),
               'E': (1, 0), 'SE': (1, -1),
               'S': (0, -1), 'SW': (-1, -1),
               'W': (-1, 0),'NW': (-1, 1)
                })
```
The value of the action, -1, 1, or 0, indicates how to shift the index of the ```graph_step```. For example, if the graph_step is NE (index 1) and the action is 'right' (1), then 1 is added to the index of NE (1), making 2, so that the new graph step is 'E' (2).
```
actions = {'left': -1, 'right': 1, 'forward': 0}
```
Defining the state space:
```
nX = 15
nY = 15
nDirections = 4 # N, S, E, W
```
I also define the number of steps it takes to turn 90 degrees. In this case, if 4,  that means it takes 4 steps to turn 90 degrees.
```
nStepsToTurn = 4
nTheta = nDirections * nStepsToTurn
```
Make nodes:
```
for x in range(nX):
    for y in range(nY):
        for th in range(nTheta):
            coord = (x, y, th)
            G.add_node(coord)
```
Make a "blueberry heat map" defining where blueberries are in the map:
```
x, y = np.mgrid[-1:1:.01, -1:1:.01]
pos = np.dstack((x, y))
berry_mean = [3, 3]
berry_std = [[0.5, 0], [0, 0.5]]
berry_max = 200
rv = multivariate_normal(berry_mean, berry_std)
```
Make edges:
```
for x in range(nX):
    for y in range(nY):
        for th in range(nTheta):
            coord = (x, y, th)
            graph_step_index = int(len(graph_steps)*th/nTheta)
            for action_direction in actions.values():
                nextTheta = (th + action_direction) % nTheta
                action_index = (graph_step_index) % len(graph_steps)
                action = list(graph_steps.values())[action_index]
                nextX = x + action[0]
                nextY = y + action[1]
                if nextX < 0 or nextY < 0 or nextX == nX or nextY == nY:
                    continue
                # penalize non-forward actions
                if action_direction == 0:
                    cost = 1
                else:
                    cost = 1.5
                # also penalize based off of the delta in position
                cost *= np.linalg.norm(np.array(action))
                berries = int(berry_max * rv.pdf([nextX, nextY]))

                next_coord = (nextX, nextY, nextTheta)
                G.add_edge(coord, next_coord, cost=cost, berries=berries)
```

Once we have this, we can visualize what the paths/kinematic constraints look like just by running ```nx.shortest_path```. In addition, I smooth the path using a ```scipy``` spline function. (I'm not going to include my interpolatePath and plotPath functions here).
```
path = nx.shortest_path(G, (6, 0, 0), (6, 0, nTheta/2), weight='cost')
total_cost = nx.path_weight(G, path, 'cost')
total_berries = nx.path_weight(G, path, 'berries')
plotPath(path, total_cost, total_berries)
```

![Shortest Path]({{ "/assets/shortest_path_berry_collector.png" | absolute_url }})

The starting position is (6, 0, 0) and the ending position is (6, 0, 7) -- basically, we're telling the robot to turn around. The image shows how the robot has to make a large loop in order to turn around, meaning that the kinematic constraints are working.

We're now ready to implement the MILP.

## Defining the MILP
First let us define the soure and target nodes (they can be arbitrary nodes in the graph).
```
source = (6, 0, 0)
target = (6, 0, nTheta/2)
```
Then we will initialize the Pulp problem and make useful ```cost``` and ```berries``` dictionaries for querying the cost and berry count of each edge:
```
prob = pulp.LpProblem("Shortest Path Problem", pulp.LpMinimize)
cost = nx.get_edge_attributes(graph, 'cost')
berries = nx.get_edge_attributes(graph, 'berries')
```
Next, we define binary variables for each edge. These will encode whether an edge is part of the path (value of 1) or not (value of 0):
```
var_dict = {}
for (i, j) in graph.edges:
    i_name = '({},{},{})'.format(*i)
    j_name = '({},{},{})'.format(*j)
    name = 'x_(%s_%s)' % (i_name,j_name)
    x = pulp.LpVariable(name, cat=pulp.LpBinary)
    var_dict[i, j] = x
```
Next we define the MTZ variables:
```
node_var_dict = {}
for node in graph.nodes:
    name = 'u_%s_%s_%s' % (node[0], node[1], node[2])
    u = pulp.LpVariable(name)
    node_var_dict[node] = u
```
Next we define the objective function:
```
prob += pulp.lpSum([(cost[i, j] - berries[i, j]) *
	var_dict[i, j] for i, j in graph.edges])
```
And now it's time for the constraints. First let's add the graph constraints, which say that the number of edges into a node must equal the number of edges going out, except in the case of the source and target nodes:
```
for node in graph.nodes:
    if node == source:
        prob += pulp.lpSum([var_dict[i, k] for i, k in graph.in_edges(node)]) - \
                pulp.lpSum([var_dict[k, j] for k, j in graph.out_edges(node)]) == -1
    elif node == target:
        prob += pulp.lpSum([var_dict[i, k] for i, k in graph.in_edges(node)]) - \
                pulp.lpSum([var_dict[k, j] for k, j in graph.out_edges(node)]) == 1
    else:
        prob += pulp.lpSum([var_dict[i, k] for i, k in graph.in_edges(node)]) - \
                pulp.lpSum([var_dict[k, j] for k, j in graph.out_edges(node)]) == 0
```
And then the berry capacity constraint (300 is an arbitrary capacity I chose):
```
prob += pulp.lpSum([berries[i, j] * var_dict[i, j] for i, j in graph.edges]) <= 300
```
And finally, the MTZ constraints:
```
num_nodes = len(node_var_dict)
prob += node_var_dict[source] == 1
for ni in graph.nodes:
    if ni != source:
        prob += node_var_dict[ni] >= 2
        prob += node_var_dict[ni] <= num_nodes
for ni, nj in graph.edges:
    if ni != source and nj != source:
        prob += node_var_dict[ni] - node_var_dict[nj] + 1 <= \\
        (num_nodes - 1)*(1 - var_dict[ni, nj])
```
We can solve the MILP and see the results:
```
prob.solve()
print(pulp.LpStatus[prob.status])
cost = pulp.value(prob.objective)
berries = pulp.value(pulp.lpSum([berries[i, j] *
	var_dict[i, j] for i, j in graph.edges]))
```
and get the path:
```
current_node = source
path = [current_node]
while current_node != target:
    next_node_exists = False
    for _, next_node in graph.out_edges([current_node]):
        if var_dict[(current_node, next_node)].value() == 1.0:
            next_node_exists = True
            path.append(next_node)
            current_node = next_node
            break
    if not next_node_exists:
        break
```

# Results

When I run the code and visualize the output, this is what I get:
![MILP Path]({{ "/assets/shortest_path_milp_berry_collector.png" | absolute_url }})

I'm very happy with this solution -- it looks very natural and "optimal". It takes the robot through two loops before returning to home base, and perfectly obeys the constraint of no more than 300 blueberries while also maximizing the number of blueberries collected.

Unfortunately, there is one huge catch here -- the optimization takes 414.44 seconds, or nearly 7 minutes, to run. That is unacceptable, especially on a graph that is only of size 15x15x16. Granted, I am using the COIN CBC optimizer, which is free. Gurobi can probably solve this problem much faster but still probably not in under a second. Also, no matter how good the optimizer is, the problem complexity will still scale exponentially with graph size and I would like to do this on much larger graphs.

There is one additional problem with my formulation -- berries are associated with edges rather than with nodes, so if the robot passes through a node twice but in different directions, it will double count the berries. I don't think there's an easy way to correct this in the formulation without adding even more variables and making the problem even more complex.

# Conclusion

In conclusion, although my formulation seems to have an output that is pretty good, it takes way too long to calculate to be useful. I think that it would be better to stick with non-optimal heuristics rather than using an MILP solver.