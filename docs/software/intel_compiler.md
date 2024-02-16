---
id: intel_compiler
title: "C/C++の使い方 (Intel Compiler)"
---

遺伝研スパコンではIntel OneAPI DPC++/C++コンパイラが利用可能です。

|コンパイラ名称 | バージョン |システム導入年月日|
|--------------|-----------|----------------|
|インテル OneAPI DPC++/C++コンパイラ| 2024.0 | |

Intelコンパイラにはデフォルトでパスが通っています。

また、遺伝研スパコンではインテルのCPUを提供しているのは、Type 2aの計算ノードになります。高度な最適化を行ったバイナリを実行する場合は、インテルCPUを搭載した計算ノードで実行してください。

### コンパイラコマンド形式

|言語 |コマンド |実行形式|
|-----|--------|-------|
|C  | icx    | icx [オプション] ファイル名 |
|C++  | icpx    | icpx [オプション] ファイル名 |

### 主なオプション

インテルコンパイラで利用可能な主なオプションの概要について以下に示します。

| オプション名 |説明|
|-------------|----|
|-o FILENAME | オブジェクトファイルの名前を指定します。 |
|-mcmodel=medium|2Gbyteを超えてメモリを利用できるようになります。|
|-shared-intel|インテルが提供するライブラリをすべて動的にリンクします。|
|-qopenmp | OpenMP指示子を有効にしてコンパイルします。|
|-qmkl | MKLライブラリをリンクします。 |
|-parallel | 自動並列化を行います。|
|-O0 / -O1 / -O2 / -O3 |最適化のレベルを指定します（デフォルトは-O2）。|
|-fast|プログラムの実行速度が最大になるように最適化します。-fast オプションにより、次のオプションが自動で付与されます。-ipo, -O3, -static, -fp-model fast=2  |
|-ip| 単一ファイル内で、手続き間の処理を最適化します。|
|-ipo| 複数ファイル間で、手続き間の処理を最適化します。コンパイルに要する時間が大幅に増加します。|
|-xCORE-AVX512  /  -xCORE-AVX2 |Intelプロセッサ向けに，指定した命令セットに対応した最適化コードを生成します。|
|-static-intel|インテルが提供するライブラリを静的にリンクします。|


基本的には-fastオプションをつけて状況を確認することがベンダ推奨となっています。

その他オプションの詳細は、インテルの以下のサイトから参照可能です。

[インテルのドキュメントサイトでのコンパイラオプションの詳細](https://www.intel.com/content/www/us/en/docs/dpcpp-cpp-compiler/developer-guide-reference/2023-0/compiler-options.html)

### OpenMPの利用
IntelコンパイラではOpenMPが利用可能です。
IntelコンパイラでサポートされているOpenMPの機能詳細については以下のIntelサイトの情報を参照して下さい。2023/11/30現在でシステムにインストールされているIntel CompilerでサポートされているのはOpenMP 5.0～6.0（一部）までがサポートされています。

[OpenMP* Features and Extensions Supported in Intel® oneAPI DPC++/C++ Compiler](https://www.intel.com/content/www/us/en/developer/articles/technical/openmp-features-and-extensions-supported-in-icx.html)

[A Survey of OpenMP* Features Implemented in Intel® Fortran and C++ Compilers](https://www.intel.com/content/www/us/en/developer/articles/technical/a-survey-of-openmp-features-implemented-in-intel-fortran-and-c-compilers.html)




### Intel MKL の利用方法
遺伝研スパコンでは、Intel Math Kernel Libraryを利用可能です。


｜ライブラリ名称| バージョン |
|------------|-----------|
|Intel OneMKL | 2023.2.0|

MKLを利用する場合のリンクオプションの指定の仕方については、以下の Link Line Advisorを利用してみてください。

[Intel oneAPI Math Kernel Library Link Line Advisor](https://www.intel.com/content/www/us/en/developer/tools/oneapi/onemkl-link-line-advisor.html#gs.4cdbls)

以下の販売元のドキュメントサイトもご参照ください。

[Intel one MKLのドキュメントサイト](https://www.xlsoft.com/jp/products/intel/perflib/mkl/index.html)

## 参考情報

詳細については販売元のドキュメントサイトをご参照ください。

- [リリースノート等　販売元の技術情報](https://www.xlsoft.com/jp/products/intel/compilers/dpcpp/index.html?tab=2)