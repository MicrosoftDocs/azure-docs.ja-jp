---
title: データ サイエンス仮想マシンのサンプルとチュートリアル - Azure | Microsoft Docs
description: Data Science Virtual Machine で一般的なタスクとシナリオを実現する方法を学習できるサンプルとチュートリアルについて説明します。
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
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: e61f0f4ba30b29fea1b2fd5f2a2ab253d3a6710c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475756"
---
# <a name="samples-on-data-science-virtual-machines"></a>データ サイエンス仮想マシンに関するサンプル

Azure データ サイエンス仮想マシンには、サンプル コードの包括的なセットが含まれています。 このサンプル コードは Jupyter ノートブックの形式であり、スクリプトは Python や R などの言語で記述されています。 
> [!NOTE]
> データ サイエンス仮想マシン上で Jupyter ノートブックを実行する方法の詳細については、「[Jupyter にアクセスする](#access-jupyter)」のセクションを参照してください。

## <a name="quick-reference-of-samples"></a>サンプルのクイック リファレンス
| サンプル カテゴリ | 説明 | 場所 |
| ------------- | ------------- | ------------- |
| R 言語  | R でのサンプルでは、Azure クラウド データ ストアに接続する方法などのシナリオについて説明します。 また、オープンソースの R と Microsoft R を比較する方法についても説明します。さらに、Microsoft R Server または SQL Server 上でモデルを運用化する方法について説明します。 <br/> [R 言語](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 言語  | Python でのサンプルでは、Azure クラウド データ ストアに接続し、Azure Machine Learning を操作する方法などのシナリオについて説明します。  <br/> [Python 言語](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 言語  | Julia でのプロットやディープ ラーニングについて詳細に説明している Julia でのサンプル。 また、Julia からの C および Python の呼び出しについても説明します。 <br/> [Julia 言語](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Machine Learning で機械学習およびディープ ラーニング モデルを構築します。 モデルを任意の場所にデプロイします。 自動化された機械学習およびインテリジェント ハイパーパラメーター チューニングを使用します。 また、モデル管理および分散トレーニングも使用します。 <br/> [Machine Learning](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch ノートブック  | PyTorch ベースのニューラル ネットワークを使用するディープ ラーニング サンプル。 ノートブックには初心者から高度なシナリオまでの幅があります。  <br/> [PyTorch ノートブック](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  TensorFlow フレームワークを使用して実装された、さまざまなニューラル ネットワーク サンプルおよび技法。 <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Microsoft の Cognitive Toolkit チームによって公開されているディープ ラーニング サンプル。  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| caffe2 | caffe2 ベースのニューラル ネットワークを使用するディープ ラーニング サンプル。 いくつかのノートブックにより、ユーザーは caffe2 およびそれを効果的に使用する方法に精通します。 その例には、イメージの前処理やデータ セットの作成が含まれます。 また、回帰や、事前トレーニング済みモデルを使用する方法も含まれます。 <br/> [caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | 実世界のシナリオの問題のために H2O を使用する Python ベースのサンプル。 <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 言語  | Apache Spark 2.x 上で pySpark および MMLSpark--Microsoft Machine Learning for Apache Spark 経由で Spark MLLib ツールキットの機能を使用するサンプル。  <br/> [SparkML 言語](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | 分類や回帰などのシナリオのための XGBoost での標準の機械学習サンプル。 <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Jupyter にアクセスする 

Jupyter にアクセスするには、デスクトップまたはアプリケーション メニューの `Jupyter` アイコンを選択します。 また、データ サイエンス仮想マシンの Linux エディションでも Jupyter にアクセスできます。 Ubuntu 上で `https://<Full Domain Name or IP Address of the DSVM>:8000` に移動することによって、Web ブラウザーからリモートでアクセスできます。

例外を追加し、ブラウザー経由で Jupyter にアクセスできるようにするには、次のスクリーンショットを参照してください。


![Jupyter の例外を有効にする](./media/ubuntu-jupyter-exception.png)


データ サイエンス仮想マシンへのログインと同じパスワードでサインインします。
<br/>

**Jupyter ホーム**
<br/>![Jupyter ホーム](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 言語 
<br/>![R サンプル](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 言語
<br/>![Python のサンプル](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 言語 
<br/>![Julia サンプル](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML サンプル](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch サンプル](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow サンプル](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK サンプル](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>caffe2 
<br/>![caffe2 サンプル](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O サンプル](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML サンプル](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost サンプル](./media/xgboost-samples.png)<br/>

