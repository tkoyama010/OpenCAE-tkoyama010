---
### @size[1.5em](GetFEM++ Contribution in 2019 @emoji[em-christmas_tree])

[@tkoyama010](https://twitter.com/tkoyama010)

+++

### Who am I? Abount @tkoyama010 @emoji[heart_eyes]

- @tkoyama010: [Twitter](https://twitter.com/tkoyama010), [Booth](https://tkoyama010.booth.pm/)
- career: Numerical analysis
- skills: Fortran, Python, C++, VBScript etc

+++

### Who am I? Abount @tkoyama010 @emoji[heart_eyes]

- 👨‍💻 Contributor to [GetFEM++](https://ja.wikipedia.org/wiki/GetFEM%2B%2B).
- 👨‍💻 Add two object.
  - 🚧lumped mass brick object🚧
  - 🚧Houbolt method objects🚧
- 😤[contributing status](https://github.com/getfem-doc/getfem/graphs/contributors) my first goal is 100 commits.

+++

### Agenda

- ✅Overview
- ✅Installation
- ✅Tutorial
- ✅Development
- ✅Summary

+++

### @size[2.0em](🤔) What is PDE and FEM❓

❗ PDE is a equation using in physics, engineering.

$$−\Delta u=1$$

+++

### @size[2.0em](🤔) What is PDE and FEM❓

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

![mesh1](mesh1.png)

+++

### @size[2.0em](📗) Using Library

@size[2.0em](💁) Compile Test Install　

```bash
$ make && make check && sudo make install
```
+++

### 🏃 Tutorial

![pdedemo1_01](https://jp.mathworks.com/help/examples/pde/win64/pdedemo1_01.png)

$$−\Delta u=1 \ {\rm on}\  \Omega, u=0 \ {\rm on}\  \delta \Omega $$

+++

### 🏃 Mesher Object

![pdedemo1_01](https://jp.mathworks.com/help/examples/pde/win64/pdedemo1_01.png)

```python
import getfem as gf
mo = gf.MesherObject('ball', [1.0, 1.0], 1.0)
```

+++

### 🏃 Mesh object

![mesh1](mesh1.png)

```python
# Approximate mesh length
h = 0.1
# Create Mesh Objects with second order elements
mesh = gf.Mesh('generate', mo, h, 2)
```

+++

### 🏃 Move Mesh and Set Region

![mesh2](mesh2.png)

```python
mesh.translate([-1.0, -1.0])
fb = mesh.outer_faces()
OUTER_BOUND = 1
mesh.set_region(OUTER_BOUND, fb)
```

+++

### 🏃 [MeshFem](http://getfem.org/python/cmdref_MeshFem.html?highlight=meshfem%20object) Object

![Lagrange Element](https://raw.githubusercontent.com/tkoyama010/OpenCAE-tkoyama010/PyConJP2019/getfemlisttriangleP2.png)

```python
# The node has 1 degree of freedom
mfu = gf.MeshFem(mesh, 1)
elements_degree = 2

# classical lagrange elements
mfu.set_classical_fem(elements_degree)
```

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
to
$$[K] \left\\{ u \right\\} = \left\\{ F \right\\}$$
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

$$[K] \left\\{ u \right\\} = \left\\{ F \right\\}$$

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

### 🎉 Error

![error1](error1.png)

+++
@snap[west span-60]
### 🚧 Development 🚧
lumped mass brick for first order
@snapend

@snap[east span-40]
@uml[span-100 bg-white](lumped_mass_brick.puml)

+++
@snap[west span-60]
### 🚧 Development 🚧
Houbolt scheme
@snapend

@snap[east span-40]
@uml[span-100 bg-white](houbolt.puml)

+++

### Summary

- ✅Overview
- ✅Installation
- ✅Tutorial
- ✅Development
- ✅Summary

@size[2.0em](🏃) source code of [tutorial](https://github.com/tkoyama010/techbookfest-getfem/blob/master/doc/sphinx/source/unit-disk.ipynb).

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

+++

### Thank you @size[2.0em](🙇)
