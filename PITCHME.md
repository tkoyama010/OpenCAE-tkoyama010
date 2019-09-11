# Introduction to FEM Analysis with Python

[@tkoyama010](https://twitter.com/tkoyama010)

+++

### Who are you?

- @tkoyama010: [Twitter](https://twitter.com/tkoyama010), [Booth](https://tkoyama010.booth.pm/)
- I have been developing civil engineering and architectural software and conducting contract analysis with a software vendor of the finite element method for about 8 years.(Fortran, Python, C++, VBScript etc)
- I'm a graduate of the Earthquake Engineering Department of Architecture.
 - [IMPROVEMENT OF CALCULATION PRECISION OF THEORETICAL GROUND MOTIONS USING THIN LAYER METHOD AND ITS VERIFICATION, PART 2](https://www.jstage.jst.go.jp/article/aijt/17/35/17_35_55/_article/-char/ja/)
 - [EVALUATION OF PERMANENT GROUND DISPLACEMENT ADJACENT TO SEISMIC FAULT USING THIN LAYER METHOD](https://www.jstage.jst.go.jp/article/aijt/22/50/22_17/_pdf)

+++

### Who are you?

- Since last year, I have been distributing translated books of [GetFEM++](https://ja.wikipedia.org/wiki/GetFEM%2B%2B) in the event technical book of [techbookfest](https://techbookfest.org/).
- 😝I'm a GetFEM + + committer, but I've been earning commits for odd jobs. (Fix types, refactoring, translation tasks, etc.)
- I have recently started to add objects because I understand the internal structure quite well.
  - lumped mass brick object
  - Houbolt method objects
- 😤[contributing status](https://github.com/tkoyama010/getfem/graphs/contributors) my first goal is 100 commits.

+++

### Agenda
- Overview of the Finite Element Method and GetFEM
- Installation Instructions
- How to solve problems using examples
- About Mayavi
- Summary

+++

### Questionnaire

✋ If you've ever heard of the word "finite element method"

✋ If you've ever heard of the word "differential equation"

+++

### What is the finite element method (FEM)?

- The behavior of all physical phenomena (electrical, thermal, elastic, etc.) can be expressed by differential equations.
- FEM is a method of solving differential equations.

![mesh2](mesh2.png)

+++

### What is the finite element method (FEM)?

![mesh2](http://getfem.org/_images/getfemuserlinearsys.png)

+++

### What is GetFEM++?

- This library provides a framework for solving the finite element method, one of the methods for solving differential equations.
- FEM is a method of solving differential equations.

![shape3](http://getfem.org/_images/shape3.png)
![shape4](http://getfem.org/_images/shape4.png)

+++

### Release Notes

[Release Notes Version 5.3](http://getfem.org/whatsnew/5.3.html) @2018/06/22

[Release Notes Version 5.2](http://getfem.org/whatsnew/5.2.html) @2017/06/30

[Release Notes Version 5.1](http://getfem.org/whatsnew/5.1.html) @2016/08/18

[Release Notes Version 5.0](http://getfem.org/whatsnew/5.0.html) @2015/07/29

[Release Notes Version 4.3](http://getfem.org/whatsnew/4.3.html) @2014/07/14

- 開発は90年代から行われています。
- 後方互換性を維持しながらソースコードのリファクタリングも行われています。
- 現在開発版はC++14に対応済みです。

+++

### Installation Instructions@Ubuntu

```bash
sudo apt install python-getfem++
```

- THAT'S ALL!!
- This is the end of the story and we don't know what's inside... so I get the source code with wget.

```bash
wget http://download-mirror.savannah.gnu.org/releases/getfem/stable/
getfem-5.3.tar.gz
```

+++

### Installation Instructions@Ubuntu

- Run the usual commands in the unzipped folder:.
```bash
./configure
```

- --with-pic : options for 64 bit compilation
- --enable-python3=yes : Option to use python3 instead of python2

+++

### Libraries Used

- [QD](https://bitbucket.org/njet/qd-library/src/master/) It says don't worry because only certain people use it. You can compile without it. What library, what...
- [Qhull](http://www.qhull.org/) A library used to compute the structure of convexs. This allows for mesh generation.
![QhullLogo](http://www.qhull.org/html/qh--cone.gif)

+++

### Libraries Used

- [MUMPS](http://mumps.enseeiht.fr/) Library for computing large sparse matrices. Used for parallel calculation.
- [LAPACK/BLAS](http://www.netlib.org/lapack/) Numerical Analysis Software Library for Linear Computing
- [Numpy/Scipy](https://www.scipy.org/) Numerical computation extension for Python

- Once these libraries are installed, a compile test and installation is performed with the following command:.
```bash
make && make check && sudo make install
```

+++

![getfem_structure1](http://getfem.org/_images/getfem_structure1.png)

+++

### Topic

- Problem of [MathWork](https://jp.mathworks.com/help/pde/ug/solve-poissons-equation-on-a-unit-disk.html)
$$−\Delta u=1 \ {\rm on}\  \Omega, u=0 \ {\rm on}\  \delta \Omega $$

![pdedemo1_01](https://jp.mathworks.com/help/examples/pde/win64/pdedemo1_01.png)

+++

### Mesher Object

- Creates the geometry by Mesher object .
```python
import getfem as gf
mo = gf.MesherObject('ball', [1.0, 1.0], 1.0)
```

![pdedemo1_01](https://jp.mathworks.com/help/examples/pde/win64/pdedemo1_01.png)

+++

### Mesh object

- Mesherオブジェクトのジオメトリ情報を基に、メッシュオブジェクトを作成します。
```python
# メッシュのおおよその長さ
h = 0.1
# 2次要素を指定してメッシュオブジェクトを作成する
mesh = gf.Mesh('generate', mo, h, 2)
```

![mesh1](mesh1.png)

+++

### Move and Region Mesh

- メッシュの中心位置を(0.0, 0.0)に移動します。
```python
mesh.translate([-1.0, -1.0])
```
- あとで境界条件を設定するための領域も設定します。
```python
fb = mesh.outer_faces()
OUTER_BOUND = 1
mesh.set_region(OUTER_BOUND, fb)
```

![mesh2](mesh2.png)

+++

### Creating a MeshFem Object

- メッシュに有限要素法を割り当てるオブジェクトMeshFEMを作成します。節点の自由度は1とします。
```python
mfu = gf.MeshFem(mesh, 1)
```
- 今回は通常の[Lagrange要素](http://getfem.org/userdoc/appendixA.html#classical-lagrange-elements-on-simplices)を使用します。要素次数は2とします。
```python
elements_degree = 2
mfu.set_classical_fem(elements_degree)
```

+++

### Model object

- 連立方程式で問題を解くための[Modelオブジェクト](http://getfem.org/userdoc/model_object.html)を作成します。
```python
md = gf.Model('real')
```
- MeshFemオブジェクトを使用して変数'u'を追加します。
```python
md.add_fem_variable('u', mfu)
```

+++

### Laplacian_brick

- メッシュに積分法を割り当てるMeshImオブジェクトを作成します。
```python
mim = gf.MeshIm(mesh, pow(elements_degree,2))
```
- Modelオブジェクトに微分方程式の左辺項を追加します。
$$−\Delta u=1 \ {\rm on}\  \Omega$$
```python
md.add_Laplacian_brick(mim, 'u')
```

+++

### Setting various conditions

- Modelオブジェクトに微分方程式の右辺項を設定します。
$$−\Delta u=1 \ {\rm on}\  \Omega$$
- 変数名は'F'とします。
```python
import numpy as np
md.add_fem_data('F', mfu)
md.add_source_term_brick(mim, 'u', 'F')
md.set_variable('F', np.repeat(1.0, mfu.nbdof()))
```
- 境界部分の条件$u=0$(Dirichlet条件)を設定します。
```python
md.add_Dirichlet_condition_with_multipliers(
    mim, 'u', elements_degree - 1, OUTER_BOUND
)
```

+++

### Calculation of unknown variable 'u'

- Modelオブジェクトが完成しましたので、solveメソッドで未知変数'u'を計算します。
```python
md.solve()
U = md.variable('u')
vtkfilename = 'displacement.vtk'
mfu.export_to_vtk(vtkfilename, mfu, U, 'Displacement')
```

+++

### Calculation of unknown variable 'u'

![solution1](solution1.png)

+++

### theoretical solution
- 理論解は次式で表されます。
$$u(x, y) = \dfrac{1-x^2-y^2}{4}$$
- 各節点の座標をnumpy.arrayで取得し理論解を計算します。
```python
xy = mfu.basic_dof_nodes()
x = xy[0, :]
y = xy[1, :]
sol = (1-x*x-y*y)/4.0
vtkfilename = 'sol.vtk'
mfu.export_to_vtk(vtkfilename, mfu, sol, 'solution')
```

+++

### theoretical solution

![solution2](solution2.png)


+++

### Comparison between calculated results and theoretical solutions

![error1](error1.png)

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
- 後は、保存した画像を使って[JupyterNotebook](https://github.com/tkoyama010/techbookfest-getfem/blob/master/doc/sphinx/source/unit-disk.ipynb) 上で画像を表示します。

+++

### Summary

- 有限要素法について説明をしました。
- GetFEMの内部構造について説明をしました。
- TransifexでGetFEM++のドキュメントとMayaviのドキュメントを[翻訳中](https://www.transifex.com/getfem-doc/)です。協力していただける方は歓迎いたします。
- 技術書典7でもGetFEM++のドキュメントの翻訳本を出す予定です。
- Mayaviのドキュメントを翻訳したものは[こちら](https://mayavi-ja.readthedocs.io/ja/latest/)で公開中です。
- 今日の内容はこちらの[JupyterNotebook](https://github.com/tkoyama010/techbookfest-getfem/blob/master/doc/sphinx/source/unit-disk.ipynb)で実行したものです。
