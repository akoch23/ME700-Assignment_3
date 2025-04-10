# Large Deformation Problem: Mesh Refinement

## Problem Definition
Consider a 2D cantilever beam with a uniformly distributed load on its top edge. Determine the displacement of the tip of the beam numerically and analytically, and compare the results.

Inputs:
- ele_type = "D2_nn8_quad"
- ndof = 2 
- L = 60.0 m
- H = 6.0 m
- nx = 20 elements   
- ny = 2 elements
- q = -0.01 N/m^2
- E = 100000 Pa
- nu = 0.3
- mu = E / (2.0 * (1.0 + nu))
- kappa (K) = E / (3.0 * (1.0 - 2.0 * nu))

# Analytic Solution
For small deflections, the tip displacement at (x=L, y=H/2) can be determined by the Euler-Bernoulli beam formula. For For a cantilever beam of length L, height H, uniform load q, and plane strain, the material properties can be derived thus:

mu    = E/(2*(1+nu))

kappa = E/(3*(1-2*nu))

The analytic tip deflection for a cantilever beam under a uniform load "q" is:

w(L) = q*L^4 / (8*E*I)

I = H^3/12


