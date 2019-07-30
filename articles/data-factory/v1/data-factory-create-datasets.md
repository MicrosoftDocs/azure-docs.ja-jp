---
title: Azure Data Factory でのデータセットの作成 | Microsoft Docs
description: Azure Data Factory でデータセットを作成する方法について説明し、offset、anchorDateTime などのプロパティを使用する例を紹介します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f88d83a851ad878ac9ee9b0195816d2ca35e4c13
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839373"
---
# <a name="datasets-in-azure-data-factory"></a>Azure Data Factory のデータセット
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください。"]
> * [Version 1](data-factory-create-datasets.md)
> * [バージョン 2 (最新バージョン)](../concepts-datasets-linked-services.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 のデータセット](../concepts-datasets-linked-services.md)に関するページを参照してください。

この記事では、データセットの詳細、データセットを JSON 形式で定義する方法、Azure Data Factory パイプラインで使用する方法について説明します。 また、データセット JSON 定義の各セクション (構造、可用性、ポリシーなど) の詳細について説明します。 さらに、データセット JSON 定義で **offset**、**anchorDateTime**、**style** の各プロパティを使用する例も紹介します。

> [!NOTE]
> Azure Data Factory を初めて使用する場合は、[Azure Data Factory の概要](data-factory-introduction.md)に関するページを参照してください。 データ ファクトリを実際に作成したことがない場合は、[データ変換のチュートリアル](data-factory-build-your-first-pipeline.md)や[データ移動のチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照すると、理解を深めることができます。

## <a name="overview"></a>概要
データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 **パイプライン**は、1 つのタスクを連携して実行する**アクティビティ**の論理的なグループです。 パイプライン内の複数のアクティビティは、データに対して実行するアクションを定義します。 たとえば、コピー アクティビティを使用して、オンプレミスの SQL Server から Azure Blob Storage にデータをコピーすることができます。 その後、Azure HDInsight クラスターで Hive スクリプトを実行する Hive アクティビティを使用して、Blob Storage のデータを処理し、出力データを生成できます。 最後に、別のコピー アクティビティを使用して、ビジネス インテリジェンス (BI) レポート ソリューションが構築されている Azure SQL Data Warehouse に出力データをコピーできます。 パイプラインとアクティビティの詳細については、「[Azure Data Factory のパイプラインとアクティビティ](data-factory-create-pipelines.md)」を参照してください。

アクティビティは 0 個以上の入力**データセット**を受け取り、1 個以上の出力データセットを生成できます。 入力データセットはパイプライン内のアクティビティに対する入力を表し、出力データセットはアクティビティの出力を表します。 データセットは、テーブル、ファイル、フォルダー、ドキュメントなど、さまざまなデータ ストア内のデータを示します。 たとえば、Azure Blob データセットは、パイプラインによってデータが読み取られる、Blob Storage 内の BLOB コンテナーと BLOB フォルダーを示しています。

データセットを作成するには、まず、**リンクされたサービス**を作成して、データ ストアとデータ ファクトリをリンクする必要があります。 リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 データセットは、SQL テーブル、ファイル、フォルダー、ドキュメントなど、リンクされたさまざまなデータ ストア内のデータを示します。 たとえば、Azure Storage のリンクされたサービスは、ストレージ アカウントをデータ ファクトリにリンクします。 Azure Blob データセットは、処理対象の入力 BLOB を含む BLOB コンテナーとフォルダーを表します。

シナリオの例を次に示します。 Blob Storage のデータを Azure SQL Database にコピーするために、2 つのリンクされたサービスを作成します Azure Storage、Azure SQL Database)。 次に、2 つのデータセットを作成します。Azure Blob データセット (Azure Storage リンクされたサービスを参照するデータセット) と、Azure SQL Table データセット (Azure SQL Database リンクされたサービスを参照するデータセット) です。 Azure Storage と Azure SQL Database の各リンクされたサービスに含まれる接続文字列を、Data Factory が実行時に使用して、Azure Storage と Azure SQL Database それぞれに接続します。 Azure Blob データセットは、Blob Storage 内の入力 BLOB が含まれた BLOB コンテナーと BLOB フォルダーを示しています。 Azure SQL Table データセットは、データのコピー先である SQL Database 内の SQL テーブルを示しています。

