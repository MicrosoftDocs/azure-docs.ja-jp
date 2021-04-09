---
title: データフローを使用して自動機械学習 (AutoML) モデルのデータを処理する
description: Azure Data Factory データ フローを使用して、自動機械学習 (AutoML) モデルのデータを処理する方法について説明します。
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: e8352b687a3cdfac7ea2a819e1217906598a6837
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563268"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>データ フローを使用して自動機械学習 (AutoML) モデルのデータを処理する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

自動機械学習 (AutoML) は、分類、回帰、および時系列予測に指定したターゲット メトリックを使用して、最適なモデルを自動的にトレーニング、調整、および取得するために、機械学習プロジェクトで採用されています。 

1 つの課題は、データ ウェアハウスまたはトランザクション データベースからの生データが、10 GB のような巨大なデータセットになることです。大規模なデータセットでは、モデルのトレーニングに時間がかかるため、Azure Machine Learning モデルをトレーニングする前にデータ処理を最適化することをお勧めします。 このチュートリアルでは、ADF を使用してデータセットを Azure Machine Learning データセットの parquet ファイルにパーティション分割する方法について説明します。 

自動機械学習 (AutoML) プロジェクトでは、次の 3 つのデータ処理シナリオが適用されます。

* モデルをトレーニングする前に、大きなデータを parquet ファイルにパーティション分割します。 

     [Pandas データ フレーム](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html)は、一般的にモデルをトレーニングする前にデータを処理するために使用されます。 Pandas データ フレームは 1 GB 未満のデータ サイズに適していますが、データが 1 GB を超える場合は、データを処理するために Pandas データ フレームの速度が低下し、メモリ不足のエラー メッセージが表示されることもあります。 [Parquet ファイル](https://parquet.apache.org/)形式は、バイナリ列形式であるため、機械学習に推奨されています。
    
    Azure Data Factory のマッピング データ フローは、データ エンジニアがコーディング不要の視覚的に設計されたデータ変換です。 パイプラインではスケールアウトされた Spark クラスターを使用するため、大きなデータを処理するためには強力です。

* トレーニング データセットとテスト データセットを分割します。
    
    トレーニング データセットはトレーニング モデルに使用され、テスト データセットは機械学習プロジェクトでのモデルの評価に使用されます。 マッピング データ フローの条件分割アクティビティでは、トレーニング データとテスト データを分割します。 

* 非修飾データを削除します。

    非修飾データ (行が 0 の parquet ファイルなど) は、削除することができます。 このチュートリアルでは、集計アクティビティを使用して行数を取得します。行数は、非修飾データを削除する条件になります。 


## <a name="preparation"></a>準備
Azure SQL Database の次のテーブルを使用します。 
```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>データ形式を parquet に変換する

データ フローで、Azure SQL Database のテーブルが parquet ファイル形式に変換されます。 

**ソース データセット**:Azure SQL Database のトランザクション テーブル

**シンク データセット**:Parquet 形式の BLOB ストレージ


## <a name="remove-unqualified-data-based-on-row-count"></a>行数に基づいて非修飾データを削除する

2 未満の行数を削除するとします。 

1. 集計アクティビティを使用して行数を取得します。Col2 に基づいて "**グループ化**" し、行数を count(1) で "**集計**" します。 

    ![行数を取得するように集計アクティビティを構成する](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. シンク アクティビティを使用し、 **[シンク]** タブで [キャッシュ] として **[シンクの種類]** を選択し、 **[設定]** タブの **[キー列]** ドロップダウン リストから目的の列を選択します。 

    ![キャッシュされたシンク内の行数を取得するように CacheSink アクティビティを構成する](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. 派生列アクティビティを使用して、ソース ストリーム内の行数列を追加します。 **[派生列の設定]** タブで、CacheSink#lookup 式を使用して SinkCache から行数を取得します。
    ![ソース 1 の行数を加算するように派生列アクティビティを構成する](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. 条件分割アクティビティを使用して、非修飾データを削除します。 この例では、行数は Col2 列に基づき、条件は 2 未満の行数を削除することであるため、2 つの行 (ID=2 と ID=7) が削除されます。 データ管理のために、非修飾データを BLOB ストレージに保存します。 

    ![2 より大きいか等しいデータを取得するように条件分割アクティビティを構成する](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    後の手順の元のソースで使用される行数を取得するための新しいソースを作成してください。 
>    *    パフォーマンスの観点から CacheSink を使用してください。 

## <a name="split-training-data-and-test-data"></a>トレーニング データとテスト データを分割する 

1. パーティションごとにトレーニング データとテスト データを分割する必要があります。 この例では、Col2 の値が同じである場合、一番上の 2 行をテスト データとして取得し、残りの行をトレーニング データとして取得します。 

    ウィンドウ アクティビティを使用して、パーティションごとに 1 列の行番号を追加します。 **[Over]** タブで、パーティションの列を選択し (このチュートリアルでは、Col2 のパーティションを分割します)、 **[並べ替え]** タブで順序を指定し (このチュートリアルでは、ID に基づいて順序を指定します)、 **[ウィンドウ列]** タブで、各パーティションの行番号として 1 つの列を追加します。 
    ![1 つの新しい列を行番号として追加するようにウィンドウ アクティビティを構成する](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. 条件分割アクティビティを使用して、各パーティションの一番上の 2 行をテスト データセットに、残りの行をトレーニング データセットに分割します。 **[条件分割の設定]** タブで、条件として式 LesserOrEqual(RowNum,2) を使用します。 

    ![現在のデータセットをトレーニング データセットとテスト データセットに分割するように条件分割アクティビティを構成する](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>parquet 形式でトレーニング データセットとテスト データセットをパーティション分割する

1. **[最適化]** タブで、 **[パーティションごとの一意の値]** を使用してパーティションの列キーとして列を設定して、シンク アクティビティを使用します。 
    ![トレーニング データセットのパーティションを設定するようにシンク アクティビティを構成する](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    パイプライン全体のロジックを見てみましょう。
    ![パイプライン全体のロジック](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>次のステップ

* マッピング データ フローの[変換](concepts-data-flow-overview.md)を使用して、残りのデータ フロー ロジックを構築します。
