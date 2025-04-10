# FEA Analysis Model (Tutorial)

[![python](https://img.shields.io/badge/python-3.12-blue.svg)](https://www.python.org/)
![os](https://img.shields.io/badge/os-ubuntu%20|%20macos%20|%20windows-blue.svg)



### Conda environment installation and testing

To install this package, first establish a new conda environment:
```bash
conda create --name dsm-solver-env python=3.12
```
Afterwards, activate the environment:
```bash
conda activate dsm-solver-env
```

You can double check if the installed version of python is indeed version 3.12 in the new conda environment:
```bash
python --version
```

Ensure that pip is using the latest version of setuptools:
```bash
pip install --upgrade pip setuptools wheel
```

Create an editable install of the newton's method code (reminder: make sure you're in the right directory for this step, using the cd: [insert folder path] command)
```bash
pip install -e .
```

Test that the code is working with pytest:
```bash
pytest -v --cov=DSM_functions --cov-report term-missing
```


## FEA Solver Overview (via tutorials/full_code_example_1.py)

- Schematic:

  ![Assignment 3 Part 3 Schematic](https://github.com/user-attachments/assets/3d5a8689-e4a9-4829-8b11-1b6868f9225b)


- Overview:
    - As stated by the initial documentation, this code solves a finite element analysis (FEA) problem for a homogeneous uniaxial extension, comparing the computed displacement field to an analytical solution. More specifically, it simulates the response of a rectangular domain under a uniaxial extension via FEA. The results are visualized through a plot and an animated GIF showing the deformation over time.

- Imports and Setup
    - Warning Handling: The line "warnings.simplefilter("always")" ensures that all warnings are displayed during the execution of the code.
    - Required libraries for FEA Solver operation:
        - Specialized: Several modules are imported specifically from the finiteelementanalysis folder (pre_process, pre_process_demo_helper, and the hyperelastic_solver function from the solver module)
        - General: matplotlib, numpy, and pathlib are imported for graphing, mathematic operations, and file system pathing respectively
          
- Defining of Boundary Condition and Analytical Solution
    - The homogeneous uniaxial extension occurs along the x-direction, with the y-direction remaining unchanged. The left (lower) boundary is set at the origin with no displacement, and the right (upper) boundary at the value "L" undergoes some displacement  in the x-direction (lambda_target - 1) * L, with no displacement in tye y-direction.
    - Top and bottom boundaries are y=0 and y=H.
    - The Analytical Solution (displacement field) is defined as u_x(x) = (lambda - 1) * x, u_y(x)
 
- Additional Problem Details
    - Element type is defined by variable ele_type = "D2_nn4_quad" (corresponding to a 2D bilinear quadrilateral with 4 nodes)
    - Domain parameters are defined: L = 10, H = 5, nx = 4, ny = 2
    - Extension amount is defined: lambda_target = 1.05 (5% extension)

- Mesh Generation
    - Using the generate_rect_mesh_2d function from the pre_process module, the domain is discretized into a 2D rectangular grid, defined by the lower-left corner coordinates (0, 0), upper-right corner coordinates (length (L). height (H)), and the number of elements in the x (nx) and y (ny) directions along x and y.
- Application of Boundary Conditions
    - Boundary nodes are identified using pre.identify_rect_boundaries, and fixed displacement conditions are applied:
        - Left boundary: u_x = 0, u_y = 0
        - Right boundary: u_x = (lambda - 1) * L, u_y = 0
        - Top and Bottom boundaries: u_y = 0
    - These fixed boundary conditions are combined into a single array "fixed_nodes".
- Solver Setup and Execution
    - Load distribution, material properties, and number of incremental loading steps are defined.
    - A nonlinear solver function "hyperelastic_solver" is used to compute the nodal displacements for the element for the defined number of incremental loading steps, with set convergence tolerances and maximum number of iterations.
  
- Comparison of genereated displacements with analytical solution values
    - After solving for the displacements, the code extracts the displacement in the x-direction (u_x) for the nodes near the mid-height (H/2)
    - The computed displacement values are compared to the analytical solution (u_z(x) = (lambda - 1) * x)
    - The computed displacement is graphically compared to the analytical solution via matplotlib. This plot is saved as a .png image in the tutorials directory/folder.
      - Additionally, an animation of the deformation is generated using the function viz.make_deformation_gif, which creates a .gif file to illustrate how the mesh deforms over time.
     

### Part 1, 2, and 4 Tutorials
See "tutorials" directory for all tutorials relating to Parts 1, 2, and 4 of this Assignment (This README constitutes Part 3).
