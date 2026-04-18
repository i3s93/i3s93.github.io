---
layout: archive
title: ""
permalink: /research/
author_profile: true
---

# Tensor network methods

Another major component of my research focuses on tensor networks and their applications to problems in kinetic theory. Kinetic models offer a mesoscale description of a system, where molecular dynamics is impractical and continuum models such as the Euler and Navier-Stokes equations breakdown. These models pose significant challenges for existing simulation tools due to their high dimensionality (often six-dimensional plus time) and multi-scale features which introduce severe numerical stiffness.

A fairly recent approach to address high-dimensional problems is based on low-rank approximation techniques. The basic idea is that the solution on the mesh is represented in a separable SVD-like form. For example, the rank \\(r\\) approximation of a function \\(f(x,y,t)\\) is given by

$$
f(x,y,t) \approx \sum_{\ell=1}^{r} \sigma_{\ell}(t) \mathbf{u}_{\ell}(x,t) \mathbf{v}_{\ell}^{\top}(y,t).
$$

Such an approximation can be built from data samples taken at discrete mesh points using the SVD algorithm. The separability of the dimensions along with SVD truncation reduces the storage at each time level from \\(\mathcal{O}(N^{2})\\) to \\(\mathcal{O}(Nr)\\), where \\(r \ll N\\). Other operations, such as computing integrals are also cheaper, since the operations can be performed locally on pieces of the decomposition. For example, we can perform numerical integration in \\(y\\) using \\(\mathcal{O}(Nr)\\) operations:

$$
\sum_{j=1}^{N} f(x_{i}, y_{j},t^{n}) \Delta y = \sum_{\ell=1}^{r} \sigma_{\ell}(t^{n}) \mathbf{u}_{\ell}(x_{i},t^{n}) \left( \sum_{j=1}^{N} \mathbf{v}_{\ell}^{\top}(y_{j},t^{n}) \right) \Delta y.
$$

The process is quite similar for high-dimensional problems, which use variants of this format to address the curse-of-dimensionality. Notable examples are the hierarchical Tucker tensor format and the tensor train format, which are two types of tensor networks.

While low-rank representations significantly reduce storage and arithmetic complexity, the design of robust time integration and solver strategies remains a central challenge. In particular, multiscale kinetic equations often require implicit discretizations to avoid restrictive stability constraints and to capture asymptotic limits. However, implicit methods introduce large-scale linear and nonlinear systems whose solution must respect the underlying tensor structure.

My recent work focuses on developing implicit solvers in low-rank tensor formats, including methods for linear systems and Newton methods for nonlinear problems, where all operations are performed in compressed form. This perspective naturally connects with asymptotic-preserving (AP) methods, which often rely on implicit formulations to remain stable across multiple physical regimes. By integrating AP discretizations with low-rank tensor solvers, we can construct methods that are both computationally tractable in high dimensions and robust in stiff regimes. This enables efficient simulation across kinetic, diffusive, and fluid limits without resolving all small scales explicitly.

The goal of my work is to develop structure-preserving low-rank methods that address the following challenges:
* __Implicit solvers__: scalable methods for linear and nonlinear systems arising from discretization of high-dimensional problems
* __AP formulations__: seamless treatment of multiscale limits through stable implicit schemes
* __Collision operators__: fast algorithms for nonlinear, nonlocal operators within compressed representations
* __Conservative low-rank methods__: preservation of mass, momentum, and energy in low-rank approximations of kinetic problems

---

# Particle methods for kinetic simulations

The particle-in-cell (PIC) method is among the most widely adopted tools used to perform kinetic simulations of plasmas. The original idea of PIC dates back to the 1950's and has been under active development in the subsequent decades. PIC methods represent the plasma as a collection of Lagrangian macroparticles which are evolved using the characteristics of the kinetic equation. The electric and magnetic fields are evolved on an Eulerian mesh, the most popular approach being the finite-difference time-domain (FDTD) method. Its popularity can be largely attributed to its simplicity and efficiency on parallel supercomputers. 

