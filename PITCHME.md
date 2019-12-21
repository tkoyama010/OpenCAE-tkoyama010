---
### @size[1.5em](GetFEM++ Contribution in @emoji[em em-bell] 2019 @emoji[em em-bell])

[@tkoyama010](https://twitter.com/tkoyama010)

+++

### Who am I? Abount @tkoyama010 @emoji[heart_eyes]

- @fab[github] @fab[twitter] [@tkoyama010](https://twitter.com/tkoyama010)
- career: Numerical analysis
- skills: Fortran, Python, C++, VBScript etc

+++

### Who am I? Abount @tkoyama010 @emoji[heart_eyes]

- @emoji[gift] Contributor to [GetFEM++](https://ja.wikipedia.org/wiki/GetFEM%2B%2B).
- @emoji[gift] Add two object.
  - @emoji[construction]lumped mass brick object@emoji[construction]
  - @emoji[construction]Houbolt method objects@emoji[construction]
- @emoji[star][contributing status](https://github.com/getfem-doc/getfem/graphs/contributors) my first goal is 100 commits.

+++

### Agenda

- @emoji[white_check_mark]Overview
- @emoji[white_check_mark]Installation
- @emoji[white_check_mark]Tutorial
- @emoji[white_check_mark]Development
- @emoji[white_check_mark]Summary

+++

### @emoji[question] What is PDE and FEM @emoji[question]

@emoji[exclamation] PDE is a equation using in physics, engineering.

$$−\Delta u=1$$

+++

### @emoji[question] What is PDE and FEM @emoji[question]

@emoji[exclamation] FEM is a method of solving PDE.

$$[K] \left\\{ u \right\\} = \left\\{ F \right\\}$$

+++

### @emoji[question] What is GetFEM++ @emoji[question]

@emoji[star] This library provides a framework for solving PDE by FEM.

![shape3](http://getfem.org/_images/shape3.png)
![shape4](http://getfem.org/_images/shape4.png)

+++

### @emoji[books]Release Notes

[Release Notes Version 5.3](http://getfem.org/whatsnew/5.3.html) @2018/06/22

[Release Notes Version 5.2](http://getfem.org/whatsnew/5.2.html) @2017/06/30

[Release Notes Version 5.1](http://getfem.org/whatsnew/5.1.html) @2016/08/18

[Release Notes Version 5.0](http://getfem.org/whatsnew/5.0.html) @2015/07/29

[Release Notes Version 4.3](http://getfem.org/whatsnew/4.3.html) @2014/07/14

@size[2.0em](@emoji[flushed]) Development has continued since the 1990s @emoji[exclamation]

+++

### @emoji[children_crossing] Installation @fab[linux]

It is easy to install by apt.

```bash
$ sudo apt install python-getfem++
```

...but

@size[2.0em](@emoji[laughing]) Let's install from source code to study @emoji[exclamation]

```
$ wget http://download-mirror.savannah.gnu.org/releases/getfem/stable/getfem-5.3.tar.gz
```
+++

### @emoji[beginner] Installation @fab[linux]

- Run ***./configure***
  - --with-pic @emoji[bulb] options for 64 bit compilation
  - --enable-python3=yes @emoji[bulb] option to use python3
```bash
$ ./configure --with-pic --enable-python3=yes
```

+++

### @emoji[beginner] Using Library

- @emoji[bookmark] [Qhull](http://www.qhull.org/) @emoji[bulb] mesh generation.
- @emoji[bookmark] [MUMPS](http://mumps.enseeiht.fr/) @emoji[bulb] parallel calculation.
- @emoji[bookmark] [LAPACK/BLAS](http://www.netlib.org/lapack/) @emoji[bulb] Linear Algebra
- @emoji[bookmark] [Numpy/Scipy](https://www.scipy.org/) @emoji[bulb] Numerical computation

![mesh1](mesh1.png)

+++

### @emoji[beginner] Using Library

@emoji[arrow_forward] Compile @emoji[arrow_forward] Test @emoji[arrow_forward] Install　

```bash
$ make && make check && sudo make install
```
+++

### @emoji[feet] Tutorial

![pdedemo1_01](https://jp.mathworks.com/help/examples/pde/win64/pdedemo1_01.png)

$$−\Delta u=1 \ {\rm on}\  \Omega, u=0 \ {\rm on}\  \delta \Omega $$

+++

### @emoji[feet] Mesher Object

![pdedemo1_01](https://jp.mathworks.com/help/examples/pde/win64/pdedemo1_01.png)

```python
import getfem as gf
mo = gf.MesherObject('ball', [1.0, 1.0], 1.0)
```

+++

### @emoji[feet] Mesh object

![mesh1](mesh1.png)

```python
# Approximate mesh length
h = 0.1
# Create Mesh Objects with second order elements
mesh = gf.Mesh('generate', mo, h, 2)
```

+++

### @emoji[feet] Move Mesh and Set Region

![mesh2](mesh2.png)

```python
mesh.translate([-1.0, -1.0])
fb = mesh.outer_faces()
OUTER_BOUND = 1
mesh.set_region(OUTER_BOUND, fb)
```

+++

### @emoji[feet] [MeshFem](http://getfem.org/python/cmdref_MeshFem.html?highlight=meshfem%20object) Object

![Lagrange Element](https://raw.githubusercontent.com/tkoyama010/OpenCAE-tkoyama010/PyConJP2019/getfemlisttriangleP2.png)

```python
# The node has 1 degree of freedom
mfu = gf.MeshFem(mesh, 1)
elements_degree = 2

# classical lagrange elements
mfu.set_classical_fem(elements_degree)
```

+++

### @emoji[feet] [Model Object](http://getfem.org/userdoc/model_object.html)

$$[K] \left\\{ u \right\\} = \left\\{ F \right\\}$$

```python
md = gf.Model('real')
# Add the variable 'u' using the MeshFem object.
md.add_fem_variable('u', mfu)
```

+++

### @emoji[feet] Laplacian Brick, RHS and condition 

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

### @emoji[feet] Solve $u$

$$[K] \left\\{ u \right\\} = \left\\{ F \right\\}$$

```python
md.solve()
U = md.variable('u')
vtkfilename = 'displacement.vtk'
mfu.export_to_vtk(vtkfilename, mfu, U, 'Displacement')
```

+++

### @emoji[confetti_ball] variable ***u***

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

### @emoji[confetti_ball] Error

![error1](error1.png)

+++
@snap[west span-60]
### @emoji[construction] Development @emoji[construction]
lumped mass brick for first order
@snapend

@snap[east span-40]
@uml[span-100 bg-white](lumped_mass_brick.puml)

+++
@snap[west span-60]
### @emoji[construction] Development @emoji[construction]
Houbolt scheme
@snapend

@snap[east span-40]
@uml[span-100 bg-white](houbolt.puml)

+++

### Summary

- @emoji[white_check_mark]Overview
- @emoji[white_check_mark]Installation
- @emoji[white_check_mark]Tutorial
- @emoji[white_check_mark]Development
- @emoji[white_check_mark]Summary

@size[2.0em](@emoji[feet]) source code of [tutorial](https://github.com/tkoyama010/techbookfest-getfem/blob/master/doc/sphinx/source/unit-disk.ipynb).

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

### Thank you @size[2.0em](@emoji[bow])
