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
- Overview:
    - As stated by the initial documentation, this code solves a finite element analysis (FEA) problem for a homogeneous uniaxial extension, comparing the computed displacement field to an analytical solution. More specifically, it simulates the response of a rectangular domain under a uniaxial extension via FEA. The results are visualized through a plot and an animated GIF showing the deformation over time.

- Imports and Setup
    - Warning Handling: The line "warnings.simplefilter("always")" ensures that all warnings are displayed during the execution of the code.
    - Required libraries for FEA Solver operation:
        - Specialized: Several modules are imported specifically from the finiteelementanalysis folder (pre_process, pre_process_demo_helper, and the hyperelastic_solver function from the solver module)
        - General: matplotlib, numpy, and pathlib are imported for graphing, mathematic operations, and file system pathing respectively
- Defining of Boundary Condition and Analytical Solution
    - 
- Mesh Generation
- Application of Boundary Conditions
- Solver Setup
- Comparison of genereated displacements with analytical solution values
- Displacement Visualization
