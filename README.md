# Solving the Poisson equation on the unit circle

Nice to meet you. My name is Koyama. Thank you for giving me the opportunity to speak today.
Today I'm going to make an announcement under the title of "The Poisson equation on the unit circle is solved.".

# Who are you?

TODO: 自己紹介は発表に関係のあるものにとどめる。
First of all, who on earth are you? So let me introduce myself. I will explain the main activities.
TwitterID is @tkoyama010. This icon is active under the account name of "Get the finite element method". Please follow me if you like. Also, there is no blog, but Booth distributes technical coterie magazines. I have been developing civil engineering and architectural software and performing commissioned analysis at a CAE software vendor for about 8 years. My work includes programming in Fortran, Python, and C + +, as well as work automation using VBScript. I'm a numerical calculator. When I was a student, I was studying at the earthquake research laboratory of the architecture department.
At that time, I became interested in programming by studying the simulation of the behavior of structures. I'll post a link to the research paper, so if you're interested, you can access it later through this presentation.

# Who are you?

Since last year, he has been distributing a translation of the documentation of the OSS library GetFEM++ in the Technical Doujinshi Event Technical Book. It was the second time since I participated in Technical Book 5. He is part of the GetFEM + + project and calls himself a committer. but I have earned a lot of commits for most of my chores.
For example, I've been working on projects for about two years, doing FixTypo, refactoring, translating, and so on. I've learned a lot from just looking at the source code, but recently I wanted to add an object myself, so I added one earlier this year. We are currently adding a second object and hope to finish testing by the end of this month.
As far as I can see, I have about 50 commits at the moment, so I am aiming for 100 commits.

# Questionnaire

Well, this library GetFEM++, provides a framework for solving finite element methods. I would like to do a questionnaire here.
- If you have ever heard of the word "finite element method".
Thank you.
- If you have ever heard of the word "differential equation".
Thank you. Even if you have heard the word differential equation, I think most of you can eat it by finite element method. It's a short time, but I'd like to explain that part first.
Thank you in advance.

# What is the finite element method?

Physical phenomena such as electricity, heat, and the behavior of objects can all be expressed by equations called differential equations.
The finite element method is a method of solving differential equations.
For example, let's solve the phenomenon that the wheel touches the ground like this.
In this way, the area is divided into mesh and the calculation is performed.
The mesh is called an element, and the intersection of the mesh sides is called a node.
You must specify a finite element method and an integral method for each region of the mesh.
The finite element method calculates this unknown variable by converting it into a form of simultaneous equations.

# GetFEM++とは

C++で実装された有限要素法ライブラリです。
Pythonインターフェースがメインで使用されます。
使用時にはUbuntu上でpython-getfem++パッケージをインストールしてください。
ちなみにPython3版は対応済みですのでDebianパッケージも間もなく作成されるかと思います。
オブジェクト指向で実装されているため、オブジェクトを定義しメソッドを呼び出すだけで実行が可能です。

# リリースノート

リリースノートには各バージョンの更新情報が載っていますので、見ておくとライブラリ内の開発で今何がホットなのかがわかります。
このように、各バージョンが1年に1回リリースされています。
今は、7月ですので、もうしばらくすれば次のバージョンがリリースされるかと思います。
開発自体は90年代から行われており、後方互換性も維持しながらソースコードのリファクタリングも行われています。
現在開発版はC++14に対応済みです。

# インストール方法@Ubuntu

基本的にaptやaptitudeを使えばUbuntuにおいてはインストールはできてしまいます。
でもそれではつまらないので各種ライブラリを使ってコンパイル&インストールしてみようというのがこのスライドの内容です。

# インストール方法@Ubuntu

ここでお決まりの./configureを使います。
すると必要なライブラリがインストールされているかの確認が行われます。
GetFEM のデフォルトのユーザーインターフェースはPython2ですがPython3にオプションで切り替えることも可能です。
次のスライドで必要なライブラリを説明していきます。

