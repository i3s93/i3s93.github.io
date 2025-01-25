---
layout: archive
title: ""
permalink: /research/
author_profile: true
---

# Particle methods for kinetic simulations

The particle-in-cell (PIC) method is among the most widely adopted tools used to perform kinetic simulations of plasmas. The original idea of PIC dates back to the 1950's and has been under active development in the subsequent decades. PIC methods represent the plasma as a collection of Lagrangian macroparticles which are evolved using the characteristics of the kinetic equation. The electric and magnetic fields are evolved on an Eulerian mesh, the most popular approach being the finite-difference time-domain (FDTD) method. Its popularity can be largely attributed to its simplicity and efficiency on parallel supercomputers. 

The historical reason for introducing a mesh is to avoid computing pair-wise interations of simulation particles, which naively scales as \\(\mathcal{O}(N_{p}^{2})\\). Instead, the fields are represented using a mesh whose number of cells is far smaller than the particles, i.e., \\(N_{m} \ll N_{p}\\). Mapping between the mesh and the particles is performed using spline interpolation, which requires \\(\mathcal{O}(N_{p})\\) operations. Depending on the field solver, the complexity is \\(\mathcal{O}(N_{m} + N_{p})\\) or \\(\mathcal{O}(N_{m}\log(N_{m}) + N_{p})\\), which is much smaller than \\(\mathcal{O}(N_{p}^{2})\\). In early simulations, the number of particles used were much smaller, e.g., \\(\mathcal{O}(10^{2})\\). Nowadays, modern PIC simulations routinely use \\(\mathcal{O}(10^{9})\\) particles. 

The most popular field solver used in PIC simulations is the explicit FDTD method which uses a staggered grid introduced by Yee in the 1960s. The special structure of this grid creates several complications:
* __Time step restrictions__: The explicit time integration introduces a Courant-Friedrichs-Lewy (CFL) condition that limits the maximum stable time step. Simulations often use highly refined Cartesian meshes to approximate curved boundaries, further restricting the time step.
* __Accuracy limitations__: The central differencing used in the staggered grid is difficult to extend beyond second-order accuracy in both time and space.
* __Numerical artifacts__: The FDTD discretization is inherently dispersive, which can lead to spurious numerical effects, especially in relativistic simulations. Additionally, the method is sensitive to the number of simulation particles.

I am interested in developing new particle methods with improved stability and structure-preserving capabilities. Our work in this area [1,2,3] has led to several improvements over the conventional FDTD:
* __Unconditional stability__: Our methods are free from the CFL condition, so they are no longer restricted by small mesh cells.
* __High-order accuracy__: Our approach easily extends to higher-order accuracy in both time and space.
* __Low-noise__: The new methods naturally introduce diffusion, reducing noise in the simulation. We have found it unnecessary to employ additional filtering strategies.
* __Preservation of the gauge__: We have developed techniques to enforce the gauge condition, without resorting to a staggered mesh, which greatly simplifies the treatment of problems with geometry.

---

# Structure-preserving low-rank tensor methods

Another major component of my research focuses on low-rank tensor methods and their applications to problems in kinetic theory. Kinetic models offer a mesoscale description of a system, where molecular dynamics is impractical and continuum models such as the Euler and Navier-Stokes equations breakdown. These models pose significant challenges for existing simulation tools due to their high dimensionality (often six-dimensional plus time) and multi-scale features which create tremendous numerical stiffness.

A fairly recent approach to address high-dimensional problems is based on low-rank approximation techniques. The basic idea is that the solution on the mesh is represented in a separable SVD-like form. For example, the rank \\(r\\) approximation of a function \\(f(x,y,t)\\) is given by

$$
f(x,y,t) \approx \sum_{\ell=1}^{r} \sigma_{\ell}(t) \mathbf{u}_{\ell}(x,t) \mathbf{v}_{\ell}^{\top}(y,t).
$$

Such an approximation can be built from data samples taken at discrete mesh points using the SVD algorithm. The separability of the dimensions along with SVD truncation reduces the storage at each time level from \\(\mathcal{O}(N^{2})\\) to \\(\mathcal{O}(Nr)\\), where \\(r \ll N\\). Other operations, such as computing integrals are also cheaper, since the operations can be performed locally on pieces of the decomposition. For example, we can perform numerical integration in \\(y\\) using \\(\mathcal{O}(Nr)\\) operations:

$$
\sum_{j=1}^{N} f(x_{i}, y_{j},t^{n}) \Delta y = \sum_{\ell=1}^{r} \sigma_{\ell}(t^{n}) \mathbf{u}_{\ell}(x_{i},t^{n}) \left( \sum_{j=1}^{N} \mathbf{v}_{\ell}^{\top}(y_{j},t^{n}) \right) \Delta y.
$$

