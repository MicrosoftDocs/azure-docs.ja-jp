---
title: データ統合サービスである Data Factory の概要 | Microsoft Docs
description: Azure Data Factory の概要を理解する:データの移動と変換を調整および自動化するクラウド データ統合サービス。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: aed073e7d65f193c4123106ba1ae3f4f442965e1
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836309"
---
# <a name="introduction-to-azure-data-factory"></a>Azure Data Factory の概要 
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-introduction.md)
> * [バージョン 2 (最新バージョン)](../introduction.md)

> [!NOTE]
> この記事は、Azure Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Data Factory V2 の概要](../introduction.md)に関するページを参照してください。


## <a name="what-is-azure-data-factory"></a>Azure Data Factory とは何ですか。
ビッグ データの世界では、既存のデータがビジネスにどのように活用されるのでしょうか。 オンプレミスのデータ ソースや他の各種データ ソースから取得した参照データを使用して、クラウドで生成されたデータを補強することはできるのでしょうか。 

たとえば、あるゲーム会社は、クラウド内のゲームによって生成されるログを収集するとします。 これらのログを分析して顧客の好みや内訳、利用行動などについての洞察を得たいと考えているとしましょう。 また同社は、アップセルやクロス セルの商機の見極め、魅力的な新機能の開発、ビジネスの成長の後押し、カスタマー エクスペリエンスの向上にも興味があります。 

これらのログを分析するには、顧客情報やゲーム情報、マーケティング キャンペーン情報など、オンプレミスのデータ ストアにある参照データを使用する必要があります。 クラウドのデータ ストアにあるログ データに加え、オンプレミスのデータ ストアにある参照データを取り込まなければなりません。 

次に、クラウドの Hadoop (Azure HDInsight) を使用してそのデータを処理します。 その結果得られたデータをクラウドのデータ ウェアハウス (Azure SQL Data Warehouse など) またはオンプレミスのデータ ストア (SQL Server など) に公開します。 この会社は、このワークフローを週 1 回実行したいと考えています。 

そこで、オンプレミスとクラウド両方のデータ ストアからデータを取り込むことが可能なワークフローを作成できるプラットフォームが必要になります。 また、既存のコンピューティング サービス (Hadoop など) を使用してデータを変換または処理したうえで、その結果を BI アプリケーションで利用できるようオンプレミスまたはクラウドのデータ ストアに公開できるようにする必要もあります。 

