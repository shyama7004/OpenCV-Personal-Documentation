### Detailed Guide for Installing NumPy

#### Prerequisites

- **Python:** Ensure you have Python installed on your system.
- **Anaconda Distribution:** For a simplified setup, especially for beginners, using the Anaconda Distribution is recommended as it includes Python, NumPy, and many other packages for scientific computing and data science.

#### Installation Methods

##### Using Conda

1. **Create and Activate Environment:**

   ```sh
   conda create -n my-env
   conda activate my-env
   ```

2. **Install from Conda-Forge (Optional):**

   ```sh
   conda config --env --add channels conda-forge
   ```

3. **Install NumPy:**
   ```sh
   conda install numpy
   ```

##### Using Pip

1. **Install NumPy:**

   ```sh
   pip install numpy
   ```

2. **Virtual Environments:** It is good practice to use virtual environments to ensure reproducible installs. Refer to guides on virtual environments for detailed instructions.

#### Python and NumPy Installation Guide

Installing and managing packages in Python can be complex due to the variety of tools available. This guide provides recommendations based on user experience and operating system.

##### Recommendations

**Beginning Users (Windows, macOS, Linux):**

1. **Install Anaconda:** This includes all necessary packages and tools.
2. **Code Writing and Execution:** Use JupyterLab for exploratory and interactive computing, and Spyder or Visual Studio Code for scripting.
3. **Package Management:** Use Anaconda Navigator to manage packages and launch tools like JupyterLab, Spyder, or Visual Studio Code.

**Advanced Users:**

- **Conda Users:**

  1. **Install Miniforge:** A minimal installer for conda.
  2. **Environment Management:** Keep the base conda environment minimal and create separate environments for different projects.

- **Pip/PyPI Users:**
  1. **Install Python:** Use python.org, Homebrew, or your Linux package manager.
  2. **Dependency Management:** Use Poetry for managing dependencies and environments.

##### Python Package Management

**Conda vs Pip:**

1. **Language Support:**

   - **Conda:** Cross-language package manager, can install Python itself.
   - **Pip:** Specific to Python, installs packages for a particular Python installation.

2. **Package Repositories:**

   - **Conda:** Uses its own channels (e.g., defaults, conda-forge).
   - **Pip:** Uses the Python Packaging Index (PyPI).

3. **Integrated Solutions:**
   - **Conda:** Manages packages, dependencies, and environments.
   - **Pip:** May require additional tools for environment and dependency management.

##### Reproducible Installs

To ensure reproducible installs:

1. **Use separate environments for different projects.**
2. **Record package names and versions:**
   - **Conda:** `conda environments` and `environment.yml`
   - **Pip:** `virtual environments` and `requirements.txt`
   - **Poetry:** `virtual environments` and `pyproject.toml`

#### NumPy Packages & Accelerated Linear Algebra Libraries

NumPy depends on accelerated linear algebra libraries (typically Intel MKL or OpenBLAS). Here are some key points:

1. **NumPy Wheels on PyPI:** Built with OpenBLAS, included in the wheel, making it larger.
2. **Conda Defaults Channel:** NumPy built against Intel MKL, which is installed as a separate package.
3. **Conda-Forge Channel:** Built against a dummy “BLAS” package, defaults to OpenBLAS but can be configured to use MKL, BLIS, or reference BLAS.

**Performance Considerations:**

- **Intel MKL:** Typically faster and more robust, but not open source. Larger install size (~700 MB).
- **OpenBLAS:** Smaller install size (~30 MB), open source.

**Threading Behavior:**

- Both MKL and OpenBLAS use multi-threading for function calls (e.g., `np.dot`), often utilizing all CPU cores. This can impact performance, especially when combined with other parallelization methods.

#### Troubleshooting

If you encounter the following error:

```
IMPORTANT: PLEASE READ THIS FOR ADVICE ON HOW TO SOLVE THIS ISSUE!

Importing the numpy c-extensions failed. This error can happen for
different reasons, often due to issues with your setup.
```

Consult the troubleshooting guide for solutions to common setup issues.
