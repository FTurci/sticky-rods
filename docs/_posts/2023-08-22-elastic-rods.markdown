---
layout: post
title:  "Elastic rods"
date:   2023-08-22 01:01:30 +0100
categories: simulation
typora-root-url: ../../docs/
---
I have attempted to change the simulation design to gain a significant speedup.

For this, I have changed the model a bit:

- the rods are now a bead-spring model with a very rigid spring $k=200$ and separation $R_0=0.5$
- the dynamics is Langevin with a significantly large timestep $dt=0.0025$ (compared to  $dt=0.001$ in Claudia's runs)
- I keep the interaction paarmeters (i.e. the shape of teh potentials) the same as Claudia's.

I still consider the same number of rods $N=600$, formed by $N_b=61$ beads each for a total of $N_p=36600$ particles.

Overall, the code appears to scale better with a large number of processors: I can run 32 cores and achieve around 3000 steps/s compared to a peak of 1400 steps/s with the rigid rods.

The structure seems to be preserved: the maain scales probed by the pair corrleation function are the same, albeit softened.

![image-20230720155748760](/sticky-rods/assets/img/elast-gr-phi0.04.png)

The overall speedup should be around a factor 4-5 . However, this seems to be insufficient to equilibrate the system in a supercooled region of the parameters. I explain more here below.



## Equilibration is sluggish

I focus on systems at a nominal volume fraction $\phi=0.004$ and attraction strength between the patches $\varepsilon\in [0.1, 4.5]$. The fraction of patches is $f=0.1$.

As shown by Claudia, at low $\varepsilon$ equilibration is fast (note that all the quantities in the plots are reported in reduced LJ units) whereas it rapidly become slower with increasing $\varepsilon$. To help with the equilibration, I have actually initially run a long run at $\varepsilon=4.5$ (which did not equilibrate) and then used that configuration to start runs at lower $\varepsilon$, in the hope of shortcutting some initial energy minimisation.



**$\varepsilon=2.5$**

![image-20230720155748760](/sticky-rods/assets/img/equilibration/plotlog-eps2.5.png)

**$\varepsilon=3.25$**, possibly the last equilibrated run

![image-20230720155748760](/sticky-rods/assets/img/equilibration/plotlog-eps3.25.png)

**$\varepsilon=3.75$**

![image-20230720155748760](/sticky-rods/assets/img/equilibration/plotlog-eps3.75.png)



**$\varepsilon=4.0$**

![image-20230720155748760](/sticky-rods/assets/img/equilibration/plotlog-eps4.0.png)

It is apparent that even one million time units (400 million steps, about 45 hours on BluePebble)   are sufficient to equilibrate at these (relatively larger) $\varepsilon$.

Unfortunately, the system is still far from being supercooled in this regime, as it is shown by the mean squared displacement.

## Anomalous and long-time diffusion

Starting from the last configurations of the runs above, I have then performed new simulations in the same ensemble to calculate the mean squared displacement. This is only an average over all of the $N_p$ particles and not an average over distinct initial conditions.



In linear scale, the MSD shows that with increasing $\varepsilon$, the rods travel less far.

![image-20230720155748760](/sticky-rods/assets/img/MSD/MSDlin.png)

In the double logarithmic scale, it is evident that for a very long transient, the particles constituting the rods do not follow a standard diffusive behaviour

![image-20230720155748760](/sticky-rods/assets/img/MSD/MSDloglog.png)

There is a long-lasting anomalous-diffusion exponent of around $\alpha=0.8$  for ${\rm MSD}=6 D t^\alpha$ . Fitting different regions, the exponent $\alpha$ increases towards one as we move to later time widnows.

This behavior is expected: the particles follow the motion of the rods, so their early motion is a mixture of random motion a single file diffusion, so that the exponent is lower. At much later times, the rods themselves diffuse, so that we can recover a diffusion constant.

**The main issue with the MSD is that it shows no plateau regime: the system is fluid and it is unclear whether there is any separation of timescales or not.**

We can plot the long-time diffusion constant obtained by fitting ${\rm MSD}=6 D t$ at late times versus the interaction strength.

![image-20230720155748760](/sticky-rods/assets/img/MSD/MSdangell.png)

The result is a pseudo-Angel plot for the diffusion constant. It shows a modest increase in the diffusion time $\tau_D = \sigma^2/D$. One may be foolish enough to fit the data with a VFT for just to have a sense of where to stop increasing $\varepsilon$: the fit indicates that already $\varepsilon=4.8$ should take us deeply into the glassy regime.

 

