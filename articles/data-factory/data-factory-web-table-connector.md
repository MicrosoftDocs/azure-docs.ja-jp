---
title: "Azure Data Factory を使用した Web テーブルからのデータ移動 | Microsoft Docs"
description: "Azure Data Factory を使用して Web ページのテーブルからデータを移動する方法を説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 08298608c2dfbe8b7226ae13bb0fe0f9cd5cec65
ms.lasthandoff: 03/29/2017


---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Azure Data Factory を使用して Web テーブル ソースからデータを移動する
この記事では、Azure Data Factory のコピー アクティビティを使用して、Web ページのテーブルからサポートされたシンク データ ストアにデータを移動する方法について説明します。 この記事では、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要と、ソース/シンクとしてサポートされるデータ ストアの一覧を紹介します。

現在のところ、データ ファクトリは Web テーブルから他のデータ ストアへのデータの移動のみに対応しており、他のデータ ストアから Web テーブルにデータを移動することはできません。

> [!NOTE]
> この Web コネクタは、現在、HTML ページからのテーブル コンテンツの抽出のみをサポートしています。 HTTP エンドポイントからデータを取得するには、代わりに [HTTP コネクタ](data-factory-http-connector.md)を使用します。

## <a name="getting-started"></a>使用の開始
さまざまなツールまたは API を使用して、オンプレミスの Cassandra データ ストアからデータを移動するコピー アクティビティでパイプラインを作成できます。 

- パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」をご覧ください。 
- 次のツールを使ってパイプラインを作成することもできます。**Azure Portal**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、**.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。 

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。 
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。  Web テーブルからデータをコピーするために使用する Data Factory エンティティに関する JSON 定義のサンプルについては、この記事のセクション、「[JSON の使用例: Web テーブルから Azure BLOB へのデータのコピー](#json-example-copy-data-from-web-table-to-azure-blob)」をご覧ください。 

次のセクションでは、Web テーブルに固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、Web のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティを **Web** |はい |
| Url |Web ソースへの URL |はい |
| authenticationType |Anonymous |はい |

### <a name="using-anonymous-authentication"></a>匿名認証を使用する

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **WebTable** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |データセットの型。 **データセット** |はい |
| path |テーブルを含むリソースの相対 URL。 |いいえ。 パスが指定されていないとき、リンクされたサービス定義に指定されている URL のみだけが使用されます。 |
| Index |リソースのテーブルのインデックス。 HTML ページのテーブルのインデックスを取得する方法については、「 [HTML ページのテーブルのインデックスを取得する](#get-index-of-a-table-in-an-html-page) 」を参照してください。 |はい |

**例:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

現時点では、ソースが **WebSource**型のコピー アクティビティの場合、追加プロパティはサポートされません。


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON の使用例: Web テーブルから Azure BLOB へのデータのコピー
次のサンプルは以下を示しています。

1. [Web](#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [WebTable](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [WebSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプル データでは、1 時間おきに Web テーブルから Azure BLOB にデータがコピーされます。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

次のサンプルは、Web テーブルから Azure BLOB にデータをコピーする方法を示します。 ただし、Azure Data Factory のコピー アクティビティを使用して、 [データ移動アクティビティ](data-factory-data-movement-activities.md) に関するトピックで説明されているシンクのいずれかにデータを直接コピーすることができます。

**Web のリンクされたサービス** : この例では、Web サービスにリンクされたサービスと匿名認証が使用されています。 使用可能なさまざまな種類の認証については、 [Web のリンクされたサービス](#linked-service-properties) に関するセクションを参照してください。

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Azure ストレージのリンクされたサービス**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**WebTable 入力データセット** **external** を **true** に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

> [!NOTE]
> HTML ページのテーブルのインデックスを取得する方法については、「 [HTML ページのテーブルのインデックスを取得する](#get-index-of-a-table-in-an-html-page) 」を参照してください。  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Azure BLOB の出力データセット**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**コピー アクティビティのあるパイプライン**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **WebSource** に設定され、**sink** 型が **BlobSink** に設定されています。

WebSource でサポートされるプロパティの一覧については、 [WebSource type プロパティに関するセクション](#copy-activity-type-properties) を参照してください。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>HTML ページのテーブルのインデックスを取得する
1. **Excel 2016** を起動し、**[データ]** タブに切り替えます。  
2. ツール バーの **[新しいクエリ]** をクリックし、**[その他のソースから]** をポイントし、**[Web から]** をクリックします。

    ![Power Query メニュー](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. **[Web から]** ダイアログ ボックスで、リンクされたサービスの JSON で使用する **URL** を入力し (例: https://en.wikipedia.org/wiki/)、データセットに指定するパスを入力し (例: AFI%27s_100_Years...100_Movies)、**[OK]** をクリックします。

    ![Web ダイアログから](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    この例で使用される URL: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. **[Web コンテンツへのアクセス]** ダイアログ ボックスが表示された場合、適切な **URL** と**認証**を選択し、**[接続]** をクリックします。

   ![[Access Web コンテンツ] ダイアログ ボックス](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. ツリー ビューの**テーブル** アイテムをクリックしてテーブルのコンテンツを表示し、一番下にある **[編集]** をクリックします。  

   ![[ナビゲーター] ダイアログ](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. **[クエリ エディター]** ウィンドウで、ツール バーの **[詳細エディター]** をクリックします。

    ![[詳細エディター] ボタン](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. [詳細エディター] ダイアログ ボックスで、"Source" の横にある数字はインデックスです。

    ![詳細エディター - インデックス](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Excel 2013 を使用している場合、 [Microsoft Power Query for Excel](https://www.microsoft.com/download/details.aspx?id=39379) を使用してインデックスを取得します。 詳細については、「 [Web ページに接続する](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) 」を参照してください。 [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/)の使用時と手順は同じです。

> [!NOTE]
> ソース データセット列からシンク データセット列へのマッピングの詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