The historical reason for introducing a mesh is to avoid computing pair-wise interations of simulation particles, which naively scales as \\(\mathcal{O}(N_{p}^{2})\\). Instead, the fields are represented using a mesh whose number of cells is far smaller than the particles, i.e., \\(N_{m} \ll N_{p}\\). Mapping between the mesh and the particles is performed using spline interpolation, which requires \\(\mathcal{O}(N_{p})\\) operations. Depending on the field solver, the complexity is \\(\mathcal{O}(N_{m} + N_{p})\\) or \\(\mathcal{O}(N_{m}\log(N_{m}) + N_{p})\\), which is much smaller than \\(\mathcal{O}(N_{p}^{2})\\). In early simulations, the number of particles used were much smaller, e.g., \\(\mathcal{O}(10^{2})\\). Nowadays, modern PIC simulations routinely use \\(\mathcal{O}(10^{9})\\) particles. 

The most popular field solver used in PIC simulations is the explicit FDTD method which uses a staggered grid introduced by Yee in the 1960s. The special structure of this grid creates several complications:
* __Time step restrictions__: The explicit time integration introduces a Courant-Friedrichs-Lewy (CFL) condition that limits the maximum stable time step. Simulations often use highly refined Cartesian meshes to approximate curved boundaries, further restricting the time step.
* __Accuracy limitations__: The central differencing used in the staggered grid is difficult to extend beyond second-order accuracy in both time and space.
* __Numerical artifacts__: The FDTD discretization is inherently dispersive, which can lead to spurious numerical effects, especially in relativistic simulations. Additionally, the method is sensitive to the number of simulation particles.

I am interested in developing new particle methods with improved stability and structure-preserving capabilities. Our work in this area has led to several improvements over the conventional FDTD methods:
* __Unconditional stability__: Our methods are free from the CFL condition, so they are no longer restricted by small mesh cells.
* __High-order accuracy__: Our approach easily extends to higher-order accuracy in both time and space.
* __Low-noise__: The new methods naturally introduce diffusion, reducing noise in the simulation. We have found it unnecessary to employ additional filtering strategies.
* __Preservation of the gauge__: We have developed techniques to enforce the gauge condition, without resorting to a staggered mesh, which greatly simplifies the treatment of problems with geometry.

---

# High-performance scientific computing

Kinetic simulations demand significant computational resources, often exceeding the capabilities of personal computers and workstations. Several factors contribute to this challenge:

- **Curse of dimensionality**: Storage requirements for traditional array formats grow _exponentially_ with the number of dimensions.  
- **Accuracy requirements**: Capturing small spatial and temporal scales requires highly refined meshes with numerous degrees of freedom, evolved over many time steps.  
- **Multiphysics simulations**: Kinetic models are often a component of larger multiphysics problems—such as chemistry, electromagnetic fields, or hydrodynamics—which generate enormous amounts of simulation data.

To illustrate, storing _a single copy_ of a six-dimensional function on a mesh with 256 degrees of freedom per dimension requires more than 2 petabytes (\\( 2 \times 10^{15} \\) bytes) in double-precision arithmetic. This far exceeds the memory available on many systems, especially newer architectures like GPUs, which are optimized for throughput but offer relatively limited global memory.

Although hardware continues to evolve, the rapid advancements predicted by [Moore’s Law](https://www.intel.com/content/www/us/en/newsroom/resources/moores-law.html#gs.js757u) have slowed in recent years. While computing architectures will improve over time, new *mathematical tools* will likely drive innovation in computational science. My research focuses on developing these tools, with an emphasis on performance portability and scalable tensor algorithms that enable efficient use of modern heterogeneous architectures.

A central component of this effort is my work on the [BoBa Tensor Network Library](https://softwarelicensing.llnl.gov/product/boba). This software provides a unified framework for implementing low-rank tensor methods across a range of architectures, including multi-core CPUs and GPUs. The design also emphasizes a flexible backend system to experiment with different vendor-provided libraries. These ongoing developments are tightly coupled with my work on numerical methods, where algorithmic design and software implementation are co-developed. In particular, I am interested in:

- **Efficient low-storage algorithms** tailored for kinetic simulations and tensor-based discretizations  
- **Performance-portable implementations** that map naturally onto modern hardware, including GPUs and distributed-memory systems
- **Software infrastructure** support the development of tensor networks and solvers for high-dimensional problems

