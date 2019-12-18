---
title: サンプル プログラムと ML のチュートリアル
titleSuffix: Azure Data Science Virtual Machine
description: これらのサンプルとチュートリアルを通じて、Data Science Virtual Machine を使用して一般的なタスクとシナリオを処理する方法を説明します。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d064e940997d13e51be4fd38e9f678b778ce31f4
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900044"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Azure Data Science Virtual Machine 上のサンプル

Azure Data Science Virtual Machines (DSVM) には、サンプル コードの包括的なセットが含まれています。 これらのサンプルには、Python や R などの言語による Jupyter ノートブックおよびスクリプトが含まれています。
> [!NOTE]
> データ サイエンス仮想マシン上で Jupyter ノートブックを実行する方法の詳細については、「[Jupyter にアクセスする](#access-jupyter)」のセクションを参照してください。

## <a name="prerequisites"></a>前提条件

これらのサンプルを実行するには、Data Science Virtual Machine をプロビジョニングしておく必要があります。 [Windows](./provision-vm.md) および [Ubuntu](./dsvm-ubuntu-intro.md) のクイックスタートを参照してください。

## <a name="available-samples"></a>使用可能なサンプル
| サンプル カテゴリ | 説明 | 場所 |
| ------------- | ------------- | ------------- |
| R 言語  | サンプルは、Azure ベースのクラウド データ ストアに接続する方法や、オープンソースの R と Microsoft Machine Learning Server を比較する方法などのシナリオを示しています。 また、Microsoft Machine Learning Server と SQL Server 上でモデルを運用化する方法についても説明しています。 <br/> [R 言語](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 言語  | サンプルでは、Azure ベースのクラウド データ ストアに接続する方法や、Azure Machine Learning を操作する方法などのシナリオについて説明します。  <br/> [Python 言語](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 言語  | Julia でのプロットとディープ ラーニングの詳細な説明を提供します。 また、Julia から C および Python を呼び出す方法についても説明します。 <br/> [Julia 言語](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Machine Learning で機械学習およびディープ ラーニング モデルを構築する方法を説明します。 モデルを任意の場所にデプロイします。 自動化された機械学習およびインテリジェント ハイパーパラメーター チューニングを使用します。 また、モデル管理および分散トレーニングも使用します。 <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch ノートブック  | PyTorch ベースのニューラル ネットワークを使用するディープ ラーニング サンプル。 ノートブックには初心者から高度なシナリオまでの幅があります。  <br/> [PyTorch ノートブック](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  TensorFlow フレームワークを使用して実装された、さまざまなニューラル ネットワーク サンプルおよび技法。 <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Microsoft の Cognitive Toolkit チームによって公開されているディープ ラーニング サンプル。  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Caffe2 ベースのニューラル ネットワークを使用するディープ ラーニング サンプル。 いくつかのノートブックにより、ユーザーは Caffe2 およびそれを効果的に使用する方法に精通します。 その例には、イメージの前処理やデータセットの作成が含まれます。 また、回帰や、事前トレーニング済みモデルを使用する方法も含まれます。 <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | 実世界の問題のシナリオのために H2O を使用する Python ベースのサンプル。 <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 言語  | pySpark および MMLSpark 経由で Apache Spark MLLib ツールキットの機能を使用するサンプル。Apache Spark または Apache Spark 2.x の Microsoft Machine Learning。  <br/> [SparkML 言語](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | 分類、回帰などのシナリオ向けの、XGBoost における標準的な機械学習サンプル。 <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Jupyter にアクセスする 

Jupyter にアクセスするには、デスクトップまたはアプリケーション メニューの **[Jupyter]** アイコンを選択します。 また、DSVM の Linux エディションでも Jupyter にアクセスできます。 Web ブラウザーからリモートでアクセスするには、Ubuntu 上で `https://<Full Domain Name or IP Address of the DSVM>:8000` に移動します。

例外を追加し、ブラウザー経由で Jupyter にアクセスできるようにするには、次のガイダンスを使用してください。


![Jupyter の例外を有効にする](./media/ubuntu-jupyter-exception.png)


Data Science Virtual Machine へのログインに使用するパスワードと同じパスワードでサインインします。
<br/>

**Jupyter ホーム**
<br/>![Jupyter ホーム](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 言語 
<br/>![R サンプル](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 言語
<br/>![Python のサンプル](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 言語 
<br/>![Julia サンプル](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Azure Machine Learning サンプル](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch サンプル](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow サンプル](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK サンプル](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 サンプル](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O サンプル](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML サンプル](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost サンプル](./media/xgboost-samples.png)<br/>

