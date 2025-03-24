**Example 1: Discretization of a 2D element (linear, triangular)**,

The existing FEA discretization modules grant users the ability to analyze and visualize several finite element types. In this example, a 2D triangular element with 3 nodes will be used. To The user need only define these variables:

- ele_type: 
- num_gauss_pts:
- fname: 

Additionally, the exact syntax that you will need to define some of these variables are explained within the discretization and discretization_demo_helper_fcns modules, so you could have those open concurrently with this tutorial.

**Visualization of Gauss Points and Element Nodes for 2D Triangular Element with 3 Nodes
For the given finite element type, here is the expected input code and resulting output:

```python
from finiteelementanalysis import discretization_demo_helper_fcns as di_demo

# Define function variables (finite element type, # of gauss points in specified element type, and plot type for specified element type)
ele_type = "D2_nn3_tri"
num_gauss_pts = 3
fname = "D2_nn3_tri_3gp.png"

# Visualize Gauss points and element nodes in natural coordinates
visualize_gauss_pts(fname, ele_type, num_gauss_pts)

```
![image](https://github.com/user-attachments/assets/47c59a26-5171-4994-85ae-675b494831bc)

**Visualization of Interpolated Field for 2D Triangular Element with 3 Nodes
For the given finite element type, here is the expected input code and resulting output:

```python
from finiteelementanalysis import discretization_demo_helper_fcns as di_demo
import numpy as np

def fcn(xi, eta):
    return 2.0 * xi + 3.0 * eta + 1.5 * xi * eta

ele_type = "D2_nn3_tri"
node_coords = np.array([[-1, -1], [1, -1], [1, 1], [-1, 1]])
node_values = np.array([fcn(xi, eta) for xi, eta in node_coords])

fname = ele_type + "_interpolate_fcn.png"
di_demo.plot_interpolate_field_natural_coords_single_element(fname, ele_type, node_values)
```