# インストール方法@Ubuntu

まずは、QDとqhullです。はじめからこんなことを言うのもなんですが、QDは何に使われているのかよくわからないライブラリです。
メッセージにも特定の人しか使わないから気にするなと書かれています。
qhullは凹凸の構造計算に使用されるライブラリでこれでメッシュを切ります。

# インストール方法@Ubuntu

次にMUMPSとLAPACK/BLASとNumpy Scipyです。
ここいらあたりは有名すぎて説明する必要もないかもしれません。
libopenblas-dev を使った方が高速になるとのアドバイスをもらいました。

# インストール方法@Ubuntu

ライブラリのインストールは以上になります。
あとは make && make check && make install でコンパイル・テスト・インストールができます。
最近気づいたんですけど、make -j8 で並列コンパイルさせると高速になります。
テストがFAILになったら開発チームにメールしてあげてください。

もしもわからないときは以下のメーリングリストに英語でメールすれば答えてもらえます。
英語を使いたくなければ私に連絡していただければ可能な限りお答えします。
ただ以上は #GetFEM の内部も知りつつ使いたい場合であって、有限要素法が使えればよいのであれば。
aptでインストールされることをおすすめします。
今はWindows10でも、bashとaptが使えます。
Python3版はまだパッケージに登録されていませんが、現在申請中です。

# オブジェクトの作成手順

こちらにオブジェクトの作成の流れを示します。
まずはじめに、Mesherオブジェクトで幾何学的にどのような形も問題を解くかを指定します。
次に、Mesherオブジェクトを基に先程説明したMeshオブジェクトを作成します。
Meshオブジェクトの各メッシュに対し有限要素法と積分法をそれぞれ定義することにより、MeshFemオブジェクトとMeshImオブジェクトを作成します。
最後に、MeshFemオブジェクトとMeshImオブジェクトをModelオブジェクトに設定することによりオブジェクトが完成します。

# 今回のお題

今回はMathWork様の問題を解くことにします。
こちらに示すように、半径1.0の円があり、その円の中でこちらに示すような微分方程式が成り立っているとします。
また、円の境界部分では、(ディリクレ条件というのですけども。)、変数uが0であるとします。

# Mesherオブジェクトの作成

それでは、先程の流れにしたがってまずはMesherオブジェクトを作成するところからはじめます。

# Mesherオブジェクトの作成

まずはじめにgetfemをインポートします。
次に、MesherObject関数で引数に円を表す、'ball'と中央の座標(1.0, 1.0)、及び、半径1.0を指定し、2次元の円を作成します。
この図の通りにするならば、中央の座標は(0.0, 0.0)とするのが適切ですが、MesherObjectは負の値を設定できません。
そこで、メッシュを中央(1.0, 1.0)の部分で作っておき、後で中央の座標を(0.0, 0.0)の位置に修正することにします。

# Mesh オブジェクトの作成

次に、MesherオブジェクトからMeshオブジェクトを作成します。
まずは、メッシュのおおよその長さhを設定します。
ここでは、MathWork様のページを参考に0.5を設定します。
次に、Mesh関数でMeshオブジェクトを作成します。
引数は'generate'を指定し、先程作成したMesherオブジェクトmoとhを指定します。
更に最後に要素の次数と呼ばれるものを2に指定します。

# メッシュの移動と領域設定

