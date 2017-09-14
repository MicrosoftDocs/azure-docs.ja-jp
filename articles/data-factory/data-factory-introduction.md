---
title: "データ統合サービスである Data Factory の概要 | Microsoft Docs"
description: "データの移動と変換を調整、自動化するクラウド データ統合サービスである Azure Data Factory について説明します。"
keywords: "データ統合, クラウド データ統合, Azure Data Factory とは"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/14/2017
ms.author: shlo
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 537bdee67ed9648c3cba2099553d847399609705
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017

---
# <a name="introduction-to-azure-data-factory"></a>Azure Data Factory の概要 
## <a name="what-is-azure-data-factory"></a>Azure Data Factory とは何ですか。
ビッグ データの世界では、既存のデータがビジネスにどのように活用されるのでしょうか。 オンプレミスのデータ ソースや他の各種データ ソースから取得した参照データを使用して、クラウドで生成されたデータを補強することはできるのでしょうか。 たとえばあるゲーム会社は、クラウドのゲームによって生成されるさまざまなログを収集しています。 アップセルやクロス セルの商機を見極めたり、ビジネスの成長を後押しする魅力的な新機能を開発したり、カスタマー エクスペリエンスを高めたりするために、その会社がこれらのログを分析して顧客の好みや内訳、利用行動などについての洞察を得たいと考えているとしましょう。 

これらのログを分析するには、顧客情報やゲーム情報、マーケティング キャンペーン情報など、オンプレミスのデータ ストアにある参照データを使用する必要があります。 クラウドのデータ ストアにあるログ データに加え、オンプレミスのデータ ストアにある参照データを取り込まなければなりません。 その後、取り込んだデータをクラウドの Hadoop (Azure HDInsight) で処理し、その結果得られたデータをクラウドのデータ ウェアハウス (Azure SQL Data Warehouse など) かオンプレミスのデータ ストア (SQL Server など) で公開します。 この会社は、このワークフローを週 1 回実行したいと考えています。 

そこで、オンプレミスとクラウドの両方のデータ ストアからデータを取り込み、既存のコンピューティング サービス (Hadoop など) を使ってそのデータを変換または処理したうえで、その結果を BI アプリケーションが利用できるようオンプレミスまたはクラウドのデータ ストアに公開できるようなワークフローを作成するためのプラットフォームが必要となります。 

