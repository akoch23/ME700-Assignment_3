**Example 1: Discretization of a 2D element (linear, triangular)**,

The existing FEA discretization modules grant users the ability to analyze and visualize several finite element types. In this example, a 2D triangular element with 3 nodes will be used.

Additionally, the exact syntax that you will need to define some of these variables are explained within the discretization and discretization_demo_helper_fcns modules, so you may have those open concurrently with this tutorial.

**Visualization of Gauss Points and Element Nodes for 2D Triangular Element with 3 Nodes**

For the given finite element type, here is the expected input code and resulting output:

```python
from finiteelementanalysis import discretization_demo_helper_fcns as di_demo

# Define function variables (finite element type, # of gauss points in specified element type, plot type for specified element type, and desired file name)
ele_type = "D2_nn3_tri"
num_gauss_pts = 1

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
    return 3.0 * xi + 2.0 * eta + 5 * xi * eta

# Define variables for visualization function (finite element type, array contain node coordinates (exact coordinates are user defined), value of the field at each node (using previously defined fcn function), and desired file name)
ele_type = "D2_nn3_tri"
node_coords = np.array([[0, 0], [1, 0], [0, 1]]) # natural coordinates
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

# Define function that will weight nodes
def fcn(x, y):
    return 3.0 * x + 2.0 * y + 5.0 * x * y

#Define variables for visualization function (finite element type, array contain node coordinates (exact coordinates are user defined), value of the field at each node (using previously defined fcn function), and desired file name)
ele_type = "D2_nn3_tri"
node_coords = np.array([[0,3], [1, 1], [3, 3]]) # physical coordinates
node_values = np.array([fcn(x, y) for x, y in node_coords])

fname = ele_type + "_interpolate_fcn_physical_coords.png"
# Visualize the isoparametric mapping of the reference element to its physical shape
di_demo.visualize_isoparametric_mapping_single_element(str(fname), ele_type, node_coords, node_values)
```

![image](https://github.com/user-attachments/assets/c2279f2f-f033-47fb-a1ed-093841c31417)

```python
from finiteelementanalysis import discretization_demo_helper_fcns as di_demo
import numpy as np

def fcn(x, y):
    return 3.0 * x + 2.0 * y + 5.0 * x * y

def fcn_deriv(x, y):
    return np.asarray([3.0 + 5.0 * y, 2.0 + 5.0 * x]).reshape((2, 1))

ele_type = "D2_nn3_tri"
node_coords = np.array([[0, 0], [4, 1], [0, 5]]) # physical coordinates
node_values = np.array([fcn(x, y) for x, y in node_coords])
print(node_values)

xi, eta = 0, 0
# element center in natural coordinates

# Map the test point from natural to physical coordinates
x_mapped = di_demo.interpolate_field_natural_coords_single_element(
    ele_type, node_coords[:, 0], [xi], [eta]
).flatten()[0]
y_mapped = di_demo.interpolate_field_natural_coords_single_element(
    ele_type, node_coords[:, 1], [xi], [eta]
).flatten()[0]

# Evaluate the function derivative in physical coordinates
mapped_deriv = fcn_deriv(x_mapped, y_mapped)

# Compute the numerical gradient in natural coordinates
gradient_natural = di_demo.interpolate_gradient_natural_coords_single_element(
    ele_type, node_values, np.array([xi]), np.array([eta])
)

# Transform the numerical gradient to physical coordinates
gradient_physical = di_demo.transform_gradient_to_physical(
    ele_type, node_coords, np.array([xi]), np.array([eta]), gradient_natural
).reshape((2, 1))

print('Mapped Derivative:\n', mapped_deriv)
print('Physical Gradient:\n',gradient_physical)

if np.allclose(mapped_deriv, gradient_physical, atol=10e-10):
    print("Analytical and numerical derivatives match!")
```


```python
from finiteelementanalysis import discretization_demo_helper_fcns as di_demo
import numpy as np

def fcn(x, y):
    return 2.0 * x + 6.0 * y

def fcn_deriv(x, y):
    return np.asarray([3.0, 10.0])

def element_area(node_coords):
    x1, y1 = node_coords[0]
    x2, y2 = node_coords[1]
    x3, y3 = node_coords[2]
    x4, y4 = node_coords[3]
    area = 0.5 * abs((x1 * y2 + x2 * y3 + x3 * y4 + x4 * y1) 
                     - (y1 * x2 + y2 * x3 + y3 * x4 + y4 * x1))
    return area

def integral_of_deriv(node_coords):
    area = element_area(node_coords)
    return np.array([3.0 * area, 10.0 * area]).reshape((2, 1))

ele_type = "D2_nn3_tri"
num_gauss_pts = 1
node_coords = np.array([[0, 0], [4, 1], [0, 5]])

# Compute nodal values from fcn(x, y)
nodal_values = np.array([[fcn(x, y) for x, y in node_coords]]).T

# Compute numerical integral
integral_numerical = di_demo.compute_integral_of_derivative(
    ele_type, num_gauss_pts, node_coords, nodal_values)

# Compute analytical integral
integral_analytical = integral_of_deriv(node_coords)

print('Numerical Integral:\n', integral_numerical)
print('Analytic Integral:\n',integral_analytical)

# check result
if np.allclose(integral_numerical, integral_analytical, atol=10e-9):
    print("analytical and numerical integrals of derivatives match!")
