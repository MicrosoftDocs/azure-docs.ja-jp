---
title: HTTP ソースからデータを移動する - Azure
description: Azure Data Factory を使用してオンプレミスまたはクラウドの HTTP ソースからデータを移動する方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e668f44bbc3d2e381edeb80c568a41355584a4ee
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924174"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Azure Data Factory を使用して HTTP ソースからデータを移動する

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-http-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-http.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Azure Data Factory サービスを使用している場合は、[V2 の HTTP コネクタ](../connector-http.md)に関するページを参照してください。


この記事では、Azure Data Factory のコピー アクティビティを使用して、オンプレミスまたはクラウドの HTTP エンドポイントからサポートされるシンク データ ストアにデータを移動する方法の概要について説明します。 この記事は、コピー アクティビティを使用したデータ移動の一般的な概要について説明している「[コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md)」に基づいて作成されています。 この記事ではまた、コピー アクティビティでソースとシンクとしてサポートされるデータ ストアの一覧も示しています。

Data Factory は現在、HTTP ソースから他のデータ ストアのへのデータの移動のみをサポートしています。 他のデータ ストアから HTTP ターゲットへのデータの移動はサポートされていません。

## <a name="supported-scenarios-and-authentication-types"></a>サポートされているシナリオと認証の種類

この HTTP コネクタを使用すると、HTTP **GET** または **POST** メソッドを使用して、*クラウドとオンプレミスの両方の HTTP/S エンドポイント*からデータを取得できます。 次の認証の種類がサポートされています。**Anonymous**、**Basic**、**Digest**、**Windows**、および **ClientCertificate**。 このコネクタと [Web テーブル コネクタ](data-factory-web-table-connector.md)の違いに注意してください。 Web テーブル コネクタは、HTML Web ページからテーブルの内容を抽出します。

オンプレミスの HTTP エンドポイントからデータをコピーする場合は、オンプレミスの環境または Azure VM に Data Management Gateway をインストールする必要があります。 Data Management Gateway の詳細、およびゲートウェイを設定する方法に関する詳細な手順については、[オンプレミスの場所とクラウドの間のデータの移動](data-factory-move-data-between-onprem-and-cloud.md)に関するページを参照してください。

## <a name="get-started"></a>作業開始

さまざまなツールまたは API を使用して、HTTP ソースからデータを移動するコピー アクティビティを含むパイプラインを作成できます。

