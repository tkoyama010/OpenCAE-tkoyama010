# 単位円上のポアソン方程式を解いてみた

[@tkoyama010](https://twitter.com/tkoyama010)

+++

### お前、誰よ

- @tkoyama010: [Twitter](https://twitter.com/tkoyama010), [Booth](https://tkoyama010.booth.pm/)
- 😀8年ほどCAEのソフトウェアベンダーで土木建築系ソフトの開発と受託解析をしてます。(Fortran, Python, C++, VBScriptなど。)
- 建築学科の地震系の研究室出身です。
 - [薄層法を用いた理論地震動の計算精度向上とその検証　その2](https://www.jstage.jst.go.jp/article/aijt/17/35/17_35_55/_article/-char/ja/)
 - [薄層法を用いた震源断層近傍における地盤の永久変位評価](https://www.jstage.jst.go.jp/article/aijt/22/50/22_17/_pdf)

+++

### お前、誰よ

- 昨年から技術系同人誌イベント[技術書典](https://techbookfest.org/)で[GetFEM++](https://ja.wikipedia.org/wiki/GetFEM%2B%2B)の翻訳本を頒布しています。
- 😝一応コミッターですが、雑用でコミット数を稼いできました。(Fix typo, リファクタリング, 翻訳作業のためのドキュメント追加, etc...)
- 最近はだいぶ内部構造を理解してきたため、GetFEM++にオブジェクトを追加し始めています。
  - 集中質量ブリックオブジェクト(追加済)
  - Houbolt法オブジェクト(テスト中)
- 😤[コントリビュート状況](https://github.com/tkoyama010/getfem/graphs/contributors)まずは100コミットが目標です。

+++

### GetFEM++とは

- 微分方程式を解く手法の一つ有限要素法を解くフレームワークを提供するライブラリです。
- FEM関連のオブジェクトをPythonで手軽に扱えます。
![shape3](http://getfem.org/_images/shape3.png)
![shape4](http://getfem.org/_images/shape4.png)

+++

### 今回のお題

- [MathWork](https://jp.mathworks.com/help/pde/ug/solve-poissons-equation-on-a-unit-disk.html)様の問題を解いてみます。
$$−\Delta u=1 \ {\rm on}\  \Omega, u=0 \ {\rm on}\  \delta \Omega $$

![pdedemo1_01](https://jp.mathworks.com/help/examples/pde/win64/pdedemo1_01.png)

+++

### Mesherオブジェクトの作成

- Mesherオブジェクトでジオメトリを作成してきます。


```
import getfem as gf
mo = gf.MesherObject('ball', [1.0, 1.0], 1.0)
```

![pdedemo1_01](https://jp.mathworks.com/help/examples/pde/win64/pdedemo1_01.png)

+++

### Meshオブジェクトの作成

- Mesherオブジェクトのジオメトリ情報を基に、メッシュオブジェクトを作成します。
```
# メッシュのおおよその長さ
h = 0.1
# 2次要素を指定してメッシュオブジェクトを作成する
mesh = gf.Mesh('generate', mo, h, 2)
```

![mesh1](mesh1.png)

+++

### メッシュの移動と領域設定

- メッシュの中心位置を(0.0, 0.0)に移動します。
```
mesh.translate([-1.0, -1.0])
```
- あとで境界条件を設定するための領域も設定します。
```
fb = mesh.outer_faces()
OUTER_BOUND = 1
mesh.set_region(OUTER_BOUND, fb)
```

![mesh2](mesh2.png)

+++

### MeshFemオブジェクトの作成

- メッシュに有限要素法を割り当てるオブジェクトMeshFEMを作成します。節点の自由度は1とします。
```
mfu = gf.MeshFem(mesh, 1)
```
- 今回は通常の[Lagrange要素](http://getfem.org/userdoc/appendixA.html#classical-lagrange-elements-on-simplices)を使用します。要素次数は2とします。
```
elements_degree = 2
mfu.set_classical_fem(elements_degree)
```

+++

### Modelオブジェクトの作成😫

- 連立方程式で問題を解くための[Modelオブジェクト](http://getfem.org/userdoc/model_object.html)を作成します。
```
md = gf.Model('real')
```
- MeshFemオブジェクトを使用して変数'u'を追加します。
```
md.add_fem_variable('u', mfu)
```

+++

### Laplacian_brickの追加

- メッシュに積分法を割り当てるMeshImオブジェクトを作成します。
```
mim = gf.MeshIm(mesh, pow(elements_degree,2))
```
- Modelオブジェクトに微分方程式の左辺項を追加します。
$$−\Delta u=1 \ {\rm on}\  \Omega$$
```
md.add_Laplacian_brick(mim, 'u')
```

+++

### 各種条件の設定

- Modelオブジェクトに微分方程式の右辺項を設定します。
$$−\Delta u=1 \ {\rm on}\  \Omega$$
- 変数名は'F'とします。
```
import numpy as np
md.add_fem_data('F', mfu)
md.add_source_term_brick(mim, 'u', 'F')
md.set_variable('F', np.repeat(1.0, mfu.nbdof()))
```
- 境界部分の条件$u=0$(Dirichlet条件)を設定します。
```
md.add_Dirichlet_condition_with_multipliers(
    mim, 'u', elements_degree - 1, OUTER_BOUND
)
```

+++

### 未知変数'u'の計算

- Modelオブジェクトが完成しましたので、solveメソッドで未知変数'u'を計算します。
```
md.solve()
U = md.variable('u')
vtkfilename = 'displacement.vtk'
mfu.export_to_vtk(vtkfilename, mfu, U, 'Displacement')
```

+++

### 未知変数'u'の計算

![solution1](solution1.png)

+++

### 理論解の計算
- 理論解は次式で表されます。
$$u(x, y) = \dfrac{1-x^2-y^2}{4}$$
- 各節点の座標をnumpy.arrayで取得し理論解を計算します。
```
xy = mfu.basic_dof_nodes()
x = xy[0, :]
y = xy[1, :]
sol = (1-x*x-y*y)/4.0
vtkfilename = 'sol.vtk'
mfu.export_to_vtk(vtkfilename, mfu, sol, 'solution')
```

+++

### 理論解の計算

![solution2](solution2.png)


+++

### 計算結果と理論解の比較

+++

![error1](error1.png)

+++
