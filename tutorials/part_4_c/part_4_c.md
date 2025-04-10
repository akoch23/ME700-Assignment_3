# Solver Limitation
Well the FEA solver is generally robust (can handle odd input and iteration values), there are certain elements that *must* be strictly defined otherwise the solver will fail. Consider the 2D cantilever beam with a uniformly distributed load on the top edge from previous examples.

In the code block below, instead of using the assign_fixed_nodes_rect function for the fixed nodes on the left edge of the beam, only one node is defined as fixed, meaning that there are no fixed edges. This will causes the solver to fail on the first iteration and return "nan", as well as messages stating that that there is an invalid number for the determinant and the Jacobian is singular.



```
import warnings
warnings.simplefilter("always")
from finiteelementanalysis import pre_process as pre
from finiteelementanalysis import pre_process_demo_helper_fcns as pre_demo
from finiteelementanalysis.solver import hyperelastic_solver
from finiteelementanalysis import visualize as viz
import matplotlib.pyplot as plt
import numpy as np
from pathlib import Path


# for saving files later
tutorials_dir = Path(__file__).parent


#  Beam Geometry/Mesh Info
L = 60.0   # length in x
H = 6.0    # height in y
nx = 20    # number of elements along length
ny = 2     # number of elements along height
q = -10 # downward load per unit length (in x) acting on top edge of beam
ele_type = "D2_nn4_quad"  # 2D, 4-node quadrilateral (linear)
ndof = 2                  # 2 DOFs per node (x, y)



# Material Properties
E = 100000.0
nu = 0.3
mu = E / (2.0 * (1.0 + nu))
kappa = E / (3.0 * (1.0 - 2.0 * nu))

material_props = np.array([mu, kappa])
print(f"Material properties: mu={mu:.3f}, kappa={kappa:.3f}")

# Mesh Generation and Boundary Condition Definition
coords, connect = pre.generate_rect_mesh_2d(ele_type, 0.0, 0.0, L, H, nx, ny)

# Identify Boundaries
boundary_nodes, boundary_edges = pre.identify_rect_boundaries(coords, connect, ele_type, 0, L, 0, H)

# Apply Boundary Conditions:

# 1. Fix Left Boundary:
fixed_left = pre.assign_fixed_nodes_rect(boundary_nodes, "left", 0.0, 0.0)

#2. Uniform Downward Load on the Top Edge (y = H)
dload_info = dload_info = pre.assign_uniform_load_rect(boundary_edges, "top", 0.0, q)

#3. Combine all fixed Boundary Conditions
fixed_nodes = np.array([[0],[0],[0]])



# Solution 

# Number of incremental load steps
nr_num_steps = 6

# Solve iteratively using Hyperelastic Solver
displacements_all, nr_info_all = hyperelastic_solver(
    material_props,
    ele_type,
    coords.T,      # shape (2, n_nodes)
    connect.T,     # shape (n_nodes_per_elem, n_elems)
    fixed_nodes,
    dload_info,
    nr_print=True,
    nr_num_steps=nr_num_steps,
    nr_tol=1e-10,
    nr_maxit=30,
)

final_disp = displacements_all[-1]  # shape: (n_nodes*ndof,)

# Compute the tip displacement from FEA result using node near beam tip (x = L, y = H/2)
tip_node = None
tol = 1e-3
for i, (x, y) in enumerate(coords):
    if abs(x - L) < tol and abs(y - H/2) < H/(2*ny):
        tip_node = i
        break
if tip_node is None:
    raise ValueError("Could not find tip node near x=L, y=H/2.")

tip_disp_y = final_disp[ndof*tip_node + 1]  # the y-component of displacement

print(f"Tip node index: {tip_node}, coordinates={coords[tip_node]}")
print(f"Computed tip deflection (y): {tip_disp_y:.6f}")

```
c:\Users\kochl\anaconda3\envs\finite-element-analysis-env\Lib\site-packages\numpy\linalg\_linalg.py:2432: RuntimeWarning: invalid value encountered in det
  r = _umath_linalg.det(a, signature=signature)
C:\Users\kochl\Downloads\finite-element-analysis-main\finite-element-analysis-main\src\finiteelementanalysis\solver.py:133: MatrixRankWarning: Matrix is exactly singular
  d_displacement = spla.spsolve(K_sparse, R)
Iteration 1, Correction=0.000000e+00, Residual=nan, tolerance=1.000000e-10
Tip node index: 41, coordinates=[60.  3.]
Computed tip deflection (y): nan
