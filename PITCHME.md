---
### @size[1.5em](Introduction to FEM Analysis with Python)

[@tkoyama010](https://twitter.com/tkoyama010)

+++

### Who are you? 👀

- @tkoyama010: [Twitter](https://twitter.com/tkoyama010), [Booth](https://tkoyama010.booth.pm/)
- Fortran, Python, C++, VBScript etc
- Simulation in Earthquake Engineering

+++

### Who are you? 👀

- 👨‍💻 Contribution to [GetFEM++](https://ja.wikipedia.org/wiki/GetFEM%2B%2B).
- 👨‍💻 Fix types, refactoring, translation tasks, etc.
- 👨‍💻 Add two object.
  - lumped mass brick object
  - Houbolt method objects
- 😤[contributing status](https://github.com/tkoyama010/getfem/graphs/contributors) my first goal is 100 commits.

+++

### Agenda

- ✅Overview
- ✅Installation
- ✅Tutorial
- ✅Summary

+++

### 📝 Question❗

✋ I KNOW "partial differential equations (PDE)"

✋ I KNOW "finite element method (FEM)"

+++

### 🤔 What is PDE and FEM❓

❗ PDE is a equation using in physics, engineering.

$$−\Delta u=1 \ {\rm on}\  \Omega, u=0 \ {\rm on}\  \delta \Omega $$

+++

### 🤔 What is PDE and FEM❓

❗ FEM is a method of solving PDE.

$$[K] \left\\{ u \right\\} = \left\\{ F \right\\}$$

+++

### 👨‍💻What is GetFEM++❓

⭐ This library provides a framework for solving PDE by FEM.

![shape3](http://getfem.org/_images/shape3.png)
![shape4](http://getfem.org/_images/shape4.png)

+++

### 📄Release Notes

[Release Notes Version 5.3](http://getfem.org/whatsnew/5.3.html) @2018/06/22

[Release Notes Version 5.2](http://getfem.org/whatsnew/5.2.html) @2017/06/30

[Release Notes Version 5.1](http://getfem.org/whatsnew/5.1.html) @2016/08/18

[Release Notes Version 5.0](http://getfem.org/whatsnew/5.0.html) @2015/07/29

[Release Notes Version 4.3](http://getfem.org/whatsnew/4.3.html) @2014/07/14

@size[2.0em](😯) Development has continued since the 1990s❗

+++

### 🔰Installation@ ![Ubuntu](https://raw.githubusercontent.com/tkoyama010/OpenCAE-tkoyama010/PyConJP2019/ubuntulogo.png)

It is easy to install by apt.

```bash
$ sudo apt install python-getfem++
```

...but

@size[2.0em](😆) Let's install from source code to study❗

```
$ wget http://download-mirror.savannah.gnu.org/releases/getfem/stable/getfem-5.3.tar.gz
```
+++

### 🔰Installation@ ![Ubuntu](https://raw.githubusercontent.com/tkoyama010/OpenCAE-tkoyama010/PyConJP2019/ubuntulogo.png)

- Run ***./configure***
  - --with-pic 💡 options for 64 bit compilation
  - --enable-python3=yes 💡 option to use python3
```bash
$ ./configure --with-pic --enable-python3=yes
```

+++

### 📗 Using Library

- 🔖 [Qhull](http://www.qhull.org/) 💡 mesh generation.
- 🔖 [MUMPS](http://mumps.enseeiht.fr/) 💡 parallel calculation.
- 🔖 [LAPACK/BLAS](http://www.netlib.org/lapack/) 💡 Linear Algebra
- 🔖 [Numpy/Scipy](https://www.scipy.org/) 💡 Numerical computation

![QhullLogo](http://www.qhull.org/html/qh--cone.gif)

+++

### 📗 Using Library

💁 Compile Test Install　

```bash
$ make && make check && sudo make install
```
+++

### 🏃 Tutorial

$$−\Delta u=1 \ {\rm on}\  \Omega, u=0 \ {\rm on}\  \delta \Omega $$

![pdedemo1_01](https://jp.mathworks.com/help/examples/pde/win64/pdedemo1_01.png)

+++

### 🏃 Mesher Object

```python
import getfem as gf
mo = gf.MesherObject('ball', [1.0, 1.0], 1.0)
```

![pdedemo1_01](https://jp.mathworks.com/help/examples/pde/win64/pdedemo1_01.png)

+++

### 🏃 Mesh object

```python
# Approximate mesh length
h = 0.1
# Create Mesh Objects with second order elements
mesh = gf.Mesh('generate', mo, h, 2)
```

![mesh1](mesh1.png)

+++

### 🏃 Move Mesh and Set Region

```python
mesh.translate([-1.0, -1.0])
fb = mesh.outer_faces()
OUTER_BOUND = 1
mesh.set_region(OUTER_BOUND, fb)
```

![mesh2](mesh2.png)

+++

### 🏃 [MeshFem](http://getfem.org/python/cmdref_MeshFem.html?highlight=meshfem%20object) Object

```python
# The node has 1 degree of freedom
mfu = gf.MeshFem(mesh, 1)
elements_degree = 2

# classical lagrange elements
mfu.set_classical_fem(elements_degree)
```
![Lagrange Element](https://raw.githubusercontent.com/tkoyama010/OpenCAE-tkoyama010/PyConJP2019/getfemlisttriangleP2.png)

+++

### 🏃 [Model Object](http://getfem.org/userdoc/model_object.html)

$$[K] \left\\{ u \right\\} = \left\\{ F \right\\}$$
```python
md = gf.Model('real')
# Add the variable 'u' using the MeshFem object.
md.add_fem_variable('u', mfu)
```

+++

### 🏃 Laplacian Brick, RHS and condition 

$$−\Delta u=1 \ {\rm on}\  \Omega, u=0 \ {\rm on}\  \delta \Omega $$
```python
# −\Delta u
mim = gf.MeshIm(mesh, pow(elements_degree,2))
md.add_Laplacian_brick(mim, 'u')

# RHS 1
import numpy as np
md.add_fem_data('F', mfu)
md.add_source_term_brick(mim, 'u', 'F')
md.set_variable('F', np.repeat(1.0, mfu.nbdof()))

# u=0 on OUTER_BOUND
md.add_Dirichlet_condition_with_multipliers(mim, 'u', elements_degree - 1, OUTER_BOUND)
```

+++

### 🏃 Solve $u$

$$−\Delta u=1 \ {\rm on}\  \Omega, u=0 \ {\rm on}\  \delta \Omega $$
```python
md.solve()
U = md.variable('u')
vtkfilename = 'displacement.vtk'
mfu.export_to_vtk(vtkfilename, mfu, U, 'Displacement')
```

+++

### 🎉 variable ***u***

![solution1](solution1.png)

+++

### theoretical solution
$$u(x, y) = \dfrac{1-x^2-y^2}{4}$$
```python
xy = mfu.basic_dof_nodes()
x = xy[0, :]
y = xy[1, :]

# theoretical solution
sol = (1-x*x-y*y)/4.0
vtkfilename = 'sol.vtk'
mfu.export_to_vtk(vtkfilename, mfu, sol, 'solution')
```

+++

### theoretical solution

![solution2](solution2.png)


+++

### 🎉 Compare

![error1](error1.png)

+++

### Summary

- ✅Overview
- ✅Installation
- ✅Tutorial
- ✅Summary

🏃 source code of [tutorial](https://github.com/tkoyama010/techbookfest-getfem/blob/master/doc/sphinx/source/unit-disk.ipynb).

+++

### Thank you 🙇

+++

### Creating an Image with Mayavi

```python
from mayavi import mlab

filename = 'solutions.png'

mlab.init_notebook()
vtk_file_reader = mlab.pipeline.open(vtkfilename)
surface = mlab.pipeline.surface(vtk_file_reader)

mlab.scalarbar(orientation='vertical')
mlab.show()
mlab.savefig(filename, magnification=2)
mlab.clf()
```

