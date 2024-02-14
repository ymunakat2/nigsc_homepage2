---
id: Slurm
title: Slurm の概要
---

遺伝研スパコンでは、個人ゲノム解析区画用のジョブ管理システムとしてAGE(Altair Grid Engine)に加えSlurm（Simple Linux Utility for Resource Management）を選択可能としています。
Slurmは元々の開発元である米国LLNL(ローレンスリバモア国立研究所)を始めとして全世界の数多くの大規模クラスタ型スパコンシステムで利用実績があるオープンソースソフトウェアのジョブ管理システムで、複数のパブリッククラウド上でもHPC利用向けのジョブ管理システムとしても利用可能となっており、ジョブ管理システムとして広く利用されています。

参考資料

-  [SchedMD(開発元)のQuick Start User Guide(英語)](https://slurm.schedmd.com/quickstart.html)
- [Slurm のオンラインマニュアルページ](https://slurm.schedmd.com/man_index.html)
-  [Slurmのコマンドのサマリ表](https://slurm.schedmd.com/pdfs/summary.pdf)
-  [SchedMD(開発元)のTutorial(英語)](https://slurm.schedmd.com/tutorials.html)


## ジョブの種類

個人ゲノム解析区画のSlurmでは以下の3種類のジョブが主に使われます。(Slurmのドキュメントではパラレルジョブはバッチジョブとして扱われますが、遺伝研スパコンのAGEの説明との対応として別に分類して説明します)

- [バッチジョブ (batch job)](software/Slurm/batch_jobs.md)
  - CPU コアを 1 コアだけ使用するプログラムを少数実行する場合に用いる。
- [パラレルジョブ (parallel job)](software/Slurm/parallel_jobs.md)
  - CPU コアを複数同時に使用するプログラムを少数実行する場合に用いる。
- [アレイジョブ (array job)](software/Slurm/array_jobs.md)
  - バッチジョブまたはパラレルジョブを多数順次実行する場合に用いる。

（その他のジョブについての説明など詳細については公式のマニュアルをご参照下さい。）

## その他のコマンド

主に使うコマンドは以下のとおりです。

- squeue
    - ジョブの現在の状況を確認する。
- scancel
    - ジョブを削除する。
- scontrol
    - ジョブの設定を変更する。

詳細は[その他のコマンド](/software/Slurm/other_commands)の項および公式マニュアルをご参照下さい。

## Slurmの概要・用語説明

AGEとSlurmは基本的な機能では類似な部分が多いのですが、若干の用語、概念の差異、コマンド体系の差異はありますので、以下にご紹介します。

### パーティションについて

Slurmでは計算サーバ等の計算資源の集合体に対してパーティションと呼ばれる管理の為の論理単位をシステム目的に合わせてシステム管理者が定義しています。ユーザは、パーティションを利用することにより、計算リソースの空き状況を自分で確認しなくてもジョブ実行をSlurm(ジョブ管理システム)にジョブの実行管理を任せることが可能になります。
AGEでは、キュー(queue)と呼ばれる論理単位で管理しますが、Slurmではこれをパーティション(partition)と呼んでいます。この二つはほぼ同一の概念を指していると考えてください。

個人ゲノム解析区画では、以下のパーティション（キュー）をシステム側で定義、設定しています。

|パーティション名|最大実行時間|ユーザ毎の最大投入ジョブ数|ユーザ毎の最大使用可能Core数|ユーザ事の最大使用可能メモリ量|
|---------|---------------|---------|-------|-------|
|         |               |

ジョブの投入時に投入パーティションを指定しない場合のデフォルトパーティションは？になります。また計算資源要求をオプションで何も指定しなかった場合、ジョブは以下の設定でSlurmに登録されます。

|要求資源項目|デフォルト|変更する場合のオプション|備考|
|-----------|---------|----------------------|---|
|             |       |                      |   |

### ジョブ投入スクリプトについて

ジョブとしてSlrumに処理を依頼する際に、ユーザは依頼したい処理をスクリプトとして記述してこれをSlurmに渡します。ジョブスクリプトの中にはSlurmのオプションを記載しておくことが可能であり、また実行時にSlurmから渡されるSlurm環境変数によって処理を制御することが可能です。ジョブ投入スクリプトの記載の仕方については、[バッチジョブの投入](/software/Slurm/batch_jobs.md)に記載します。


### ジョブ実行の流れ

個人ゲノム解析区画でのSlurmをジョブ管理システムとして利用したジョブ実行の流れは概略以下のようになります。

1. 個人ゲノム解析区画のゲートウェイからsshで計算ノードのログインノードか、又はGPU用Slurmログインノードにログインする。
2. 投入用のジョブ投入スクリプトをログインノード上で作成する。
3. ログインノードから[sbatchコマンド](/software/Slurm/batch_jobs#%E3%83%90%E3%83%83%E3%83%81%E3%82%B8%E3%83%A7%E3%83%96%E3%81%AE%E6%8A%95%E5%85%A5%E6%96%B9%E6%B3%95)を利用してジョブをパーティションに投入する。
4. [各種のSlurmコマンド](/software/Slurm/batch_jobs#ジョブの状態確認)を使用してジョブの実行状態を観察する。
5. ジョブの実行状態が期待通りであれば、ジョブの終了まで待つ。ジョブの実行状態が期待通りでなければ[scancelコマンド](/software/Slurm/batch_jobs#ジョブの削除)でジョブ実行を中断させる。エラー出力ファイルの内容を確認しながらジョブスクリプトをデバッグして、修正したジョブ投入スクリプトを用いてsbatchコマンドでジョブを再投入する。
6. ジョブが正常完了したら、出力ファイルから実行結果を確認する。


## AGE/Slurmコマンド簡易対比表

AGEとSlurmの利用目的別の基本的なコマンド対比表を示します。

| コマンド| AGE|Slurm|Slurmのオンラインマニュアル記述|
|--------|-----|----|----|
|ジョブの投入| qsub ジョブファイル名 | sbatch ジョブファイル名 |[sbatch](https://slurm.schedmd.com/sbatch.html)  |
|ジョブのキャンセル| qdel ジョブID | scancel ジョブID|[scancel](https://slurm.schedmd.com/scancel.html)   |
|キューの状態表示|qstat |squeue| [squeue](https://slurm.schedmd.com/squeue.html)  |
|ジョブのホールド|qhold ジョブID|scontrol hold ジョブID|[scontrol](https://slurm.schedmd.com/scontrol.html)    |
|キュー・パーティションのリスト表示|qconf -sql | scontrol show partition | [scontrol](https://slurm.schedmd.com/scontrol.html)   |
|ノードのリスト表示| qhost |scontrol show nodes |[scontrol](https://slurm.schedmd.com/scontrol.html)    |
|クラスタの状態表示|qhost -q | sinfo | [sinfo](https://slurm.schedmd.com/sinfo.html)   |

各コマンドのオプション指定詳細はまた異なりますので、詳しくは各コマンドのオンラインマニュアル、または米国SchedMD社のドキュメントサイトを参照してください。

- Slurmのクイックスタートガイド https://slurm.schedmd.com/quickstart.html
- Slurmのコマンドのサマリ表　https://slurm.schedmd.com/pdfs/summary.pdf
- 各ジョブ管理システムの比較 https://slurm.schedmd.com/rosetta.pdf




















