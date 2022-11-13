---
created: 2022-11-13 15:52
updated: 2022-11-13 16:35
---
---
**Links**: [[108 Python Index]]

---
**INCOMPLETE**

## Packaging
- `distutils` which is an old format evolved into `setuptools`.
	- `pip` is a side tool for setup tools.
- There are two types of Python package: 
	- `sdist` (*source distribution*): 
		- Source packages that *contain code* and some means to build and install it
	- `wheel`: 
		- *Binary packages* which can be installed by just extracting the files as-is.
		- A wheel is an *ordinary zip file* with a name of the form `{name}-{version}-{python version}-{python abi}-{os}.whl`
		- Conceptually, a wheel is installed by simply extracting it somewhere `import` will look: no extra build steps are required
		- Wheels are fancy zip files that have `whl` as extension. We can unzip them using the `unzip` command: `unzip file_name.whl`

### More about wheels
- As an example, one of NumPy’s wheels is named `numpy-1.20.1-cp39-cp39-macosx_10_9_x86_64.whl` meaning:
	- `numpy` = Package name is "numpy"
	- `1.20.1` = Package version is 1.20.1
	- `cp39` = For use specifically with CPython 3.9 (e.g. not other CPython versions or other Python implementations)
	- `cp39` = Contains binary code compiled against the CPython 3.9 ABI
	- `macosx_10_9_x86_64` = Built for 64-bit Intel computers running MacOS 10.9.

- For *wheels containing only platform-independent Python code, many of the filename parts are replaced with more generic values*. 
	- For *example, the (pure-Python) "requests" package* is named `requests-2.25.1-py2.py3-none-any.whl` meaning:
	- `requests` = Package name is ‘requests’
	- `2.25.1` = Package version is 2.25.1
	- `py2.py3` = For use with any Python 2.x or Python 3.x interpreter
	- `none` = Contains no binary code compiled against the Python ABI
	- `any` = Suitable for any operating system.

> [!question]- Why use wheels for pure python packages?
> - You may be wondering what the benefit of the wheel format is for pure-python packages over just using sdists. 
> - *Whilst its true that a pure-python wheel will contain more-or-less the same files as an sdist, installing an sdist still requires the execution of arbitrary code (either `setup.py` file or some other build system)*. 
> - This step might even require the installation of the build system itself! Wheels, however, only require a simple unzipping operation. 
> - Furthermore, unlike sdists, wheels include important metadata such as a list of package dependencies which are essential for proper installation.

### Building packages
- For pure Python packages, the build process boils down to gathering the required files and metadata and stuffing them into the appropriate archive format. 
- Building 'impure' packages (including C code, for example) is complicated by the need to invoke compilers to produce binaries when making wheel packages.

- PEP 517 conceptually divides the build process in two parts:
	- *Preparing a build environment* (e.g. an isolated virtual environment with the build software installed)
	- Building the sdist or wheel
- Step 1 is known as the *build frontend* and the software doing the actual building (step 2) as the *build backend*.

> [!note]- How pip installs a package
> When pip installs a package from source using the PEP 517 mechanism, *pip acts as a build frontend*. 
> First *pip will create a temporary virtual environment for the build.* 
> Next it *installs the build backend* specified by the package (e.g. setuptools, Flit, Poetry, etc.) and then *invokes it to produce a wheel*. 
> Finally *pip will install the wheel and destroy the temporary virtual environment*.

- Developers might choose use a generic build frontend instead of, for example, `setup.py`, or Poetry and Flit’s native command line tools. 
	- PyPA have created such a generic frontend for this purpose called `build`
	- `python -m build --sdist  # Build an sdist package`
	- `python -m build --wheel  # Build a wheel package`
	- For this to work we need either a `setup.py` or a `pyproject.toml` in the root of the folder.
	- As a developer, this guarantees that no matter which PEP 517 compliant build system you choose for your project, your users (and tools like pip) will be able to build and install your software.
	

## References
- [This Way Up: A Bottom-Up Look At Python Packaging | cloudfit-public-docs (bbc.github.io)](https://bbc.github.io/cloudfit-public-docs/packaging/this_way_up.html)