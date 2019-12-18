---
title: Azure Machine Learning を使用したデータの分析
description: Azure Machine Learning を使用し、Azure SQL Data Warehouse で保存されたデータに基づいて予測機械学習モデルを構築します。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f91960eaac92047e76275e63b1feaf471de3bac3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692790"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Azure Machine Learning を使用したデータの分析
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

このチュートリアルでは、Azure Machine Learning を使用し、Azure SQL Data Warehouse で保存されたデータに基づいて予測機械学習モデルを構築します。 具体的には、顧客が自転車を購入する可能性があるかどうかを予測することで、Adventure Works (自転車店) のターゲット マーケティング キャンペーンを作成します。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>前提条件
このチュートリアルを進めるには、次が必要です。

* AdventureWorksDW サンプル データが事前に読み込まれた SQL Data Warehouse。 これをプロビジョニングするには、 [SQL Data Warehouse の作成][Create a SQL Data Warehouse] に関するページを参照し、サンプル データの読み込みを選択してください。 データ ウェアハウスは既にあってもサンプル データがない場合は、 [サンプル データを手動で読み込む][load sample data manually]ことができます。

## <a name="1-get-the-data"></a>1.データを取得する
このデータは、AdventureWorksDW データベースの dbo.vTargetMail ビューにあります。 このデータを読み取るには、次の手順を実行します。

1. [Azure Machine Learning Studio][Azure Machine Learning studio] にサインインし、[実験] をクリックします。
2. 画面の左下にある **[+ 新規]** をクリックし、 **[Blank Experiment]\(空白の実験\)** を選択します。
3. 実験の名前を入力します: Targeted Marketing。
4. **[Data Input and output]\(データの入力と出力\)** の **[データのインポート]** モジュールを、モジュール ウィンドウからキャンバスにドラッグします。
5. [プロパティ] ウィンドウで、SQL Data Warehouse データベースの詳細を指定します。
6. 目的のデータを読み取るためのデータベース **クエリ** を指定します。

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

実験キャンバスの下にある **[実行]** をクリックして、実験を実行します。
![実験を実行する][1]

実験の実行が正常に終了したら、[リーダー] モジュールの下部にある出力ポートをクリックし、 **[視覚化]** を選択して、インポートしたデータを表示します。
![インポート済みデータを表示する][3]

## <a name="2-clean-the-data"></a>2.データを整理する
データを整理するには、モデルとの関連性が低い列をいくつか削除します。 これを行うには、次の手順を実行します。

1. **[データ変換] < [操作]** の **[Select Columns in Dataset]\(データセットの列の選択\)** モジュールをキャンバスにドラッグします。 このモジュールを **[データのインポート]** モジュールに接続します。
2. [プロパティ] ウィンドウの **[列セレクターの起動]** をクリックし、削除する列を指定します。
   ![[プロジェクト列]][4]
3. 2 つの列が除外されます: CustomerAlternateKey と GeographyKey。
   ![不要な列を削除する][5]

## <a name="3-build-the-model"></a>3.モデルを構築する
データが 80 対 20 に分割されます。80% が機械学習モデルのトレーニングに、20% はモデルのテストに使用されます。 今回の二項分類の問題には "2 クラス" アルゴリズムを使用します。

1. **[分割]** モジュールをキャンバスにドラッグします。
2. プロパティ ウィンドウで、[Fraction of rows in the first output dataset]\(最初の出力データセットにおける列の割合\) に「0.8」と入力します。
   ![データをトレーニング セットとテスト セットに分割する][6]
3. **[2 クラス ブースト デシジョン ツリー]** モジュールをキャンバスにドラッグします。
4. **[モデルのトレーニング]** モジュールをキャンバスにドラッグし、そのモジュールを **[Two-Class Boosted Decision Tree]\(2 クラス ブースト デシジョン ツリー\)** モジュール (ML アルゴリズム) と **[分割]** (アルゴリズムをトレーニングするデータ) モジュールに接続して、入力を指定します。 
     ![[モデルのトレーニング] モジュールを接続する][7]
5. 次に、[プロパティ] ウィンドウで **[Launch column selector (列セレクターの起動)]** をクリックします。 予測する列として **[BikeBuyer]** 列を選択します。
   ![予測する列を選択する][8]

## <a name="4-score-the-model"></a>4.モデルにスコアを付ける
ここでは、テスト データに対するモデルのパフォーマンスをテストします。 選択したアルゴリズムを別のアルゴリズムと比較し、どちらのパフォーマンスが優れているかを評価します。

1. **[Score Model]\(モデルのスコア付け\)** モジュールをキャンバスにドラッグし、 **[モデルのトレーニング]** モジュールと **[Split Data]\(データの分割\)** モジュールに接続します。
   ![モデルにスコアを付ける][9]
2. **[2 クラスのベイズ ポイント マシン]** を実験キャンバスにドラッグします。 このアルゴリズムのパフォーマンスを 2 クラスのブースト デシジョン ツリーのパフォーマンスと比較します。
3. [モデルのトレーニング] モジュールと [モデルのスコア付け] モジュールをコピーしてキャンバスに貼り付けます。
4. **[モデルの評価]** モジュールをキャンバスにドラッグし、2 つのアルゴリズムを比較します。
5. **[実行]** します。
   ![実験を実行する][10]
6. [Evaluate Model (モデルの評価)] モジュールの下部にある出力ポートをクリックし、[Visualize (視覚化)] をクリックします。
   ![評価結果を視覚化する][11]

指定されているメトリックは、ROC 曲線、精度/再現率図、およびリフト曲線です。 これらのメトリックを見ると、最初に実行されたモデルの方が 2 つ目のモデルよりもパフォーマンスが優れていることがわかります。 1 つ目のモデルが予測した内容を確認するには、[Score Model (モデルのスコア付け)] の出力ポートをクリックし、[Visualize (視覚化)] をクリックします。
![スコア結果を視覚化する][12]

テスト データセットに追加された 2 つの列が表示されます。

* スコア付け確率: 顧客が自転車を購入する可能性
* スコア付けラベル: モデルによって行われた分類 - 自転車を購入する顧客 (1) か、購入しない顧客 (0) このラベル付けの確率のしきい値は 50% に設定されており、調整できます。

[BikeBuyer] 列 (実際) をスコア付けラベル (予測) と比較すると、モデルのパフォーマンスがどの程度優れていたかを評価できます。 次のステップとして、このモデルを使用して新規顧客の予測を行い、Web サービスとしてこのモデルを発行したり、SQL Data Warehouse に結果を書き戻したりできます。

## <a name="next-steps"></a>次の手順
予測機械学習モデルの構築の詳細については、 [Azure での機械学習の概要][Introduction to Machine Learning on Azure]に関するページを参照してください。

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2-visualize-new.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