The process is quite similar for high-dimensional problems, which use variants of this format to address the curse-of-dimensionality. Notable examples are the hierarchical Tucker tensor format and the tensor train format. In any case, the use of truncation is crucial for compressing the function.

My research is aimed at constructing high-order low-rank methods with structure-preserving capabilities. In particular, I am interested in methods with asymptotic-preserving (AP) capabilities and moment preservation. Methods with the AP properties are highly desireable for multiscale kinetic simulations because they can treat the diverse range of regimes present in these types of problems. Additionally, such methods can alleviate the more stringent restrictions on the time step and the spatial resolution introduced by small-scale phenomena, leading to more efficient methods. There is extensive literature on AP methods developed in the last several decades, however, many of these schemes remain impractical due to high-dimensionality. The goal of my work is to bridge these techniques and is aimed at addressing the following challenges [4,5]:
* __Improved flexibility__: arbitrary high-order accuracy, use of novel tensor formats, and coupling with AP schemes
* __Asymptotic-preservation__: treatment of problems with different asymptotic limits, e.g., diffusive and fluid limits
* __Collision operators__: fast algorithms for collision operators with strong nonlinear effects which may be non-local
* __Conservative low-rank methods__: techniques to preserve conserved quantities such as mass, momentum, and energy.


---

# High-performance scientific computing

Kinetic simulations demand significant computational resources, often exceeding the capabilities of personal computers and workstations. Several factors contribute to this challenge:

- **Curse of dimensionality**: Storage requirements for traditional array formats grow _exponentially_ with the number of dimensions.  
- **Accuracy requirements**: Capturing small spatial and temporal scales requires highly refined meshes with numerous degrees of freedom, evolved over many time steps.  
- **Multiphysics simulations**: Kinetic models are often a component of larger multiphysics problems—such as chemistry, electromagnetic fields, or hydrodynamics—which generate enormous amounts of simulation data.

To illustrate, storing _a single copy_ of a six-dimensional function on a mesh with 256 degrees of freedom per dimension requires more than 2 petabytes (\\( 2 \times 10^{15} \\) bytes) in double-precision arithmetic. This far exceeds the memory available on many systems, especially newer architectures like GPUs, which are optimized for throughput but offer relatively limited global memory.

Although hardware continues to evolve, the rapid advancements predicted by [Moore’s Law](https://www.intel.com/content/www/us/en/newsroom/resources/moores-law.html#gs.js757u) have slowed in recent years. While computing architectures will improve over time, new **mathematical tools** will likely drive innovation in computational science. My research focuses on developing these tools, which complement my broader interests by enabling efficient use of modern computing devices [6,7]. Specifically, I am working on methods with the following properties:

- **Efficient low-storage algorithms** tailored for kinetic simulations.  
- **Portable algorithms** optimized for a variety of computing devices.  
- **Software tools** to facilitate the development of kinetic algorithms.

--- 

# Selected research

1. A.J. Christlieb, **W.A. Sands**, and S.R. White. ["A particle-in-cell method for plasmas with a generalized momentum formulation: part I, model formulation."](https://arxiv.org/abs/2208.11291) *arXiv:2208.11291*
2. A.J. Christlieb, **W.A. Sands**, and S.R. White. ["A particle-in-cell method for plasmas with a generalized momentum formulation: part II, enforcing the Lorenz gauge condition."](https://link.springer.com/article/10.1007/s10915-024-02728-6) *Journal of Scientific Computing*, 101(73) (2024)
3. A.J. Christlieb, **W.A. Sands**, and S.R. White. ["A particle-in-cell method for plasmas with a generalized momentum formulation: part III, a family of gauge-conserving methods."](https://arxiv.org/abs/2410.18414) *arXiv:2410.18414*
4. **W.A. Sands**, W. Guo, J.-M. Qiu, and T. Xiong. ["High-order adaptive rank integrators for multi-scale linear kinetic transport equations in the hierarchical Tucker format."](https://arxiv.org/abs/2406.19479) *arXiv:2406.19479* 
5. N.Zheng, **W.A. Sands**, D.Hayes, and J.-M. Qiu. "A local macroscopic conservative (LoMaC) semi-Lagrangian adaptive-rank (SLAR) method for the multi-scale BGK equation" (in-progress).
6. A.J. Christlieb, P.T. Guthrey, **W.A. Sands**, and M. Thavappiragasam. ["Parallel algorithms for successive convolution."](https://link.springer.com/article/10.1007/s10915-020-01359-x) *Journal of Scientific Computing*, 86(1) (2021)
7. G. Alabandi, **W. Sands**, G. Biros, and M. Burtscher, ["A GPU algorithm for detecting strongly connected components."](https://dl.acm.org/doi/abs/10.1145/3581784.3607071) *Proceedings of the 2023 ACM/IEEE International Conference for High Performance Computing, Networking, Storage, and Analysis*, 17: 1–13 (2023)

