# Mesh Construction Example
Construction of BU Terrier Mesh using triangular finite element (D2_nn3_tri) with 3 gauss points.

Interpolation Function: 

```python
%run pre_process.ipynb
%run pre_process_demo_helper_fcns.ipynb
import numpy as np

def fcn_interp_1(x, y):
    return np.sin(x / 10.0) * np.cos(y / 10.0)

def fcn_interp_deriv_1(x, y):
    df_dx = np.cos(x / 10.0) * np.cos(y / 10.0) * 1.0 / 10.0
    df_dy = -np.sin(x / 10.0) * np.sin(y / 10.0) * 1.0 / 10.0
    return np.array([df_dx, df_dy])

# Create mesh
ele_type = "D2_nn3_tri"
num_gauss_pts = 3
mesh_name = f"bulldog_mesh_{ele_type}_gp{num_gauss_pts}"
complex_outline = get_bulldog_outline()
mesh_size = 10.0
coords, connect = mesh_outline(complex_outline, ele_type, mesh_name, mesh_size)
mesh_gauss_pts = get_all_mesh_gauss_pts(ele_type, num_gauss_pts, coords, connect)

# Plot mesh
plot_mesh_2D(mesh_name, ele_type, coords, connect, mesh_gauss_pts)

# Create element quality historams
aspect_ratios, skewness, min_angles, max_angles = compute_element_quality_metrics(ele_type, coords, connect)
cond_nums, jac_dets = compute_condition_and_jacobian(ele_type, coords, connect)
plot_file = mesh_name + "_histograms"
plot_element_quality_histograms(
        fname=str(plot_file),
        super_title="Test Mesh Quality Metrics (%s)" % (ele_type),
        ele_type=ele_type,
        cond_nums=cond_nums,
        jac_dets=jac_dets,
        aspect_ratios=aspect_ratios,
        skewness=skewness,
        min_angles=min_angles,
        max_angles=max_angles
    )

# Example interpolation w/ error plots
ground_truth_fcn = fcn_interp_1(mesh_gauss_pts[..., 0], mesh_gauss_pts[..., 1])  # Vectorized evaluation
interpolated = interpolate_scalar_to_gauss_pts(ele_type, num_gauss_pts, fcn_interp_1, coords, connect)
error_plot = mesh_name + "_fcn_errors"
plot_interpolation_with_error(
        str(error_plot),
        ele_type,
        coords,
        connect,
        mesh_gauss_pts,
        interpolated,
        ground_truth_fcn
    )

# Example interpolation of derivatives w/ error plots
ground_truth_grad = np.zeros_like(mesh_gauss_pts)
for kk in range(mesh_gauss_pts.shape[0]):
    for jj in range(mesh_gauss_pts.shape[1]):
        x = mesh_gauss_pts[kk, jj, 0]
        y = mesh_gauss_pts[kk, jj, 1]
        ground_truth_grad[kk, jj] = fcn_interp_deriv_1(x, y)  # should return [∂f/∂x, ∂f/∂y]

# Compute interpolated gradient from nodal values of fcn_3
interpolated_grad = interpolate_scalar_deriv_to_gauss_pts(
    ele_type,
    num_gauss_pts,
    fcn_interp_1,
    coords,
    connect
)

# Save gradient interpolation error plot
grad_error_plot = mesh_name + "_fcn_grad_errors"
plot_interpolation_gradient_with_error(
    str(grad_error_plot),
    ele_type,
    coords,
    connect,
    mesh_gauss_pts,
    interpolated_grad,
    ground_truth_grad
)
```

### 2D Mesh with Gauss Points:

![image](https://github.com/user-attachments/assets/451ac8f0-2ec2-4100-92c7-8bf6b07f75dd)

### Element Quality Histograms:

![image](https://github.com/user-attachments/assets/f0004aa5-32f0-4f5a-8737-1660646ea5d0)


### Gauss Point Interpolation Plot with Error:

![image](https://github.com/user-attachments/assets/155917a4-b556-4317-bfcb-490e026a92bf)


### Gauss Point Gradient Interpolation Plot with Error:

![image](https://github.com/user-attachments/assets/8b8843df-5e4d-41dc-a89d-4e643689a69c)
