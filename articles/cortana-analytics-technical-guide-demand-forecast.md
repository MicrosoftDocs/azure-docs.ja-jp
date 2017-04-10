---
title: "エネルギーの需要予測に関する技術ガイド | Microsoft Docs"
description: "エネルギーの需要予測のための Microsoft Cortana Intelligence を使用したソリューション テンプレートに関する技術ガイドです。"
services: cortana-analytics
documentationcenter: 
author: yijichen
manager: ilanr9
editor: yijichen
ms.assetid: 7f1a866b-79b7-4b97-ae3e-bc6bebe8c756
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: inqiu;yijichen;ilanr9
translationtype: Human Translation
ms.sourcegitcommit: 1fe845d442c7010580d4592f205e92e8ef70e34a
ms.openlocfilehash: c3bbef8fee018dc54e7d3edb86e3f9434999bdae


---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>エネルギーの需要予測のための Cortana Intelligence ソリューション テンプレートに関する技術ガイド
## <a name="overview"></a>**概要**
ソリューション テンプレートは、Cortana Intelligence Suite に基づいて、エンド ツー エンドのデモを構築するプロセスを促進するために設計されています。 デプロイされたテンプレートは、必要な Cortana Intelligence コンポーネントのサブスクリプションをプロビジョニングし、それらの関係を構築します。 さらに、データ シミュレーション アプリケーションから生成されるサンプル データでデータ パイプラインのシード処理を実行します。 表示されたリンクからデータ シミュレーターをダウンロードしてローカル コンピューターにインストールしてください。シミュレーターの使い方については、readme.txt ファイルを参照してください。 シミュレーターから生成されたデータがデータ パイプラインに入力され、機械学習予測の生成が開始されます。生成された機械学習予測は Power BI ダッシュボードで視覚化できます。

