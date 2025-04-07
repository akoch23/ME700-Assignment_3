**Validation of Example Code (full_code_example_1.py) with Analytical Solution:**

This short tutorial requires the usage of the full_code_example_1.py file from the "finite-element-analysis" repository, located within the "tutorials" folder. That file also requires the main files from the library, so downloading the entire repository is necessary. 
Link: https://github.com/Lejeune-Lab-Graduate-Course-Materials/finite-element-analysis/tree/main

The only elements of the code that are changed are some of the input parameters, and output file naming.
Within the full_code_example_1.py file, alter the following variables to equate these values:

Inputs:
ele_type = "D2_nn4_quad"

L = 40.0
H = 20.0
nx = 16    
ny = 8 

material_props = np.array([200, 80])
nr_num_steps = 10

Outputs:
mesh_img_fname = tutorials_dir / "full_code_example_1_mesh_new.png"
img_fname = tutorials_dir / "uniaxial_extension_error_new.png"
img_name = "full_code_example_1_new.gif"

Validation of code performance is presented visually through the generated image file "uniaxial_extension_error_new.png", which compares the computed displacement (homogenous deformation) of the defined finite element in the x-direction (itself visualized step-wise in the animated "full_code_example_1_new.gif" file) to the analytical solution, which is defined as u_x(x) = (lambda - 1)*x for x, and u_y(x) = 0 for y.

Output files should look like this:

_uniaxial_extension_error_new.png_
![uniaxial_extension_error_new](https://github.com/user-attachments/assets/3467874a-6bdd-4236-87dc-6025910fd82e)
