---
title: "Azure Machine Learning を使用した顧客離れ分析 | Microsoft Docs"
description: "Azure ML Workbench を使用してチャーン分析を実行する方法。"
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 55298a7ff96bd9662310fa6b5d1764370f500be5
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Azure Machine Learning を使用した顧客離れ予測

平均すると、既存の顧客を維持するコストは、新規顧客を作るコストよりも 5 倍安くなります。 そのため、多くのマーケティング部門の役員は、顧客離れの可能性を見積もり、チャーン レートを最小限に抑えるために必要な対応を見つけることを重視しています。

このソリューションの目的は、Azure Machine Learning Workbench を使用する予測チャーン分析を紹介することです。 このソリューションを使用すると、テンプレートを使用して、小売店向けにチャーン予測データ パイプラインを簡単に開発できます。 このテンプレートは、さまざまなデータセットとさまざまなチャーン定義で使用できます。 この実践的な例の目的は以下のとおりです。

1. Azure Machine Learning Workbench のデータ準備ツールで、チャーン分析のために顧客リレーションシップ データをクリーニングし、取り込む方法を理解します。

2. 機能変換を実行して、ノイズの多い異種データを処理します。

3. サードパーティ ライブラリ (`scikit-learn`、`azureml` など) を統合して、チャーン予測のためにベイジアンおよびツリーベースの分類を開発します。

4. デプロイします。

## <a name="link-of-the-gallery-github-repository"></a>ギャラリーの GitHub リポジトリのリンク
以下は、すべてのコードがホストされているパブリック GitHub リポジトリのリンクです。

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>ユース ケースの概要
企業は、顧客離れを管理する効果的な戦略を必要としています。 顧客離れには、サービスの利用を停止した顧客、競合他社のサービスに切り替えた顧客、サービスの低階層のエクスペリエンスに切り替えた顧客、またはサービスの契約を減らした顧客が含まれます。

このユース ケースでは、フランスの通信会社 Orange のデータを見て、サービスを改善し、顧客の維持に役立つ顧客アウトリーチ キャンペーンを作成するために、近い将来に解約する可能性が高い顧客を特定します。

通信会社は、競争市場に直面しています。 多くの通信会社は、顧客離れのために後払いの顧客からの収益を失っています。 そのため、顧客離れを正確に特定できることは、競合上の大きな利点になります。

通信会社の顧客離れに影響する主な要素は次のとおりです。

* サービスの中断が頻繁に見られる
* オンライン/小売店舗で顧客サービスのエクスペリエンスが低品質
* 他の競合通信会社からのプラン (優れた家族プラン、データ プランなど)。

このソリューションでは、通信会社の顧客離れ予測モデルを構築するという具体的な例を使用します。

## <a name="prerequisites"></a>前提条件

* [Azure アカウント](https://azure.microsoft.com/free/) (無料試用版もご利用いただけます)

* [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) のインストール済みコピー。[クイックスタート インストール ガイド](./quickstart-installation.md)に従ってプログラムをインストールし、ワークスペースを作成します

* 運用化のために、Docker エンジンをローカルにインストールして実行することをお勧めします。 インストールせずにクラスター オプションを使用することもできますが、Azure Container Service (ACS) の実行コストは高くなります。

* このソリューションでは、Docker エンジンをローカルにインストールした Windows 10 で Azure Machine Learning Workbench を実行しているものとします。 macOS を使用している場合も、手順の多くは同じです。

## <a name="create-a-new-workbench-project"></a>新しいワークベンチ プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。
1.  Azure Machine Learning Workbench を開きます
2.  **[プロジェクト]** ページで **+** 記号をクリックし、**[新しいプロジェクト]** を選択します
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します
4.  **[プロジェクト テンプレートの検索]** 検索ボックスに「Customer Churn Prediction」と入力し、テンプレートを選択します
5.  **[作成]** をクリックします

## <a name="data-description"></a>データの説明

ソリューションで使用されているデータ セットは、SIDKDD 2009 競合のデータです。 これは `CATelcoCustomerChurnTrainingSample.csv` と呼ばれ、[`Data`](https://github.com/mezmicrosoft/MachineLearningSamples-ChurnPrediction/tree/master/Data) フォルダーにあります。 データセットは、フランスの通信会社 Orange のノイズの多い異種データ (数値/カテゴリ変数) で構成され、匿名化されます。

これらの変数は、顧客の統計情報、呼び出しの統計情報 (平均呼び出し期間、呼び出しエラー率など)、コントラクト情報、苦情の統計情報をキャプチャします。 チャーン変数はバイナリです (0 - 解約なし、1 - 解約あり)。

## <a name="scenario-structure"></a>シナリオの構造

フォルダー構造は次のようになっています。

__Code__: Azure Machine Learning Workbench を使用したチャーン予測に関連するすべてのコードが含まれています。

__Data__: このソリューションで使用するデータセットが含まれています。  

__Labs__: すべてのハンズオン ラボが含まれています。

このソリューションのハンズオン ラボの実行順序は次のとおりです。
1. データの準備: コード フォルダーのデータ準備に関連するファイルは、`CATelcoCustomerChurnTrainingSample.dprep`、`CATelcoCustomerChurnTrainingSample.dconn`、および `CATelcoCustomerChurnTrainingSample.csv` です
2. モデリングと評価: コード フォルダー内のモデリングと評価に関連する主なファイルは `CATelcoCustomerChurnModeling.py` です
3. Docker のモデリングと評価: コード フォルダーのこのタスクのメイン ファイルは `CATelcoCustomerChurnModelingDocker.py` です
4. 運用化: デプロイのメイン ファイルはモデル (`model.pkl`) と `churn_schema_gen.py` です

| 順序| ファイル名 | 関連するファイル |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/Docs/DataPreparation.md) | 'Data/CATelcoCustomerChurnTrainingSample.csv' |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/Docs/ModelingAndEvaluation.md) | 'Code/CATelcoCustomerChurnModeling.py' |
| 3 | [`ModelingAndEvaluationDocker.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/Docs/ModelingAndEvaluationDocker.md) | 'Code/CATelcoCustomerChurnModelingDocker.py' |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/Docs/Operationalization.md) | 'Code/model.pkl'<br>'Code/churn_schema_gen.py' |

上記の順序でラボを実行します。

## <a name="conclusion"></a>まとめ
この実践的なシナリオでは、Azure Machine Learning Workbench を使用してチャーン予測を実行する方法について説明しました。 まず、ノイズが多い異種データを処理するためにデータのクリーニングを実行してから、データ準備ツールを使用して特徴エンジニアリングを実行します。 次に、オープン ソースの機械学習ツールを使用して分類モデルを作成して評価してから、ローカル Docker コンテナーを使用して、運用の準備を整えるモデルをデプロイします。

