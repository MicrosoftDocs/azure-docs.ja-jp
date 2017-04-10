---
title: "Azure Machine Learning を使用したデータの分析 | Microsoft Docs"
description: "Azure Machine Learning を使用し、Azure SQL Data Warehouse で保存されたデータに基づいて予測機械学習モデルを構築します。"
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 95635460-150f-4a50-be9c-5ddc5797f8a9
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 03/02/2017
ms.author: kevin
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: a5befbf1dc1862b3b0803eb4940341d246ec036c
ms.lasthandoff: 03/14/2017


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

* AdventureWorksDW サンプル データが事前に読み込まれた SQL Data Warehouse。 これをプロビジョニングするには、[SQL Data Warehouse の作成][Create a SQL Data Warehouse]に関するページを参照し、サンプル データを読み込んでください。 データ ウェアハウスは既にあるもののサンプル データがない場合は、[サンプル データを手動で読み込む][load sample data manually]ことができます。

## <a name="1-get-the-data"></a>1.データを取得する
このデータは、AdventureWorksDW データベースの dbo.vTargetMail ビューにあります。 このデータを読み取るには、次の手順を実行します。

1. [Azure Machine Learning Studio][Azure Machine Learning studio] にサインインし、実験をクリックします。
2. **[+新規]** をクリックし、**[Blank Experiment (空の実験)]** を選択します。
3. 実験の名前として「対象を絞ったマーケティング」と入力します。
4. [モジュール] ウィンドウから **[リーダー]** モジュールをキャンバスにドラッグします。
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

1. **[プロジェクト列]** モジュールをキャンバスにドラッグします。
2. [プロパティ] ウィンドウの **[列セレクターの起動]** をクリックし、削除する列を指定します。
   ![[プロジェクト列]][4]
3. CustomerAlternateKey と GeographyKey の&2; つの列を除外します。
   ![不要な列を削除する][5]

## <a name="3-build-the-model"></a>3.モデルを構築する
データを 80 対 20 に分割し、80% を機械学習モデルのトレーニングに、20% をモデルのテストに使用します。 今回の二項分類の問題には "2 クラス" アルゴリズムを使用します。

1. **[分割]** モジュールをキャンバスにドラッグします。
2. [プロパティ] ウィンドウの [Fraction of rows in the first output dataset (最初の出力データセットにおける列の割合)] に「0.8」と入力します。
   ![データをトレーニング セットとテスト セットに分割する][6]
3. **[2 クラス ブースト デシジョン ツリー]** モジュールをキャンバスにドラッグします。
4. **[モデルのトレーニング]** モジュールをキャンバスにドラッグし、入力内容を指定します。 次に、[プロパティ] ウィンドウで **[Launch column selector (列セレクターの起動)]** をクリックします。
   * 1 つ目の入力: ML アルゴリズム。
   * 2 つ目の入力: アルゴリズムをトレーニングするためのデータ。
     ![[モデルのトレーニング] モジュールを接続する][7]
5. 予測する列として **[BikeBuyer]** 列を選択します。
   ![予測する列を選択する][8]

## <a name="4-score-the-model"></a>4.モデルにスコアを付ける
ここでは、テスト データに対するモデルのパフォーマンスをテストします。 選択したアルゴリズムを別のアルゴリズムと比較し、どちらのパフォーマンスが優れているかを評価します。

1. **[Score Model (モデルのスコア付け)]** モジュールをキャンバスにドラッグします。
    1 つ目の入力: トレーニング済みのモデル、2 つ目の入力: テスト データ。![[Score Model (モデルのスコア付け)]][9]
2. **[2 クラスのベイズ ポイント マシン]** を実験キャンバスにドラッグします。 このアルゴリズムのパフォーマンスを&2; クラスのブースト デシジョン ツリーのパフォーマンスと比較します。
3. [モデルのトレーニング] モジュールと [モデルのスコア付け] モジュールをコピーしてキャンバスに貼り付けます。
4. **[モデルの評価]** モジュールをキャンバスにドラッグし、2 つのアルゴリズムを比較します。
5. **[実行]** します。
   ![実験を実行する][10]
6. [Evaluate Model (モデルの評価)] モジュールの下部にある出力ポートをクリックし、[Visualize (視覚化)] をクリックします。
   ![評価結果を視覚化する][11]

指定されているメトリックは、ROC 曲線、精度/再現率図、およびリフト曲線です。 これらのメトリックを見ると、最初に実行されたモデルの方が&2; つ目のモデルよりもパフォーマンスが優れていることがわかります。 1 つ目のモデルが予測した内容を確認するには、[Score Model (モデルのスコア付け)] の出力ポートをクリックし、[Visualize (視覚化)] をクリックします。
![スコア結果を視覚化する][12]

テスト データセットに追加された&2; つの列が表示されます。

* スコア付け確率: 顧客が自転車を購入する可能性
* スコア付けラベル: モデルによって行われた分類 - 自転車を購入する顧客 (1) か、購入しない顧客 (0) このラベル付けの確率のしきい値は 50% に設定されており、調整できます。

[BikeBuyer] 列 (実際) をスコア付けラベル (予測) と比較すると、モデルのパフォーマンスがどの程度優れていたかを評価できます。 次のステップとして、このモデルを使用して新規顧客の予測を行い、Web サービスとしてこのモデルを発行したり、SQL Data Warehouse に結果を書き戻したりできます。

## <a name="next-steps"></a>次のステップ
予測機械学習モデルの構築の詳細については、[Azure での機械学習の概要][Introduction to Machine Learning on Azure]に関するページを参照してください。

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

