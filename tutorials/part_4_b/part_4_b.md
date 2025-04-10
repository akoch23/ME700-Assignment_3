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

## h-refinement method
H-refinement involves increasing the number of elements within the domain of the deflection funciton. The element size will shrink in order to increase the mesh resolution and be refined in the x- amd y-directions.


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
nx_list = np.array([10, 25, 50, 100, 125])    # number of elements along length
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

# Solution 

# Number of incremental load steps
nr_num_steps = 6

# Empty list for x-displacement values (to be filled)
all_tip_disp_x = []

# Mesh Generation and Boundary Condition Definition
for ix, nx in enumerate(nx_list):

    # Generate Mesh
    coords, connect = pre.generate_rect_mesh_2d(ele_type, 0.0, 0.0, L, H, nx, ny)

    # Identify Boundaries
    boundary_nodes, boundary_edges = pre.identify_rect_boundaries(coords, connect, ele_type, 0, L, 0, H)

    # Apply Boundary Conditions:

    # 1. Fix Left Boundary:
    fixed_left = pre.assign_fixed_nodes_rect(boundary_nodes, "left", 0.0, 0.0)

    #2. Uniform Downward Load on the Top Edge (y = H)
    dload_info = dload_info = pre.assign_uniform_load_rect(boundary_edges, "top", 0.0, q)

    #3. Combine all fixed Boundary Conditions
    fixed_nodes = fixed_left

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
    all_tip_disp_x.append(tip_disp_y) # Append final displacement values to list of all x-displacements



# Plot Mesh with Final Shape Deformation
img_name = "full_code_example_2_new.gif"
fname = str(tutorials_dir / img_name)
viz.make_deformation_gif(displacements_all, coords, connect, ele_type, fname)

# Plot Results
plt.figure(figsize=(8, 6))
plt.plot(nx_list, all_tip_disp_x, 'ro-', label="Tip Displacement")
#plt.semilogx(q_loop, all_w_analytic, 'b--', label="Analytical w")
plt.xlabel("nx ")
plt.ylabel("w (m)")
plt.title("Mesh Refinement: h-refinment, nx")
plt.legend()
plt.grid(True)
plt.tight_layout()
```

###full_code_example_2_new (Animation of Deformation)###
![full_code_example_2_new](https://github.com/user-attachments/assets/6fdc6b02-91db-442a-9db3-5affd6795bb1)





## p-refinement method
P-refinement refers to increasing the polynomial degree of the shape functions without altering the mesh (creating elements with more nodes). By comparing another, more complex finite element with the previous one (D2_nn8_quad vs. D2_nn4_quad):

```
# New Beam and Mesh Info

ele_type = "D2_nn8_quad"
ndof = 2 # 2 DOF per node (x, y)

L = 60.0   # length in x
H = 6.0    # height in y
nx_list_8 = np.array([5, 10, 25, 50, 100, 125])    # number of elements along length
ny = 2     # number of elements along height

q = -10 # load per unit length 


# Material Properties

E = 100000.0
nu = 0.3
mu = E / (2.0 * (1.0 + nu))
kappa = E / (3.0 * (1.0 - 2.0 * nu))

material_props = np.array([mu, kappa])
print(f"Material properties: mu={mu:.3f}, kappa={kappa:.3f}")


# Solution Info

nr_num_steps = 6
all_tip_disp_x_d8 = []


# Mesh Generation and Boundary Condition Definition

for ix, nx in enumerate(nx_list_8):

    # Generate Mesh
    coords, connect = pre.generate_rect_mesh_2d(ele_type, 0.0, 0.0, L, H, nx, ny)

    # Identify Boundaries
    boundary_nodes, boundary_edges = pre.identify_rect_boundaries(coords, connect, ele_type, 0, L, 0, H)

    # Apply Boundary Conditions:

    # 1. Fix Left Boundary:
    fixed_left = pre.assign_fixed_nodes_rect(boundary_nodes, "left", 0.0, 0.0)

    # 2. Uniform Downward Load on the Top Edge (y = H)
    dload_info = dload_info = pre.assign_uniform_load_rect(boundary_edges, "top", 0.0, q)

    # 3. Combine all fixed Boundary Conditions
    fixed_nodes = fixed_left


    # Solve iteratively using Hyperelastic Solver
    displacements_all, nr_info_all = hyperelastic_solver(
        material_props,
        ele_type,
        coords.T,      # solver expects coords as (ncoord, n_nodes)
        connect.T,     # and connectivity as (n_nodes_per_elem, n_elems)
        fixed_nodes,
        dload_info,
        nr_print=True,
        nr_num_steps=nr_num_steps,
        nr_tol=1e-10,
        nr_maxit=30,
    )

    final_disp = displacements_all[-1]  # final global displacement vector (length = n_nodes * ndof)

    # Compute the tip displacement from FEA result using node near beam tip (x = L, y = H/2)
    tip_node = None
    tol = 1e-3
    for i, (x, y) in enumerate(coords):
        if abs(x - L) < tol and abs(y - H/2) < H/(2*ny):
            tip_node = i
            break
    if tip_node is None:
        raise ValueError("Could not find tip node near x=L, y=H/2.")

    # and find the displacement at this node
    tip_disp_y = final_disp[ndof*tip_node + 1]  # the y-component of displacement

    all_tip_disp_x_d8.append(tip_disp_y)

plt.figure(figsize=(8, 6))
plt.semilogx(nx_list_4, all_tip_disp_x_d4, 'ro-', label="D2_nn4_quad")
plt.semilogx(nx_list_8, all_tip_disp_x_d8, 'b--', label="D2_nn8_quad")
plt.xlabel("nx")
plt.ylabel("w (m)")
plt.title("Mesh Refinements: D2_nn4_quad vs D2_nn8_quad")
plt.legend()
plt.grid(True)
plt.tight_layout()
```