![Data Factory の概要](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory は、この種のシナリオに対応するプラットフォームです。 **そのクラウドベースのデータ統合サービスを通じて、データの移動と変換を制御して自動化するデータ主導型のワークフローをクラウドに作成**することができます。 Azure Data Factory を使えば、データ主導型のワークフロー (パイプライン) を作成し、スケジューリングできます。具体的には、各種データ ストアからデータを取り込む、そのデータを各種コンピューティング サービス (Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning など) で処理/変換する、データ ストア (Azure SQL Data Warehouse など) に出力データを公開して、それを利用するビジネス インテリジェンス (BI) アプリケーションに提供するという一連の処理を行えるワークフローです。  

従来の ETL (Extract-Transform-Load) プラットフォームというよりも、むしろ EL (Extract-Load) と TL (Transform-Load) とを組み合わせたプラットフォームであるといえます。 実行される変換は、派生列の追加や行数のカウント、データの並べ替えといった変換ではなく、コンピューティング サービスを使ったデータの変換/処理です。 

現在、Azure Data Factory のワークフローが取り込んだり生成したりするデータは**タイム スライス データ** (時間単位、日単位、週単位など) です。 たとえばパイプラインで 1 日 1 回、入力データを読み取り、データを処理して、出力データを生成することができます。 ワークフローを 1 回だけ実行することもできます。  
  

## <a name="how-does-it-work"></a>それはどのように機能しますか? 
通常、Azure Data Factory のパイプライン (データ 主導型のワークフロー) では次の 3 つのステップが実行されます。

![Azure Data Factory の 3 つのステージ](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>接続と収集
企業が所有するデータは多種多様で、ソースもさまざまです。 情報生成システム構築の最初のステップは、SaaS サービス、ファイル共有、FTP、Web サービスなど、必要なすべてのデータ ソースと処理の機能に接続し、必要に応じて、後続の処理を行う 1 か所にデータを移動させることです。

Data Factory を使用していない企業では、カスタムのデータ移動コンポーネントを構築するか、これらのデータ ソースと処理を統合するカスタム サービスを作成しなければなりません。 このような作業にはコストがかかり、システムの統合や保守が容易ではありません。エンタープライズ クラスの監視やアラートが欠け、完全に管理されたサービスが提供できるような制御機能を用意できないことも少なくありません。

Data Factory を使用すれば、データ パイプラインのコピー アクティビティで、オンプレミスとクラウドの両方のソース データ ストアからクラウド内の一元化されたデータ ストアにデータを移動し、詳しく分析することができます。 たとえば、Azure Data Lake Store でデータを収集し、後で Azure Data Lake Analytics コンピューティング サービスを使用してデータを変換することができます。 または、Azure BLOB ストレージでデータを収集し、後で Azure HDInsight Hadoop クラスターを使用してデータを変換することもできます。

### <a name="transform-and-enrich"></a>変換と強化
クラウド上の一元的なデータ ストアにデータが集まったら、HDInsight Hadoop、Spark、Data Lake Analytics、Machine Learning などのコンピューティング サービスを使って、その収集されたデータを処理または変換します。 保守しやすい管理されたスケジュールで確実に変換データを生成し、信頼性の高いデータを運用環境に提供する必要があります。 

### <a name="publish"></a>[発行] 
クラウドから SQL Server などのオンプレミス ソースに変換データを送信したり、ビジネス インテリジェンス (BI) や分析ツールなどのアプリケーションで利用できるよう、そのデータをクラウド ストレージ ソース内に保持したりします。

## <a name="key-components"></a>主なコンポーネント
1 つの Azure サブスクリプションで 1 つ以上の Azure Data Factory インスタンス (データ ファクトリ) を利用できます。 Azure Data Factory は、4 つの主要コンポーネントで構成されたプラットフォームです。このプラットフォームを基盤とし、データ移動とデータ変換のステップを含んだデータ主導型のワークフローを作成することができます。 

### <a name="pipeline"></a>パイプライン
データ ファクトリには、1 つまたは複数のパイプラインを設定できます。 パイプラインは、アクティビティをグループ化したものです。 パイプライン内のアクティビティがまとまって 1 つのタスクを実行します。 たとえば、パイプラインに Azure BLOB からデータを取り込むアクティビティのグループを含め、HDInsight クラスターで Hive クエリを実行してデータをパーティション分割することができます。 パイプラインのメリットは、アクティビティを個別にではなく、セットとして管理できることです。 たとえば、デプロイとスケジュール設定を、アクティビティごとではなく、パイプライン単位で行うことができます。 

### <a name="activity"></a>アクティビティ
パイプラインには、1 つまたは複数のアクティビティを含めることができます。 アクティビティは、データに対して実行するアクションを定義します。 たとえば、コピー アクティビティを使用して、データ ストア間でデータをコピーできます。 同様に、Azure HDInsight クラスターに対して Hive クエリを実行する Hive アクティビティを使用して、データを変換または分析できます。 Data Factory では、データ移動アクティビティとデータ変換アクティビティの 2 種類のアクティビティがサポートされています。

### <a name="data-movement-activities"></a>データ移動アクティビティ
Data Factory のコピー アクティビティは、ソース データ ストアからシンク データ ストアにデータをコピーします。 Data Factory は次のデータ ストアをサポートしています。 また、任意のソースのデータを任意のシンクに書き込むことができます。 データ ストアをクリックすると、そのストアとの間でデータをコピーする方法がわかります。

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。

### <a name="data-transformation-activities"></a>データ変換アクティビティ
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

詳細については、[データ変換アクティビティ](data-factory-data-transformation-activities.md)に関する記事を参照してください。

### <a name="custom-net-activities"></a>カスタム .NET アクティビティ
コピー アクティビティでサポートされていないデータ ストアとの間でデータを移動する必要がある場合、つまり独自のロジックを使用してデータを変換する場合は、 **カスタム .NET アクティビティ**を作成します。 カスタム アクティビティの作成と使用の詳細については、「 [Azure Data Factory パイプラインでカスタム アクティビティを使用する](data-factory-use-custom-activities.md)」をご覧ください。

### <a name="datasets"></a>データセット
アクティビティは、入力として 0 個以上のデータセットを受け取り、出力として 1 つまたは複数のデータセットを生成します。 データセットは、データ ストア内のデータ構造を表しています。アクティビティ内でデータを入力または出力として使用したい場合は、そのデータをポイントまたは参照するだけで済みます。 たとえば、Azure BLOB データセットは、パイプラインによってデータが読み取られる、Blob Storage ストレージ内の BLOB コンテナーと BLOB フォルダーを指定します。 または、アクティビティの出力データの書き込み先となるテーブルを Azure SQL Table データセットで指定します。 

### <a name="linked-services"></a>リンクされたサービス
リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 リンクされたサービスはデータ ソースへの接続を定義するもので、データセットはデータの構造を表すもの、と捉えることができます。 たとえば、Azure Storage アカウントに接続するための接続文字列は、Azure Storage のリンクされたサービスで指定します。 また、データを格納する BLOB コンテナーやフォルダーは、Azure BLOB データセットで指定します。   

Data Factory ではリンクされたサービスは 2 つの目的に使用されます。

* オンプレミスの SQL Server、Oracle データベース、ファイル共有、Azure Blob Storage アカウント、その他の **データ ストア** を表すため。 サポートされているデータ ストアの一覧については、 [データ移動アクティビティ](#data-movement-activities) に関する記事をご覧ください。
* アクティビティの実行をホストできる **コンピューティング リソース** を表すため。 たとえば、HDInsightHive アクティビティは HDInsight Hadoop クラスターで実行されます。 サポートされているコンピューティング環境の一覧については、[データ変換アクティビティ](#data-transformation-activities)に関する記事をご覧ください。

### <a name="relationship-between-data-factory-entities"></a>Data Factory エンティティ間の関係
![図: Data Factory、クラウド データ統合サービス - 主要な概念](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**図 2.** データセット、アクティビティ、パイプライン、リンクされたサービスの間の関係

## <a name="supported-regions"></a>サポートされているリージョン
現時点では、データ ファクトリは、**米国西部**、**米国東部**、**北ヨーロッパ** リージョンで作成できます。 ただし、データ ファクトリは、他の Azure リージョン内のデータ ストアやコンピューティング サービスにアクセスし、データ ストア間でデータを移動したり、コンピューティング サービスを使用してデータを処理したりできます。

Azure Data Factory 自体は、データを保存しません。 Azure Data Factory を使用すると、データ主導型のワークフローを作成し、[サポートされているデータ ストア](#data-movement-activities)間でのデータ移動と、他のリージョンまたはオンプレミス環境にある[コンピューティング サービス](#data-transformation-activities)を使用したデータ処理を調整できます。 また、プログラムと UI の両方のメカニズムを使用して、 [ワークフローを監視および管理](data-factory-monitor-manage-pipelines.md) することもできます。

Data Factory を利用できるリージョンが**米国西部**、**米国東部**、**北ヨーロッパ**のみであっても、Data Factory 内でデータ移動を実行するサービスは、いくつかのリージョンで[グローバル](data-factory-data-movement-activities.md#global)に利用できます。 データ ストアがファイアウォールの内側にある場合は、オンプレミス環境にインストールされている [Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) がデータを移動します。

たとえば、Azure HDInsight クラスターや Azure Machine Learning などのコンピューティング環境が西ヨーロッパ リージョン以外で稼働しているものと想定します。 北ヨーロッパに Azure Data Factory インスタンスを作成して利用すると、西ヨーロッパのコンピューティング環境でジョブのスケジュール設定にそのインスタンスを使用することができます。 Data Factory がコンピューティング環境でジョブをトリガーするまでに数ミリ秒かかりますが、コンピューティング環境でのジョブの実行時間は変わりません。

## <a name="get-started-with-creating-a-pipeline"></a>パイプライン作成の概要
Azure Data Factory には、次のいずれかのツールまたは API でデータ パイプラインを作成できます。 

- Azure ポータル
- Visual Studio
- PowerShell
- .NET API
- REST API
- Azure Resource Manager テンプレート 

データ パイプラインでデータ ファクトリを構築する方法については、以下のチュートリアルで紹介されている具体的な手順に従ってください。

| チュートリアル | Description |
| --- | --- |
| [2 つのクラウド データ ストア間でのデータの移動](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |このチュートリアルでは、BLOB ストレージから SQL データベースに **データを移動** するパイプラインを備えたデータ ファクトリを作成します。 |
| [Hadoop クラスターを使用したデータの変換](data-factory-build-your-first-pipeline.md) |このチュートリアルでは、Azure HDInsight (Hadoop) クラスターで Hive スクリプトを実行して **データを処理する** データ パイプラインを備えた最初の Azure データ ファクトリを構築します。 |
| [Data Management Gateway を使用したオンプレミス データ ストアとクラウド データ ストア間でのデータの移動](data-factory-move-data-between-onprem-and-cloud.md) |このチュートリアルでは、**オンプレミス**の SQL Server データベースから Azure BLOB に**データを移動**するパイプラインを備えたデータ ファクトリを構築します。 チュートリアルの一環として、ご使用のコンピューターに Data Management Gateway をインストールして構成します。 |

