---
title: データセット
description: Data factory のデータセットについて説明します。 データセットは、入力/出力データを表します。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/25/2019
ms.openlocfilehash: 878ad98b118fa02a6659584ac60e3343a948cd20
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928475"
---
# <a name="datasets-in-azure-data-factory"></a>Azure Data Factory のデータセット
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください。"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [現在のバージョン](concepts-datasets-linked-services.md)

この記事では、データセットの詳細、データセットを JSON 形式で定義する方法、Azure Data Factory パイプラインで使用する方法について説明します。

Azure Data Factory を初めて使用する場合は、[Azure Data Factory の概要](introduction.md)に関するページを参照してください。

## <a name="overview"></a>概要
データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 **パイプライン**は、1 つのタスクを連携して実行する**アクティビティ**の論理的なグループです。 パイプライン内の複数のアクティビティは、データに対して実行するアクションを定義します。 ここで、**データセット**とは、**アクティビティ**で入力と出力として使用するデータを単に指定または参照するデータの名前付きビューです。 データセットは、テーブル、ファイル、フォルダー、ドキュメントなど、さまざまなデータ ストア内のデータを示します。 たとえば、Azure Blob データセットは、アクティビティによってデータが読み取られる、Blob Storage 内の BLOB コンテナーと BLOB フォルダーを示しています。

データセットを作成する前に、[**リンクされたサービス**](concepts-linked-services.md)を作成して、データ ストアとデータ ファクトリをリンクする必要があります。 リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 データセットはリンクされたデータ ストア内のデータの構造を表すもので、リンクされたサービスはデータ ソースへの接続を定義するもの、と捉えることができます。 たとえば、Azure Storage のリンクされたサービスは、ストレージ アカウントをデータ ファクトリにリンクします。 Azure Blob データセットは、処理対象の入力 BLOB を含む Azure ストレージ アカウント内の BLOB コンテナーとフォルダーを表します。

シナリオの例を次に示します。 Blob Storage のデータを Azure SQL Database にコピーするために、2 つのリンクされたサービスを作成します Azure Storage、Azure SQL Database)。 次に、2 つのデータセットを作成します。Azure Blob データセット (Azure Storage リンクされたサービスを参照するデータセット) と、Azure SQL Table データセット (Azure SQL Database リンクされたサービスを参照するデータセット) です。 Azure Storage と Azure SQL Database の各リンクされたサービスに含まれる接続文字列を、Data Factory が実行時に使用して、Azure Storage と Azure SQL Database それぞれに接続します。 Azure Blob データセットは、Blob Storage 内の入力 BLOB が含まれた BLOB コンテナーと BLOB フォルダーを示しています。 Azure SQL Table データセットは、データのコピー先である SQL Database 内の SQL テーブルを示しています。

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