これにより作成されたメッシオブジェクトの中心は先程説明したように(1.0, 1.0)にあります。
そこで、translateメソッドを使用してメッシュの位置を(0.0, 0.0)に移動します。
更に、メッシュオブジェクトに対し境界も設定しておきます。
円の境界は'u'の値が0.0になる条件とするため、その部分を境界として設定する必要がございます。
境界の設定にはset_regionメソッドを使用します。
1つ目の引数が境界の番号、2つ目の引数が境界の面番号です。
境界の面番号を覚えておくのは大変ですので、この境界番号をOUTER_BOUNDと変数に置き換えておきます。
また、2つ目の引数fbとして使用される円の境界の面番号(この場合2次元なので線ですが)はouter_facesというメソッドで取得することができます。
以上でメッシュオブジェクトの作成ができました、今回作成したのはとても簡単な形のメッシュでした。
通常、実務で使われるメッシュはより複雑なものになります。
その場合は、他のメッシュ作成ソフトで作成したメッシュをインポートしてメッシュオブジェクトを作成します。

# MeshFEMオブジェクトの作成

次に、Meshに有限要素法を割り当てる、MeshFEMオブジェクトを作成します。
オブジェクトを定義する際には前のスライドで定義した、meshオブジェクトとメッシュの交点(節点といいます。)の次元が必要です。
今回の問題では、節点の次元は1となりますので、1を設定します。
次にメッシュに有限要素法を割り当てます。
今回は、通常よく使われるLagrange要素を使用します。
メソッドはset_calssical_femを使用します。
その際には、要素次数を設定する必要があります。
先程メッシュの次数を2としていますので有限要素法の次数も2とします。
今回は、デフォルトの有限要素法を使用しましたが、GetFEM++では他の種類の有限要素法を使用することもできます。
こちらのページにありますように様々な手法が揃っていますので、ご興味のある方は後でご覧ください。

# Modelオブジェクトの作成

ここまでの説明ですでに心が折れている方もいらっしゃるかもしれませんが、ここまでで半分です。もうしばらくお付き合いください。
最後に、Modelオブジェクトを作成します。
このオブジェクトは連立方程式を解くための変数と微分方程式の項を管理するオブジェクトです。
今回は、実数のModelオブジェクトとして変数mdを定義します。
次にmdのメソッドadd_fem_variableを使用して変数を定義します。
必要な引数は変数名と対応するMeshFemオブジェクトです。
今回は式の表現に習って変数名は'u'とし、MeshFemオブジェクトは先程作成したmfuを使用します。

# Laplacian_brickの追加

次にModelオブジェクトに微分方程式の項を追加してきます。
まずは微分方程式のこちらの左辺の項を追加します。
この項は数学的にはLaplacianと呼ばれています。
項を追加する際にはモデルオブジェクトのadd_Laplacian_brickというメソッドを使用します。
必要な引数はMeshImオブジェクトと、項を追加する変数です。
MeshImオブジェクトは積分法をメッシュに割り当てるためのオブジェクトです。
MeshIm関数で定義します。
引数にはmeshオブジェクトと積分法の次数が必要です。
meshオブジェクトはMeshFEMオブジェクトと同じもの、次数は要素の次数の2乗を定義します。

# 右辺項と境界条件の設定

次にModelオブジェクトに微分方程式の右辺項を設定します。
ここでは、この1の値を変数名'F'で定義します。
左辺項を定義した際にはadd_fem_variableメソッドを使用していましたが、右辺項を指定する場合にはset_fem_dataメソッドを使用します。
variableメソッドのときと同様に変数名とMeshFEMオブジェクトを指定します。
その上で、add_source_term_brickメソッドでソース項'F'を、変数'u'に対して与えます。
ソース項'F'は問題を解く前から既にわかっている値ですので、値1.0を設定する必要があります。
変数に値を設定する際にはset_variableメソッドを使用して値1.0のベクトルを設定します。
最後に境界部分のu=0の条件を設定します。
これはDirichlet条件と呼ばれており、add_Dirichlet_condition_with_multipliersメソッドを使用します。
引数はそれぞれ、MeshImオブジェクトのmimと先程追加した変数'u'、Dirichlet条件の係数として使用される値(これは通常要素次数から1を引いたものです。)
さらに、先程Meshオブジェクト作成の際に指定した領域番号OUTER_BOUNDを指定します。

# 未知変数'u'の計算

