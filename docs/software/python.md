---
id: python
title: "Pythonの使い方"
---

Pythonの処理系はシステムにインストールされています。Pythonのバージョン、およびインストール済みパッケージを確認する手順は次のとおりです。
```
$ python --version
Python 3.10.12
$ pip list
Package                                          Version
------------------------------------------------ ------------------
matplotlib                                       3.5.1
numpy                                            1.21.5
pandas                                           1.3.5
scikit-learn                                     0.23.2
scipy                                            1.8.0
```
インストールされていないパッケージ、またはバージョンを使用したい場合はホームディレクトリに仮想環境をセットアップしてください。この場合システムにインストールされているPython、およびパッケージを使用する場合に比べて処理は低速になりますのでご注意ください。ここでは仮想環境の構築方法についてvenv、virtualenv、pyenv、Minicondaによる４つのアプローチを説明します。列挙の順に手順が複雑になりますがより柔軟なセットアップが可能になります。目的・用途に合わせていずれかのアプローチを選択してください。

## venv
Pyhtonに付属する仮想環境作成用モジュールです。インストールされていないパッケージが必要な場合に使用してください。
```
$ python -m venv ~/venv_p310
$ source ~/venv_p310/bin/activate
```
pipを使用して必要なライブラリをインストールしてください。
```
$ pip install torch
```
環境から抜ける場合は次のとおりです。
```
$ deactivate
```
詳細は公式ページをご確認ください。[venv --- 仮想環境の作成](https://docs.python.org/ja/3/library/venv.html)```
## virtualenv
Python2系の仮想環境が必要な場合に使用してください。
```
$ virtualenv -p python2.7 ~/p27
$ source ~/p27/bin/activate
```
pipを使用して必要なライブラリをインストールしてください。
```
$ pip install torch
```
環境から抜ける場合は次のとおりです。
```
$ deactivate
```
詳細は公式ページをご確認ください。[virtualenv User Guide](https://virtualenv.pypa.io/en/latest/user_guide.html)

## pyenv
システムにインストールされていないバージョンのPythonが必要な場合に使用してください。Pythonのバージョン毎に使用するパッケージを管理できます。全体、およびカレントディレクトリ毎に設定が可能です。

```
$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv
$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
$ echo 'eval "$(pyenv init -)"' >> ~/.bashrc
$ source ~/.bashrc
```
インストール可能なPythonのバージョンを確認し必要なバージョンをインストールしてください。
```
$ pyenv install --list
Available versions:
  3.10.13
  3.11.7
  3.12.0
$ pyenv install 3.12.1
```
必要なバージョンのPythonを有効化します。全体への設定はglobal、カレントディレクトリへの設定はlocalを使用してください。
```
$ pyenv global 3.12.1
$ pyenv local 3.11.7
```
pipを使用して必要なライブラリをインストールしてください。
```
$ pip install torch
```
詳細は公式ページをご確認ください。[pyenv](https://github.com/pyenv/pyenv)

## Miniconda
システムにインストールされていないバージョンのPythonが必要な場合、かつ同一バージョンでパッケージ構成を変更して仮想環境をセットアップしたい場合に使用してください。
```
$ mkdir ~/miniconda3
$ wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
$ sh ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
$ rm -f ~/miniconda3/miniconda.sh
$ ~/miniconda3/bin/conda init bash
$ source ~/.bashrc
```
conda-forgeレポジトリをデフォルトに設定し、.condarcのトップに追加されたことを確認してください。

```
$ conda config --add channels conda-forge
$ conda config --set channel_priority strict
$ vim ~/.condarc
```

使用可能なPythonバージョンを確認して必要なバージョンで仮想環境を作成してください。
```
$ conda search -f python
Loading channels: done
# Name                       Version           Build  Channel
python                        3.9.18      h955ad1f_0  pkgs/main
python                       3.10.13      h955ad1f_0  pkgs/main
python                        3.11.7      h955ad1f_0  pkgs/main
python                        3.12.1      h996f2a0_0  pkgs/main
$ conda create --name py312 python=3.12.1
$ conda activate py312
```
conda installを使用して必要なライブラリをインストールしてください。
```
$ conda install pytorch
```
Conda環境を終了する場合次のとおりです。
```
$ conda deactivate
```
詳細は公式ページをご確認ください。 [Installing on Linux conda 4.10.3.post11+888309718 documentation](https://conda.io/projects/conda/en/latest/user-guide/install/linux.html)


## 動作確認

```
$ python --version
Python 3.11.5
```