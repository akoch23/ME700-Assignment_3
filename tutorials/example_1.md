**Example 1: Discretization of a 2D element (linear, triangular)**,

The existing FEA discretization modules grant users the ability to analyze and visualize several finite element types. In this example, a 2D triangular element with 3 nodes will be used.

Additionally, the exact syntax that you will need to define some of these variables are explained within the discretization and discretization_demo_helper_fcns modules, so you may have those open concurrently with this tutorial.

**Visualization of Gauss Points and Element Nodes for 2D Triangular Element with 3 Nodes**

For the given finite element type, here is the expected input code and resulting output:

```python
from finiteelementanalysis import discretization_demo_helper_fcns as di_demo

# Define function variables (finite element type, # of gauss points in specified element type, plot type for specified element type, and desired file name)
ele_type = "D2_nn3_tri"
num_gauss_pts = 3

# Visualize Gauss points and element nodes in natural coordinates
fname = "D2_nn3_tri_3gp.png"
di_demo.visualize_gauss_pts(fname, ele_type, num_gauss_pts)

```
![image](https://github.com/user-attachments/assets/47c59a26-5171-4994-85ae-675b494831bc)

**Visualization of Interpolated Field for 2D Triangular Element with 3 Nodes (Natural Coordinates)**

For the given finite element type, here is the expected input code and resulting output:

```python
from finiteelementanalysis import discretization_demo_helper_fcns as di_demo
import numpy as np

# Define function that will weight nodes
def fcn(xi, eta):
    return 2.0 * xi + 3.0 * eta + 1.5 * xi * eta

# Define variables for visualization function (finite element type, array contain node coordinates (exact coordinates are user defined), value of the field at each node (using previously defined fcn function), and desired file name)
ele_type = "D2_nn3_tri"
node_coords = np.array([[0, 0], [1, 0], [0, 1]])
node_values = np.array([fcn(xi, eta) for xi, eta in node_coords])

# Visualize Scalar Field interpolated across a sampling of points in natural coordinates
fname = ele_type + "_interpolate_fcn.png"
di_demo.plot_interpolate_field_natural_coords_single_element(fname, ele_type, node_values)
```
![image](https://github.com/user-attachments/assets/3b201a00-85fa-4aa8-85ba-1b7175ab9708)

**Visualzation of Reference Elemement (Natural Coordinates) and Mapped Element (Physical Coordinates)**

For the given finite element type, here is the expected input code and resulting output:

```python
from finiteelementanalysis import discretization_demo_helper_fcns as di_demo
import numpy as np

# Define function that will weight nodes (can omit if combing code with previous step, as fcn is already defined)
def fcn(x, y):
    return 2.0 * x + 3.0 * y + 1.5 * x * y

#Define variables for visualization function (finite element type, array contain node coordinates (exact coordinates are user defined), value of the field at each node (using previously defined fcn function), and desired file name)
ele_type = "D2_nn3_tri"
node_coords = np.array([[0,0], [1, 0], [0, 1]])
node_values = np.array([fcn(x, y) for x, y in node_coords])

fname = ele_type + "_interpolate_fcn_physical_coords.png"
# Visualize the isoparametric mapping of the reference element to its physical shape
di_demo.visualize_isoparametric_mapping_single_element(str(fname), ele_type, node_coords, node_values)
```

![image](https://github.com/user-attachments/assets/c2279f2f-f033-47fb-a1ed-093841c31417)
