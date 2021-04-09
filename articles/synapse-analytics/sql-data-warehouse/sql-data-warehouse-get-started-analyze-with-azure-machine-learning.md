---
title: Azure Machine Learning を使用したデータの分析
description: Azure Machine Learning を使用して、Azure Synapse に格納されたデータに基づいて予測機械学習モデルを構築します。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 76a154d3a137017f374247308a3980d598698246
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678661"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Azure Machine Learning を使用したデータの分析

このチュートリアルでは、[Azure Machine Learning デザイナー](../../machine-learning/concept-designer.md)を使用して、予測機械学習モデルを構築します。 このモデルは、Azure Synapse に格納されているデータに基づきます。 チュートリアルのシナリオは、Adventure Works (自転車店) でターゲット マーケティング キャンペーンを作成できるように、顧客が自転車を購入する可能性があるかどうかを予測することです。

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を実行するには、次のものが必要です。

* AdventureWorksDW サンプル データが事前に読み込まれた SQL プール。 この SQL プールをプロビジョニングするには、[SQL プールの作成](create-data-warehouse-portal.md)に関するページを参照し、サンプル データを読み込むことを選択します。 データ ウェアハウスは既にあるが、サンプル データがない場合は、[サンプル データを手動で読み込む](./load-data-from-azure-blob-storage-using-copy.md)ことができます。
* Azure Machine Learning ワークスペース。 [このチュートリアル](../../machine-learning/how-to-manage-workspace.md)に従って、新しいものを作成します。

## <a name="get-the-data"></a>データを取得する

使用しているデータは、AdventureWorksDW の dbo.vTargetMail ビューにあります。 このチュートリアルでデータストアを使用するには、Azure Synapse で現在データセットをサポートしていないため、データをまず Azure Data Lake Storage アカウントにエクスポートします。 Azure Data Factory を使用することで、[コピー アクティビティ](../../data-factory/copy-activity-overview.md)を使用して、データ ウェアハウスから Azure Data Lake Storage にデータをエクスポートできます。 インポートには、次のクエリを使用します。

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Azure Data Lake Storage でデータが使用できるようになったら、Azure Machine Learning のデータストアを使用して、[Azure Storage サービスに接続](../../machine-learning/how-to-access-data.md)します。 次の手順に従って、データストアと対応するデータセットを作成します。