- パイプラインを作成するための最も簡単な方法は、データのコピー ウィザードの使用です。 コピー ウィザードを使用してパイプラインを作成する簡単な手順については、「[チュートリアル:コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。

- また、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API** などのツールを使用してパイプラインを作成することもできます。 コピー アクティビティを含むパイプラインを作成する方法に関する詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。 HTTP ソースから Azure Blob Storage にデータをコピーする JSON サンプルについては、「[JSON の使用例](#json-examples)」を参照してください。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

次の表は、HTTP のリンクされたサービスに固有の JSON 要素について説明しています。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type | **type** プロパティは **Http** に設定する必要があります。 | はい |
| url | Web サーバーへのベース URL | はい |
| authenticationType | 認証の種類を指定します。 使用できる値は、**Anonymous**、**Basic**、**Digest**、**Windows**、**ClientCertificate** です。 <br><br> これらの認証の種類のその他のプロパティや JSON サンプルについては、この記事の後の方のセクションを参照してください。 | はい |
| enableServerCertificateValidation | ソースが HTTPS Web サーバーである場合に、サーバーの SSL 証明書の検証を有効にするかどうかを指定します。 HTTPS サーバーで自己署名証明書を使用している場合は、これを **false** に設定します。 | いいえ<br /> (既定値は **true** です)。 |
| gatewayName | オンプレミスの HTTP ソースに接続するために使用する Data Management Gateway インスタンスの名前。 | オンプレミスの HTTP ソースからデータをコピーしている場合は はい |
| encryptedCredential | HTTP エンドポイントにアクセスするための暗号化された資格情報。 この値は、コピー ウィザードで、または **ClickOnce** ダイアログ ボックスを使用して認証情報を構成していると自動生成されます。 | いいえ<br /> (オンプレミスの HTTP サーバーからデータをコピーする場合にのみ適用されます) |

オンプレミスの HTTP コネクタ データ ソースの資格情報を設定する方法の詳細については、「[Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。

### <a name="using-basic-digest-or-windows-authentication"></a>基本、ダイジェスト、または Windows 認証の使用

**authenticationType** を **Basic**、**Basic**、または **Windows** に設定します。 前のセクションで説明された汎用の HTTP コネクタ プロパティに加えて、次のプロパティを設定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| userName | HTTP エンドポイントにアクセスするために使用するユーザー名。 | はい |
| password | ユーザー (**username**) のパスワード。 | はい |

**例:基本、ダイジェスト、または Windows 認証の使用**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>クライアント証明書認証の使用

基本認証を使用するには、**authenticationType** を **ClientCertificate** に設定します。 前のセクションで説明された汎用の HTTP コネクタ プロパティに加えて、次のプロパティを設定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| embeddedCertData | PFX ファイルのバイナリ データの Base64 でエンコードされたコンテンツ。 | **embeddedCertData** または **certThumbprint** のどちらかを指定します。 |
| certThumbprint | ゲートウェイ コンピューターの証明書ストアにインストールされた証明書の拇印。 オンプレミスの HTTP ソースからデータをコピーする場合にのみ適用されます。 | **embeddedCertData** または **certThumbprint** のどちらかを指定します。 |
| password | 証明書に関連付けられているパスワード。 | いいえ |

認証に **certThumbprint** を使用し、証明書がローカル コンピューターの個人用ストアにインストールされている場合は、ゲートウェイ サービスに読み取りアクセス許可を付与します。

1. Microsoft 管理コンソール (MMC) を開きます。 **ローカル コンピューター**を対象とする **[証明書]** スナップインを追加します。
2. **[証明書]**  >  **[個人]** を展開し、 **[証明書]** を選択します。
3. 個人用ストアの証明書を右クリックし、 **[すべてのタスク]**  > **[秘密キーの管理]** の順に選択します。
3. **[セキュリティ]** タブで、証明書に対する読み取りアクセス権を使用して、Data Management Gateway ホスト サービスが実行されているユーザー アカウントを追加します。  

**例:クライアント証明書の使用**

このリンクされたサービスは、データ ファクトリをオンプレミスの HTTP Web サーバーにリンクします。 これは、Data Management Gateway がインストールされているコンピューターにインストールされているクライアント証明書を使用します。

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

**例:ファイル内のクライアント証明書の使用**

このリンクされたサービスは、データ ファクトリをオンプレミスの HTTP Web サーバーにリンクします。 これは、Data Management Gateway がインストールされているコンピューター上のクライアント証明書ファイルを使用します。

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

構造、可用性、ポリシーなどのデータセット JSON ファイルの一部のセクションは、すべてのデータセットの型 (Azure SQL Database、Azure Blob Storage、Azure Table Storage) について同様です。

データセットの定義に使用できるセクションとプロパティの完全な一覧については、[データセットの作成](data-factory-create-datasets.md)をご覧ください。

**typeProperties** セクションは、データセットの型ごとに異なります。 **typeProperties** セクションは、データ ストア内のデータの場所に関する情報を示します。 **Http** 型のデータセットの **typeProperties** セクションには、次のプロパティが含まれています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** は **Http** に設定する必要があります。 | はい |
| relativeUrl | データを含むリソースへの相対 URL。 パスが指定されない場合は、リンクされたサービスの定義で指定されている URL のみが使用されます。 <br><br> 動的 URL を構築するには、[Data Factory の関数とシステム変数](data-factory-functions-variables.md)を使用できます。 例: **relativeUrl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)** 。 | いいえ |
| requestMethod | HTTP メソッド。 使用できる値は **GET** と **POST** です。 | いいえ <br />(既定値は **GET**) |
| additionalHeaders | 追加の HTTP 要求ヘッダー。 | いいえ |
| requestBody | HTTP 要求の本文。 | いいえ |
| format | *HTTP エンドポイントからそのままデータを取得する*だけで解析しない場合は、**format** の設定を省略してください。 <br><br> コピー中に HTTP 応答の内容を解析する場合に、次の種類の形式がサポートされます。**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 詳細については、[Text 形式](data-factory-supported-file-and-compression-formats.md#text-format)、[JSON 形式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 形式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 形式](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet 形式](data-factory-supported-file-and-compression-formats.md#parquet-format)の各セクションを参照してください。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |

**例:GET (既定) メソッドの使用**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**例:POST メソッドの使用**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
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

名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 

アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、プロパティはソースとシンクの種類によって異なります。

現在、コピー アクティビティのソースが **HttpSource** 型である場合は、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 これは応答データを読み取るタイムアウトではなく、応答を取得するタイムアウトです。 | いいえ<br />(既定値:**00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>サポートされているファイル形式と圧縮形式

詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md)」を参照してください。

## <a name="json-examples"></a>JSON の使用例

次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、HTTP ソースから Azure Blob Storage にデータをコピーする方法を示しています。 ただし、Azure Data Factory のコピー アクティビティを使用すると、任意のソースから[サポートされている](data-factory-data-movement-activities.md#supported-data-stores-and-formats)任意のシンクに*直接*データをコピーできます。

**例:HTTP ソースから Azure Blob Storage にデータをコピーする**

このサンプルの Data Factory ソリューションには、次の Data Factory エンティティが含まれています。

*   [HTTP](#linked-service-properties) 型のリンクされたサービス。
*   [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
*   [Http](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
*   [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
*   [HttpSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルでは、1 時間おきに HTTP ソースから Azure BLOB にデータがコピーされます。 これらのサンプルで使用されている JSON プロパティは、サンプルの後の各セクションで説明されています。

### <a name="http-linked-service"></a>HTTP のリンクされたサービス

この例では、HTTP のリンクされたサービスと匿名認証を使用します。 使用できるさまざまな種類の認証については、「[HTTP のリンクされたサービス](#linked-service-properties)」を参照してください。

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>HTTP の入力データセット

**external** を **true** に設定すると、データセットがデータ ファクトリの外部にあり、データ ファクトリのアクティビティによって生成されていないことが Data Factory サービスに通知されます。

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Azure BLOB の出力データセット

データは新しい BLOB に 1 時間おきに書き込まれます (**frequency**: **Hour**、**interval**:**1**)。

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

### <a name="pipeline-that-uses-a-copy-activity"></a>コピー アクティビティを使用するパイプライン

パイプラインには、入力データセットと出力データセットを使うように構成されたコピー アクティビティが含まれます。 コピー アクティビティは、1 時間おきに実行するようにスケジュールされています。 パイプライン JSON 定義では、**source** の種類が **HttpSource** に設定され、**sink** の種類が **BlobSink** に設定されています。

**HttpSource** でサポートされているプロパティの一覧については、[HttpSource](#copy-activity-properties) に関するページを参照してください。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
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

> [!NOTE]
> ソース データセット列からシンク データセット列へのマッピングについては、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング

Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響を与える主な要因と、それを最適化するための各種の方法の詳細については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。
