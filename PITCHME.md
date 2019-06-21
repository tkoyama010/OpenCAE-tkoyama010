# GetFEM++入門

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
- Pythonインターフェースが充実しており、Numpy/Scipyのように有限要素法を解くことができます。
![shape3](http://getfem.org/_images/shape3.png)
![shape4](http://getfem.org/_images/shape4.png)

+++
