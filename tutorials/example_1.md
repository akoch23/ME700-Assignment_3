**Example 1: Discretization of a 2D element (linear, triangular)**,

The existing FEA discretization modules grant users the ability to analyze and visualize several finite element types. In this example, a 2D triangular element with 3 nodes will be used. To The user need only define these few variables:

- ele_type: 
- num_gauss_pts:
- fname: 

For a 2D triangular element with 3 nodes, here is the expected input code and resulting output:

```python
from finiteelementanalysis import discretization_demo_helper_fcns as di_demo

# Define function variables (finite element type, # of gauss points in specified element type, and plot type for specified element type)
ele_type = "D2_nn3_tri"
num_gauss_pts = 3
fname = "D2_nn6_tri_3gp.png"

# Visualize Gauss points and element nodes in natural coordinates
visualize_gauss_pts(fname, ele_type, num_gauss_pts)

```
![image](https://github.com/user-attachments/assets/47c59a26-5171-4994-85ae-675b494831bc)

