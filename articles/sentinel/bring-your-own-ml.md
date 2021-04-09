---
title: 独自の ML を Azure Sentinel に持ち込む | Microsoft Docs
description: この記事では、Azure Sentinel でデータ分析用の独自の機械学習アルゴリズムを作成して使用する方法について説明します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: f8d795dcf64230140e1dd07e3f30ca3aa6825ab4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99807005"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>独自の機械学習 (ML) を Azure Sentinel に持ち込む

機械学習 (ML) は Azure Sentinel の主要な基盤の 1 つであり、Azure Sentinel を個別に設定する主要な属性の 1 つです。 Azure Sentinel はいくつかのエクスペリエンスで ML を提供します。これらは、[Fusion](fusion.md) 相関関係エンジンと Jupyter Notebook、新しく使用可能な独自の ML の構築 (BYO ML) プラットフォームに組み込まれています。 

ML 検出モデルでは、個々の環境とユーザー動作の変化に適応して誤検知を減らし、従来のアプローチでは検出されない脅威を特定することができます。 多くのセキュリティ組織がセキュリティに対する ML の価値を理解していますが、セキュリティと ML の両方の専門知識を持っているプロフェッショナルは多くはありません。 ここで紹介するフレームワークは、セキュリティ組織とプロフェッショナルが ML の取り組みの中で成長できるように設計されています。 ML を初めて使用する組織、または必要な専門知識がない組織は、Azure Sentinel の組み込み ML 機能から大幅な保護価値を得ることができます。

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="機械学習フレームワーク":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>独自の機械学習 (BYO-ML) プラットフォームとは

