---
title: Asynchronous task execution in Pydra
author: Mathias
---

**[Pydra](https://github.com/nipy/nipype)** is a task scheduling and execution workflow framework implemented in pure Python. Inspired from the _[Nipype](https://github.com/nipy/nipype)_ engine, Pydra adds a number of additional features while serving as a low level, discipline-agnostic tool capable of generating as-complex-as-you-would-like workflows. This post will discuss new directions implemented for task submission and execution.

##### Pydra-ese