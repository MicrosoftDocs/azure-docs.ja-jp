---
title: "Azure Data Factory のデータセットとリンクされたサービス | Microsoft Docs"
description: "Data Factory のデータセットとリンクされたサービスについて説明します。 リンクされたサービスは、計算/データ ストアをデータ ファクトリにリンクします。 データセットは、入力/出力データを表します。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: 
ms.date: 09/05/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f03c91b7b27a4fb39b996599efd11242a785b2b2
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Azure Data Factory のデータセットとリンクされたサービス 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-create-datasets.md)
> * [バージョン 2 - プレビュー](concepts-datasets-linked-services.md)

この記事では、データセットの詳細、データセットを JSON 形式で定義する方法、Azure Data Factory V2 パイプラインで使用する方法について説明します。 

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory V1 のデータセット](v1/data-factory-create-datasets.md)を参照してください。

Azure Data Factory を初めて使用する場合は、[Azure Data Factory の概要](introduction.md)に関するページを参照してください。 

## <a name="overview"></a>概要
データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 **パイプライン**は、1 つのタスクを連携して実行する**アクティビティ**の論理的なグループです。 パイプライン内の複数のアクティビティは、データに対して実行するアクションを定義します。 たとえば、コピー アクティビティを使用して、オンプレミスの SQL Server から Azure Blob Storage にデータをコピーすることができます。 その後、Azure HDInsight クラスターで Hive スクリプトを実行する Hive アクティビティを使用して、Blob Storage のデータを処理し、出力データを生成できます。 最後に、別のコピー アクティビティを使用して、ビジネス インテリジェンス (BI) レポート ソリューションが構築されている Azure SQL Data Warehouse に出力データをコピーできます。 パイプラインとアクティビティの詳細については、Azure Data Factory の[パイプラインとアクティビティ](concepts-pipelines-activities.md)を参照してください。

ここで、**データセット**とは、**アクティビティ**で入力と出力として使用するデータを単に指定または参照するデータの名前付きビューです。 データセットは、テーブル、ファイル、フォルダー、ドキュメントなど、さまざまなデータ ストア内のデータを示します。 たとえば、Azure Blob データセットは、アクティビティによってデータが読み取られる、Blob Storage 内の BLOB コンテナーと BLOB フォルダーを示しています。

データセットを作成する前に、**リンクされたサービス**を作成して、データ ストアとデータ ファクトリをリンクする必要があります。 リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 データセットはリンクされたデータ ストア内のデータの構造を表すもので、リンクされたサービスはデータ ソースへの接続を定義するもの、と捉えることができます。 たとえば、Azure Storage のリンクされたサービスは、ストレージ アカウントをデータ ファクトリにリンクします。 Azure Blob データセットは、処理対象の入力 BLOB を含む Azure ストレージ アカウント内の BLOB コンテナーとフォルダーを表します。

シナリオの例を次に示します。 Blob Storage のデータを Azure SQL Database にコピーするために、Azure Storage と Azure SQL Database という 2 つのリンクされたサービスを作成します。 次に、2 つのデータセットを作成します。Azure Blob データセット (Azure Storage リンクされたサービスを参照するデータセット) と、Azure SQL Table データセット (Azure SQL Database リンクされたサービスを参照するデータセット) です。 Azure Storage と Azure SQL Database の各リンクされたサービスに含まれる接続文字列を、Data Factory が実行時に使用して、Azure Storage と Azure SQL Database それぞれに接続します。 Azure Blob データセットは、Blob Storage 内の入力 BLOB が含まれた BLOB コンテナーと BLOB フォルダーを示しています。 Azure SQL Table データセットは、データのコピー先である SQL Database 内の SQL テーブルを示しています。

次の図は、Data Factory でのパイプライン、アクティビティ、データセット、リンクされたサービスの関係を示しています。