ML リソースがあり、独自のビジネス ニーズに合わせてカスタマイズされた ML モデルを構築する必要がある組織には、**BYO-ML プラットフォーム** を提供しています。 このプラットフォームでは、[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)/[Apache Spark](http://spark.apache.org/) 環境と Jupyter Notebook を使用して ML 環境を生成します。 提供されるコンポーネントを次に示します。

- BYO-ML パッケージ。データにアクセスして結果を Log Analytics (LA) にプッシュするために役立つライブラリが含まれているため、結果を検出、調査、および検索に統合できます。 

- ML アルゴリズム テンプレート。組織内の特定のセキュリティ上の問題に合わせてカスタマイズすることができます。 

- モデルをトレーニングし、モデルのスコアリングをスケジュールするサンプル ノートブック。 

これ以外にも、独自の ML モデルや独自の Spark 環境を使用して Azure Sentinel と統合できます。

BYO-ML プラットフォームでは、独自の ML モデルの構築に関するジャンプ スタートを利用できます。 

- サンプル データを含むノートブックを使用すると、運用データの処理を気にせずに、実践的な体験をエンドツーエンドで行うことができます。

- Spark 環境に統合されたパッケージにより、インフラストラクチャの管理における課題と摩擦が軽減されます。

- これらのライブラリはデータ移動をサポートしています。 トレーニング ノートブックとスコアリング ノートブックは、エンドツーエンドのエクスペリエンスのデモンストレーションであり、環境に合わせるためのテンプレートとして機能します。

### <a name="use-cases"></a>ユース ケース
 
BYO-ML プラットフォームとパッケージによって、独自の ML 検出を構築するために必要な時間と労力が大幅に削減され、Azure Sentinel で特定のセキュリティ問題に対処する機能が提供されます。 このプラットフォームでは、次のユース ケースがサポートされています。

**ML アルゴリズムをトレーニングして、カスタマイズされたモデルを取得する:** (Microsoft またはユーザー コミュニティによって共有されている) 既存の ML アルゴリズムを使用して、独自のデータで簡単にトレーニングを行い、データと環境に合わせてカスタマイズされた ML モデルを取得できます。

**ML アルゴリズム テンプレートを変更して、カスタマイズされたモデルを取得する:** (Microsoft またはユーザー コミュニティによって共有されている) ML アルゴリズム テンプレートを変更し、独自のデータに対して変更されたアルゴリズムをトレーニングして、特定の問題に合わせてカスタマイズされたモデルを作成することができます。

**独自のモデルを作成する:** Azure Sentinel の BYO-ML プラットフォームとユーティリティを使用して、独自のモデルを最初から作成します。

**Databricks/Spark 環境を統合する:** 既存の Databricks/Spark 環境を Azure Sentinel に統合し、BYO-ML ライブラリとテンプレートを使用して、独自の状況に合わせて ML モデルを構築します。

**独自の ML モデルをインポートする:** 独自の ML モデルをインポートし、BYO-ML プラットフォームとユーティリティを使用して Azure Sentinel と統合できます。

**ML アルゴリズムを共有する:** ML アルゴリズムを共有して、コミュニティが採用して適応できるようにします。

**ML を使用して SecOps を強化する:** 独自のカスタム ML モデルと、検索、検出、調査、および対応の結果を使用します。

この記事では、BYO-ML プラットフォームのコンポーネントと、プラットフォームと異常なリソース アクセス アルゴリズムを活用して、Azure Sentinel でカスタマイズされた ML 検出を提供する方法について説明します。

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark 環境

[Apache Spark™](http://spark.apache.org/) は、データ パイプラインを構築するための統一されたフレームワークを提供することで、ビッグ データの単純化を進歩させました。 Azure Databricks は、Spark を中心に構築されたゼロ管理クラウド プラットフォームを提供することで、これをさらに強化します。 BYO-ML プラットフォームには Databricks を使用することをお勧めします。これにより、データ パイプラインやプラットフォームの問題に取り組むのではなく、ビジネスに直ちに影響を与える答えを探すことに専念できます。
既に Databricks またはその他の Spark 環境があり、既存のセットアップを使用する場合は、BYO-ML パッケージも同様に機能します。 

## <a name="byo-ml-package"></a>BYO-ML パッケージ

BYO ML パッケージには、セキュリティのための ML のフロントエンドにおける Microsoft のベスト プラクティスと調査が含まれています。 このパッケージには、セキュリティ上の問題について、次のユーティリティ、ノートブック、およびアルゴリズム テンプレートの一覧が用意されています。

| ファイル名 | 説明 |
| --------- | ----------- |
| azure_sentinel_utilities.whl | Azure から BLOB を読み取り、Log Analytics に書き込むためのユーティリティが含まれています。 |
| AnomalousRASampleData | ノートブックは、生成されたトレーニングとテストのサンプル データを使用して、Azure Sentinel で異常なリソース アクセス モデルを使用する方法を示します。 |
| AnomalousRATraining.ipynb | アルゴリズムをトレーニングし、モデルを作成して保存するためのノートブック。 |
| AnomalousRAScoring.ipynb | モデルの実行をスケジュールして結果を視覚化し、スコアを Azure Sentinel に書き込むためのノートブック。 |
|

最初に提供される ML アルゴリズム テンプレートは、[異常なリソース アクセス検出](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML)用です。 これは協調フィルタリング アルゴリズムに基づいており、Windows ファイル共有のアクセス ログ (イベント ID 5140 のセキュリティ イベント) でトレーニングされています。 ログでこのモデルに必要な重要な情報は、アクセスされたユーザーとリソースの組み合わせです。 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>例のチュートリアル:異常なファイル共有アクセス検出 

ここでは、BYO-ML プラットフォームの主要なコンポーネントについて説明しました。次の例では、このプラットフォームとコンポーネントを使用して、カスタマイズされた ML 検出を提供する方法を示します。

### <a name="setup-the-databricksspark-environment"></a>Databricks/Spark 環境の設定

まだお持ちでない場合は、独自の Databricks 環境を設定する必要があります。 手順については、[Databricks のクイック スタート](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal)に関するドキュメントを参照してください。

### <a name="auto-export-instruction"></a>自動エクスポート命令

Azure Sentinel で独自のデータに基づいてカスタム ML モデルを作成するには、データを Log Analytics から Blob Storage またはイベント ハブ リソースにエクスポートし、ML モデルが Databricks からアクセスできるようにする必要があります。 [Azure Sentinel にデータを取り込む](connect-data-sources.md)方法について説明します。

この例では、Azure Blob Storage のファイル共有アクセス ログのトレーニング データが必要です。 データの形式については、ノートブックとライブラリに記載されています。

[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/monitor/log-analytics) を使用して、Log Analytics からデータを自動的にエクスポートできます。 

コマンドを実行するには、Log Analytics ワークスペース、ストレージ アカウント、および EventHub リソースの **共同作成者** ロールが割り当てられている必要があります。 

自動エクスポートを設定するためのコマンドのサンプル セットを次に示します。

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>カスタム データをエクスポートする

Log Analytics の自動エクスポートでサポートされていないカスタム データの場合は、ロジック アプリまたはその他のソリューションを使用してデータを移動できます。 ブログ記事「[Log Analytics データを BLOB ストアにエクスポートする](https://www.borninthecloud.com/exporting-log-analytics-data-to-blob-store/?preview=true)」とスクリプトを参照してください。

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Azure Sentinel 以外のデータとの関連付け

Azure Sentinel の外部から Blob Storage またはイベント ハブにデータを取り込み、それを Azure Sentinel データと関連付けて ML モデルを構築することもできます。 
 
### <a name="copy-and-install-the-related-packages"></a>関連パッケージをコピーしてインストールする

前述の Azure Sentinel GitHub リポジトリから Databricks 環境に BYO-ML パッケージをコピーします。 次にノートブックを開き、ノートブック内の指示に従って、必要なライブラリをクラスターにインストールします。

### <a name="model-training-and-scoring"></a>モデルのトレーニングとスコアリング

2 つのノートブックに記載されている手順に従って自分の環境とリソースに応じて構成を変更し、モデルのトレーニングとビルドの手順に従ってから、ファイル共有アクセスの受信ログをスコアリングするようにモデルをスケジュールします。

### <a name="write-results-to-log-analytics"></a>結果を Log Analytics に書き込む

スコアリングがスケジュールされたら、スコアリング ノートブックのモジュールを使用して、Azure Sentinel インスタンスに関連付けられている Log Analytics ワークスペースにスコアの結果を書き込むことができます。

### <a name="check-results-in-azure-sentinel"></a>Azure Sentinel で結果を確認する

スコアリングされた結果を関連ログの詳細と共に表示するには、Azure Sentinel ポータルに戻ります。 **[ログ]** > [カスタム ログ] で、**AnomalousResourceAccessResult_CL** テーブル (または独自のカスタム テーブル名) に結果が表示されます。 これらの結果を使用して、調査と検索のエクスペリエンスを向上させることができます。

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="異常なリソース アクセス ログ":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>ML の結果を使用してカスタム分析ルールを作成する

ML の結果がカスタム ログ テーブルにあり、スコアの忠実度が満たされていることを確認したら、結果に基づいて検出を作成できます。 Azure Sentinel ポータルから **Analytics** にアクセスし、[新しい検出ルールを作成します](tutorial-detect-threats-custom.md)。 検出の作成に使用されるクエリの例を次に示します。

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="B Y O M L 検出のためのカスタム分析ルールを作成する":::

### <a name="view-and-respond-to-incidents"></a>インシデントを表示して対応する
ML の結果に基づいて分析ルールを設定した後、クエリで設定したしきい値を超える結果が発生すると、インシデントが生成されて Azure Sentinel の **[インシデント]** ページに表示されます。 

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Sentinel の BYO-ML プラットフォームを使用して、データを分析して脅威を検出する独自の機械学習アルゴリズムを作成またはインポートする方法について学習しました。

- [Azure Sentinel ブログ](https://aka.ms/azuresentinelblog)にある機械学習やその他の関連トピックに関する投稿をご覧ください。