1. Azure portal から、または [Azure Machine Learning スタジオ](https://ml.azure.com/)にサインインして、Azure Machine learning スタジオを起動します。

1. **管理** セクションの左ウィンドウで **データストア** をクリックし、**New Datastore\(新しいデータストア\)** をクリックします。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Azure Machine Learning インターフェイスの左ウィンドウのスクリーンショット":::

1. データストアの名前を指定し、種類を 'Azure Blob Storage' として選択し、場所と資格情報を指定します。 **[作成]** をクリックします。

1. 次に、 **[アセット]** セクションの左ウィンドウの **[データセット]** をクリックし ます。 **[From datastore]\(データストアから\)** オプションで、 **[データセットの作成]** を選択します。

1. データセットの名前を指定し、種類を **[表形式]** として選択します。 次に、 **[次へ]** をクリックして先に進みます。

1. **データストアの選択または作成セクション** で、 **[Previously created datastore]\(以前に作成されたデータストア\)** オプションを選択します。 前に作成したデータストアを選択します。 [次へ] をクリックし、パスとファイルの設定を指定します。 ファイルに列ヘッダーが含まれる場合は、必ずそれを指定してください。

1. 最後に、 **[作成]** をクリックしてデータセットを作成します。

## <a name="configure-designer-experiment"></a>デザイナーの実験を構成する

次に、デザイナーの構成について次の手順に従います。

1. **[作成者]** セクションの左ウィンドウで、 **[デザイナー]** タブをクリックします。

1. **[Easy-to-use prebuilt modules]\(使いやすい事前構築済みモジュール\)** を選択して、新しいパイプラインを作成します。

1. 右側の設定ウィンドウで、パイプラインの名前を指定します。

1. また、以前にプロビジョニングされたクラスターの設定ボタンで、実験全体のターゲット コンピューティング クラスターを選択します。 [設定] ウィンドウを閉じます。

## <a name="import-the-data"></a>データをインポートする

1. 検索ボックスの下の左ウィンドウで、 **[データセット]** サブタブを選択します。

1. 先に作成したデータセットをキャンバスにドラッグします。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="キャンバス上のデータセット モジュールのスクリーンショット。":::

## <a name="clean-the-data"></a>データを整理する

データを整理するには、モデルに関係のない列を削除します。 次の手順に従ってください。

1. 左ウィンドウで、 **[モジュール]** サブタブを選択します。

1. **[データ変換] < [操作]** の **[Select Columns in Dataset]\(データセットの列の選択\)** モジュールをキャンバスにドラッグします。 このモジュールを **[データセット]** モジュールに接続します。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="キャンバス上の列選択モジュールのスクリーンショット。" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. モジュールをクリックして、プロパティ ウィンドウを開きます。 [列の編集] をクリックして、削除する列を指定します。

1. 2 つの列が除外されます: CustomerAlternateKey と GeographyKey。 **[保存]**

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="削除される列を示すスクリーンショット。":::

## <a name="build-the-model"></a>モデルを構築する

データは 80-20 で分割されます。80% が機械学習モデルのトレーニングに、20% はモデルのテストに使用されます。 今回の二項分類の問題には "2 クラス" アルゴリズムが使用されています。

1. **[データの分割]** モジュールをキャンバスにドラッグします。

1. プロパティ ウィンドウで、 **[Fraction of rows in the first output dataset]\(最初の出力データセットにおける列の割合\)** に「0.8」と入力します。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="0.8 の分割比を示すスクリーンショット。":::

1. **[2 クラス ブースト デシジョン ツリー]** モジュールをキャンバスにドラッグします。

1. **[モデルのトレーニング]** モジュールをキャンバスにドラッグします。 それを **[2 クラス ブースト デシジョン ツリー]** (ML アルゴリズム) モジュールと **[データの分割]** (アルゴリズムをトレーニングするデータ) モジュールに接続して、入力を指定します。

1. [モデルのトレーニング] の [プロパティ] ウィンドウの **[ラベル列]** オプションで、[列の編集] を選択します。 予測する列として **[BikeBuyer]** 列を選択し、 **[保存]** を選択します。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="選択されたラベル列 BikeBuyer を示すスクリーンショット。":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="2 クラス ブースト デシジョン ツリー モジュールとデータの分割モジュールに接続されているモデルのトレーニング モジュールを示すスクリーンショット。":::

## <a name="score-the-model"></a>モデルにスコアを付ける

ここで、テスト データに対してモデルがどのように実行されるかをテストします。 2 つの異なるアルゴリズムを比較して、どちらの方がパフォーマンスが向上するかを確認します。 次の手順に従ってください。

1. **[Score Model]\(モデルのスコア付け\)** モジュールをキャンバスにドラッグし、 **[モデルのトレーニング]** モジュールと **[Split Data]\(データの分割\)** モジュールに接続します。

1. **[Two-Class Bayes Averaged Perceptron]\(2 クラス ベイズ平均化パーセプトロン\)** を実験キャンバスにドラッグします。 このアルゴリズムのパフォーマンスを 2 クラスのブースト デシジョン ツリーのパフォーマンスと比較します。

1. **[モデルのトレーニング]** モジュールと **[モデルのスコア付け]** モジュールをコピーしてキャンバスに貼り付けます。

1. **[モデルの評価]** モジュールをキャンバスにドラッグし、2 つのアルゴリズムを比較します。

1. **[送信]** をクリックして、パイプラインの実行を設定します。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="キャンバス上の残りのすべてのモジュールのスクリーンショット。" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. 実行が完了したら、 **[モデルの評価]** モジュールを右クリックし、 **[Visualize Evaluation results]\(評価結果の視覚化\)** をクリックします。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="結果のスクリーンショット。":::

指定されているメトリックは、ROC 曲線、精度/再現率図、およびリフト曲線です。 これらのメトリックを見ると、最初のモデルの方が 2 つ目のものよりもパフォーマンスが優れていることがわかります。 最初のモデルの予測を確認するには、[モデルのスコア付け] モジュールを右クリックし、[Visualize Scored dataset]\(スコア付けされたデータセットの視覚化\) をクリックして予測される結果を表示します。

テスト データセットに追加された 2 つの列が表示されます。

* スコア付け確率: 顧客が自転車を購入する可能性
* スコア付けラベル: モデルによって行われた分類 - 自転車を購入する顧客 (1) か、購入しない顧客 (0) このラベル付けの確率のしきい値は 50% に設定されており、調整できます。

[BikeBuyer] 列 (実際) をスコア付けラベル (予測) と比較して、モデルのパフォーマンスがどの程度優れていたか確認します。 次に、このモデルを使用して、新しい顧客の予測を行うことができます。 [このモデルを Web サービスとして発行する](../../machine-learning/tutorial-designer-automobile-price-deploy.md)か、または Azure Synapse に結果を書き戻すことができます。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning の詳細については、[Azure での機械学習の概要](../../machine-learning/overview-what-is-azure-ml.md)に関するページを参照してください。

データ ウェアハウスの組み込みのスコアリングについて、[こちら](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)を参照してください。