![パイプライン、アクティビティ、データセット、リンクされたサービスの関係](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>データセットの JSON
Data Factory のデータセットは JSON 形式では次のように定義されます。

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                 "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}

```
次の表では、上記の JSON のプロパティについて説明します。

プロパティ | 説明 | 必須 | 既定値
-------- | ----------- | -------- | -------
name | データセットの名前。 | [Azure Data Factory - 名前付け規則](naming-rules.md)を参照してください。 | あり | 該当なし
type | データセットの型。 | Data Factory でサポートされている型のいずれかを指定します (例: AzureBlob、AzureSqlTable)。 <br/><br/>詳細については、[データセットの型](#dataset-types)を参照してください。 | あり | 該当なし
structure | データセットのスキーマ。 | 詳細については、「[データセット構造](#dataset-structure)」セクションを参照してください。 | いいえ | 該当なし
typeProperties | typeProperties は型 (Azure Blob、Azure SQL テーブルなど) によって異なります。 サポートされている型とそのプロパティの詳細については、「[データセットの型](#dataset-type)」セクションを参照してください。 | はい | 該当なし

## <a name="dataset-example"></a>データセットの例
以下の例では、データセットは SQL Database 内にある MyTable という名前のテーブルを表しています。

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                 "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
以下の点に注意してください。

- type が AzureSqlTable に設定されています。
- (AzureSqlTable 型に固有の) 型プロパティ tableName が MyTable に設定されています。
- linkedServiceName は、型が AzureSqlDatabase であるリンクされたサービスを参照します。これは、次の JSON スニペットで定義されています。

## <a name="linked-service-example"></a>リンクされたサービスの例
AzureSqlLinkedService は次のように定義されます。

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```
上記の JSON スニペットは、次のようになっています。

- **type** が AzureSqlDatabase に設定されています。
- 型プロパティ **connectionString** は、SQL Database に接続するための情報を示しています。

ご覧のように、リンクされたサービスによって、SQL Database に接続する方法が定義されています。 また、データセットは、どのテーブルがパイプライン内のアクティビティの入力/出力として使用されるかを定義しています。

## <a name="dataset-type"></a>データセットの型
使用するデータ ストアによって、さまざまなデータセットの種類があります。 Data Factory でサポートされているデータ ストアの一覧については、次の表を参照してください。 データ ストアをクリックすると、そのデータ ストアに対応するリンクされたサービスとデータセットの作成方法を確認できます。

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

前のセクションの例では、データセットの型は **AzureSqlTable** に設定されています。 同様に、Azure Blob データセットの場合は、次の JSON に示すように、データセットの型が **AzureBlob** に設定されます。

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                 "type": "LinkedServiceReference",
        }, 
 
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```
## <a name="dataset-structure"></a>データセット構造
**structure** セクションは省略できます。 このセクションでは、列の名前とデータ型のコレクションを含めることで、データセットのスキーマを定義します。 structure セクションを使用して、変換元から変換先への型の変換や列のマップに使用される型の情報を指定します。 次の例では、データセットに timestamp、projectname、pageviews の 3 つの列があります。 それぞれの列の型は、String、String、Decimal です。

```json
[
    { "name": "timestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

structure の各列には次のプロパティが含まれます。

プロパティ | 説明 | 必須
-------- | ----------- | --------
name | 列の名前です。 | あり
type | 列のデータ型です。 | いいえ
culture | .NET 型 (`Datetime` または `Datetimeoffset`) の場合に使用される .NET ベースのカルチャ。 既定では、 `en-us`です。 | なし
BlobSink の format | .NET 型 (`Datetime` または `Datetimeoffset`) の場合に使用される書式設定文字列。 | いいえ

構造情報を含めるべき状況と、**structure** セクションに含める内容については、次のガイドラインに従ってください。

- **構造化データ ソース**の場合は、ソース列をシンク列とマップする必要があって、その列名が同じでない場合にのみ、"structure" セクションを指定します。 このような構造化データ ソースでは、データ自体と共にデータ スキーマと型情報が格納されています。 構造化データ ソースの例には、SQL Server、Oracle、Azure SQL Database などがあります。<br/><br/>構造化データ ソースでは型情報が既に提供されているため、"structure" セクションを含める場合は、型情報を含めないでください。
- **読み取りデータ ソースのスキーマの場合 (具体的には Blob Storage)**、データと共にスキーマや型情報を格納せずに、データを格納することができます。 このようなデータ ソースでは、ソース列をシンク列にマップする必要がある場合に "structure" 列を含めます。 また、データセットがコピー アクティビティの入力データセットである場合にも、"structure" 列を含めます。ソース データセットのデータ型はシンクのネイティブ型に変換する必要があります。<br/><br/> Data Factory は、構造体の型情報を提供するため、値 `Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset, and Timespan` をサポートしています。 

データ ファクトリが[スキーマと型マッピング]( copy-activity-schema-and-type-mapping.md)からシンクにソース データをマップする方法および構造体の情報を指定する場合の詳細を確認してください。

## <a name="create-datasets"></a>データセットを作成する
ツール [.NET API](quickstart-create-data-factory-dot-net.md)、[PowerShell]((quickstart-create-data-factory-powershell.md)、[REST API](quickstart-create-data-factory-rest-api.md)、Azure Resource Manager テンプレート、Azure ポータルのいずれかまたは SDK を使用してデータセットを作成することができます。

## <a name="v1-vs-v2-datasets"></a>V1 と V2 データセット

Data Factory v1 と v2 のデータセット間の相違点を次に示します。 

- 外部プロパティは v2 ではサポートされません。 [トリガー](concepts-pipeline-execution-triggers.md)に置き換えらます。
- ポリシーと可用性のプロパティは、V2 ではサポートされません。 パイプラインの開始時刻は、[トリガー](concepts-pipeline-execution-triggers.md)によって異なります。
- 範囲指定されたデータセット (パイプラインで定義されたデータセット) は、V2 ではサポートされません。 

## <a name="next-steps"></a>次のステップ
これらのツールや SDK のいずれかを使用してパイプラインとデータセットを作成する詳しい手順については、次のチュートリアルを参照してください。 

- [クイック スタート: .NET を使用してデータ ファクトリを作成する](quickstart-create-data-factory-dot-net.md)
- [クイック スタート: PowerShell を使用してデータ ファクトリを作成する](quickstart-create-data-factory-powershell.md)
- [クイック スタート: REST API を使用してデータ ファクトリを作成する](quickstart-create-data-factory-rest-api.md)
- クイック スタート: Azure Portal を使用してデータ ファクトリを作成する

