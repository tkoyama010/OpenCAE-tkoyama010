# Contribution for GetFEM++ Project

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
- Object structure of brick
- Object structure of transient term
- Adding lumped mass matrix
- Adding Houbolt method
- Example
- Conculution

+++

### Questionnaire

✋ 「有限要素法」という言葉を聞いたことのある方

✋ 「微分方程式」という言葉を聞いたことのある方

+++

### What is the finite element method (FEM)?

- 物理現象(電気、熱、弾性体etc) の挙動は全て微分方程式で表現することができます。
- FEMは微分方程式を解く手法の1つです。

![mesh2](mesh2.png)

+++

### What is the finite element method (FEM)?

![mesh2](http://getfem.org/_images/getfemuserlinearsys.png)

+++

### What is GetFEM++?

- 微分方程式を解く手法の一つ有限要素法を解くフレームワークを提供するライブラリです。
- FEM関連のオブジェクトをPythonで手軽に扱えます。

![shape3](http://getfem.org/_images/shape3.png)
![shape4](http://getfem.org/_images/shape4.png)

+++

### Release Notes

[リリースノートバージョン5.3](http://getfem.org/whatsnew/5.3.html) @2018/06/22

[リリースノートバージョン5.2](http://getfem.org/whatsnew/5.2.html) @2017/06/30

[リリースノートバージョン5.1](http://getfem.org/whatsnew/5.1.html) @2016/08/18

[リリースノートバージョン5.0](http://getfem.org/whatsnew/5.0.html) @2015/07/29

[リリースノートバージョン4.3](http://getfem.org/whatsnew/4.3.html) @2014/07/14

- 開発は90年代から行われています。
- 後方互換性を維持しながらソースコードのリファクタリングも行われています。
- 現在開発版はC++14に対応済みです。

+++

### Installation Instructions@Ubuntu

- これだけ！！！
```bash
sudo apt install python-getfem++
```

- これだと話が終わるし中身もわからないということで・・・wgetでソースコードを取得します。

```bash
wget http://download-mirror.savannah.gnu.org/releases/getfem/stable/
getfem-5.3.tar.gz
```

+++

### Installation Instructions@Ubuntu

- 解凍後フォルダでお決まりの以下のコマンドを実行します。
```bash
./configure
```

- --with-picオプション　64bit版コンパイルのためのオプション
- --enable-python3=yes python2ではなくpython3を使うというオプション

+++

### Libraries Used

- [QD](https://bitbucket.org/njet/qd-library/src/master/) 特定の人しか使わないから気にするなと表示されます。入れなくてもコンパイルできます。何のライブラリ何だろう・・・
- [Qhull](http://www.qhull.org/)凹凸の構造計算に使用されるライブラリです。これによりメッシュ生成が可能になります。
![QhullLogo](http://www.qhull.org/html/qh--cone.gif)

+++

### Libraries Used

- [MUMPS](http://mumps.enseeiht.fr/) 巨大な疎行列を計算するためのソフトウェア。並列計算に使用される。
- [LAPACK/BLAS](http://www.netlib.org/lapack/) 線形計算のための数値解析ソフトウェアライブラリ
- [Numpy/Scipy](https://www.scipy.org/) Pythonの数値計算拡張モジュール

- 以上のライブラリをインストールしたら、以下のコマンドでコンパイル・テスト・インストールが行われます。
```bash
make && make check && sudo make install
```

+++

![getfem_structure1](http://getfem.org/_images/getfem_structure1.png)

+++

### Topic

- [MathWork](https://jp.mathworks.com/help/pde/ug/solve-poissons-equation-on-a-unit-disk.html)様の問題を解いてみます。
$$−\Delta u=1 \ {\rm on}\  \Omega, u=0 \ {\rm on}\  \delta \Omega $$

![pdedemo1_01](https://jp.mathworks.com/help/examples/pde/win64/pdedemo1_01.png)

+++

### Mesher Object

- Mesherオブジェクトでジオメトリを作成してきます。
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