次の図は、Data Factory でのパイプライン、アクティビティ、データセット、リンクされたサービスの関係を示しています。

![パイプライン、アクティビティ、データセット、リンクされたサービスの関係](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>データセットの JSON
Data Factory のデータセットは JSON 形式では次のように定義されます。

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

次の表では、上記の JSON のプロパティについて説明します。

| プロパティ | 説明 | 必須 | 既定値 |
| --- | --- | --- | --- |
| 名前 |データセットの名前。 名前付け規則については、「 [Azure Data Factory - 名前付け規則](data-factory-naming-rules.md) 」を参照してください。 |はい |NA |
| type |データセットの型。 Data Factory でサポートされている型のいずれかを指定します (たとえば、AzureBlob、AzureSqlTable)。 <br/><br/>詳細については、「[データセットの型](#Type)」セクションを参照してください。 |はい |NA |
| structure |データセットのスキーマ。<br/><br/>詳細については、「[データセット構造](#Structure)」セクションを参照してください。 |いいえ |NA |
| typeProperties | 型のプロパティは型によって異なります (たとえば、Azure Blob、Azure SQL テーブル)。 サポートされている型とそのプロパティの詳細については、「[データセットの型](#Type)」セクションを参照してください。 |はい |NA |
| external | データセットをデータ ファクトリ パイプラインによって明示的に生成するかどうかを指定するブール型のフラグ。 アクティビティの入力データセットが現在のパイプラインによって生成されない場合は、このフラグを true に設定します。 パイプラインの最初のアクティビティの入力データセットについてはこのフラグを true に設定します。  |いいえ |false |
| availability | データセット生成の処理時間枠 (例: 時間単位、日単位) またはスライシング モデルを定義します。 アクティビティ実行で使用および生成されるデータの各ユニットは、データ スライスと呼ばれます。 出力データセットの可用性が日単位 (frequency - Day、interval - 1) に設定された場合、スライスは毎日生成されます。 <br/><br/>詳細については、「データセットの可用性」を参照してください。 <br/><br/>データセットのスライシング モデルの詳細については、[スケジュール設定と実行](data-factory-scheduling-and-execution.md)に関する記事を参照してください。 |はい |NA |
| policy |データセット スライスで満たさなければならない基準または条件を定義します。 <br/><br/>詳細については、「[データセット ポリシー](#Policy)」セクションを参照してください。 |いいえ |NA |

## <a name="dataset-example"></a>データセットの例
以下の例では、データセットは SQL Database 内にある **MyTable** という名前のテーブルを表しています。

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

以下の点に注意してください。

* **type** が AzureSqlTable に設定されています。
* (AzureSqlTable 型に固有の) 型プロパティ **tableName** が MyTable に設定されています。
* **linkedServiceName** は、型が AzureSqlDatabase であるリンクされたサービスを参照します。これは、次の JSON スニペットで定義されています。
* **availability の frequency** は Day に、**interval** は 1 に設定されています。 これは、データセット スライスが毎日生成されることを意味します。

**AzureSqlLinkedService** は次のように定義されています。

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

* **type** が AzureSqlDatabase に設定されています。
* 型プロパティ **connectionString** は、SQL Database に接続するための情報を示しています。

ご覧のように、リンクされたサービスによって、SQL Database に接続する方法が定義されています。 また、データセットは、どのテーブルがパイプライン内のアクティビティの入力/出力として使用されるかを定義しています。

> [!IMPORTANT]
> データセットはパイプラインで作成される場合を除き、**external** とマークする必要があります。 この設定は通常、パイプライン内の最初のアクティビティの入力に適用されます。

## <a name="Type"></a> データセットの型
データセットの型は、使用するデータ ストアに依存します。 Data Factory でサポートされているデータ ストアの一覧については、次の表を参照してください。 データ ストアをクリックすると、そのデータ ストアに対応するリンクされたサービスとデータセットの作成方法を確認できます。

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> \* が付いているデータ ストアは、オンプレミスと Azure IaaS (サービスとしてのインフラストラクチャ) のどちらにある場合もサポートされています。 これらのデータ ストアでは、[Data Management Gateway](data-factory-data-management-gateway.md) のインストールが必要になります。

前のセクションの例では、データセットの型は **AzureSqlTable** に設定されています。 同様に、Azure Blob データセットの場合は、次の JSON に示すように、データセットの型が **AzureBlob** に設定されます。

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

## <a name="Structure"></a>データセット構造
**structure** セクションは省略できます。 このセクションでは、列の名前とデータ型のコレクションを含めることで、データセットのスキーマを定義します。 structure セクションを使用して、変換元から変換先への型の変換や列のマップに使用される型の情報を指定します。 次の例では、データセットに `slicetimestamp`、`projectname`、`pageviews` の 3 つの列があります。 それぞれの列の型は、String、String、Decimal です。

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

structure の各列には次のプロパティが含まれます。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| 名前 |列の名前です。 |はい |
| type |列のデータ型です。  |いいえ |
| culture |.NET 型 (`Datetime` または `Datetimeoffset`) の場合に使用される .NET ベースのカルチャ。 既定では、 `en-us`です。 |いいえ |
| format |.NET 型 (`Datetime` または `Datetimeoffset`) の場合に使用される書式設定文字列。 |いいえ |

構造情報を含めるべき状況と、**structure** セクションに含める内容については、次のガイドラインに従ってください。

* **構造化データ ソース**の場合は、ソース列をシンク列とマップする必要があって、その列名が同じでない場合にのみ、"structure" セクションを指定します。 このような構造化データ ソースでは、データ自体と共にデータ スキーマと型情報が格納されています。 構造化データ ソースの例には、SQL Server、Oracle、Azure テーブルなどがあります。
  
    構造化データ ソースでは型情報が既に提供されているため、"structure" セクションを含める場合は、型情報を含めないでください。
* **読み取りデータ ソースのスキーマの場合 (具体的には Blob Storage)** 、データと共にスキーマや型情報を格納せずに、データを格納することができます。 このようなデータ ソースでは、ソース列をシンク列にマップする必要がある場合に "structure" 列を含めます。 また、データセットがコピー アクティビティの入力データセットである場合にも、"structure" 列を含めます。ソース データセットのデータ型はシンクのネイティブ型に変換する必要があります。
    
    Data Factory は、構造体の型情報を提供するため、値 **Int16、Int32、Int64、Single、Double、Decimal、Byte[]、Boolean、String、Guid、Datetime、Datetimeoffset、および Timespan** をサポートしています。 これらの値は、共通言語仕様 (CLS) に準拠している .NET ベースの型値です。

また、ソース データ ストアのデータをシンク データ ストアにデータを移動するときに、型変換を自動的に実行します。

## <a name="dataset-availability"></a>データセットの可用性
データセットの **availability** セクションでは、データセットの処理時間枠 (例: 時間単位、日単位、週単位) を定義します。 アクティビティ ウィンドウの詳細については、[スケジュールと実行](data-factory-scheduling-and-execution.md)に関するページを参照してください。

次の availability セクションは、出力データセットが 1 時間ごとに生成されるか、入力データセットが 1 時間ごとに使用可能となるように指定しています。

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

パイプラインの開始時刻と終了時刻が次のように設定されているとします。

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

出力データセットは、パイプラインの開始時刻から終了時刻までに 1 時間ごとに生成されます。 そのため、このパイプラインでは、各アクティビティ ウィンドウ (12 AM - 1 AM、1 AM - 2AM、2 AM - 3 AM、3 AM - 4 AM、4 AM - 5 AM) に 1 つずつ、合計 5 個のデータセット スライスが生成されます。

次の表では、availability セクションで使用できるプロパティについて説明します。

| プロパティ | 説明 | 必須 | 既定値 |
| --- | --- | --- | --- |
| frequency |データセット スライス生成の時間単位を指定します。<br/><br/><b>サポートされる frequency</b>: Minute、Hour、Day、Week、Month |はい |NA |
| interval |頻度の乗数を指定します。<br/><br/>"frequency x interval" で、スライスが生成される頻度が決まります。 たとえば、データセットを時間単位でスライスする必要がある場合は、<b>frequency</b> を <b>Hour</b> に設定し、<b>interval</b> を <b>1</b> に設定します。<br/><br/>注: **frequency** に **Minute** を指定する場合は、interval を 15 以上に設定してください。 |はい |NA |
| style |スライスを間隔の始めと終わりのどちらで生成するかを指定します。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>**frequency** を **Month** に設定し、**style** を **EndOfInterval** に設定すると、スライスは月の最終日に生成されます。 **style** が **StartOfInterval** に設定されていると、スライスは月の最初の日に生成されます。<br/><br/>**frequency** を **Day** に設定し、**style** を **EndOfInterval** に設定すると、スライスは 1 日の最後の 1 時間に生成されます。<br/><br/>**frequency** を **Hour** に設定し、**style** を **EndOfInterval** に設定すると、スライスは時間の終わりに生成されます。 たとえば、午後 1 時 ～ 午後 2 時のスライスの場合、午後 2 時にスライスが生成されます。 |いいえ |EndOfInterval |
| anchorDateTime |データセット スライスの境界を計算するためにスケジューラによって使用される時間の絶対位置を定義します。 <br/><br/>注: 指定された頻度より細かい日付部分がこのプロパティに含まれている場合、その部分は無視されます。 たとえば、**間隔**が**時間単位** (frequency: Hour、interval:1) で、**anchorDateTime** に**分と秒**が含まれる場合、**anchorDateTime** の分と秒部分は無視されます。 |いいえ |01/01/0001 |
| offset |すべてのデータセット スライスの開始と終了がシフトされる時間帯です。 <br/><br/>注: **anchorDateTime** と **offset** の両方が指定されている場合、結果的にシフトが結合されます。 |いいえ |NA |

### <a name="offset-example"></a>offset の例
既定では、毎日 (`"frequency": "Day", "interval": 1`) のスライスは協定世界時 (UTC) 12 AM (午前 0 時) に開始します。 開始時刻を 6 AM UTC にするには、次のスニペットに示すようにオフセットを設定します。

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime の例
次の例では、データセットは 23 時間ごとに 1 回生成されます。 最初のスライスは **anchorDateTime** で指定された時刻に開始します (`2017-04-19T08:00:00` (UTC) に設定されています)。

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>offset/style の例
次のデータセットは月単位です。毎月 3 日の午前 8 時 (`3.08:00:00`) に生成されます。

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>データセット ポリシー
データセット定義の **policy** セクションでは、データセット スライスで満たさなければならない基準または条件を定義します。

### <a name="validation-policies"></a>検証ポリシー
| ポリシー名 | 説明 | 適用先 | 必須 | 既定値 |
| --- | --- | --- | --- | --- |
| minimumSizeMB |**Azure Blob Storage** のデータが最小サイズ要件 (MB 単位) を満たしていることを検証します。 |Azure BLOB ストレージ |いいえ |NA |
| minimumRows |**Azure SQL データベース**または **Azure テーブル**のデータに最小行数が含まれていることを検証します。 |<ul><li>Azure SQL データベース</li><li>Azure テーブル</li></ul> |いいえ |NA |

#### <a name="examples"></a>例
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>外部データセット
外部データセットはデータ ファクトリのパイプライン実行で生成されないデータセットです。 データセットが **external** としてマークされている場合は、**ExternalData** ポリシーを定義することで、データセット スライス可用性の動作を変更できます。

データセットは Data Factory で作成されている場合を除き、**external** とマークされます。 この設定は通常、パイプライン内の最初のアクティビティの入力に適用されます (アクティビティまたはパイプラインの連鎖が使用されている場合を除く)。

| EnableAdfsAuthentication | 説明 | 必須 | Default value |
| --- | --- | --- | --- |
| dataDelay |特定のスライスの外部データの可用性チェックを遅らせる時間。 たとえば、この設定を使用して、時間単位のチェックを延期することができます。<br/><br/>この設定は、現在の時刻にのみ適用されます。 たとえば、現在時刻が午後 1 時 00 分で、この値が 10 分の場合、検証は午後 1 時 10 分に開始されます。<br/><br/>注: この設定は、過去のスライスには影響しません。 **スライス終了時間** + **dataDelay** < **現在時刻**であるスライスは、遅延なく処理されます。<br/><br/>23 時間 59 分を超える時間は、`day.hours:minutes:seconds` 形式で指定してください。 たとえば、24 時間を指定する場合は、24:00:00 を使用するのではなく、 1\.00:00:00 を使用してください。 24:00:00 を使用した場合は、24 日間 (24.00:00:00) として処理されます。 1 日と 4 時間の場合は 1:04:00:00 と指定します。 |いいえ |0 |
| retryInterval |エラーから次の試行までの待機時間です。 この設定は、現在の時刻に適用されます。 前の試行が失敗した場合に、次に試行できるのは **retryInterval** が経過した後です。 <br/><br/>現在時刻が午後 1 時 00 分の場合に最初の試行を開始したとします。 最初の検証チェックを完了するための時間が 1 分のとき、操作に失敗した場合、次の再試行は "午後 1 時 00 分 + 1 分 (チェック時間) + 1 分 (再試行間隔) = 午後 1 時 02 分" になります。 <br/><br/>過去のスライスの場合、遅延はありません。 再試行は直ちに行われます。 |いいえ |00:01:00 (1 分) |
| retryTimeout |各再試行のタイムアウト。<br/><br/>このプロパティが 10 分に設定されている場合は、検証を 10 分以内に完了してください。 検証に 10 分より長い時間がかかった場合、再試行がタイムアウトします。<br/><br/>検証のすべての試行がタイムアウトになった場合、スライスに **TimedOut** のマークが付きます。 |いいえ |00:10:00 (10 分) |
| maximumRetry |外部データの可用性の確認回数です。 許可される最大値は 10 です。 |いいえ |3 |


## <a name="create-datasets"></a>データセットを作成する
次のツールや SDK のいずれかを使用してデータセットを作成できます。

- コピー ウィザード
- Visual Studio
- PowerShell
- Azure Resource Manager テンプレート
- REST API
- .NET API

これらのツールや SDK のいずれかを使用してパイプラインとデータセットを作成する詳しい手順については、次のチュートリアルを参照してください。

- [データ変換アクティビティを含むパイプラインの作成](data-factory-build-your-first-pipeline.md)
- [データ移動アクティビティを含むパイプラインの作成](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

パイプラインを作成してデプロイしたら、Azure Portal のブレードまたは監視と管理アプリを使用して、パイプラインを管理および監視できます。 詳しい手順については、次のトピックを参照してください。

- [Azure Portal のブレードを使用したパイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)
- [監視と管理アプリを使用したパイプラインの監視と管理](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>範囲指定されたデータセット
**datasets** プロパティを使用すると、対象となるパイプラインを指定したデータセットを作成できます。 これらのデータセットは、そのパイプライン内のアクティビティでのみ使用できます。他のパイプラインのアクティビティでは使用できません。 次の例では、パイプラインと、そのパイプライン内で使用する 2 つのデータセット (InputDataset-rdc と OutputDataset-rdc) が定義されています。

> [!IMPORTANT]
> 範囲指定されたデータセットは、1 回限りのパイプライン (**pipelineMode** が **OneTime** に設定されたパイプライン) でのみサポートされます。 詳細については、「 [1 回限りのパイプライン](data-factory-create-pipelines.md#onetime-pipeline) 」を参照してください。
>
>

```json
{
    "name": "CopyPipeline-rdc",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>次の手順
- パイプラインの詳細については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。
- パイプラインのスケジュール設定と実行の方法については、[Azure Data Factory でのスケジュール設定と実行](data-factory-scheduling-and-execution.md)に関する記事を参照してください。
