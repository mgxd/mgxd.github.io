---
title: Asynchronous task execution in Pydra
author: Mathias
---

**[Pydra](https://github.com/nipy/nipype)** is a task scheduling and execution workflow framework implemented in pure Python. Stemming from the _[Nipype](https://github.com/nipy/nipype)_ engine, Pydra adds a number of additional features while serving as a scalable, discipline-agnostic tool capable of generating "as-complex-as-you-would-like" [DAGs](https://en.wikipedia.org/wiki/Directed_acyclic_graph). This post will discuss new directions implemented for task submission and execution.

##### Pydra-ese
In Pydra, an execution graph consists of compute objects, called _Tasks_, and connections between them. Each Task is a unique vertex, which can be a singular operation (shell command, standalone function, etc.) or another execution graph of these Tasks, also known as a _Workflow_.

![alt text](../assets/pydra-sample-graph.png "Pydra graph (simple)")

Tasks can also have states - this allows mapping, combining, and reducing of inputs and outputs.

