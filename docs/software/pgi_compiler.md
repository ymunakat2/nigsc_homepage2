---
id: pgi_compiler
title: "C/C++の使い方(NVIDIA HPC Compiler)"
---

旧PGI(Portland Group Inc.)は2013年にNVIDIAに買収され、現在旧PGI Compilerは、NVIDIA HPC SDK内のNVIDIA HPC Compilerとして開発が継続されています。本節ではNVIDIA HPCコンパイラ(C/C++)のシステム内での利用環境について説明します。

## 利用環境

### 利用できるバージョン・システム

遺伝研スパコンシステムではEnvironment Moduleのモジュールファイルを提供しており、以下をユーザ環境で読み込むことによりコンパイラを利用可能とすることができます。

|バージョン |モジュールファイル名| システムへの導入日 |
|----------|------------------|-------------------|
|23.7     | nvhpc/23.7       | 2023/11/30        |

バージョン23.7の各コンポーネントのバージョン等は[23.7 リリースノード](https://docs.nvidia.com/hpc-sdk/archive/23.7/hpc-sdk-release-notes/index.html)を参照してください。


遺伝研スパコンでは、NVIDIA HPCコンパイラについてはデフォルトでコンパイラにパスが通っています。パスが通っていることを確認します。

```
which nvc
/opt/pkg/nvidia/hpc_sdk/Linux_x86_64/23.7/compilers/bin/nvc
which nvc++
/opt/pkg/nvidia/hpc_sdk/Linux_x86_64/23.7/compilers/bin/nvc++
```

### コマンドライン形式

利用可能なC、C++コンパイラは以下になります。

|言語  | コマンド |	コマンドライン |言語規格・機能|
|------|---------|---------|----------|
|C| nvc|nvc [オプション] ファイル名 |ISO/ANSI C11 OpenMP,OpenACCサポート|
|C++|nvc++| nvc++ [オプション] ファイル名 |ISO/ANSI C++17  OpenMP,OpenACCサポート|
|NVCC|nvcc|nvcc [オプション] ファイル名|CUDA C/C++ Compiler driver|


### 利用可能なコンパイラオプション

汎用的な最適化オプションとしては-fastオプションを指定しておくことがベンダ推奨になっています。また-Minfoオプションはコンパイル時の最適化メッセージを出力する為のオプションで、これも状況を確認する為につけておくことを推奨します。また、プログラム内のグローバル最適化を有効にする為には、Interprocedual Analysisの機能を有効にします。
以上を纏めると、ベンダにより推奨されるオプション指定は以下のようになります。

```
nvc -fast -Mipa=fast,inline -Minfo test.c (Cコンパイラ)
nvc++ -fast -Mipa=fast,inline -Minfo test.cpp (C++コンパイラ)
```

プログラムによっては、さらにオプションを調整すると性能が向上する場合もありますが、専門的かつ大部な話題になりますのでここでは説明を省略します。詳細については[開発元のドキュメント](https://docs.nvidia.com/hpc-sdk/archive/23.7/compilers/hpc-compilers-user-guide/index.html#cmdln-options-use)をご参照ください。

*** 参考資料 ***

- [HPC SDK Documentation](https://docs.nvidia.com/hpc-sdk/archive/23.7/compilers/hpc-compilers-user-guide/index.html)

その他、よく利用される主なオプションについては以下のようになります。（下記は基本的に出典外部サイトの意訳です。用語等についても参照をお願いします。）

- [出典外部サイト](https://docs.nvidia.com/hpc-sdk/archive/23.7/compilers/hpc-compilers-user-guide/index.html#freq-used-options)

|オプション |  機能 |
|----------|-------|
|-⁠acc	| OpenACC ディレクティブを使用して並列化を有効にします。|
|-⁠fast	|SIMD 機能をサポートするターゲットに対して一般的に最適なフラグのセットを作成します。ベクトルストリーミング SIMD 命令、キャッシュ・アライメント、flushz を使用できるようにする最適化オプションが組み込まれています。|
|-⁠g | オブジェクト・モジュールにシンボリック・デバッグ情報を含めるようコンパイラに指示します。コマンドラインに-Oオプションがない限り、最適化レベルをゼロに設定します。逆に、[DWARF](https://ja.wikipedia.org/wiki/DWARF)情報を生成しないようにするには、-Mnodwarfオプションを使用します。|
|-⁠gpu | コードが生成されるGPUの種類、対象となるCUDAのバージョン、その他GPUコード生成のいくつかの側面を制御します。|
|-⁠help|利用可能なオプションの情報を提供します。|
|-⁠mcmodel=medium|64ビットターゲット用のコード生成を有効にし、プログラムのデータ容量が4GBを超える場合に有効です。|
|-⁠mp|OpenMPディレクティブを使用した並列化を有効にします。-mp=gpuはNVIDIA GPUを利用してOpenMP領域をオフロードする為に利用します。|
|-⁠Mconcur|ループの自動並列化を有効にするようコンパイラに指示します。指定された場合、コンパイラは並列化可能であると判断したループを複数のCPUコアを使用して実行するため、ループの反復はマルチスレッド実行コンテキストで最適に実行されるように分割されます。|
|-⁠Minfo|標準エラーに情報を出力するようコンパイラに指示します。|
|-⁠Minline | 関数のインライン化を可能にします。|
|-⁠Mipa=fast,inline| プロシージャ間のグローバル解析と最適化を可能にします。|
|-⁠Munroll | ループアンローラーを起動してループを展開し、各反復の間にループの複数のインスタンスを実行します。また、最適化レベルが 2 未満に設定されている場合、または -O や -g オプションが指定されていない場合、最適化レベルが 2 に設定されます。|
|-⁠o|出力する実行可能ファイル名を指定します。|
|-⁠O level | コード最適化レベルを指定します。levelは0、1、2、3、4です。|


## OpenMPの利用

NVIDIA HPC CompilerではOpenMPが利用可能です。OpenMP4.0以降ではCPU以外へのオフロードにも対応しGPU、FPGAなどのアクセラレータ用の汎用並列規格にもなっています。現在OpenMPはIntel、AMD、NVIDIAがサポートしておりベンダ間での汎用的な並列規格になっています。
使用方法詳細については以下のサイトを参照してください。

- [HPC SDK Documentation Using OpenMP](https://docs.nvidia.com/hpc-sdk/archive/23.7/compilers/hpc-compilers-user-guide/index.html#openmp-use)

- [The OpenMP Specification(OpenMP Organizationのサイト)](https://www.openmp.org/)


## OpenACCの利用

NVIDIA HPC Compilerでは、OpenACCが利用可能です。使用方法については以下のサイトをご参照ください。

- [OpenACC Getting Started(NVIDIA)(利用方法についての概説があります)](https://docs.nvidia.com/hpc-sdk/archive/23.7/compilers/openacc-gs/index.html)

OpenACC organizationにより仕様が策定されており、オープンな仕様になっています。かつてPGIがOpenACC organizationの主要なメンバとして仕様策定を行っていました。以下の外部サイトが参考になります。

- [OpenACC Organization ](https://www.openacc.org/)

- [OpenACC Programming and Best Practices Guide](https://openacc-best-practices-guide.readthedocs.io/en/latest/)

(以下は、企業のサイトなのでリンク貼るかは判断必要)

- [OpenACC Programing](https://hpcworld.jp/archive/SPG/Pgi/OpenACC/)

- [GPUコラム OpenACCで始めるGPUプログラミング](https://gdep-sol.co.jp/gpu-programming/)



Intel/AMD/NVIDIAのGPU間でのポータビリティを重視する場合OpenMPが利用される傾向があるようです。しかしNVIDIA GPU向けであれば、NVIDIAがOpenACCのサポートにより積極的ではあるため、OpenMPよりOpenACCのほうがより高性能のコードを生成できる傾向があるようです。

## MPIの利用?
NVIDIA HPC SDKではOpenMPIが利用可能になっています。以下のCompiler Wrapperが利用可能です。

|言語 | Compiler Wrapper |
|-----|------------------|
|C    | mpicc |
|C++ | mpic++ |

PATHにCompiler Wrapperのパスを以下のように加え、MPIプログラムのコンパイルを実施してください。

```
export PATH=/opt/nvidia/hpc_sdk/Linux_x86_64/23.7/mpi/openmpi/bin:$PATH
mpicc ソースコードファイル -o オブジェクトファイル
```

参考　[using MPI(NVIDIA SDK Documantation)](https://docs.nvidia.com/hpc-sdk/archive/23.7/compilers/hpc-compilers-user-guide/index.html#mpi-use)

## CUDAの利用

CUDAはNVIDIAが開発するNVIDIA GPU用の並列プログラミングモデルです。OpenMP/OpenACCよりさらに低レイヤーでのGPUプログラミングを行うことが可能で、計算性能を重視するプログラマ向けのプログラミングモデルになります。NVIDIA HPC Compilerでは以下のCUDAコンパイラを提供しています。

### 遺伝研スパコンでのCUDA関連の環境について

|コンポーネント|バージョン|備考|
|-------------|---------|----|
|CUDA Driver |12.1 ||
|nvccコンパイラ|12.2.53 |

nvccコンパイラについても環境設定がされています。nvccにパスが通っているか確認してください。

```
$ which nvcc
/opt/pkg/nvidia/hpc_sdk/Linux_x86_64/23.7/compilers/bin/nvcc
```

nvccでのコンパイル方法含めての情報については以下のNVIDIAのサイトを参照してください。

[NVIDIA CUDA Compiler Driver NVCC](https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html)

### CUDAプログラミング情報について

以下のNVIDIAのサイトの情報を参照してください。ここでは詳細は省略します。

[CUDA C Programming Guide (NVIDIA)](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html)
