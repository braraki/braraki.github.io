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

## Linear programming formulation of the shortest path problem

What we have on our hands seems to be a variant of a shortest path problem. There are tons of algorithms for solving shortest path problems, even with weighted edges, including Djikstra and Bellman-Ford. However, we need to use some sort of linear program formulation because our problem statement has constraints, such as a blueberry capacity, that normal algorithms don't account for. That's the beauty of optimization programs versus set algorithms -- once you have the formulation down, it's easy to add new constraints or costs and still get an optimal solution.

Wikipedia has [the linear programming formulation of the shortest path problem](https://en.wikipedia.org/wiki/Shortest_path_problem#Linear_programming_formulation):

$$ \min \sum_{ij \in A} w_{ij}x_{ij} \\ 
\text{s.t.} x \geq 0 \forall i, j $$