---
title: "データ サイエンス仮想マシンのサンプルとチュートリアル - Azure | Microsoft Docs"
description: "データ サイエンス仮想マシンのサンプルとチュートリアル。"
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
ms.openlocfilehash: 89bb5d255db9ab266d04169a3101e2b694236029
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>データ サイエンス仮想マシン (DSVM) に関するサンプル

DSVM には、Jupyter Notebook 形式の完全に動作するサンプルと、Jupyter ベースでないいくつかのサンプルが付属します。 デスクトップまたはアプリケーション メニューの `Jupyter` アイコンをクリックすると Jupyter にアクセスできます。  
> [!NOTE]
> DSVM で Jupyter Notebook を有効にするには、「[Access Jupyter (Jupyter にアクセスする)](#access-jupyter)」セクションを参照してください。

## <a name="quick-reference-of-samples"></a>サンプルのクイックリファレンス
| サンプル カテゴリ | Description | 場所 |
| ------------- | ------------- | ------------- |
| **R** 言語  | **R** のサンプルでは、Azure クラウド データ ストアとの接続、オープン ソースの R と Microsoft R の比較、Microsoft R Server または SQL Server での操作可能化モデルなどのシナリオを説明します。 <br/> [スクリーンショット](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** 言語  | **Python** のサンプルで、Azure クラウド データ ストアとの接続や、**Azure Machine Learning** の操作などのシナリオを説明します。  <br/> [スクリーンショット](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Julia** 言語  | **Julia** 内のサンプルで、Julia でのプロット、Julia でのディープ ラーニング、Julia からの C や Python の呼び出しなどを詳しく説明します。 <br/> [スクリーンショット](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **CNTK** <br/> (Microsoft Cognitive Toolkit)  | Microsoft の Cognitive Toolkit チームによって公開されているディープ ラーニング サンプル。  <br/> [スクリーンショット](#cntk) | <br/>**Windows**:<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **MXnet** ノートブック  | ニューラル ネットワークに基づいて **MXnet** を利用したディープ ラーニング サンプル。 初心者から高度なシナリオまでのさまざまなノートブックがあります。  <br/> [スクリーンショット](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| **Azure Machine Learning** AzureML  | クラウドベースのスコアリング ワークフローのために、ローカルでトレーニングしたモデルから、**Azure Machine Learning** Studio と対話し、Web サービス エンドポイントを作成します。 <br/> [スクリーンショット](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **caffe2** | ニューラル ネットワークに基づいて **caffe2** を利用したディープ ラーニング サンプル。 イメージの前処理、データセット作成、回帰、事前トレーニング済みモデルの使用などの例を含め、caffe2 とその効果的な使用方法をユーザーに理解してもらうために設計された、複数のノートブックがあります。 <br/> [スクリーンショット](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | さまざまな現実世界のシナリオの問題に対して **H2O** を利用する、Python ベースのサンプル。 <br/> [スクリーンショット](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **SparkML** 言語  | **Apache Spark 2.0** 上の **pySpark 2.0** を通じて、Spark の **MLlib** ツールキットの特徴および機能を利用するサンプル。  <br/> [スクリーンショット](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| **MMLSpark** 言語  | **MMLSpark - Microsoft Machine Learning for Apache Spark** を利用するさまざまなサンプル。MMLSpark は、**Apache Spark** 向けに数々のディープ ラーニングおよびデータ サイエンス ツールを提供するフレームワークです。 <br/> [スクリーンショット](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **TensorFlow**  | **TensorFlow** フレームワークを使用して実装された、ニューラル ネットワークの各種サンプルおよび技法。 <br/> [スクリーンショット](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | 分類、回帰などのシナリオ向けの、**XGBoost** における標準的な機械学習サンプル。 <br/> [スクリーンショット](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Jupyter にアクセスする 

Windows では **`https://localhost:9999`**、Ubuntu では **`https://localhost:8000`** から Jupyter ホームにアクセスします。


### <a name="enabling-jupyter-access-from-browser"></a>ブラウザーからの Jupyter アクセスの有効化

**Windows DSVM**

デスクトップのショートカットから **`Jupyter SetPassword`** を実行し、プロンプトに従って Jupyter のパスワードを設定/再設定し、Jupyter プロセスを開始します。 
<br/>![Jupyter の例外を有効にする](./media/jupyter-setpassword.png)<br/>
VM 上で Jupyter プロセスが正常に開始したら、ブラウザーで **`https://localhost:9999`** にアクセスすることによって Jupyter ホームにアクセスできます。 スクリーンショットを参考に、例外を追加してブラウザー経由の Jupyter アクセスを有効にします
<br/>![Jupyter の例外を有効にする](./media/windows-jupyter-exception.png)<br/>
設定した新しいパスワードでサインインします。
<br/>
**Linux DSVM**

ブラウザーで **`https://localhost:8000`** にアクセスすることにより、VM 上で Jupyter ホームにアクセスできます。 スクリーンショットを参考に、例外を追加してブラウザー経由の Jupyter アクセスを有効にします。
<br/>![Jupyter の例外を有効にする](./media/ubuntu-jupyter-exception.png)<br/>
DSVM のログインと同じパスワードでサインインします。
<br/>

**Jupyter ホーム**
<br/>![Jupyter ホーム](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 言語 
<br/>![R サンプル](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 言語
<br/>![Python サンプル](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 言語 
<br/>![Julia サンプル](./media/julia-samples.png)<br/>

## <a name="cntk"></a>CNTK 
<br/>![CNTK サンプル](./media/cntk-samples2.png)<br/>
<br/>![CNTK サンプル](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXnet
<br/>![MXnet サンプル](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML サンプル](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>caffe2 
<br/>![caffe2 サンプル](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O サンプル](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML サンプル](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow サンプル](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost サンプル](./media/xgboost-samples.png)<br/>

