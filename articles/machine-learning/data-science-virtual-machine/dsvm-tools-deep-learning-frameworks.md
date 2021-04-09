---
title: ディープ ラーニングと AI のフレームワーク
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine で使用できるディープ ラーニングのフレームワークとツール。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 0bfdea6c0bbbe40e4c72ab9da70a1b16d4bc4d3a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175670"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Azure Data Science VM 用のディープ ラーニングと AI のフレームワーク
以下の一覧では、DSVM でのディープ ラーニング フレームワークを示します。

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | |
| サポートされている DSVM エディション      | Ubuntu 16.04    |
| DSVM での構成/インストール方法  | Caffe は `/opt/caffe` にインストールされます。   サンプルは `/opt/caffe/examples` にあります。|
| 実行方法      | X2Go を使用して VM にサインインした後、新しいターミナルを起動して次のように入力します。<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>新しいブラウザー ウィンドウでサンプルのノートブックが開きます。 バイナリは、/opt/caffe/build/install/bin にインストールされています。<br/><br/>Caffe のインストールされるバージョンは Python 2.7 を必要とします。既定でアクティブ化される Python 3.5 では機能しません。 Python 2.7 に切り替えるには、`source activate root` を実行して Anaconda 環境に切り替えます。|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | |
| サポートされている DSVM エディション      | Ubuntu 16.04     |
| DSVM での構成/インストール方法  | Caffe2 は、Python 2.7 (ルート) Conda 環境にインストールされます。 |
| 実行方法      | ターミナル: Python を開始し、Caffe2 をインポートします。 <br/> * JupyterHub: [JupyterHub に接続](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)し、Caffe2 ディレクトリに移動してサンプル ノートブックを見つけます。 一部のノートブックについては、Caffe2 ルートを Python コードで設定する必要があります。/opt/caffe2 と入力します。 |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | 5.2 |
| サポートされている DSVM エディション      | Ubuntu 16.04    |
| DSVM での構成/インストール方法  | Chainer は、Python 3.5 にインストールされます。 |
| 実行方法      | ターミナル: Python 3.5 環境をアクティブ化した後、`python` を実行してから、`import chainer` を実行します。 <br/> * JupyterHub: [JupyterHub に接続](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)し、Chainer ディレクトリに移動してサンプル ノートブックを見つけます。| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA、cuDNN、NVIDIA ドライバー](https://developer.nvidia.com/cuda-toolkit)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | 10.0.130|
| サポートされている DSVM エディション      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04  |
| DSVM での構成/インストール方法  |_nvidia-smi_ はシステム パスから実行できます。  |
| 実行方法      | コマンド プロンプト (Windows) またはターミナル (Linux) を開いて、_nvidia-smi_ を実行します。 |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | 0.16.1|
| サポートされている DSVM エディション      | Ubuntu 18.04<br> Ubuntu 16.04   |
| DSVM での構成/インストール方法  | Horovod は、Python 3.5 にインストールされます |
| 実行方法      | ターミナルで適切な環境をアクティブ化した後、Python を実行します。 |

## <a name="keras"></a>[Keras](https://keras.io/)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | 2.2.4 |
| サポートされている DSVM エディション      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04   |
| DSVM での構成/インストール方法  | Keras は、Windows では Python 3.6 に、Linux では Python 3.5 にインストールされます |
| 実行方法      | ターミナルで適切な環境をアクティブ化した後、Python を実行します。 |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](/cognitive-toolkit/)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | 2.5.1 |
| サポートされている DSVM エディション      | Windows 2016 <br> Ubuntu 16.04   |
| DSVM での構成/インストール方法  | CNTK は、[Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) では Python 3.6 に、[Linux](./dsvm-tools-languages.md#python-linux-edition) では Python 3.5 にインストールされます |
| 実行方法      | ターミナル: 適切な環境をアクティブ化し、Python を実行します。 <br/>Jupyter: [Jupyter](provision-vm.md) または [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine) に接続し、サンプル用の CNTK ディレクトリを開きます。 |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | 1.3.0 |
| サポートされている DSVM エディション      | Windows 2016 <br> Ubuntu 16.04    |
| DSVM での構成/インストール方法  | MXNet は、Windows では `C:\dsvm\tools\mxnet` に、Ubuntu では `/dsvm/tools/mxnet` にインストールされます。 Python バインディングは、[Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) では Python 3.6 に、[Linux](./dsvm-tools-languages.md#python-linux-edition) では Python 3.5 にインストールされます。R バインディングは、Ubuntu DSVM にも含まれています。 |
| 実行方法      | ターミナル: 適切な conda 環境をアクティブ化して、`import mxnet` を実行します。 <br/>Jupyter: [Jupyter](provision-vm.md#access-the-dsvm) または [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine) に接続して、サンプル用の `mxnet` ディレクトリを開きます。 |

## <a name="mxnet-model-server"></a>[MXNet モデル サーバー](https://github.com/awslabs/mxnet-model-server#quick-start)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | 1.0.1 |
| サポートされている DSVM エディション      | Windows 2016 <br> Ubuntu 16.04    |
| DSVM での構成/インストール方法  | MXNet モデル サーバーは、[Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) では Python 3.6 に、[Linux](./dsvm-tools-languages.md#python-linux-edition) では Python 3.5 にインストールされます |
| 実行方法      | ターミナル: まず、`sudo systemctl stop jupyterhub` を実行して、JupyterHub サービスを停止させます。両方とも同じポートでリッスンしているためです。 次に、適切な conda 環境をアクティブ化し、`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` を実行します。 |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia システム管理インターフェイス (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン |  |
| サポートされている DSVM エディション      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04   |
| 何を目的としていますか? | GPU アクティビティをクエリするための NVIDIA ツール |
| DSVM での構成/インストール方法  | `nvidia-smi` はシステム パス上にあります。 |
| 実行方法      | **GPU を備えた** 仮想マシンで、コマンド プロンプト (Windows の場合) またはターミナル (Linux の場合) を開き、`nvidia-smi` を実行します。 |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | 1.2.0 (Ubuntu 16.04)、1.7.1 (Ubuntu 18.04、Windows 2019) |
| サポートされている DSVM エディション      | Windows Server 2019<br>Ubuntu 18.04<br> Ubuntu 16.04 |
| DSVM での構成/インストール方法  | py37_pytorch conda 環境で [Python 3.7](dsvm-tools-languages.md#python-linux-edition) にインストールします。 サンプルの Jupyter ノートブックが含まれており、サンプルは /dsvm/samples/pytorch にあります。 |
| 実行方法      | ターミナル: 適切な環境をアクティブ化した後、Python を実行します。<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): 接続した後、サンプル用の PyTorch ディレクトリを開きます。  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | 1.13 |
| サポートされている DSVM エディション      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04 |
| DSVM での構成/インストール方法  | [Linux](dsvm-tools-languages.md#python-linux-edition) では Python 3.5 に、[Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) では Python 3.6 でインストールされます |
| 実行方法      | ターミナル: 適切な環境をアクティブ化した後、Python を実行します。 <br/> * Jupyter: [Jupyter](provision-vm.md) または [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine) に接続し、サンプル用の TensorFlow ディレクトリを開きます。   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | 1.12 |
| サポートされている DSVM エディション      | Ubuntu 16.04 |
| DSVM での構成/インストール方法  | tensorflow_model_server はターミナルで利用できます。 |
| 実行方法      |  サンプルは[オンラインで](https://www.tensorflow.org/serving/)利用可能   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | 1.0.3 |
| サポートされている DSVM エディション      | Ubuntu 16.04 |
| DSVM での構成/インストール方法  |Theano は Python 2.7 (_root_) および Python 3.5 (_py35_) 環境にインストールされます。 |
| 実行方法      |  ターミナル: 必要な Python バージョン (root または py35) をアクティブ化し、Python を実行した後、Theano をインポートします。<br/>* Jupyter: Python 2.7 または 3.5 カーネルを選択した後、Theano をインポートします。  <br/>最近の数式カーネル ライブラリ (MKL) バグを回避するには、まず、MKL スレッドレイヤーを次のように設定する必要があります。<br/><br/>`export MKL_THREADING_LAYER=GNU`  |
