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
ms.date: 01/25/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 757e91c3c702a62fb143f45a3144069400c5a757
ms.openlocfilehash: e2c2611d6f1094d2c1ba2bdb2398b0b17723af8a


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>クラウドによるデータ統合サービスである Azure Data Factory サービスの概要
## <a name="what-is-azure-data-factory"></a>Azure Data Factory とは何ですか。
Data Factory は、データの**移動**や**変換**を調整し自動化するクラウドベースのデータ統合サービスです。 さまざまなデータ ストアからデータを取り込み、データを変換/処理して結果データをデータ ストアに発行できる Data Factory サービスを使用することで、データ統合ソリューションを作成できます。

Data Factory サービスでは、データを移動して変換するデータ パイプラインを作成し、指定したスケジュール (毎時、毎日、毎週など) でパイプラインを実行できます。 また、このサービスには視覚化機能が豊富に用意されています。このため、データ パイプライン間の系列と依存関係を表示できるほか、1 つの統合されたビューからパイプラインを監視して、容易に問題を特定し、監視アラートを設定できます。

![Diagram: Data Factory Overview, a data integration service](./media/data-factory-introduction/what-is-azure-data-factory.png)

**図&1;.** さまざまなデータ ソースからデータを取り込み、準備、変換、分析を行って、すぐ使用できるデータを発行します。

## <a name="pipelines-and-activities"></a>パイプラインとアクティビティ
Data Factory ソリューションでは、1 つ以上のデータ **パイプライン**を作成します。 パイプラインは、アクティビティの論理的なグループです。 パイプラインは、まとまってタスクを実行するユニットにアクティビティをグループ化するために使用されます。

**アクティビティ** は、データに対して実行するアクションを定義します。 たとえば、コピー アクティビティを使用して、データ ストア間でデータをコピーできます。 同様に、Azure HDInsight クラスターに対して Hive クエリを実行する Hive アクティビティを使用して、データを変換または分析できます。 Data Factory では、データ移動アクティビティとデータ変換アクティビティの&2; 種類のアクティビティがサポートされています。

