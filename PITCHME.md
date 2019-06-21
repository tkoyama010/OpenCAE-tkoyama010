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
- Numpy/Scipyのarrayオブジェクトを扱うようにFEM関連のオブジェクトを手軽に扱えます。
![shape3](http://getfem.org/_images/shape3.png)
![shape4](http://getfem.org/_images/shape4.png)

+++

### 今回のお題

- [MathWork](https://jp.mathworks.com/help/pde/ug/solve-poissons-equation-on-a-unit-disk.html)様の問題を解いてみます。
$$−\Delta u=1 \ {\rm on}\  \Omega, u=0 \ {\rm on}\  \delta \Omega $$

![pdedemo1_01](https://jp.mathworks.com/help/examples/pde/win64/pdedemo1_01.png)

+++

### Mesherオブジェクトの作成

- Mesherオブジェクトでジオメトリを作成してきます。今回は単位円の問題ですので、中心を(1.0, 1.0)とした半径1.0の円を描きます。


```
import getfem as gf
mo = gf.MesherObject('ball', [1.0, 1.0], 1.0)
```

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

### 

+++
