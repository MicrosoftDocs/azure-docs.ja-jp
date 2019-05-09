---
title: ディープ ラーニングと AI のフレームワーク - Azure | Microsoft Docs
description: Data Science Virtual Machine でサポートされているディープ ラーニングのフレームワークとツールについて説明します。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 59f88d54d3542738f1a500c8c476995eb1535ecf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006489"
---
# <a name="deep-learning-and-ai-frameworks"></a>ディープ ラーニングと AI のフレームワーク
[データ サイエンス仮想マシン](https://aka.ms/dsvm) (DSVM) と[ディープ ラーニング VM](https://aka.ms/dsvm/deeplearning) は、多数のディープ ラーニング フレームワークをサポートし、予測分析や、画像および言語の理解などの認識機能を駆使した人工知能 (AI) アプリケーションの構築を支援します。

ここでは、DSVM で利用可能すべてのなディープ ラーニング フレームワークの詳細を示します。

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Windows、Linux     |
| DSVM での構成/インストール方法  | Microsoft Cognitive Toolkit (CNTK) は、[Linux および Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上の Python 3.5 および [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) 上の Python 3.6 にインストールされています。   |
| サンプルへのリンク      | サンプル Jupyter ノートブックが含まれています。     |
| DSVM 上の関連ツール      | Keras      |
| 使用/実行方法    | * ターミナルで、適切な環境をアクティブ化し、Python を実行します。 <br/> * Jupyter で: [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) または [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続し、サンプルの保存されている CNTK ディレクトリを開きます。 |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Windows、Linux     |
| DSVM での構成/インストール方法  | TensorFlow は、[Linux および Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上の Python 3.5 および [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) 上の Python 3.6 にインストールされています。  |
| サンプルへのリンク      | サンプル Jupyter ノートブックが含まれています。     |
| DSVM 上の関連ツール      | Keras      |
| 使用/実行方法    | * ターミナルで、適切な環境をアクティブ化し、Python を実行します。 <br/> * Jupyter で: [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) または [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続し、サンプルの保存されている TensorFlow ディレクトリを開きます。  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| 紹介   | TensorFlow のための分散深層学習フレームワーク      |
| サポートされている DSVM エディション      | Ubuntu     |
| DSVM での構成/インストール方法  | Horovod は、[Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上の Python 3.5 にインストールされています。  |
| サンプルへのリンク      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| DSVM 上の関連ツール      | TensorFlow      |
| 使用/実行方法    | ターミナルで、適切な環境をアクティブ化し、Python を実行します。 |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| 紹介   | 高度なディープ ラーニング API      |
| サポートされている DSVM エディション      | Windows、Linux     |
| DSVM での構成/インストール方法  | TensorFlow は、[Linux および Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上の Python 3.5 および [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) 上の Python 3.6 にインストールされています。 |
| サンプルへのリンク      | https://github.com/fchollet/keras/tree/master/examples      |
| DSVM 上の関連ツール      | Microsoft Cognitive Toolkit、TensorFlow、Theano      |
| 使用/実行方法    | * ターミナルで、適切な環境をアクティブ化し、Python を実行します。 <br/> * Jupyter で: GitHub の場所からサンプルをダウンロードし、[Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) または [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続してサンプル ディレクトリを開きます。 |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Ubuntu     |
| DSVM での構成/インストール方法  | Caffe は `/opt/caffe` にインストールされます。    |
| Python 2.7 に切り替える方法 | `source activate root` を実行します。 |
| サンプルへのリンク      | サンプルは `/opt/caffe/examples` に含まれています。      |
| DSVM 上の関連ツール      | Caffe2      |

### <a name="how-to-use--run-it"></a>使用/実行方法

X2Go を使用して VM にログインした後、新しいターミナルを起動して次のように入力します

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

新しいブラウザー ウィンドウでサンプルのノートブックが開きます。

バイナリは、/opt/caffe/build/install/bin にインストールされています。

Caffe のインストールされるバージョンは Python 2.7 を必要とします。既定でアクティブ化される Python 3.5 では機能しません。 `source activate root` を実行して Anaconda 環境に切り替えます。

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Ubuntu     |
| DSVM での構成/インストール方法  | Caffe2 は、[Python 2.7 (ルート) conda 環境](dsvm-languages.md#python-linux-and-windows-server-2012-edition)にインストールされています。 ソースは、`/opt/caffe2` にあります。 |
| サンプルへのリンク      | サンプル ノートブックは、JupyterHub に含まれています。 |
| DSVM 上の関連ツール      | Caffe      |
| 使用/実行方法    | * ターミナルで、[ルート Python 環境](dsvm-languages.md#python-linux-and-windows-server-2012-edition)をアクティブ化して Python を起動し、caffe2 をインポートします。 <br/> * JupyterHub で、[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続し、サンプル ノートブックが保存されている Caffe2 ディレクトリに移動します。 一部のノートブックについては、Caffe2 ルートを Python コードで設定する必要があります。/opt/caffe2 と入力します。 |
| ビルド ノート | Caffe2 は、Linux 上のソースから構築されており、 CUDA、cuDNN、および Intel MKL が含まれています。 現在のコミットは、すべての GPU 上での安定性のために選択され、サンプルがテストされた 0d9c0d48c6f20143d6404b99cc568efd29d5a4be です。 |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Windows、Linux     |
| DSVM での構成/インストール方法  | Chainer は、[Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) にインストールされています。 ChainerRL と ChainerCV もインストールされます。   |
| サンプルへのリンク      | サンプル ノートブックは、JupyterHub に含まれています。 |
| DSVM 上の関連ツール      | Caffe      |
| 使用/実行方法  | * ターミナルで、[Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 環境をアクティブ化し、_python_ を実行してから Chainer をインポートします。 <br/> * JupyterHub で: [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続し、サンプルのノートブックが保存されている Chainer ディレクトリに移動します。


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| 紹介   | H2O 向けのディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Ubuntu     |
| DSVM での構成/インストール方法  | Deep Water は [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) にインストールされており、`/dsvm/tools/deep_water` からも使用できます。   |
| サンプルへのリンク      | サンプル ノートブックは、JupyterHub に含まれています。      |
| DSVM 上の関連ツール      | H2O、Sparkling Water      |

### <a name="how-to-use--run-it"></a>使用/実行方法

Deep Water では、cuDNN 5.1 と CUDA 8 が必要です。 他の深層学習フレームワークでは CUDA 9 および cuDNN 7 が使用されているため、これは既定ではライブラリ パスにありません。 Deep Water 用の CUDA 8 + cuDNN 5.1 を使用するには:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Deep Water を使用するには:
* ターミナルで [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 環境をアクティブ化し、_python_ を実行します。 <br/>
* JupyterHub で [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続し、サンプルのノートブックが保存されている deep_water ディレクトリに移動します。

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Windows、Linux     |
| DSVM での構成/インストール方法  | MXNet は、Windows では `C:\dsvm\tools\mxnet` に、Linux では `/dsvm/tools/mxnet` にインストールされます。 Python バインディングは、[Linux および Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上の Python 3.5 および [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) 上の Python 3.6 にインストールされています。 Ubuntu にはR バインディングもインストールされています。   |
| サンプルへのリンク      | サンプル Jupyter ノートブックが含まれています。    |
| DSVM 上の関連ツール      | Keras      |
| 使用/実行方法    | * ターミナルで、適切な環境をアクティブ化し、Python を実行します。 <br/> * Jupyter で: [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) または [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続し、サンプルの保存されている mxnet ディレクトリを開きます。  |
 | ビルド ノート | MXNet は、Linux 上のソースから作成されています。 このビルドには、CUDA、cuDNN、NCCL、および MKL が含まれています。 |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング モデルを迅速にトレーニングするための NVIDIA 製ディープ ラーニング システム      |
| サポートされている DSVM エディション      | Ubuntu     |
| DSVM での構成/インストール方法  | DIGITS は `/dsvm/tools/DIGITS` にインストールされ、_digits_ という名前のサービスとして利用できます。   |

### <a name="how-to-use--run-it"></a>使用/実行方法

X2Go で VM にログインします。 ターミナルで、サービスを開始します:

    sudo systemctl start digits

サービスが開始するまで 1 分ほどかかります。 Web ブラウザーを起動し、`http://localhost:5000` にアクセスします。 DIGITS ではセキュリティ保護されたログインが提供されないので、VM の外部には公開しないでください。



## <a name="nvidia-smi"></a>nvidia-smi

|    |           |
| ------------- | ------------- |
| 紹介   | GPU アクティビティをクエリするための NVIDIA ツール      |
| サポートされている DSVM エディション      | Windows、Linux     |
| DSVM での構成/インストール方法  | _nvidia-smi_ はシステム パスから実行できます。   |
| 使用/実行方法 | コマンド プロンプト (Windows) またはターミナル (Linux) を起動し、_nvidia-smi_ を実行します。



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Ubuntu     |
| DSVM での構成/インストール方法  | Theano は Python 2.7 (_root_) および Python 3.5 (_py35_) 環境にインストールされます。   |
| DSVM 上の関連ツール      | Keras      |
| 使用/実行方法    | * ターミナルで、必要な Python バージョン (root または py35) をアクティブ化し、python を実行した後、theano をインポートします。 <br/> * Jupyter で、Python 2.7 または 3.5 カーネルを選択した後、theano をインポートします。  <br/>最近の MKL バグを回避するには、まず、MKL スレッドレイヤーを設定する必要があります。<br/><br/>_export MKL_THREADING_LAYER=GNU_|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Ubuntu     |
| DSVM での構成/インストール方法  | Torch は `/dsvm/tools/torch` にインストールされます。 PyTorch は Python 2.7 (_root_) および Python 3.5 (_py35_) 環境にインストールされます。   |
| サンプルへのリンク      | Torch サンプルは `/dsvm/samples/torch` にあります。 PyTorch サンプルは `/dsvm/samples/pytorch` にあります。      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Linux     |
| DSVM での構成/インストール方法  | PyTorch は、[Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) にインストールされています。  |
| サンプルへのリンク      | サンプル Jupyter ノートブックが含まれています。また、サンプルは /dsvm/samples/pytorch でも見つけることができます。      |
| DSVM 上の関連ツール      | Torch      |
| 使用/実行方法 |* ターミナルで、適切な環境をアクティブ化し、Python を実行します。 <br/> * Jupyter で: [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続し、サンプルの保存されている PyTorch ディレクトリを開きます。  |

## <a name="mxnet-model-server"></a>MXNet モデル サーバー

|    |           |
| ------------- | ------------- |
| 紹介   | MXNet および ONNX モデルの HTTP エンドポイントを作成するサーバー      |
| サポートされている DSVM エディション      | Linux     |
| DSVM での構成/インストール方法  | _mxnet-model-server_ は端末で利用可能   |
| サンプルへのリンク      | [[MXNet Model Server] (MXNet モデル サーバー) ページ](https://github.com/awslabs/mxnet-model-server)で、最新のサンプルを探す    |
| DSVM 上の関連ツール      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow Serving

|    |           |
| ------------- | ------------- |
| 紹介   | TensorFlow モデルで推論を実行するサーバー      |
| サポートされている DSVM エディション      | Linux     |
| DSVM での構成/インストール方法  | _tensorflow_model_server_ は端末で利用可能   |
| サンプルへのリンク      | サンプルは[オンラインで](https://www.tensorflow.org/serving/)利用可能      |
| DSVM 上の関連ツール      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| 紹介   | NVIDIA からの深層学習推論サーバーです。 |
| サポートされている DSVM エディション      | Ubuntu     |
| DSVM での構成/インストール方法  | TensorRT は _apt_ パッケージとしてインストールされています。   |
| サンプルへのリンク      | サンプルは[オンラインで](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples)利用可能      |
| DSVM 上の関連ツール      | TensorFlow Serving、MXNet Model Server  |



