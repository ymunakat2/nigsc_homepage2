---
id: interactive_jobs
title: 対話形式ジョブの投入
---
Slurmでは、バッチ形式ではなく、ジョブを対話的(interactive)、同期的に処理するジョブ実行形式をサポートしています。この場合に利用するコマンドはsalloc,srunコマンドです。

srunでqloginコマンドのように要求したリソース条件を満たしたインタラクティブノードにログインする。という動作を実行させたい場合、例えば以下のように--ptyオプションを使用します。

```
srun --pty bash
```
AGEのqloginコマンドと若干意味合いが違い、srun --pty bash はリソース条件に合致したノード上でbashを起動して疑似端末に結び付けるということを意味します。個人ゲノム解析区画の場合、ゲートウェイノードから直接sshで計算ノードにログインする運用になっている為、運用としては不要ですが、一般的な対話形式の場合の利用方法として記述しておきます。

ログインノードで、srunコマンドを投入する際、何度もオプションを指定したくない場合、sallocで計算リソースを確保しておけば、その後のsrunコマンドではオプションを指定する必要はありません。sallocは指定したオプションでリソースを割り当て要求します。その割り当てられたリソースをsrunで利用するという形式になります。

```



```

### MPIプログラムのsrunでの起動

Slurmでは、MPI実装との連携はPMIxの規格に基づいてMPIプロセスの制御を行うことが可能です。Slurmの開発元の推奨としては、MPI実装側のMPIジョブの起動コマンドmpirunではなく、srunでMPIプログラムを実行することが推奨されています。

この為、バッチジョブ経由でなくとも、srunコマンドで直接MPIジョブを対話的にSlurmに投入することが可能です。


MPIジョブの投入例

```
#!/bin/bash
#SBATCH --job-name=sim_1        # job name (default is the name of this file)
#SBATCH --output=log.%x.job_%j  # file name for stdout/stderr (%x will be replaced with the job name, %j with the jobid)
#SBATCH --time=1:00:00          # maximum wall time allocated for the job (D-H:MM:SS)
#SBATCH --partition=gpXY        # put the job into the gpu partition
#SBATCH --exclusive             # request exclusive allocation of resources
#SBATCH --mem=20G               # RAM per node
#SBATCH --threads-per-core=1    # do not use hyperthreads (i.e. CPUs = physical cores below)
#SBATCH --cpus-per-task=4 


srun ./test

```