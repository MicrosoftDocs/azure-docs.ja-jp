---
title: ストアド プロシージャ アクティビティを使用したデータの変換
description: SQL Server ストアド プロシージャ アクティビティを使用して、Data Factory パイプラインから Azure SQL Database/Data Warehouse でストアド プロシージャを呼び出す方法について説明します。
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: b9ba2f9de82522d4348fa341ad0b41d43c3eebcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375648"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Azure Data Factory での SQL Server ストアド プロシージャ アクティビティを使用したデータの変換
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-stored-proc-activity.md)
> * [現在のバージョン](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data Factory [パイプライン](concepts-pipelines-activities.md)のデータ変換アクティビティを使用して、生データを予測や洞察に変換および処理します。 ストアド プロシージャ アクティビティは、Data Factory でサポートされる変換アクティビティの 1 つです。 この記事は、データ変換と Data Factory でサポートされている変換アクティビティの概要を説明する、[データ変換](transform-data.md)に関する記事に基づいています。

> [!NOTE]
> Azure Data Factory を初めて利用する場合は、この記事を読む前に、「[Azure Data Factory の概要](introduction.md)」を参照してから、[データの変換に関するチュートリアル](tutorial-transform-data-spark-powershell.md)を実行してください。 

ストアド プロシージャ アクティビティを使用して、社内または Azure 仮想マシン (VM) 上の次のいずれかのデータ ストアでストアド プロシージャを呼び出すことができます。 

- Azure SQL データベース
- Azure Synapse Analytics
- SQL Server データベース  SQL Server を使用している場合は、データベースをホストしているコンピューター、またはデータベースにアクセスできる別のコンピューターにセルフホステッド統合ランタイムをインストールします。 セルフホステッド統合ランタイムは、管理された確実な方法でオンプレミスまたは Azure VM 上のデータ ソースをクラウド サービスに接続するコンポーネントです。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。

> [!IMPORTANT]
> Azure SQL Database または SQL Server にデータをコピーする場合、**sqlWriterStoredProcedureName** プロパティを使用してストアド プロシージャを呼び出すように、コピー アクティビティで **SqlSink** を構成できます。 プロパティの詳細については、[Azure SQL Database](connector-azure-sql-database.md)、[SQL Server](connector-sql-server.md) の各コネクタに関する記事をご覧ください。 コピー アクティビティを使用して Azure Synapse Analytics にデータをコピー中に、ストアド プロシージャを呼び出すことはできません。 しかし、Azure Synapse Analytics のストアド プロシージャを呼び出すためにストアド プロシージャのアクティビティを使用することは可能です。 
>
> Azure SQL Database、SQL Server、または Azure Synapse Analytics からデータをコピーする場合、**sqlReaderStoredProcedureName** プロパティを使用して、ソース データベースからデータを読み取るストアド プロシージャを呼び出すように、コピー アクティビティで **SqlSource** を構成できます。 詳細については、[Azure SQL Database](connector-azure-sql-database.md)、[SQL Server](connector-sql-server.md)、[Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)          

 

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
        "storedProcedureName": "usp_sample",
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
| description               | アクティビティの用途を説明するテキストです。 | いいえ       |
| type                      | ストアド プロシージャ アクティビティの場合、アクティビティの種類は **SqlServerStoredProcedure** です | はい      |
| linkedServiceName         | Data Factory のリンクされたサービスとして登録されている **Azure SQL Database**、**Azure Synapse Analytics**、または **SQL Server** への参照。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 | はい      |
| storedProcedureName       | 呼び出すストアド プロシージャの名前を指定します。 | はい      |
| storedProcedureParameters | ストアド プロシージャのパラメーター値を指定します。 パラメーター値と、データ ソースでサポートされるパラメーター値の型を渡すには、`"param1": { "value": "param1Value","type":"param1Type" }` を使います。 パラメーターで null を渡す必要がある場合は、`"param1": { "value": null }` (すべて小文字) を使います。 | いいえ       |

## <a name="parameter-data-type-mapping"></a>パラメーターのデータ型のマッピング
パラメーターに指定するデータ型は、使用しているデータ ソースのデータ型にマップされる Azure Data Factory の型です。 データ ソースのデータ型マッピングは、コネクタ領域で確認できます。 次に例をいくつか示します。

| Data Source          | データ型のマッピング |
| ---------------------|-------------------|
| Azure Synapse Analytics | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL データベース   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |




## <a name="next-steps"></a>次のステップ
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop ストリーミング アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning スタジオ (クラシック) のバッチ実行アクティビティ](transform-data-using-machine-learning.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
