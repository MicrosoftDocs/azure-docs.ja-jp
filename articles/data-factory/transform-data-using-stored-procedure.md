---
title: "Azure Data Factory でストアド プロシージャ アクティビティを使用してデータを変換する | Microsoft Docs"
description: "SQL Server ストアド プロシージャ アクティビティを使用して、Data Factory パイプラインから Azure SQL Database/Data Warehouse でストアド プロシージャを呼び出す方法について説明します。"
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: shengc
ms.openlocfilehash: f13f8aa0ca8686c0582bed77d047c9e6b39f7aa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Azure Data Factory での SQL Server ストアド プロシージャ アクティビティを使用したデータの変換
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - GA](v1/data-factory-stored-proc-activity.md)
> * [バージョン 2 - プレビュー](transform-data-using-stored-procedure.md)


Data Factory [パイプライン](concepts-pipelines-activities.md)のデータ変換アクティビティを使用して、生データを予測や洞察に変換および処理します。 ストアド プロシージャ アクティビティは、Data Factory でサポートされる変換アクティビティの 1 つです。 この記事は、データ変換と Data Factory でサポートされている変換アクティビティの概要を説明する、[データ変換](transform-data.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 のストアド プロシージャ アクティビティ](v1/data-factory-stored-proc-activity.md)を参照してください。
> 
> Azure Data Factory の使用経験がない場合は、この記事を読む前に、「[Azure Data Factory の概要](introduction.md)」を参照し、[データの変換のチュートリアル](tutorial-transform-data-spark-powershell.md)を実行してください。 

ストアド プロシージャ アクティビティを使用して、社内または Azure 仮想マシン (VM) 上の次のいずれかのデータ ストアでストアド プロシージャを呼び出すことができます。 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server データベース  SQL Server を使用している場合は、データベースをホストしているコンピューター、またはデータベースにアクセスできる別のコンピューターにセルフホステッド統合ランタイムをインストールします。 セルフホステッド統合ランタイムは、管理された確実な方法でオンプレミスまたは Azure VM 上のデータ ソースをクラウド サービスに接続するコンポーネントです。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。

> [!IMPORTANT]
> Azure SQL Database または SQL Server にデータをコピーする場合、**sqlWriterStoredProcedureName** プロパティを使用してストアド プロシージャを呼び出すように、コピー アクティビティで **SqlSink** を構成できます。 プロパティの詳細については、[Azure SQL Database](connector-azure-sql-database.md)、[SQL Server](connector-sql-server.md) の各コネクタに関する記事をご覧ください。 コピー アクティビティを使用して Azure SQL Data Warehouse にデータをコピー中に、ストアド プロシージャを呼び出すことはできません。 しかし、SQL Data Warehouse のストアド プロシージャを呼び出すためにストアド プロシージャのアクティビティを使用することは可能です。 
>
> Azure SQL Database、SQL Server、または Azure SQL Data Warehouse からデータをコピーする場合、**sqlReaderStoredProcedureName** プロパティを使用して、ソース データベースからデータを読み取るストアド プロシージャを呼び出すように、コピー アクティビティで **SqlSource** を構成できます。 詳細については、[Azure SQL Database](connector-azure-sql-database.md)、[SQL Server](connector-sql-server.md)、[Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) の各コネクタに関する記事をご覧ください。          

 

## <a name="syntax-details"></a>構文の詳細
JSON 形式のストアド プロシージャ アクティビティの定義を次に示します。

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

次の表に、JSON のプロパティを示します。

| プロパティ                  | 説明                              | 必須 |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | アクティビティの名前                     | はい      |
| 説明               | アクティビティの用途を説明するテキストです。 | なし       |
| type                      | ストアド プロシージャ アクティビティの場合、アクティビティの種類は SqlServerStoredProcedure です。 | あり      |
| 既定のコンテナー         | Data Factory のリンクされたサービスとして登録されている Azure SQL Database、Azure SQL Data Warehouse、または SQL Server への参照。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 | あり      |
| storedProcedureName       | 出力テーブルで使用するリンクされたサービスで表される Azure SQL Database、Azure SQL Data Warehouse、または SQL Server データベースのストアド プロシージャの名前を指定します。 | あり      |
| storedProcedureParameters | ストアド プロシージャのパラメーター値を指定します。 `"param1": { "value": "param1Value","type":"param1Type" }` を使用して、パラメーター値と、データ ソースでサポートされるパラメーター値のネイティブ型を渡します。 パラメーターで null を渡す必要がある場合は、*"param1": { "value": null }* (すべて小文字) を使用します。 | いいえ       |

## <a name="next-steps"></a>次のステップ
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop ストリーミング アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning バッチ実行アクティビティ](transform-data-using-machine-learning.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
