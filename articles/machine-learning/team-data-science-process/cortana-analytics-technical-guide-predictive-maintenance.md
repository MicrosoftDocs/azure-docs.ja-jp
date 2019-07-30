---
title: 航空宇宙業界の予測メンテナンス ガイド - Team Data Science Process
description: 航空宇宙、公益事業、および輸送業界における予測メンテナンスのための Microsoft Cortana Intelligence によるソリューション テンプレートに関する技術ガイドです。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 37acd88dbfe8ed1790f1bfa68d5e9762a861ddc7
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626401"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace"></a>航空宇宙の業務における予測メンテナンスのための Cortana Intelligence Solution Template に関する技術ガイド

> [!Important]
> これは非推奨の記事です。 航空宇宙業界の予測メンテナンスに関する議論には今でも意義がありますが、最新情報については、[ビジネス ユーザー向けのソリューションの概要](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace)に関するページをご覧ください。


ソリューション テンプレートは、Cortana Intelligence Suite に基づいて、エンド ツー エンドのデモを構築するプロセスを促進するために設計されています。 デプロイされたテンプレートは、必要な Cortana Intelligence コンポーネントと共にサブスクリプションをプロビジョニングし、それらの関係を構築します。 さらに、データ ジェネレーター アプリケーションからのサンプル データでデータ パイプラインのシード処理を実行します。データ ジェネレーターは、ソリューション テンプレートのデプロイ後にローカル コンピューターにダウンロードしてインストールします。 ジェネレーターからのデータがデータ パイプラインに入力され、Machine Learning 予測の生成が開始されます。生成された Machine Learning 予測は Power BI ダッシュボードで視覚化されます。

デプロイ プロセスでは、ソリューション資格情報を設定するためのいくつかの手順を進みます。 デプロイ時に指定したソリューション名、ユーザー名、パスワードなどの資格情報を記録しておくようにしてください。 


この記事の目標を以下に示します。
- サブスクリプション内にプロビジョニングされるリファレンス アーキテクチャとコンポーネントについて説明します。
- サンプル データを独自のデータに置き換える方法を示します。 
- ソリューション テンプレートを変更する方法を示します。  

