---
title: "Data Lake Store と他の Azure サービスを統合する | Microsoft Docs"
description: "Data Lake Store が他の Azure サービスと統合されるしくみを理解します"
documentationcenter: 
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ce6a611db7be7f1ae5f5027a77727e54e5e538ae


---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>Data Lake Store と他の Azure サービスを統合する
Azure Data Lake Store を他の Azure サービスと連携させれば、さまざまなシナリオが可能になります。 次の記事では、Data Lake Store と統合できるサービスについてまとめてあります。

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Azure HDInsight で Data Lake Store を使用する
HDFS 準拠ストレージとして Data Lake Store を使用する [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) クラスターにプロビジョニングできます。 このリリースでは、Windows と Linux の Hadoop または Storm クラスターの場合、Data Lake Store は追加ストレージとしてのみ利用できます。 このようなクラスターでは、依然として Azure Storage (WASB) がデフォルト ストレージとして使用されます。 ただし、Windows と Linux の HBase クラスターの場合、Data Lake Store をデフォルト ストレージと追加ストレージの両方として使用できます。

Data Lake Store で HDInsight クラスターにプロビジョニングする方法については、次を参照してください。

* [Azure ポータルを使用して、Data Lake Store を使用する HDInsight クラスターをプロビジョニングする](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Azure PowerShell を使用して、Data Lake Store を使用する HDInsight クラスターをプロビジョニングする](data-lake-store-hdinsight-hadoop-use-powershell.md)

**ビデオの方がよいですか?**   HDInsight クラスターで Data Lake Store を使用する方法を説明しているビデオをご覧になる場合は、以下のリンクをクリックしてください。

* [Azure ポータルを使用して、Data Lake Store を使用する HDInsight クラスターを作成する](https://mix.office.com/watch/l93xri2yhtp2)
* クラスターがセットアップされたら、 [Hive および Pig スクリプトを使用して Data Lake Store のデータにアクセス](https://mix.office.com/watch/1n9g5w0fiqv1q)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Azure Data Lake Analytics で Data Lake Store を使用する
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) を利用すれば、クラウド スケールでビッグ データを操作できます。 リソースを動的にプロビジョニングし、サポートされているさまざまなデータ ソース (Data Lake Store を含む) に保存可能なテラバイト単位の、さらにはエクサバイト単位の分析を可能にします。 Data Lake Analytics は Azure Data Lake Store と連動するように最適化されており、ビッグ データの作業負荷に対して最高レベルのパフォーマンス、スループット、並列化を提供します。

Data Lake Store で Data Lake Analytics を使用する方法については、「 [Data Lake Store を使用して Data Lake Analytics を始める](../data-lake-analytics/data-lake-analytics-get-started-portal.md)」を参照してください。

**ビデオの方がよいですか?**   HDInsight クラスターで Data Lake Store を使用する方法を説明しているビデオをご覧になる場合は、以下のリンクをクリックしてください。

* [Azure Data Lake Analytics を Azure Data Lake Store に接続する](https://mix.office.com/watch/qwji0dc9rx9k)
* [Azure Data Lake Store に Data Lake Analytics 経由でアクセスする](https://mix.office.com/watch/1n0s45up381a8)

## <a name="use-data-lake-store-with-azure-data-factory"></a>Azure Data Factory で Data Lake Store を使用する
[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用し、Azure テーブル、Azure SQL Database、Azure SQL DataWarehouse、Azure Storage BLOB、オンプレミス データベースからデータを取り込むことができます。 Azure エコシステムの第一級オブジェクトである Azure Data Factory を使用し、これらのソースから Azure Data Lake Store へのデータ取り込みを調整できます。

Data Lake Store で Azure Data Factory を使用する方法については、「 [Data Factory を使用し、Data Lake Store のデータを移動する](../data-factory/data-factory-azure-datalake-connector.md)」を参照してください。

**ビデオをご覧になる場合** は、 「 [Azure Data Lake Store 用 Azure Data Factory を使用したデータのオーケストレーション](https://mix.office.com/watch/1oa7le7t2u4ka)」を参照してください。 

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Azure Storage BLOB から Data Lake Store へのデータのコピー
Azure Data Lake Store には、AdlCopy というコマンド ライン ツールがあります。このツールを使用すると、Azure Blob Storage のデータを Data Lake Store アカウントにコピーできます。 詳細については、「[Azure Storage BLOB から Data Lake Store へのデータのコピー](data-lake-store-copy-data-azure-storage-blob.md)」を参照してください。

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Azure SQL Database と Data Lake Store の間でデータをコピーする
Apache Sqoop を使用して Azure SQL Database と Data Lake Store の間でデータのインポートおよびエクスポートを行うことができます。 詳細については、「 [Sqoop を使用して Data Lake Store と Azure SQL Database の間でデータをコピーする](data-lake-store-data-transfer-sql-sqoop.md)」を参照してください。

**ビデオをご覧になる場合** は、「 [リレーショナル ソースと Azure Data Lake Store との間で Apache Sqoop を使用してデータを移動する](https://mix.office.com/watch/1butcdjxmu114)」に移動してください。

## <a name="use-data-lake-store-with-stream-analytics"></a>Stream Analytics で Data Lake Store を使用する
Azure Stream Analytics を使用してストリーミングされたデータを保存する場合は、出力の 1 つとして Data Lake Store を使用できます。 詳細については、「 [Azure Stream Analytics を使用した Azure Storage BLOB から Data Lake Store へのデータ ストリーム](data-lake-store-stream-analytics.md)」を参照してください。

## <a name="use-data-lake-store-with-power-bi"></a>Power BI で Data Lake Store を使用する
Power BI を使用して、Data Lake Store からデータをインポートし、そのデータを分析および視覚化することができます。 詳細については、「 [Power BI を使用して Data Lake Store のデータを分析する](data-lake-store-power-bi.md)」を参照してください。

## <a name="use-data-lake-store-with-data-catalog"></a>Data Catalog で Data Lake Store を使用する
組織全体でデータを検出できるように、Azure Data Catalog に Data Lake Store のデータを登録することができます。 詳細については、「 [Azure Data Catalog に Data Lake Store のデータを登録する](data-lake-store-with-data-catalog.md)」を参照してください。

## <a name="see-also"></a>関連項目
* [Azure Data Lake Store の概要](data-lake-store-overview.md)
* [Azure プレビュー ポータルで Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)
* [Azure PowerShell で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-powershell.md)  




<!--HONumber=Nov16_HO3-->