ソリューション テンプレートは、 [ここ](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

デプロイメント プロセスでは、ソリューション資格情報を設定するためのいくつかの手順を進みます。 デプロイメント時に指定したソリューション名、ユーザー名、パスワードなどの資格情報を記録しておくようにしてください。

このドキュメントの目的は、このソリューション テンプレートの一部として、サブスクリプションでプロビジョニングされる参照アーキテクチャとさまざまなコンポーネントについて説明することです。 さらに、独自のデータから洞察と予測を表示できるように、サンプル データを実際のデータと置き換える方法についても説明します。 また、独自のデータによってソリューションをカスタマイズする必要がある場合に、変更する必要があると考えられるソリューション テンプレートの部分についても説明します。 このソリューション テンプレートの Power BI ダッシュボードを構築する方法については、最後に説明します。

## <a name="big-picture"></a>**全体像**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>アーキテクチャの説明
ソリューションをデプロイすると、Cortana Analytics Suite 内のさまざまな Azure サービス ("*つまり*"、Event Hubs、Stream Analytics、HDInsight、Data Factory、Machine Learning "*など*") がアクティブ化されます。 上のアーキテクチャ図は、エネルギーの需要予測ソリューション テンプレートの構築方法を大まかに示しています。 これらのサービスを調査するには、ソリューションのデプロイメントによって作成されたソリューション テンプレート図で、それらをクリックしてください。 次のセクションでは、各部分について説明します。

## <a name="data-source-and-ingestion"></a>**データ ソースと取り込み**
### <a name="synthetic-data-source"></a>合成データ ソース
このテンプレートでは、デプロイメントの成功後にローカルにダウンロードして、実行するデスクトップ アプリケーションから、使用するデータ ソースが生成されます。 このアプリケーションをダウンロードしてインストールする手順については、ソリューション テンプレート図のエネルギー予測データ シミュレーターと呼ばれる最初のノードを選択すると、プロパティ バーに表示されます。 このアプリケーションは、 [Azure Event Hub](#azure-event-hub) サービスに、ソリューション フローの残りで使用されるデータ ポイント (イベント) を提供します。

イベント生成アプリケーションは、コンピューターで実行中の場合にのみ、Azure Event Hub にデータを入力します。

### <a name="azure-event-hub"></a>Azure Event Hub
[Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) サービスは、上記の合成データ ソースによって提供される入力の受け取り側です。

## <a name="data-preparation-and-analysis"></a>**データの準備と分析**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) サービスを使用して、[Azure Event Hubs](#azure-event-hub) サービスからの入力ストリームをほぼリアルタイムで分析し、結果を [Power BI](https://powerbi.microsoft.com) ダッシュボードに公開するほか、後で [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) サービスで処理するために、未加工のすべての受信イベントを [Azure Storage](https://azure.microsoft.com/services/storage/) サービスにアーカイブできます。

### <a name="hd-insights-custom-aggregation"></a>HD Insights カスタム集計
Azure HD Insight サービスを使用して、 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプト (Azure Data Factory によって調整される) を実行し、Azure Stream Analytics サービスによってアーカイブされた未加工のイベントの集計を行います。

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) サービスを使用して (Azure Data Factory によって調整される)、受け取った入力から、特定の地域の将来の電力消費を予測します。

## <a name="data-publishing"></a>**データの発行**
### <a name="azure-sql-database-service"></a>Azure SQL Database サービス
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) サービスを使用して、[Power BI](https://powerbi.microsoft.com) ダッシュボードで使用される Azure Machine Learning サービスが受け取った予測を格納します (Azure Data Factory によって管理される)。

## <a name="data-consumption"></a>**データの使用**
### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) サービスを使用して、[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) サービスによって行われた集計に加え、[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) サービスを使用して生成され、[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) に格納された需要予測結果が掲載されたダッシュボードを表示します。 このソリューション テンプレートの Power BI ダッシュボードを構築する方法については、次のセクションを参照してください。

## <a name="how-to-bring-in-your-own-data"></a>**独自のデータを取り込む方法**
このセクションでは、Azure に独自のデータを取り込む方法およびこのアーキテクチャに取り込むデータに応じて変更が必要になりそうな領域について説明します。

取り込むデータセットが、このソリューション テンプレートで使用されているデータセットに一致している可能性はほとんどありません。 自分のデータと要件を理解することは、独自のデータで動作するように、このテンプレートを変更する方法においてきわめて重要です。 Azure Machine Learning サービスを初めて使用する場合は、 [初めての実験を作成する方法](machine-learning/machine-learning-create-experiment.md)の例を使用して、概要を把握することができます。

以降のセクションでは、新しいデータセットを導入したときに変更が必要となるテンプレートのセクションについて説明します。

### <a name="azure-event-hub"></a>Azure Event Hub
[Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) サービスはきわめて汎用的で、データを CSV または JSON 形式でハブに投稿できます。 Azure Event Hubs では特別な処理は行われませんが、重要なのは、そのサービスに入力されるデータを理解することです。

このドキュメントでは、データを取り込む方法について説明しませんが、[Event Hubs API](event-hubs/event-hubs-programming-guide.md) を使用すると、イベントまたはデータを Azure イベント ハブに簡単に送信できます。

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) サービスを使用して、データ ストリームから読み取り、任意の数のソースにデータを出力することによって、ほぼリアルタイムで分析を行います。

エネルギーの需要予測ソリューション テンプレートの場合、Azure Stream Analytics クエリは&2; つのサブクエリで構成されています。サブクエリはそれぞれ、Azure Event Hubs サービスからのイベントを入力として使用し、2 つの異なる場所に出力します。 これらの出力は、1 つの Power BI データセットと&1; つの Azure Storage の場所から構成されます。

[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) クエリは、次のようにして見つけることができます。

* [Azure 管理ポータル](https://manage.windowsazure.com/)
* ソリューションがデプロイされたときに生成された Stream Analytics ジョブ ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png) を検索する。 1 つはデータを Blob Storage にプッシュするジョブ (例: mytest1streaming432822asablob)、もう&1; つはデータを Power BI にプッシュするジョブ (例: mytest1streaming432822asapbi) です。
* 次を選択する

  * 入力クエリを表示する場合は ***[入力]***
  * クエリ自体を表示する場合は ***[クエリ]***
  * 別の出力を表示する場合は ***[出力]***

