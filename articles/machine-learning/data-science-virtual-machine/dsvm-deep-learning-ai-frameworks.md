---
title: "ディープ ラーニングと AI のフレームワーク - Azure | Microsoft Docs"
description: "ディープ ラーニングと AI のフレームワーク"
keywords: "データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 46662c15374f781a6527bb1435d883ae060c63f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="deep-learning-and-ai-frameworks"></a>ディープ ラーニングと AI のフレームワーク
[データ サイエンス仮想マシン](http://aka.ms/dsvm) (DSVM) と[ディープ ラーニング VM](http://aka.ms/dsvm/deeplearning) は、多数のディープ ラーニング フレームワークをサポートし、予測分析や、画像および言語の理解などの認識機能を駆使した人工知能 (AI) アプリケーションの構築を支援します。 

ここでは、DSVM で利用可能すべてのなディープ ラーニング フレームワークの詳細を示します。

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Windows、Linux     |
| DSVM での構成/インストール方法  | Microsoft Cognitive Toolkit (CNTK) は、_root_ 環境では Python 2.7 に、_py35_ 環境では Python 3.5 にそれぞれインストールされます。   |
| サンプルへのリンク      | サンプル Jupyter ノートブックが含まれています。     |
| DSVM 上の関連ツール      | Keras      |
| 使用/実行方法    | Jupyter を開き、CNTK フォルダーを探します  |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Windows、Linux     |
| DSVM での構成/インストール方法  | Linux では、TensorFlow は Python 2.7 (_root_) および Python 3.5 (_py35_) 環境にインストールされます。 Windows では、Tensorflow は Python 3.5(_py35_) 環境にインストールされます。  |
| サンプルへのリンク      | サンプル Jupyter ノートブックが含まれています。     |
| DSVM 上の関連ツール      | Keras      |
| 使用/実行方法    | Jupyter を開き、TensorFlow フォルダーを探します。  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Windows、Linux     |
| DSVM での構成/インストール方法  | Keras は Python 2.7 (_root_) および Python 3.5 (_py35_) 環境にインストールされます。   |
| サンプルへのリンク      | https://github.com/fchollet/keras/tree/master/examples      |
| DSVM 上の関連ツール      | Microsoft Cognitive Toolkit、TensorLlow、Theano      |
| 使用/実行方法    | Github の場所からサンプルをダウンロードして ~/notebooks 配下のディレクトリにコピーし、Jupyter で開きます   |




## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Linux     |
| DSVM での構成/インストール方法  | Caffe は `/opt/caffe` にインストールされます。    |
| サンプルへのリンク      | サンプルは `/opt/caffe/examples` に含まれています。      |
| DSVM 上の関連ツール      | Caffe2      |
### <a name="how-to-use--run-it"></a>使用/実行方法  

X2Go を使用して VM にログインした後、新しいターミナルを起動して次のように入力します

```
cd /opt/caffe/examples
jupyter notebook
```

新しいブラウザー ウィンドウでサンプルのノートブックが開きます。

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Linux     |
| DSVM での構成/インストール方法  | Caffe2 は `/opt/caffe2` にインストールされます。 Python 2.7(_root_) conda 環境でも利用可能です。     |
| サンプルへのリンク      | サンプル Jupyter ノートブックが含まれています     |
| DSVM 上の関連ツール      | Caffe      |
| 使用/実行方法    | Jupyter を開いて Caffe2 ディレクトリに移動し、サンプルのノートブックを探します。 一部のノートブックについては、Caffe2 ルートを Python コードで設定する必要があります。/opt/caffe2 と入力します。   |


## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Windows、Linux     |
| DSVM での構成/インストール方法  | Chainer は Python 2.7 (_root_) および Python 3.5 (_py35_) 環境にインストールされます。 ChainerRL と ChainerCV もインストールされます。   |
| サンプルへのリンク      | サンプル Jupyter ノートブックが含まれています。      |
| DSVM 上の関連ツール      | Caffe      |

### <a name="how-to-use--run-it"></a>使用/実行方法  

ターミナルで、必要な Python バージョン (_root_ または _py35_) をアクティブ化し、_python_ を実行した後、Chainer をインポートします。 Jupyter で、Python 2.7 または 3.5 カーネルを選択した後、Chainer をインポートします。


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| 紹介   | H2O 向けのディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Linux     |
| DSVM での構成/インストール方法  | Deep Water は `/dsvm/tools/deep_water` にインストールされます。   |
| サンプルへのリンク      | サンプルは Deep Water サーバー経由で入手できます。      |
| DSVM 上の関連ツール      | H2o、Sparkling Water      |

### <a name="how-to-use--run-it"></a>使用/実行方法  

X2Go を使用して VM に接続します。 ターミナルで、Deep Water サーバーを起動します:

    java -jar /dsvm/tools/deep_water/h2o.jar

ブラウザーを開いて `http://localhost:54321` に接続します。



## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Windows、Linux     |
| DSVM での構成/インストール方法  | MXNet は、Windows では `C:\dsvm\tools\mxnet` に、Linux では `/dsvm/tools/mxnet` にインストールされます。 Python バインディングは、Python 2.7 (_root_) および Python 3.5 (_py35_) 環境にインストールされます。 R バインディングもインストールされます。   |
| サンプルへのリンク      | サンプル Jupyter ノートブックが含まれています。    |
| DSVM 上の関連ツール      | Keras      |
| 使用/実行方法    | Jupyter を開き、mxnet フォルダーを探します  |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング モデルを迅速にトレーニングするための NVIDIA 製ディープ ラーニング システム      |
| サポートされている DSVM エディション      | Linux     |
| DSVM での構成/インストール方法  | DIGITS は `/dsvm/tools/DIGITS` にインストールされ、_digits_ という名前のサービスとして利用できます。   |
### <a name="how-to-use--run-it"></a>使用/実行方法  

X2Go で VM にログインします。 ターミナルで、サービスを開始します:

    sudo systemctl start digits

サービスが開始するまで 1 分ほどかかります。 Web ブラウザーを起動し、`http://localhost:5000` にアクセスします。



## <a name="nvdia-smi"></a>nvdia-smi

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
| サポートされている DSVM エディション      | Linux     |
| DSVM での構成/インストール方法  | Theano は Python 2.7 (_root_) および Python 3.5 (_py35_) 環境にインストールされます。   |
| DSVM 上の関連ツール      | Keras      |
| 使用/実行方法    | ターミナルで、必要な Python バージョン (root または py35) をアクティブ化し、python を実行した後、theano をインポートします。 Jupyter で、Python 2.7 または 3.5 カーネルを選択した後、theano をインポートします。  |



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| 紹介   | ディープ ラーニング フレームワーク      |
| サポートされている DSVM エディション      | Linux     |
| DSVM での構成/インストール方法  | Torch は `/dsvm/tools/torch` にインストールされます。 PyTorch は Python 2.7 (_root_) および Python 3.5 (_py35_) 環境にインストールされます。   |
| サンプルへのリンク      | Torch サンプルは `/dsvm/samples/torch` にあります。 PyTorch サンプルは `/dsvm/samples/pytorch` にあります。      |

