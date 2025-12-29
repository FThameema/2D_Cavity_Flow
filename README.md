# 2D Lid-Driven Cavity Flow

### Overview

I implemented a Navier-Stokes solver for the classic lid-driven cavity benchmark. The top wall moves at constant velocity while other walls are stationary, creating recirculating vortices. This tests fundamental CFD methods for velocity-pressure coupling.

### The Challenge

Incompressible Navier-Stokes equations require enforcing continuity (∇·v = 0) through pressure, but pressure has no evolution equation. This velocity-pressure coupling is the key difficulty.

### Numerical Method - Projection Method

**1. Predictor:** Advance velocity ignoring pressure
```
u* = u^n + Δt[-u·∇u + ν∇²u]
```
**2. Pressure Poisson:** Enforce continuity
```
∇²p = ρ/Δt ∇·u*
```
**3. Corrector:** Update velocity
```
u^(n+1) = u* - Δt/ρ ∇p
```
I used Gauss-Seidel with SOR to solve the Poisson equation iteratively.

### Implementation Details

- Grid: 128 × 128 uniform mesh
- Boundary conditions: Moving lid (u=U₀, v=0) on top, no-slip elsewhere
- Time stepping: Semi-implicit (explicit convection, implicit diffusion)
- Stability: CFL condition Δt < Δx/U_max

#### Reynolds Number Effects

- Re ~ 100: Single primary vortex, steady
- Re ~ 400: Primary vortex + small corner vortices  
- Re ~ 1000: Complex multi-vortex structure

I tested at all three Reynolds numbers to observe the transition.

### Results

<img width="1401" height="1189" alt="vortex" src="https://github.com/user-attachments/assets/acd81eba-f39b-4301-8741-0c62fb23e045" />
Primary vortex driven by moving top wall. Center is offset (not at geometric center) due to momentum transfer.
Strong shear layers near walls where vorticity concentrates and diffuses into domain.

<img width="1777" height="515" alt="vorticity" src="https://github.com/user-attachments/assets/0702ac2d-5471-48a9-b9b7-7590d48aa938" />

### Challenges

Getting pressure boundary conditions right was tricky - improper BCs cause checkerboard patterns. The Poisson solver needs sufficient iterations or continuity isn't satisfied and solution becomes unstable. Had to tune SOR relaxation parameter carefully.

### Running the Code

```bash
python cavity_flow.py
```

Adjust Reynolds number, grid resolution, and simulation time in the code.

### Future Work

Higher-order spatial schemes, efficient pressure solvers (multigrid), 3D extension, turbulence at very high Re.
---
This project gave me hands-on experience with the fundamental challenge in incompressible CFD: velocity-pressure coupling through projection methods.