長くなりましたが、以上でModelオブジェクトを完成させることができました。
問題を解く際にはModelオブジェクトのsolveメソッドを呼び出すことにより問題を解くことができます。
解いた後はvariableメソッドで変数名を指定してnumpy.arrayの型の変数データを取得することができます。
この結果を表示する際には前のご発表で紹介されていたMayaviでの結果表示が推奨されています。
その際にはMeshFemのvtk出力機能を使用します。
使用するメソッドはexport_to_vtkです。
vtkのファイル名とMeshFemオブジェクト、取得した変数'U'、とvtkファイルに出力する変数名を指定します。

# 未知変数'u'の計算

出力したvtkファイルをMayaviで表示させた結果がこちらです。
ご覧のように中央から同心円状に値が分布していることが分かります。

# 理論解の計算

この問題の理論解はこちらのように表されます。
この理論解との比較をすることで今回計算した結果が妥当であるかを確認しようと思います。
まずはじめに、各節点の座標をnumpy.arrayで取得します。
MeshFemオブジェクトのbasic_dof_nodesメソッドで各節点の座標をnumpy.arrayで取得することができます。
座標が分かりましたので、これを基に理論解を計算しvtkファイルに出力して比較してみます。

# 理論解の計算

先程の結果と同様に同心円状に値が分布していることが分かります。

# 計算結果と理論解の比較
計算した結果と理論解を比較してみます。
その結果、最も誤差が大きい部分でもその差は10^-6のオーダーであることが分かります。

# MayaviによるJupyterNotebookでの画像の作成

さて、まだ時間がありますので、こちらの図をMayaviでどのように作成したかご説明しておきます。
こちらは、MayaviをJupyter上で動かして画像を作成しています。
まず、mayaviからmlabをimportします。
次に、init_notebook()メソッドでmlabの情報を初期化します。
初期化終了後、mlab.pipleline.open()メソッドでvtkファイルを表示させます。
その上で、mlab.pipeline.surfaceという関数を使うと表面のコンターが表示されます。
scalarbar、show、savefigそれぞれのメソッドを使って先程のような図を表示させ画像を保存することができます。
Mayaviでvtkファイルの結果を表示させる方法はなかなか見つかりませんでしたので、ご参考に慣れば幸いです。

# まとめ

私の発表は以上になりますので、最後にまとめをさせていただきます。
まずはじめに有限要素法とはどのようなものかについて簡単に紹介させていただきました。
この手法は空間をメッシュで分割することによりそのメッシュ空間上の微分方程式を解く手法です。
次に、GetFEMの内部構造について簡単に説明しました。
まずはじめにMesherオブジェクトを作成し、それを基にMeshオブジェクトを作成します。
Meshオブジェクトを作成したら、それを基にメッシュに有限要素法を割り当てるMeshFemオブジェクトと積分法を割り当てるMeshImオブジェクトを作成します。
最後にMeshFemオブジェクトとMeshImオブジェクトを使ってModelオブジェクトに微分方程式と境界条件を構築していきます。
構築が終了した後は、solveメソッドを使用して未知変数を計算し、real_variableで変数の値を取得します。
MeshFEMのメソッドで結果を出力しMayaviで結果をプロットすることが可能です。
最後に宣伝をさせていただきます。
このような解析の例が載った技術系同人誌を頒布中です。
内容は、公式の英語のチュートリアルを日本語に翻訳したものになっております。
ご興味のある方はこちらも読んでみてください。
また、同人誌の翻訳の際にはTransifexというサイトで翻訳をしております。
翻訳活動への参加いただける方は歓迎いたしますので、ご連絡ください。
また、同じプロジェクト内でMayaviの日本語訳のプロジェクトも進めています。
こちらは、Mayaviの開発者の方からGitHubで了解をいただきまして翻訳を進めています。
こちらもよろしくお願いいたします。
以上、ご清聴ありがとうございました。