プロパティ | 説明 | 必須 |
-------- | ----------- | -------- |
名前 | データセットの名前。 [Azure Data Factory - 名前付け規則](naming-rules.md)を参照してください。 |  はい |
type | データセットの型。 Data Factory でサポートされている型のいずれかを指定します (たとえば、AzureBlob、AzureSqlTable)。 <br/><br/>詳細については、[データセットの型](#dataset-type)を参照してください。 | はい |
structure | データセットのスキーマ。 詳細については、「[データセット スキーマ](#dataset-structure-or-schema)」を参照してください。 | いいえ |
typeProperties | 型のプロパティは型によって異なります (たとえば、Azure Blob、Azure SQL テーブル)。 サポートされている型とそのプロパティの詳細については、「[データセットの型](#dataset-type)」セクションを参照してください。 | はい |

### <a name="data-flow-compatible-dataset"></a>Data Flow の互換性のあるデータセット



[データ フロー](concepts-data-flow-overview.md)が互換性のあるデータセット型のリストについて、「[サポートされているデータセットの型](#dataset-type)」を参照してください。 データ フローの互換性があるデータセットでは、変換にあたり詳細なデータセットの定義が必要です。 そのため、JSON 定義は若干異なります。 _構造体_プロパティの代わりに、データ フローと互換性のあるデータセットには_スキーマ_プロパティがあります。

データ フロー内のデータセットは、ソースとシンクの変換で使用されます。 データセットは、基本的なデータ スキーマを定義します。 データがスキーマを持たない場合は、ソースとシンクのスキーマ ドリフトを使用できます。 データセット内のスキーマは、実際のデータ型と形状を表します。

データセットからスキーマを定義すると、関連するリンクされたサービスから関連するデータの種類、データ形式、ファイルの場所、および接続情報が得られます。 データセットから取得されたメタデータは、ソース変換でソース *プロジェクション*として表示されます。 ソースの変換におけるプロジェクションは、定義された名前と型のデータ フローのデータを表します。

データ フローのデータセットのスキーマをインポートするときに、 **[Import Schema]** (スキーマのインポート) ボタンをクリックし、ソースまたはローカル ファイルからインポートを選択します。 ほとんどの場合、ソースからスキーマを直接インポートします。 ただしローカル スキーマ ファイルが既にある (Parquet　ファイルまたはヘッダー付きの CSV ) 場合は、そのファイルのスキーマを基に、Data Factory を指示できます。


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
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

プロパティ | 説明 | 必須 |
-------- | ----------- | -------- |
名前 | データセットの名前。 [Azure Data Factory - 名前付け規則](naming-rules.md)を参照してください。 |  はい |
type | データセットの型。 Data Factory でサポートされている型のいずれかを指定します (たとえば、AzureBlob、AzureSqlTable)。 <br/><br/>詳細については、[データセットの型](#dataset-type)を参照してください。 | はい |
schema | データセットのスキーマ。 詳細は、「[Data Flow の互換性のあるデータセット](#dataset-type)」を参照してください。 | いいえ |
typeProperties | 型のプロパティは型によって異なります (たとえば、Azure Blob、Azure SQL テーブル)。 サポートされている型とそのプロパティの詳細については、「[データセットの型](#dataset-type)」セクションを参照してください。 | はい |


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

## <a name="dataset-type"></a>データセットの型
使用するデータ ストアによって、さまざまなデータセットの種類があります。 Data Factory によってサポートされているデータ ストアの一覧は、[コネクタの概要](connector-overview.md)に関する記事をご覧ください。 データ ストアをクリックすると、そのデータ ストアに対応するリンクされたサービスとデータセットの作成方法を確認できます。

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

## <a name="dataset-structure-or-schema"></a>データセット構造またはスキーマ
**構造体**セクションまたは**スキーマ** (互換性のあるデータフロー) セクションのデータセットは省略可能です。 このセクションでは、列の名前とデータ型のコレクションを含めることで、データセットのスキーマを定義します。 structure セクションを使用して、変換元から変換先への型の変換や列のマップに使用される型の情報を指定します。

structure の各列には次のプロパティが含まれます。

プロパティ | 説明 | 必須
-------- | ----------- | --------
名前 | 列の名前です。 | はい
type | 列のデータ型です。 Data Factory は次の中間データ型を許容値としてサポートしています。**Int16、Int32、Int64、Single、Double、Decimal、Byte[]、Boolean、String、Guid、Datetime、Datetimeoffset、および Timespan** | いいえ
culture | .NET 型 (`Datetime` または `Datetimeoffset`) の場合に使用される .NET ベースのカルチャ。 既定では、 `en-us`です。 | いいえ
format | .NET 型 (`Datetime` または `Datetimeoffset`) の場合に使用される書式設定文字列。 日時の書式を設定する方法については、「[カスタム日時書式指定文字列](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)」を参照してください。 | いいえ

### <a name="example"></a>例
次の例は、ソース BLOB データが、CSV 形式であり、userid、name、lastlogindate の 3 つの列を含むことを前提としています。 その型は、それぞれ、Int64、String、Datetime (曜日を表すフランス語の省略形を使用するカスタムの日付/時刻形式) です。

BLOB データセットの構造を、列の型の定義と共に次のように定義します。

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>ガイダンス

構造情報を含めるべき状況と、**structure** セクションに含める内容については、次のガイドラインに従ってください。 データ ファクトリがシンクにソース データをマップする方法および構造体の情報を指定する場合の詳細を[スキーマと型マッピング](copy-activity-schema-and-type-mapping.md)に関するページで確認してください。

- **強力なスキーマのデータ ソースの場合は**、ソース列をシンク列とマップする必要があって、その列名が同じでない場合にのみ、"structure" セクションを指定します。 このような構造化データ ソースでは、データ自体と共にデータ スキーマと型情報が格納されています。 構造化データ ソースの例には、SQL Server、Oracle、Azure SQL Database などがあります。<br/><br/>構造化データ ソースでは型情報が既に提供されているため、"structure" セクションを含める場合は、型情報を含めないでください。
- **スキーマのない、またはスキーマが弱いデータ ソース (Blob Storage 内のテキスト ファイルなど) の場合は**、データセットがコピー アクティビティの入力データ セットであるときに "structure" 列を含めます。ソース データセットのデータ型はシンクのネイティブ型に変換する必要があります。 また、ソース列をシンク列にマップする必要がある場合に "structure" 列を含めます。

## <a name="create-datasets"></a>データセットを作成する
データセットの作成には、[.NET API](quickstart-create-data-factory-dot-net.md)、[PowerShell](quickstart-create-data-factory-powershell.md)、[REST API](quickstart-create-data-factory-rest-api.md)、Azure Resource Manager テンプレート、Azure Portal のいずれかのツール、またはいずれかの SDK を使用できます

## <a name="current-version-vs-version-1-datasets"></a>最新バージョンとバージョン 1 のデータセットの比較

Data Factory と Data Factory バージョン 1 のデータセット間の相違点を次に示します。

- 外部プロパティは最新バージョンではサポートされません。 [トリガー](concepts-pipeline-execution-triggers.md)に置き換えらます。
- ポリシーと可用性のプロパティは、最新バージョンではサポートされません。 パイプラインの開始時刻は、[トリガー](concepts-pipeline-execution-triggers.md)によって異なります。
- 範囲指定されたデータセット (パイプラインで定義されたデータセット) は、最新バージョンではサポートされません。

## <a name="next-steps"></a>次の手順
これらのツールや SDK のいずれかを使用してパイプラインとデータセットを作成する詳しい手順については、次のチュートリアルを参照してください。

- [クイック スタート: .NET を使用してデータ ファクトリを作成する](quickstart-create-data-factory-dot-net.md)
- [クイック スタート: PowerShell を使用してデータ ファクトリを作成する](quickstart-create-data-factory-powershell.md)
- [クイック スタート: REST API を使用してデータ ファクトリを作成する](quickstart-create-data-factory-rest-api.md)
- [クイック スタート: Azure portal を使用したデータ ファクトリの作成](quickstart-create-data-factory-portal.md)