## <a name="data-movement-activities"></a>データ移動アクティビティ
[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。

## <a name="data-transformation-activities"></a>データ変換アクティビティ
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

詳細については、[データ変換アクティビティ](data-factory-data-transformation-activities.md)に関する記事を参照してください。

コピー アクティビティでサポートされていないデータ ストアとの間でデータを移動する必要がある場合、つまり独自のロジックを使用してデータを変換する場合は、 **カスタム .NET アクティビティ**を作成します。 カスタム アクティビティの作成と使用の詳細については、「 [Azure Data Factory パイプラインでカスタム アクティビティを使用する](data-factory-use-custom-activities.md)」をご覧ください。

## <a name="linked-services"></a>リンクされたサービス
リンクされたサービスは、Data Factory が外部リソース (例: Azure Storage、オンプレミスの SQL Server、Azure HDInsight) に接続するために必要な情報を定義します。 Data Factory ではリンクされたサービスは&2; つの目的に使用されます。

* オンプレミスの SQL Server、Oracle データベース、ファイル共有、Azure Blob Storage アカウント、その他の **データ ストア** を表すため。 サポートされているデータ ストアの一覧については、 [データ移動アクティビティ](#data-movement-activities) に関する記事をご覧ください。
* アクティビティの実行をホストできる **コンピューティング リソース** を表すため。 たとえば、HDInsightHive アクティビティは HDInsight Hadoop クラスターで実行されます。 サポートされているコンピューティング環境の一覧については、[データ変換アクティビティ](#data-transformation-activities)に関する記事をご覧ください。

## <a name="datasets"></a>[データセット]
リンクされたサービスは、データ ストアを Azure データ ファクトリにリンクします。 データセットは、データ ストア内のデータ構造を表します。 たとえば、Azure Storage のリンクされたサービスは、Azure Storage アカウントに接続するための接続情報を Data Factory に提供します。 Azure BLOB データセットは、パイプラインによってデータが読み取られる、Azure BLOB ストレージ内の BLOB コンテナーと BLOB フォルダーを指定します。 同様に、Azure SQL のリンクされたサービスは、Azure SQL データベースに関する接続情報を提供します。また、Azure SQL データセットは、データが含まれているテーブルを指定します。   

## <a name="relationship-between-data-factory-entities"></a>Data Factory エンティティ間の関係
Data Factory には、入力と出力データ、処理イベント、および目的のデータ フローの実行に必要なスケジュールとリソースを定義するために連携するいくつかの重要なエンティティがあります。

![図: Data Factory、クラウド データ統合サービス - 主要な概念](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**図 2.** データセット、アクティビティ、パイプライン、リンクされたサービスの間の関係

リンクされたサービス、データセット、アクティビティ、パイプラインという&4; つの簡単な概念を理解したら、開始する準備は完了です。 [最初のパイプラインを作成](data-factory-build-your-first-pipeline.md)できます。

## <a name="supported-regions"></a>サポートされているリージョン
現時点では、データ ファクトリは、**米国西部**、**米国東部**、**北ヨーロッパ** リージョンで作成できます。 ただし、データ ファクトリは、他の Azure リージョン内のデータ ストアやコンピューティング サービスにアクセスし、データ ストア間でデータを移動したり、コンピューティング サービスを使用してデータを処理したりできます。

Azure Data Factory 自体は、データを保存しません。 Azure Data Factory を使用すると、データ主導型のフローを作成し、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)間でのデータ移動と、他のリージョンまたはオンプレミス環境にある[コンピューティング サービス](data-factory-compute-linked-services.md)を使用したデータ処理を調整できます。 また、プログラムと UI の両方のメカニズムを使用して、 [ワークフローを監視および管理](data-factory-monitor-manage-pipelines.md) することもできます。

Data Factory を利用できるリージョンが**米国西部**、**米国東部**、**北ヨーロッパ**のみであっても、Data Factory 内でデータ移動を実行するサービスは、いくつかのリージョンで[グローバル](data-factory-data-movement-activities.md#global)に利用できます。 データ ストアがファイアウォールの内側にある場合は、オンプレミス環境にインストールされている [Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) がデータを移動します。

たとえば、Azure HDInsight クラスターや Azure Machine Learning などのコンピューティング環境が西ヨーロッパ リージョン以外で稼働しているものと想定します。 北ヨーロッパに Azure Data Factory インスタンスを作成して利用すると、西ヨーロッパのコンピューティング環境でジョブのスケジュール設定にそのインスタンスを使用することができます。 Data Factory がコンピューティング環境でジョブをトリガーするまでに数ミリ秒かかりますが、コンピューティング環境でのジョブの実行時間は変わりません。

将来的には、Azure がサポートするすべての地域で Azure Data Factory が提供される予定です。

## <a name="next-steps"></a>次のステップ
データ パイプラインでデータ ファクトリを構築する方法については、以下のチュートリアルで紹介されている具体的な手順に従ってください。

| チュートリアル | Description |
| --- | --- |
| [2 つのクラウド データ ストア間でのデータの移動](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |このチュートリアルでは、BLOB ストレージから SQL データベースに **データを移動** するパイプラインを備えたデータ ファクトリを作成します。 |
| [Hadoop クラスターを使用したデータの変換](data-factory-build-your-first-pipeline.md) |このチュートリアルでは、Azure HDInsight (Hadoop) クラスターで Hive スクリプトを実行して **データを処理する** データ パイプラインを備えた最初の Azure データ ファクトリを構築します。 |
| [Data Management Gateway を使用したオンプレミス データ ストアとクラウド データ ストア間でのデータの移動](data-factory-move-data-between-onprem-and-cloud.md) |このチュートリアルでは、**オンプレミス**の SQL Server データベースから Azure BLOB に**データを移動**するパイプラインを備えたデータ ファクトリを構築します。 チュートリアルの一環として、ご使用のコンピューターに Data Management Gateway をインストールして構成します。 |



<!--HONumber=Jan17_HO4-->