Azure Stream Analytics クエリの構築については、MSDN の [Stream Analytics クエリ リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx) を参照してください。

このソリューションにおいて、Power BI ダッシュボードへの受信データ ストリームに関するほぼリアルタイムの分析情報と共にデータセットを出力する Azure Stream Analytics ジョブは、このソリューション テンプレートの一部として提供されます。 受信データ形式に関する暗黙的知識があるため、これらのクエリはデータ形式に基づいて、変更が必要になる場合があります。

もう&1; つの Azure Stream Analytics ジョブは、すべての [Event Hubs](https://azure.microsoft.com/services/event-hubs/) イベントを [Azure Storage](https://azure.microsoft.com/services/storage/) に出力します。完全なイベント情報がストレージにストリームされるため、データ形式に関係なく変更を必要としません。

### <a name="azure-data-factory"></a>Azure Data Factory
[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) サービスは、データの移動や処理を調整します。 エネルギーの需要予測ソリューション テンプレートでは、さまざまなテクノロジを使用してデータを移動して処理する&12; 個の[パイプライン](data-factory/data-factory-create-pipelines.md)でデータ ファクトリが構成されます。

  データ ファクトリにアクセスするには、ソリューションのデプロイによって作成されたソリューション テンプレート図の下部にある Data Factory ノードを開きます。 これにより、Azure 管理ポータルのデータ ファクトリに移動します。 データセットの下にエラーが表示された場合、それらはデータ ファクトリのデータ ジェネレーターが起動する前に、データ ファクトリがデプロイされていることが原因であるため、無視できます。 これらのエラーによって、データ ファクトリの機能が妨げられることはありません。

このセクションでは、[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) に含まれる必要な[パイプライン](data-factory/data-factory-create-pipelines.md)と[アクティビティ](data-factory/data-factory-create-pipelines.md)について説明します。 ソリューションの図のビューを以下に示します。

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

このファクトリのパイプラインのうち&5; つには、データのパーティション分割と集計に使用される [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトが含まれます。 このスクリプトは、セットアップ時に作成される [Azure Storage](https://azure.microsoft.com/services/storage/) アカウントに置かれます。 それらの場所は demandforecasting\\\\script\\\\hive\\\\ (または https://[Your solution name].blob.core.windows.net/demandforecasting) になります。

[Azure Stream Analytics](#azure-stream-analytics-1) クエリと同様に、[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトは、受信データ形式に関して暗黙的に認識するため、これらのクエリをデータ形式と[特徴エンジニアリング](machine-learning/machine-learning-feature-selection-and-engineering.md)要件に基づいて変更する必要がある場合があります。

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*
この[パイプライン](data-factory/data-factory-create-pipelines.md)には 1 つのアクティビティが含まれます。これは [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) を使用する [HDInsightHive](data-factory/data-factory-hive-activity.md) アクティビティで、[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトを実行し、変電所レベルの 10 秒ごとにストリームに入る需要データを 1 時間ごとに地域レベルに集計し、Azure Stream Analytics ジョブによって [Azure Storage](https://azure.microsoft.com/services/storage/) に格納します。

このパーティション分割タスク用の [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトは ***AggregateDemandRegion1Hr.hql*** です。

#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*
この[パイプライン](data-factory/data-factory-create-pipelines.md)には、次の&2; つのアクティビティが含まれます。

* [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) を使用する [HDInsightHive](data-factory/data-factory-hive-activity.md) アクティビティは、Hive スクリプトを実行し、変電所レベルの&1; 時間ごとの履歴需要データを&1; 時間ごとに地域レベルに集計し、Azure Stream Analytics ジョブの実行中に Azure Storage に格納します。
* [コピー](https://msdn.microsoft.com/library/azure/dn835035.aspx) アクティビティは、集計されたデータを Azure Storage BLOB から、ソリューション テンプレートのインストールの一環としてプロビジョニングされた Azure SQL Database に移動します。

このタスク用の [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトは ***AggregateDemandHistoryRegion.hql*** です。

#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*
これらの[パイプライン](data-factory/data-factory-create-pipelines.md)には、複数のアクティビティが含まれ、それらの最終結果は、このソリューション テンプレートに関連付けられている Azure Machine Learning の実験からのスコア付けされた予測です。 これらはほぼ同じですが、それぞれで処理される地域のみが異なります。これは、ADF パイプラインに渡された各種 RegionID と各地域の Hive スクリプトによって処理されます。  
これに含まれるアクティビティは次のとおりです。

* [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) を使用する [HDInsightHive](data-factory/data-factory-hive-activity.md) アクティビティは、Hive スクリプトを実行し、Azure Machine Learning の実験に必要な集計と特徴エンジニアリングを実行します。 このタスク用の Hive スクリプトは、それぞれの ***PrepareMLInputRegionX.hql*** です。
* [コピー](https://msdn.microsoft.com/library/azure/dn835035.aspx) アクティビティ: [HDInsightHive](data-factory/data-factory-hive-activity.md) アクティビティからの結果を、[AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) アクティビティによってアクセスできる&1; つの Azure Storage BLOB に移動します。
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) アクティビティ:&1; つの Azure Storage BLOB に結果が配置されることになる Azure Machine Learning の実験を呼び出します。

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
これらの[パイプライン](data-factory/data-factory-create-pipelines.md)には&1; つのアクティビティが含まれます。[コピー](https://msdn.microsoft.com/library/azure/dn835035.aspx) アクティビティは、Azure Machine Learning 実験の結果を、それぞれの ***MLScoringRegionXPipeline*** から、ソリューション テンプレートのインストールの一環としてプロビジョニングされた Azure SQL Database に移動します。

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
この[パイプライン](data-factory/data-factory-create-pipelines.md)には&1; つのアクティビティが含まれます。[コピー](https://msdn.microsoft.com/library/azure/dn835035.aspx) アクティビティは、集計された現行の需要データを、***LoadHistoryDemandDataPipeline*** から、ソリューション テンプレートのインストールの一環としてプロビジョニングされた Azure SQL Database に移動します。

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline、CopySubstationDataPipeline、CopyTopologyDataPipeline*
これらの[パイプライン](data-factory/data-factory-create-pipelines.md)には&1; つのアクティビティが含まれます。[コピー](https://msdn.microsoft.com/library/azure/dn835035.aspx) アクティビティは、ソリューション テンプレートのインストールの一環として Azure Storage BLOB にアップロードされる地域/変電所/トポロジ geo の参照データを、ソリューション テンプレートのインストールの一環としてプロビジョニングされた Azure SQL Database に移動します。

### <a name="azure-machine-learning"></a>Azure Machine Learning
このソリューション テンプレートで使用されている [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) の実験は、地域の需要の予測を提供します。 実験は使用されるデータセットに固有であるため、取り込まれるデータに固有の変更や置換が必要になります。

## <a name="monitor-progress"></a>**進行状況の監視**
データ ジェネレーターを起動すると、データを取り込むパイプラインが開始され、Data Factory によって発行されたコマンドに従ってソリューションのさまざまなコンポーネントがアクションを開始します。 パイプラインを監視する&2; つの方法があります。

1. Azure Blob Storage からデータを確認します。

    いずれかの Stream Analytics ジョブが、未加工の受信データを BLOB ストレージに書き込みます。 ソリューションが正常にデプロイされた画面からソリューションの **Azure Blob Storage** コンポーネントをクリックし、右側のパネルの **[開く]** をクリックすると、[Azure 管理ポータル](https://portal.azure.com)に移動します。 管理ポータルで、**[BLOB]** をクリックします。 次のパネルに、コンテナーの一覧が表示されます。 **[energysadata]** をクリックします。 次のパネルに、**demandongoing** フォルダーが表示されます。 rawdata フォルダーの中に、date=2016-01-28 などの名前の付いたフォルダーが表示されます。これらのフォルダーの表示は、生データがコンピューター上に正常に生成され、BLOB ストレージに格納されたことを示します。 これらのフォルダーの中には、有限サイズ (MB 単位) が設定されたファイルがあります。
2. Azure SQL Database からデータを確認します。

    パイプラインの最後の手順は、データ (MachineLearning からの予測など) を SQL Database に書き込むことです。 データが SQL Database に表示されるまで、最大 2 時間の待機が必要な場合があります。 SQL Database で使用できるデータの量を監視する&1; つの方法は、 [Azure 管理ポータル](https://manage.windowsazure.com/)を使用することです。 左側のパネルで、[SQL データベース]![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png) を見つけてそれをクリックします。 次に、データベース (例: demo123456db) を見つけてそれをクリックします。 次のページの **[データベースに接続する]** セクションで、**[SQL データベースに対して Transact-SQL クエリを実行する]** をクリックします。

    ここで、[新しいクエリ] をクリックし、行数を照会できます (例: select count(*) from DemandRealHourly)。データベースが大きくなるにつれ、テーブル内の行数も増加します。
3. Power BI ダッシュボードからデータを確認します。

    Power BI ホット パス ダッシュボードは、未加工の受信データを監視するよう設定できます。 「Power BI ダッシュボード」の手順に従ってください。

## <a name="power-bi-dashboard"></a>**Power BI ダッシュボード**
### <a name="overview"></a>概要
このセクションでは、Azure Stream Analytics (ホット パス) からのリアルタイムのデータのほか、Azure Machine Learning (コールド パス) からの予測結果を表示する Power BI ダッシュボードの設定方法について説明します。

### <a name="setup-hot-path-dashboard"></a>ホット パス ダッシュボードの設定
次の手順では、ソリューションのデプロイメント時に生成された Stream Analytics ジョブからのリアルタイム データの出力を表示する方法について説明します。 次の手順を実行するには、 [Power BI オンライン](http://www.powerbi.com/) アカウントが必要です。 アカウントがない場合は、 [作成](https://powerbi.microsoft.com/pricing)できます。

1. Azure Stream Analytics (ASA) に Power BI 出力を追加します。

   * Azure Stream Analytics ジョブの出力を Power BI ダッシュボードとして設定するには、[Azure Stream Analytics と Power BI のストリーミング データをリアルタイムで表示するリアルタイム分析ダッシュボード](stream-analytics/stream-analytics-power-bi-dashboard.md)に関する記事の手順に従う必要があります。
   * [Azure 管理ポータル](https://manage.windowsazure.com)で Stream Analytics ジョブを見つけます。 ジョブの名前は、<ソリューション名> + "streamingjob" + <ランダムな数字> + "asapbi" となっています (例: demostreamingjob123456asapbi)。
   * ASA ジョブに PowerBI 出力を追加します。 **[出力のエイリアス]** を「**PBIoutput**」に設定します。 **[データセット名]** と **[テーブル名]** を「**EnergyStreamData**」に設定します。 出力を追加したら、ページの下部にある **[開始]** をクリックして Stream Analytics ジョブを開始します。 確認メッセージが表示されます (*例*: "Stream Analytics ジョブ 'myteststreamingjob12345asablob' が正常に開始されました")。
2. [Power BI オンライン](http://www.powerbi.com)

   * [マイ ワークスペース] の左側のパネルにある [データセット] セクションには、Power BI の左側のパネルに表示されている新しいデータセットが表示されます。 これは、前の手順で Azure Stream Analytics からプッシュ送信したストリーミング データです。
   * ***[処理済み]*** ウィンドウが開き、画面の右側に表示されることを確認します。
3. "Demand by Timestamp" タイルを作成します。

   * 左側のパネルの [データセット] セクションで **[EnergyStreamData]** をクリックします。
   * **[折れ線グラフ]** アイコン ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png) をクリックします。
   * **[フィールド]** パネルの [EnergyStreamData] をクリックします。
   * **[タイムスタンプ]** をクリックして、これが [軸] の下に表示されることを確認します。 **[負荷]** をクリックして、これが [値] の下に表示されることを確認します。
   * 上部の **[保存]** をクリックし、レポートに "EnergyStreamDataReport" という名前を付けます。 左側の [ナビゲーター] ウィンドウの [レポート] セクションに、"EnergyStreamDataReport" という名前のレポートが表示されます。
   * この折れ線グラフの右上隅にある **[ビジュアルをピン留めする]** ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) アイコンをクリックします。[ダッシュボードにピン留めする] ウィンドウが表示され、ダッシュボードを選択できるようになります。 [EnergyStreamDataReport] を選択し、[ピン留め] をクリックします。
   * ダッシュボードのこのタイルにマウス ポインターを置き、右上隅の [編集] アイコンをクリックして、そのタイトルを "Demand by Timestamp" に変更します。
4. 適切なデータセットに基づいて他のダッシュボード タイルを作成します。 最終的なダッシュボードの表示を次に示します。
     ![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>コールド パス ダッシュボードの設定
コールド パス データ パイプラインでの重要な目標は、各地域の需要予測を取得することです。 Power BI は、そのデータ ソースとして、予測結果が格納されている Azure SQL データベースに接続します。

> [!NOTE]
> 1) ダッシュボードに表示するのに十分な予測結果が収集されるまで数時間かかります。 データ ジェネレーターを起動後 2 ～ 3 時間経ってからこのプロセスを開始することをお勧めします。 2) この手順の前提条件は、無料のソフトウェア [Power BI Desktop](https://powerbi.microsoft.com/desktop)をダウンロードしてインストールしていることです。
>
>

1. データベースの資格情報を取得します。

   次の手順に進む前に、 **データベース サーバー名、データベース名、ユーザー名、およびパスワード** が必要です。 以下の手順で、それらを見つける方法を説明します。

   * ソリューション テンプレート図の **[Azure SQL Database]** が緑色に変わったら、それをクリックしてから **[開く]** をクリックします。 Azure 管理ポータルが表示され、データベース情報ページが開きます。
   * このページには、[データベース] セクションがあります。 ここには、作成済みのデータベースの一覧が表示されます。 データベースの名前は、 **"&lt;ソリューション名&gt; + &lt;ランダムな数字&gt; + 'db'"** となります (例: "mytest12345db")。
   * 目的のデータベースをクリックします。新しいポップアップ パネルの上部にデータベース サーバー名が見つかります。 データベース サーバー名は、**"<ソリューション名> + <ランダムな数字> + "database.windows.net,1433""** となります (例: "mytest12345.database.windows.net,1433")。
   * データベースの**ユーザー名**と**パスワード**は、ソリューションのデプロイ時に記録しておいたユーザー名とパスワードと同じです。
2. コールド パス Power BI ファイルのデータ ソースを更新します。

   * [Power BI Desktop](https://powerbi.microsoft.com/desktop)の最新バージョンがインストールされていることを確認します。
   * ダウンロードした **DemandForecastingDataGeneratorv1.0** フォルダー内にある **Power BI Template\DemandForecastPowerBI.pbix** ファイルをダブルクリックします。 初期の視覚エフェクトは、ダミー データに基づいています。 **注:** エラー メッセージが表示された場合は、Power BI Desktop の最新バージョンがインストールされていることを確認してください。

     ファイルを開いたら、ファイルの上部にある **[クエリを編集]**をクリックします。 ポップアップ ウィンドウで、右側のパネルの **[ソース]** をダブルクリックします。
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * ポップアップ ウィンドウの **[サーバー]** と **[データベース]** を独自のサーバーとデータベースの名前に置き換えて、**[OK]** をクリックします。 サーバー名については、ポート 1433 (**YourSolutionName.database.windows.net 1433**) を指定していることを確認してください。 画面に表示される警告メッセージは無視します。
   * 次のポップアップ ウィンドウで、左側のウィンドウに&2; つのオプション (**[Windows]** と **[データベース]**) が表示されます。 **[データベース]** をクリックし、**[ユーザー名]** と **[パスワード]** (これは、初めてソリューションをデプロイし、Azure SQL データベースを作成したときに入力したユーザー名とパスワードです) を入力します。 ***[これらの設定の適用対象レベルの選択]*** で、データベース レベル オプションをオンにします。 次に **[接続]**をクリックします。
   * 前のページに戻ったら、ウィンドウを閉じます。 メッセージが表示されます。**[適用]** をクリックします。 最後に、**[保存]** ボタンをクリックして、変更を保存します。 これで、Power BI ファイルは、サーバーへの接続を確立しました。 視覚エフェクトが空の場合、凡例の右上隅にある消しゴム アイコンをクリックして、視覚エフェクトの選択をクリアし、すべてのデータを表示します。 更新ボタンを使用して、視覚エフェクトに新しいデータを反映させます。 最初、視覚エフェクトにはシード データのみ表示されます。データ ファクトリは 3 時間ごとに更新されるようにスケジュールされています。 3 時間後、データを更新すると、視覚エフェクトに反映された新しい予測が表示されます。
3. (省略可能) コールド パス ダッシュボードを [Power BI オンライン](http://www.powerbi.com/)に公開します。 この手順では、Power BI アカウント (または Office 365 アカウント) が必要であることに注意してください。

   * **[公開]** をクリックします。 数秒後、緑色のチェック マークの付いた 「Power BI への公開が成功しました」と表示するウィンドウが表示されます。 [Power BI で demoprediction.pbix を開く] の下のリンクをクリックします。 詳細な手順については、「 [Power BI Desktop からの発行](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)」を参照してください。
   * 新しいダッシュボードを作成するには、左側のウィンドウで **[ダッシュボード]** セクションの横の **[+]** 記号をクリックします。 この新しいダッシュボードの名前として、「Demand Forecasting Demo」と入力します。
   * レポートが開いたら、![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) をクリックして、すべての視覚エフェクトをダッシュボードにピン留めします。 詳細な手順については、「 [レポートから Power BI ダッシュボードにタイルをピン留め](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report)」を参照してください。
     ダッシュボード ページに移動し、視覚エフェクトのサイズと場所を調整し、タイルを編集します。 タイルを編集する方法の詳細については、「 [タイルの編集 -- サイズ変更、移動、名前の変更、ピン留め、削除、ハイパーリンクの追加](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename)」を参照してください。 いくつかのコールド パス視覚エフェクトがピン留めされたサンプル ダッシュボードを次に示します。

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. (省略可能) データ ソースの更新のスケジュールを設定します。

   * データの更新のスケジュールを設定するには、**EnergyBPI-Final** データセットの上にマウス ポインターを移動し、![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png) をクリックして、**[更新のスケジュール設定]** を選択します。
     **注:** 警告メッセージが表示された場合は、**[資格情報の編集]** をクリックし、データベース資格情報が、手順 1. で説明したものと同じかどうかを確認します。

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * **[更新のスケジュール設定]** セクションを展開します。 [データを最新の状態に保つ] を有効にします。
   * 必要に応じて更新をスケジュールします。 詳細については、「 [Power BI でのデータの更新](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)」を参照してください。

## <a name="how-to-delete-your-solution"></a>**ソリューションを削除する方法**
データ ジェネレーターはコストを上げるので、ソリューションを活発に使用していないときはデータ ジェネレーターを停止してください。 使用していない場合、ソリューションを削除してください。 ソリューションを削除すると、ソリューションのデプロイ時にサブスクリプションにプロビジョニングされたすべてのコンポーネントが削除されます。 ソリューションを削除するには、ソリューション テンプレートの左パネルでソリューション名をクリックし、[削除] をクリックします。

## <a name="cost-estimation-tools"></a>**コスト見積もりツール**
各自のサブスクリプションでエネルギーの需要予測ソリューション テンプレートを実行する場合に関連する合計コストを詳しく知るために役立つ次の&2; つのツールがあります。

* [Microsoft Azure 料金計算ツール (オンライン)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure 料金計算ツール (デスクトップ)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**謝辞**
この記事は、Microsoft のデータ サイエンティスト Yijing Chen とソフトウェア エンジニア Qiu Min によって作成されています。



<!--HONumber=Jan17_HO1-->


