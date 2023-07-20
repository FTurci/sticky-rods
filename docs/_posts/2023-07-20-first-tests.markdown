---
layout: post
title:  "First runs"
date:   2023-07-20 15:01:30 +0100
categories: simulation
typora-root-url: ../../docs
---
I have managed to upgrade `lammps` and run some initial tests with Claudia's code.

In particular:

- I tested only $\phi = 0.0087$ 
- I simply restarted from the corresponding `rods_center_time_20000000.restart`
- I ran the calculation `sticky` calculation for a small number of steps

## Scalability

The code does not seem to scale well. I benchmark the execution speed during the sticky interaction time evolution. These are single runs.

| nproc | execution speed (timesteps/s) |
| ----- | ----------------------------- |
| 8     | 1148.273                      |
| 16    | 1391.423                      |
| 32    | 1231.919                      |



## Snapshots

The system seems to be behaving correctly, with slow dynamics of the rods



![image-20230720155748760](/img/earlysnap.png)