> [!TIP]
> [この記事の PDF バージョン](https://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf)をダウンロードして印刷できます。
> 
> 

## <a name="overview"></a>概要
![予測的なメンテナンス アーキテクチャ](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

ソリューションをデプロイすると、Cortana Analytics Suite 内のさまざまな Azure サービスがアクティブ化されます (イベント ハブ、Stream Analytics、HDInsight、データ ファクトリ、Machine Learning など)。 上のアーキテクチャ図は、航空宇宙ソリューション テンプレートの予測メンテナンスの構築方法を示しています。 これらのサービスは Azure Portal で調査できます。その場合、ソリューションのデプロイで作成されたソリューション テンプレート図でサービスをクリックします (HDInsight は例外となります。このサービスは、関連するパイプライン アクティビティを実行し、後で削除するときにオンデマンドでプロビジョニングされます)。
[図のフルサイズ バージョン](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png)をダウンロードします。

以下のセクションでは、ソリューションの構成要素について説明します。

## <a name="data-source-and-ingestion"></a>データ ソースと取り込み
### <a name="synthetic-data-source"></a>合成データソース
このテンプレートでは、デプロイの成功後にローカルにダウンロードして、実行するデスクトップ アプリケーションから、使用するデータ ソースが生成されます。

このアプリケーションをダウンロードしてインストールする手順を確認するには、ソリューション テンプレート図の最初のノード (予測メンテナンス データ ジェネレーター) を選択します。 手順はプロパティ バーに表示されます。 このアプリケーションは、[Azure Event Hub](#azure-event-hub) サービスに、ソリューション フローの残りで使用されるデータ ポイント、またはイベントを提供します。 このデータ ソースは、[Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) (ターボファン エンジンの劣化シミュレーション データ セット) を使用して、[NASA データ リポジトリ](https://c3.nasa.gov/dashlink/resources/139/)で公開されているデータから得たものです。

イベント生成アプリケーションは、コンピューターで実行中の場合にのみ、Azure Event Hub にデータを入力します。  

### <a name="azure-event-hub"></a>Azure Event Hub  
[Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) サービスは、合成データ ソースによって提供される入力の受け取り側です。

## <a name="data-preparation-and-analysis"></a>データの準備と分析  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) を使用して、[Azure イベント ハブ](#azure-event-hub) サービスから取得した入力ストリームをほぼリアルタイムで分析できます。 次に結果を [Power BI](https://powerbi.microsoft.com) ダッシュボード上に公開し、後で [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) サービスによって処理するために、すべての未加工の受信イベントを [Azure Storage](https://azure.microsoft.com/services/storage/) サービスにアーカイブします。

### <a name="hdinsight-custom-aggregation"></a>HDInsight カスタム集計
HDInsight を使用して、(Azure Data Factory によって調整される) [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトを実行し、Azure Stream Analytics サービスによってアーカイブされた未加工のイベントの集計を行います。

### <a name="azure-machine-learning"></a>Azure Machine Learning
(Azure Data Factory によって調整される) [Azure Machine Learning サービス](https://azure.microsoft.com/services/machine-learning/)で受け取った入力を使用して、特定の航空機エンジンの残存耐用年数 (RUL) を予測します。 

## <a name="data-publishing"></a>データの公開
### <a name="azure-sql-database"></a>Azure SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) を使用して、Azure Machine Learning サービスが受け取った予測を格納します。予測は次に [Power BI](https://powerbi.microsoft.com) ダッシュボードで使用されます。

## <a name="data-consumption"></a>データの使用
### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) を使用して、[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) によって提供される集計とアラートのほか、[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) を使用して生成され、[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) に保存された RUL 予測が記載されたダッシュボードを表示します。

## <a name="how-to-bring-in-your-own-data"></a>独自のデータを取り込む方法
このセクションでは、Azure に独自のデータを取り込む方法と、このアーキテクチャに取り込むデータに応じて変更が必要になる領域について説明します。

データセットが、このソリューション テンプレートで使用されている [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) (ターボファン エンジンの劣化シミュレーション データ セット) によって使用されているデータセットに一致している可能性はほとんどありません。 自分のデータと要件を理解することは、独自のデータで動作するように、このテンプレートを変更する方法においてきわめて重要です。 

以降のセクションでは、新しいデータセットを導入したときに変更が必要となるテンプレートの部分について説明します。

### <a name="azure-event-hub"></a>Azure Event Hub
Azure Event Hub は汎用的で、データを CSV または JSON 形式でハブに投稿できます。 Azure Event Hub では特別な処理は行われませんが、それに入力されるデータを理解することが重要です。

このドキュメントでは、データを取り込む方法について説明しませんが、Event Hub API を使用して、イベントやデータを Azure Event Hub に簡単に送信できます。

### <a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Azure Stream Analytics サービスを使用して、データ ストリームから読み取り、任意の数のソースにデータを出力することによって、ほぼリアルタイムで分析を行います。

航空宇宙ソリューション テンプレートの予測メンテナンスの場合、Azure Stream Analytics クエリは、それぞれ Azure Event Hub サービスからのイベントを使用し、4 つの異なる場所に出力する 4 つのサブ クエリから構成されます。 これらの出力は、3 つの Power BI データセットと 1 つの Azure Storage の場所から構成されます。

Azure Stream Analytics クエリは次によって見つけることができます。

* Azure Portal に接続する
* ソリューションがデプロイされたときに生成された Stream Analytics ジョブ ![Stream Analytics アイコン](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) ("*例*": 予測メンテナンス ソリューションの **maintenancesa02asapbi** および **maintenancesa02asablob**) を見つける
* 次を選択する
  
  * 入力クエリを表示する場合は ***[入力]***
  * クエリ自体を表示する場合は ***[クエリ]***
  * 別の出力を表示する場合は ***[出力]***

Azure Stream Analytics クエリの構築については、MSDN の [Stream Analytics クエリ リファレンス](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) を参照してください。

このソリューションでは、クエリは、このソリューション テンプレートの一部として提供されている Power BI ダッシュボードへの受信データ ストリームに関するほぼリアルタイムの分析情報を含む、3 つのデータセットを出力します。 受信データ形式に関する暗黙的知識があるため、これらのクエリはデータ形式に基づいて変更する必要があります。

2 番目の Stream Analytics ジョブ **maintenancesa02asablob** のクエリは、すべての [Event Hub](https://azure.microsoft.com/services/event-hubs/) イベントを [Azure Storage](https://azure.microsoft.com/services/storage/) に出力するだけです。完全なイベント情報が記憶域にストリームされるため、データ形式に関係なく変更を必要としません。

### <a name="azure-data-factory"></a>Azure Data Factory
[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) サービスは、データの移動や処理を調整します。 航空宇宙ソリューション テンプレートの予測メンテナンスでは、さまざまなテクノロジを使用してデータを移動し、処理する 3 つの[パイプライン](../../data-factory/concepts-pipelines-activities.md)からデータ ファクトリが構成されます。  ソリューションのデプロイによって作成されたソリューション テンプレート図の下部にあるデータ ファクトリ ノードを開くことで、データ ファクトリにアクセスします。 データセットの下のエラーは、データ ジェネレーターが起動する前にデータ ファクトリがデプロイされていることが原因です。 これらのエラーは無視でき、データ ファクトリの機能が妨げられることはありません

![データ ファクトリ データセット エラー](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

このセクションでは、[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) に含まれる必要な[パイプラインとアクティビティ](../../data-factory/concepts-pipelines-activities.md)について説明します。 ソリューションの図のビューを以下に示します。

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

このファクトリの 2 つのパイプラインには、データのパーティション分割と集計に使用される [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトが含まれます。 このスクリプトは、セットアップ時に作成される [Azure ストレージ](https://azure.microsoft.com/services/storage/) アカウントに置かれます。 その場所は maintenancesascript\\\\script\\\\hive\\\\ (または https://[Your solution name].blob.core.windows.net/maintenancesascript) になります。

[Azure Stream Analytics](#azure-stream-analytics-1) クエリと同様に、[Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトは、受信データ形式に関して暗黙的に認識するため、データ形式に基づいて変更する必要があります。

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
この[パイプライン](../../data-factory/concepts-pipelines-activities.md)には、[HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) アクティビティが含まれます。このアクティビティは、[Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトを実行する [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) を使用して、[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) ジョブの実行時に [Azure Storage](https://azure.microsoft.com/services/storage/) に入れられるデータをパーティション分割します。

このパーティション分割タスク用の [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトは ***AggregateFlightInfo.hql*** です

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
この[パイプライン](../../data-factory/concepts-pipelines-activities.md)には複数のアクティビティが含まれ、それらの最終結果は、このソリューション テンプレートに関連付けられている [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) の実験からのスコア付けされた予測です。

含まれるアクティビティは次のとおりです。

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) アクティビティ: [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトを実行する [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) を使用して、[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) の実験に必要な集計と特徴エンジニアリングを実行します。
  このパーティション分割タスク用の [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトは ***PrepareMLInput.hql*** です。
* [コピー](https://msdn.microsoft.com/library/azure/dn835035.aspx) アクティビティ: [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) アクティビティからの結果を、[AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) アクティビティによってアクセスされる 1 つの [Azure Storage](https://azure.microsoft.com/services/storage/) BLOB に移動します。
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) アクティビティ: [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) の実験を呼び出します。この実験の結果は、1 つの [Azure Storage](https://azure.microsoft.com/services/storage/) BLOB に配置されます。

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
この[パイプライン](../../data-factory/concepts-pipelines-activities.md)には[コピー](https://msdn.microsoft.com/library/azure/dn835035.aspx) アクティビティが含まれます。このコピー アクティビティは、[Azure Machine Learning](#azure-machine-learning) の実験の結果を、***MLScoringPipeline*** から、ソリューション テンプレート インストールの一部としてプロビジョニングされた [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) に移動します。

### <a name="azure-machine-learning"></a>Azure Machine Learning
このソリューション テンプレートで使用されている [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) の実験は、航空機のエンジンの残存耐用年数 (RUL) を提供します。 実験は使用されるデータセットに固有であり、取り込まれるデータに固有の変更や置換が必要になります。

Azure Machine Learning の実験の作成方法については、「[Predictive Maintenance: Step 1 of 3, data preparation and feature engineering (予測メンテナンス: ステップ 1/3、データの準備と特徴エンジニアリング)](https://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)」を参照してください。

## <a name="monitor-progress"></a>進行状況の監視
データ ジェネレーターを起動すると、データを取り込むパイプラインのデハイドレートが開始され、データ ファクトリによって発行されたコマンドに従ってソリューションのさまざまなコンポーネントがアクションを開始します。 パイプラインを監視する方法は 2 つあります。

1. いずれかの Stream Analytics ジョブが、未加工の受信データを Blob Storage に書き込みます。 ソリューションが正常にデプロイされた画面からソリューションの Blob Storage コンポーネントをクリックし、右側のパネルの [開く] をクリックすると、[Azure Portal](https://portal.azure.com/) に移動します。 管理ポータルで、BLOB をクリックします。 次のパネルに、コンテナーの一覧が表示されます。 **maintenancesadata** をクリックします。 次のパネルに **rawdata** フォルダーが表示されます。 rawdata フォルダーの中に、hour=17、hour=18 などの名前の付いたフォルダーが表示されます。 これらのフォルダーの存在は、生データがコンピューター上に生成され、BLOB ストレージに格納されたことを示しています。 これらのフォルダーの中に、有限サイズ (MB 単位) の csv ファイルがあります。
2. パイプラインの最後の手順は、データ (Machine Learning からの予測など) を SQL Database に書き込むことです。 データが SQL Database に表示されるまで、最大 3 時間の待機が必要な場合があります。 SQL Database で使用できるデータの量を監視する 1 つの方法は、[Azure Portal](https://portal.azure.com/) を使用することです。 左側のパネルで、SQL DATABASES ![SQL アイコン](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png)」を見つけてそれをクリックします。 次に、データベース **pmaintenancedb** を見つけて、それをクリックします。 次のページの下部にある [管理] をクリックします。
   
    ![管理アイコン](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png)
   
    ここで、[新しいクエリ] をクリックし、行数をクエリできます (例: select count(*) from PMResult)。 データベースが大きくなれば、テーブル内の行数も増加します。

## <a name="power-bi-dashboard"></a>Power BI ダッシュボード

Power BI ダッシュボードを設定して、Azure Stream Analytics データ (ホット パス)や、Azure Machine Learning からのバッチ予測結果 (コールド パス) を視覚化します。

### <a name="set-up-the-cold-path-dashboard"></a>コールド パス ダッシュボードの設定
コールド パス データ パイプラインでの目標は、フライト (サイクル) の終了時に、各航空機エンジンの予測 RUL (残存耐用年数) を取得することです。 過去 3 時間以内にフライトを終了した航空機のエンジンを予測するために、予測結果は 3 時間ごとに更新されます。

Power BI は、そのデータ ソースとして、予測結果が格納されている Azure SQL データベースに接続します。 注:ソリューションをデプロイすると、3 時間以内に予測がデータベース内に現れます。
ジェネレーターのダウンロードに付属する pbix ファイルには、Power BI ダッシュボードをすぐに作成できるようにシード データが含まれています。 2) この手順の前提条件は、無料のソフトウェア [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)をダウンロードしてインストールしていることです。

次の手順は、視覚化するデータ (予測結果など) を含むソリューションのデプロイ時にスピンアップされた SQL Database に、pbix ファイルを接続する方法を示しています。

1. データベースの資格情報を取得します。
   
   次の手順に進む前に、 **データベース サーバー名、データベース名、ユーザー名、およびパスワード** が必要です。 以下の手順で、それらを見つける方法を説明します。
   
   * ソリューション テンプレート図の **[Azure SQL Database]** が緑色に変わったら、それをクリックしてから **[開く]** をクリックします。
   * 新しいブラウザー タブ/ウィンドウが表示され、Azure ポータル ページが表示されます。 左側のパネルの **[リソース グループ]** をクリックします。
   * ソリューションのデプロイに使用しているサブスクリプションを選択し、 **[YourSolutionName\_ResourceGroup]** を選択します。
   * 新しいポップアップ パネルで、![SQL アイコン](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) アイコンをクリックして、データベースにアクセスします。 データベース名はこのアイコンの横にあり (例: **'pmaintenancedb'** )、**データベース サーバー名**は、サーバー名のプロパティの下に **YourSoｌutionName.database.windows.net** のように表示されます。
   * データベースの**ユーザー名**と**パスワード**は、ソリューションのデプロイ時に記録しておいたユーザー名とパスワードと同じです。
2. Power BI Desktop でコールド パス レポート ファイルのデータ ソースを更新します。
   
   * ジェネレーター ファイルをダウンロードして解凍したフォルダー内の **PowerBI\\PredictiveMaintenanceAerospace.pbix** ファイルをダブルクリックします。 ファイルを開くときに警告メッセージが表示された場合、それらを無視します。 ファイルの先頭の **[クエリを編集]** をクリックします。
     
     ![クエリの編集](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * **RemainingUsefulLife** と **PMResult** という 2 つのテーブルが表示されます。 最初のテーブルを選択し、右側の **[クエリの設定]** パネルの **[適用したステップ]** の **[ソース]** の横にある ![クエリの設定アイコン](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) をクリックします。 表示される警告メッセージは無視します。
   * ポップアップ ウィンドウの **[サーバー]** と **[データベース]** を独自のサーバーとデータベースの名前に置き換えて、 **[OK]** をクリックします。 サーバー名については、ポート 1433 (**YourSolutionName.database.windows.net 1433**) を指定していることを確認してください。 [データベース] フィールドは **pmaintenancedb**のままにします。 画面に表示される警告メッセージは無視します。
   * 次のポップアップ ウィンドウで、左側のウィンドウに 2 つのオプション ( **[Windows]** と **[データベース]** ) が表示されます。 **[データベース]** をクリックし、 **[ユーザー名]** と **[パスワード]** (これは、初めてソリューションをデプロイし、Azure SQL データベースを作成したときに入力したユーザー名とパスワードです) を入力します。 ***[これらの設定の適用対象レベルの選択]*** で、データベース レベル オプションをオンにします。 次に **[接続]** をクリックします。
   * 2 番目のテーブル **[PMResult]** をクリックし、右側の **[クエリの設定]** パネルの **[適用したステップ]** の **[ソース]** の横にある ![ナビゲーション アイコン](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) をクリックし、上記の手順と同様に、サーバーとデータベース名を更新して、[OK] をクリックします。
   * 前のページに戻ったら、ウィンドウを閉じます。 メッセージが表示されるので、 **[適用]** をクリックします。 最後に、 **[保存]** ボタンをクリックして、変更を保存します。 これで、Power BI ファイルは、サーバーへの接続を確立しました。 視覚エフェクトが空の場合、凡例の右上隅にある消しゴム アイコンをクリックして、視覚エフェクトの選択をクリアし、すべてのデータを表示します。 更新ボタンを使用して、視覚エフェクトに新しいデータを反映させます。 最初、視覚エフェクトにはシード データのみ表示されます。データ ファクトリは 3 時間ごとに更新されるようにスケジュールされています。 3 時間後、データを更新すると、視覚エフェクトに反映された新しい予測が表示されます。
3. (省略可能) コールド パス ダッシュボードを [Power BI オンライン](https://www.powerbi.com/)に公開します。 この手順では、Power BI アカウント (または Office 365 アカウント) が必要であることに注意してください。
   
   * **[公開]** をクリックします。 数秒後、緑色のチェック マークの付いた 「Power BI への公開が成功しました」と表示するウィンドウが表示されます。 "Power BI で PredictiveMaintenanceAerospace.pbix を開く" の下のリンクをクリックします。 詳細な手順については、「 [Power BI Desktop からの発行](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)」を参照してください。
   * 新しいダッシュボードを作成するには、左側のウィンドウで **[ダッシュボード]** セクションの横の **[+]** 記号をクリックします。 この新しいダッシュボードの名前として、「Predictive Maintenance Demo」と入力します。
   * レポートが開いたら、![ピン留めアイコン](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) をクリックして、すべての視覚エフェクトをダッシュボードにピン留めします。 詳細な手順については、「 [レポートから Power BI ダッシュボードにタイルをピン留め](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report)」を参照してください。
     ダッシュボード ページに移動し、視覚エフェクトのサイズと場所を調整し、タイルを編集します。 タイルを編集する方法の詳細については、「 [タイルの編集 -- サイズ変更、移動、名前の変更、ピン留め、削除、ハイパーリンクの追加](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename)」を参照してください。 いくつかのコールド パス視覚エフェクトがピン留めされたサンプル ダッシュボードを次に示します。  データ ジェネレーターの実行時間に応じて、視覚エフェクトの番号が異なる場合があります。
     <br/>
     ![最終的なビュー](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * データの更新をスケジュールするには、 **[PredictiveMaintenanceAerospace]** データセットの上にマウス ポインターを移動し、![省略記号アイコン](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png)をクリックして、 **[更新のスケジュール設定]** を選択します。
     <br/>
     **注:** 警告メッセージが表示された場合は、 **[資格情報の編集]** をクリックし、データベース資格情報が、手順 1. で説明したものと同じかどうかを確認します。
     <br/>
     ![更新のスケジュール設定](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * **[更新のスケジュール設定]** セクションを展開します。 [データを最新の状態に保つ] を有効にします。
     <br/>
   * 必要に応じて更新をスケジュールします。 詳細については、「 [Power BI でのデータの更新](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi)」を参照してください。

### <a name="setup-hot-path-dashboard"></a>ホット パス ダッシュボードの設定
次の手順では、ソリューションのデプロイ時に生成された Stream Analytics ジョブからのデータの出力を視覚化する方法について説明します。 次の手順を実行するには、 [Power BI オンライン](https://www.powerbi.com/) アカウントが必要です。 アカウントがない場合は、 [作成](https://powerbi.microsoft.com/pricing)できます。

1. Azure Stream Analytics (ASA) に Power BI 出力を追加します。
   
   * Azure Stream Analytics ジョブの出力を Power BI ダッシュボードとして設定するには、[Azure Stream Analytics と Power BI のストリーミング データをリアルタイムで視覚化する分析ダッシュボード](../../stream-analytics/stream-analytics-power-bi-dashboard.md)に関する記事の手順に従う必要があります。
   * ASA クエリには、**aircraftmonitor**、**aircraftalert**、**flightsbyhour** の 3 つの出力があります。 [クエリ] タブをクリックすると、クエリを表示できます。これらの各テーブルに合わせ、ASA に出力を追加する必要があります。 最初の出力を追加するとき (**aircraftmonitor**)、**出力の別名**、**データセット名**、**テーブル名**が同じ (**aircraftmonitor**) であることを確認します。 この手順を繰り返し、**aircraftalert** と **flightsbyhour** の出力を追加します。 3 つすべての出力テーブルを追加し、ASA ジョブを開始すると、確認メッセージ ("Stream Analytics ジョブ maintenancesa02asapbi の開始に成功しました") が表示されます。
2. [Power BI オンライン](https://www.powerbi.com)
   
   * [マイ ワークスペース] の左側のパネルにある [データセット] セクションには、***データセット***名 **aircraftmonitor**、**aircraftalert**、および **flightsbyhour** が表示されます。 これは、前の手順で Azure Stream Analytics からプッシュ送信したストリーミング データです。 データセット **flightsbyhour** は、その背後にある SQL クエリの性質により、ほかの 2 つのデータセットと同時に表示されない可能性があります。 ただし、1 時間後には表示されるはずです。
   * ***[処理済み]*** ウィンドウが開き、画面の右側に表示されることを確認します。
3. Power BI にデータが送信されていれば、ストリーミング データの視覚化を開始できます。 いくつかのホット パス視覚エフェクトがピン留めされているサンプル ダッシュボードを次に示します。 適切なデータセットに基づいて他のダッシュボード タイルを作成できます。 データ ジェネレーターの実行時間に応じて、視覚エフェクトの番号が異なる場合があります。

    ![ダッシュボード ビュー](media/cortana-analytics-technical-guide-predictive-maintenance/dashboard-view.png)

1. 上記のタイルを作成する手順を以下に示します。"Fleet View of Sensor 11 vs. Threshold 48.26" タイルを作成します。
   
   * 左側のパネルの [データセット] セクションで、データセット **aircraftmonitor** をクリックします。
   * **[折れ線グラフ]** アイコンをクリックします。
   * **[視覚エフェクト]** ウィンドウの [軸] の下に表示されるように、 **[フィールド]** ウィンドウの **[処理済み]** をクリックします。
   * どちらも [値] の下に表示されるように、[s11] と [s11\_alert] をクリックします。 **[s11]** と **[s11\_alert]** の横にある小さな矢印をクリックして、[合計] を [平均] に変更します。
   * 上部の **[保存]** をクリックし、レポートに "aircraftmonitor" という名前を付けます。 左側の **[ナビゲーター]** ウィンドウの **[レポート]** セクションに、"aircraftmonitor" という名前のレポートが表示されます。
   * 折れ線グラフの右上隅の **[ビジュアルをピン留めする]** アイコンをクリックします。 ダッシュボードを選択する [ダッシュボードにピン留めする] ウィンドウが表示されることがあります。 [Predictive Maintenance Demo] を選択し、[ピン留め] をクリックします。
   * ダッシュボードのこのタイルにマウス ポインターを置き、右上隅の [編集] アイコンをクリックし、そのタイトルを「Fleet View of Sensor 11 vs.Threshold 48.26」に変更し、サブタイトルを「Average across fleet over time」に変更します。

## <a name="delete-your-solution"></a>ソリューションの削除
データ ジェネレーターはコストを上げるので、ソリューションを活発に使用していないときはデータ ジェネレーターを停止してください。 使用していない場合、ソリューションを削除してください。 ソリューションを削除すると、ソリューションのデプロイ時にサブスクリプションにプロビジョニングされたすべてのコンポーネントが削除されます。 ソリューションを削除するには、ソリューション テンプレートの左パネルでソリューション名をクリックし、 **[削除]** をクリックします。

## <a name="cost-estimation-tools"></a>コスト見積もりツール
各自のサブスクリプションで航空宇宙ソリューション テンプレートの予測メンテナンスを実行する場合に関連する合計コストを詳しく知るために役立つ次の 2 つのツールがあります。

* [Microsoft Azure 料金計算ツール (オンライン)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure 料金計算ツール (デスクトップ)](https://www.microsoft.com/download/details.aspx?id=43376)