![Data Factory の概要](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory は、このようなシナリオに対応するプラットフォームです。 "*そのクラウドベースのデータ統合サービスを通じて、データの移動と変換を調整および自動化するデータ主導型のワークフローをクラウドに作成することができます*"。 Azure Data Factory を使用すると、次のタスクを実行できます。 

- 各種のデータ ストアからデータを取り込むことができるデータ主導型のワークフロー (パイプライン) を作成し、スケジューリングする。

- そのデータを Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning などのコンピューティング サービスを使って処理または変換する。

-  ビジネス インテリジェンス (BI) アプリケーションから利用できるよう、Azure SQL Data Warehouse などのデータ ストアに出力データを公開する。  

従来の ETL (Extract-Transform-Load) プラットフォームというよりも、むしろ EL (Extract-Load) と TL (Transform-Load) とを組み合わせたプラットフォームであるといえます。 変換では、派生列の追加、行数のカウント、データの並べ替えなどではなく、コンピューティング サービスを使用してデータを処理します。 

現在、Azure Data Factory のワークフローで使用したり生成したりするデータは "*タイム スライス データ*" (時間単位、日単位、週単位など) です。 たとえば、パイプラインで 1 日 1 回、入力データを読み取り、データを処理して、出力データを生成するとします。 ワークフローを 1 回だけ実行することもできます。  
  

## <a name="how-does-it-work"></a>それはどのように機能しますか? 
通常、Azure Data Factory のパイプライン (データ 主導型のワークフロー) では次の 3 つのステップが実行されます。

![Azure Data Factory の 3 つのステージ](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>接続と収集
企業が所有するデータは多種多様で、ソースもさまざまです。 情報生成システム構築の最初のステップは、必要なすべてのデータ ソースと処理の機能に接続することです。 これらのソースには、SaaS サービス、ファイル共有、FTP、Web サービスが含まれます。 その後、必要に応じて後続の処理機能にデータを移動します。

Data Factory を使用していない企業では、カスタムのデータ移動コンポーネントを構築するか、これらのデータ ソースと処理を統合するカスタム サービスを作成しなければなりません。 このようなシステムの統合と保守は高コストで、容易ではありません。 また、このようなシステムでは、エンタープライズ クラスの監視やアラートが欠け、フル マネージドのサービスが提供できるような制御機能を用意できないことも少なくありません。

Data Factory を使用すれば、データ パイプラインのコピー アクティビティで、オンプレミスとクラウドの両方のソース データ ストアからクラウド内の一元化されたデータ ストアにデータを移動し、詳しく分析することができます。 

たとえば、Azure Data Lake Store でデータを収集し、後で Azure Data Lake Analytics コンピューティング サービスを使用してデータを変換することができます。 または、Azure Blob Storage でデータを収集し、後で Azure HDInsight Hadoop クラスターを使用してデータを変換できます。

### <a name="transform-and-enrich"></a>変換と強化
クラウド上の一元的なデータ ストアにデータが集まったら、HDInsight Hadoop、Spark、Data Lake Analytics、Machine Learning などのコンピューティング サービスを使って、そのデータを処理または転送します。 保守しやすい管理されたスケジュールで確実に変換データを生成し、信頼性の高いデータを運用環境に提供する必要があります。 

### <a name="publish"></a>発行 
変換したデータをクラウドから SQL Server などのオンプレミス ソースに送信します。 または、BI や分析ツールなどのアプリケーションで利用できるよう、それをクラウド ストレージ ソース内に保持することもできます。

## <a name="key-components"></a>主なコンポーネント
1 つの Azure サブスクリプションで 1 つ以上の Azure Data Factory インスタンス (データ ファクトリ) を利用できます。 Azure Data Factory は、4 つの主要コンポーネントで構成されています。 これらのコンポーネントの連携によって実現するプラットフォームを基盤として、データ移動とデータ変換のステップを含んだデータ主導型のワークフローを作成することができます。 

### <a name="pipeline"></a>パイプライン
データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインは、アクティビティをグループ化したものです。 パイプライン内のアクティビティがまとまって 1 つのタスクを実行します。 

たとえば、パイプラインに Azure BLOB からデータを取り込むアクティビティのグループを含め、HDInsight クラスターで Hive クエリを実行してデータをパーティション分割することができます。 パイプラインのメリットは、アクティビティを個別にではなく、セットとして管理できることです。 たとえば、個々のアクティビティをスケジュールする代わりに、パイプラインをデプロイしてスケジュールすることができます。 

### <a name="activity"></a>アクティビティ
パイプラインには、1 つまたは複数のアクティビティを含めることができます。 アクティビティは、データに対して実行するアクションを定義します。 たとえば、コピー アクティビティを使用して、データ ストア間でデータをコピーできます。 同様に、Hive アクティビティを使用できます。 Hive アクティビティは、Azure HDInsight クラスターに対して Hive クエリを実行して、データを変換または分析します。 Data Factory では、データ移動アクティビティとデータ変換アクティビティの 2 種類のアクティビティがサポートされています。

### <a name="data-movement-activities"></a>データ移動アクティビティ
Data Factory のコピー アクティビティは、ソース データ ストアからシンク データ ストアにデータをコピーします。 また、任意のソースのデータを任意のシンクに書き込むことができます。 データ ストアを選択すると、そのストアとの間でデータをコピーする方法がわかります。 Data Factory は次のデータ ストアをサポートしています。

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

詳細については、「[コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md)」を参照してください。

### <a name="data-transformation-activities"></a>データ変換アクティビティ
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

詳細については、「[コピー アクティビティを使用したデータの移動](data-factory-data-transformation-activities.md)」を参照してください。

### <a name="custom-net-activities"></a>カスタム .NET アクティビティ
コピー アクティビティでサポートされていないデータ ストアとの間でデータを移動する必要がある場合または独自のロジックを使用してデータを変換する場合は、 カスタム .NET アクティビティを作成します。 カスタム アクティビティの作成方法と使用方法の詳細については、「[Azure Data Factory パイプラインでカスタム アクティビティを使用する](data-factory-use-custom-activities.md)」を参照してください。

### <a name="datasets"></a>データセット
アクティビティは、入力として 0 個以上のデータセットを受け取り、出力として 1 つまたは複数のデータセットを生成します。 データセットは、データ ストア内のデータ構造を表します。 これらの構造は、アクティビティ (入力や出力など) で使用するデータをポイントまたは参照します。 

たとえば、Azure BLOB データセットは、パイプラインによるデータの読み取り先として、Azure BLOB Storage 内の BLOB コンテナーと BLOB フォルダーを指定します。 または、アクティビティの出力データの書き込み先となるテーブルを Azure SQL テーブル データセットで指定します。 

### <a name="linked-services"></a>リンクされたサービス
リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 リンクされたサービスはデータ ソースへの接続を定義するもので、データセットはデータの構造を表すもの、と捉えることができます。 

たとえば、Azure Storage アカウントに接続するための接続文字列は、Azure Storage のリンクされたサービスで指定します。 データを格納する BLOB コンテナーやフォルダーは、Azure BLOB データセットで指定します。   

Data Factory ではリンクされたサービスは 2 つの理由で使用されます。

* オンプレミスの SQL Server データベース、Oracle データベース、ファイル共有、Azure Blob Storage アカウント、その他の "*データ ストア*" を表すため。 サポートされているデータ ストアの一覧については、 [データ移動アクティビティ](#data-movement-activities) に関する記事をご覧ください。

* アクティビティの実行をホストできる *コンピューティング リソース* を表すため。 たとえば、HDInsightHive アクティビティは HDInsight Hadoop クラスターで実行されます。 サポートされているコンピューティング環境の一覧については、「[データ変換アクティビティ](#data-transformation-activities)」セクションを参照してください。

### <a name="relationship-between-data-factory-entities"></a>Data Factory エンティティ間の関係

![図:クラウド データ統合サービスである Data Factory - 主要な概念](./media/data-factory-introduction/data-integration-service-key-concepts.png)

## <a name="supported-regions"></a>サポートされているリージョン
現時点では、データ ファクトリは、米国西部、米国東部、北ヨーロッパ リージョンで作成できます。 ただし、データ ファクトリは、他の Azure リージョン内のデータ ストアやコンピューティング サービスにアクセスし、データ ストア間でデータを移動したり、コンピューティング サービスを使用してデータを処理したりできます。

Azure Data Factory 自体は、データを保存しません。 これを使用すると、データ主導型のワークフローを作成し、[サポートされているデータ ストア](#data-movement-activities)間でのデータ移動を調整できます。 さらに、他のリージョンまたはオンプレミスの環境にある[コンピューティング サービス](#data-transformation-activities)を使用してデータを処理することもできます。 また、プログラムと UI の両方のメカニズムを使用して、[ワークフローを監視および管理](data-factory-monitor-manage-pipelines.md)することもできます。

Data Factory は、米国西部、米国東部、北ヨーロッパ リージョンでのみ使用できます。 ただし、Data Factory 内でデータ移動を実行するサービスは、いくつかのリージョンで[グローバルに](data-factory-data-movement-activities.md#global)利用できます。 データ ストアがファイアウォールの内側にある場合は、オンプレミスの環境にインストールされている[データ管理ゲートウェイ](data-factory-move-data-between-onprem-and-cloud.md)がデータを移動します。

たとえば、Azure HDInsight クラスターや Azure Machine Learning などのコンピューティング環境が西ヨーロッパ リージョンにあると想定します。 Azure Data Factory インスタンスは、北ヨーロッパに作成して使用できます。 その後、それを使用すると、西ヨーロッパのコンピューティング環境でジョブをスケジュールできます。 Data Factory がコンピューティング環境でジョブをトリガーするまでに数ミリ秒かかりますが、コンピューティング環境でのジョブの実行時間は変わりません。

## <a name="get-started-with-creating-a-pipeline"></a>パイプライン作成の概要
Azure Data Factory には、次のいずれかのツールまたは API でデータ パイプラインを作成できます。 

- Visual Studio
- PowerShell
- .NET API
- REST API
- Azure Resource Manager テンプレート

データ パイプラインでデータ ファクトリを構築する方法については、以下のチュートリアルで紹介されている具体的な手順に従ってください。

| チュートリアル | 説明 |
| --- | --- |
| [2 つのクラウド データ ストア間でのデータの移動](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Blob Storage から SQL データベースにデータを移動するパイプラインを備えたデータ ファクトリを作成します。 |
| [Hadoop クラスターを使用したデータの変換](data-factory-build-your-first-pipeline.md) |Azure HDInsight (Hadoop) クラスターで Hive スクリプトを実行してデータを処理するデータ パイプラインを備えた最初の Azure データ ファクトリを構築します。 |
| [データ管理ゲートウェイを使用したオンプレミス データ ストアとクラウド データ ストア間でのデータの移動](data-factory-move-data-between-onprem-and-cloud.md) |オンプレミスの SQL Server データベースから Azure BLOB にデータを移動するパイプラインを備えたデータ ファクトリを構築します。 チュートリアルの一環として、ご使用のコンピューターに Data Management Gateway をインストールして構成します。 |
