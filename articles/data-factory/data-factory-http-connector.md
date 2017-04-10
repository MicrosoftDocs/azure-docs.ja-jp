---
title: "HTTP ソースからデータを移動する - Azure | Microsoft Docs"
description: "Azure Data Factory を使用してオンプレミスまたはクラウドの HTTP ソースからデータを移動する方法について説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: d01dd1fdeed4aa809ba5118f79968f7719b11865
ms.lasthandoff: 03/27/2017


---
# <a name="move-data-from-an-http-source-using-azure-data-factory"></a>Azure Data Factory を使用して HTTP ソースからデータを移動する
この記事では、Azure Data Factory のコピー アクティビティを使用して、オンプレミス/クラウドの HTTP エンドポイントからサポートされたシンク データ ストアにデータを移動する方法について説明します。 この記事では、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要と、ソース/シンクとしてサポートされるデータ ストアの一覧を紹介します。

現在のところ、データ ファクトリは HTTP ソースから他のデータ ストアへのデータの移動のみに対応しており、他のデータ ストアから HTTP ターゲットにデータを移動することはできません。

## <a name="supported-scenarios-and-authentication-types"></a>サポートされているシナリオと認証の種類
この HTTP コネクタを使用すると、HTTP **GET** または **POST** メソッドを利用して、**クラウドとオンプレミス両方の HTTP/s エンドポイント**からデータを取得することができます。 サポートされている認証の種類は、**Anonymous**、**Basic**、**Digest**、**Windows**、および **ClientCertificate** です。 このコネクタと [Web テーブル コネクタ](data-factory-web-table-connector.md)の違いに注意してください。後者は、Web HTML ページからテーブルの内容を抽出するために使用されます。

オンプレミスの HTTP エンドポイントからデータをコピーする場合は、オンプレミスの環境/Azure VM に Data Management Gateway をインストールする必要があります。 Data Management Gateway の詳細およびゲートウェイの設定手順については、「 [オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md) 」を参照してください。

## <a name="getting-started"></a>使用の開始
さまざまなツールや API を使用して、HTTP ソースからデータを移動するコピー アクティビティを含むパイプラインを作成できます。

- パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」をご覧ください。

- パイプラインの作成には、**Azure Portal**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、**.NET API**、**REST API** というツールを使用することもできます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。 HTTP ソースから Azure Blob Storage にデータをコピーする JSON サンプルについては、この記事の「[JSON の使用例](#json-examples)」セクションを参照してください。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、HTTP のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type | type プロパティを `Http` に設定する必要があります。 | はい |
| url | Web サーバーへのベース URL | はい |
| authenticationType | 認証の種類を指定します。 使用可能な値は、**Anonymous**、**Basic**、**Digest**、**Windows**、**ClientCertificate** です。 <br><br> これらの認証の種類それぞれのプロパティと JSON の使用例については、この表の後のセクションを参照してください。 | はい |
| enableServerCertificateValidation | ソースが HTTPS Web サーバーである場合に、サーバーの SSL 証明書の検証を有効にするかどうかを指定します。 | いいえ。既定値は true です。 |
| gatewayName | オンプレミスの HTTP ソースに接続するための Data Management Gateway の名前。 | はい (オンプレミスの HTTP ソースからデータをコピーする場合)。 |
| encryptedCredential | HTTP エンドポイントにアクセスするための暗号化された資格情報。 コピー ウィザードまたは ClickOnce ポップアップ ダイアログで認証情報を構成すると自動生成されます。 | いいえ。 オンプレミスの HTTP サーバーからデータをコピーする場合にのみ適用します。 |

オンプレミスの HTTP コネクタ データ ソースの資格情報を設定する方法の詳細については、「[Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。

### <a name="using-basic-digest-or-windows-authentication"></a>基本、ダイジェスト、または Windows 認証の使用

`authenticationType` を `Basic`、`Digest`、または `Windows` として設定し、上で紹介した HTTP コネクタの一般的なプロパティのほかに、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| username | HTTP エンドポイントにアクセスするためのユーザー名。 | はい |
| パスワード | ユーザー (username) のパスワード。 | はい |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>例: 基本、ダイジェスト、または Windows 認証を使用する

```JSON
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

基本認証を使用するには、`authenticationType` を `ClientCertificate` として設定し、上で紹介した HTTP コネクタの一般的なプロパティのほかに、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| embeddedCertData | Personal Information Exchange (PFX) ファイルのバイナリ データの Base64 でエンコードされたコンテンツ。 | `embeddedCertData` または `certThumbprint` を指定します。 |
| certThumbprint | ゲートウェイ コンピューターの証明書ストアにインストールされた証明書の拇印。 オンプレミスの HTTP ソースからデータをコピーする場合にのみ適用します。 | `embeddedCertData` または `certThumbprint` を指定します。 |
| パスワード | 証明書に関連付けられているパスワード。 | いいえ |

認証に `certThumbprint` を使用し、証明書がローカル コンピューターの個人用ストアにインストールされている場合は、ゲートウェイ サービスに読み取りアクセス許可を付与する必要があります。

1. Microsoft 管理コンソール (MMC) を起動します。 **ローカル コンピューター**を対象とする **[証明書]** スナップインを追加します。
2. **[証明書]**、**[個人]** の順に展開し、**[証明書]** をクリックします。
3. 個人用ストアの証明書を右クリックし、**[すべてのタスク]**->**[秘密キーの管理]** の順に選択します。
3. **[セキュリティ]** タブで、証明書に対する読み取りアクセス権を使用して Data Management Gateway Host Service を実行しているユーザー アカウントを追加します。  

#### <a name="example-using-client-certificate"></a>例: クライアント証明書を使用する
このリンクされたサービスは、データ ファクトリをオンプレミスの HTTP Web サーバーにリンクします。 Data Management Gateway がインストールされているコンピューターにインストールされているクライアント証明書を使用します。

```JSON
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

#### <a name="example-using-client-certificate-in-a-file"></a>例: ファイル内のクライアント証明書を使用する
このリンクされたサービスは、データ ファクトリをオンプレミスの HTTP Web サーバーにリンクします。 Data Management Gateway がインストールされているコンピューターのクライアント証明書ファイルを使用します。

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **Http** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの型を指定します。 `Http` に設定する必要があります。 | はい |
| relativeUrl | データを含むリソースへの相対 URL。 パスが指定されていないとき、リンクされたサービス定義に指定されている URL のみだけが使用されます。 | なし |
| requestMethod | Http メソッド。 使用できる値は、**GET** または **POST** です。 | いいえ。 既定値は `GET`です。 |
| additionalHeaders | 追加の HTTP 要求ヘッダー。 | なし |
| requestBody | HTTP 要求の本文。 | なし |
| partitionedBy | partitionedBy を使用して時系列データに動的な folderPath と fileName を指定できます。 たとえば、1 時間ごとのデータに対して folderPath がパラメーター化されます。 | いいえ |
| BlobSink の format | **HTTP エンドポイントからデータをそのまま取得する**だけで、解析しない場合は、この形式の設定を省略してください。 <br><br> コピー中に HTTP 応答の内容を解析する場合、サポートされている形式は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](#specifying-textformat)、[Json Format](#specifying-jsonformat)、[Avro Format](#specifying-avroformat)、[Orc Format](#specifying-orcformat)、[Parquet Format](#specifying-parquetformat) の各セクションを参照してください。  |なし |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は **GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。サポートされるレベルは **Optimal** と **Fastest** です。 詳細については、「[圧縮の指定](#specifying-compression)」セクションを参照してください。 |なし |

### <a name="example-using-the-get-default-method"></a>例: GET (既定) メソッドを使用する

```JSON
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

### <a name="example-using-the-post-method"></a>例: POST メソッドを使用する

```JSON
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

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

現時点では、コピー アクティビティのソースが **Http** 型の場合、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (TimeSpan)。 応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。 | いいえ。 既定値: 00:01:40 |

## <a name="json-examples"></a>JSON の使用例
次の例は、[Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、HTTP ソースから Azure Blob Storage にデータをコピーする方法を示しています。 ただし、Azure Data Factory のコピー アクティビティを使用して、 **こちら** に記載されているいずれかのシンクに、任意のソースからデータを [直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats) コピーすることができます。

### <a name="example-copy-data-from-http-source-to-azure-blob-storage"></a>例: HTTP ソースから Azure Blob Storage にデータをコピーする
このサンプルの Data Factory ソリューションには、次の Data Factory エンティティが含まれています。

1. [HTTP](#linked-service-properties) 型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [Http](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [HttpSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルでは、1 時間おきに HTTP ソースから Azure BLOB にデータがコピーされます。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

### <a name="http-linked-service"></a>HTTP のリンクされたサービス
この例では、HTTP のリンクされたサービスと匿名認証を使用します。 使用可能なさまざまな種類の認証については、[HTTP のリンクされたサービス](#linked-service-properties)に関するセクションを参照してください。

```JSON
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

```JSON
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

### <a name="http-input-dataset"></a>HTTP の入力データセット
**external** を **true** に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```JSON
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

### <a name="azure-blob-output-dataset"></a>Azure BLOB の出力データセット

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。

```JSON
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

### <a name="pipeline-with-copy-activity"></a>コピー アクティビティのあるパイプライン

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義では、**source** の種類が **HttpSource** に設定され、**sink** の種類が **BlobSink** に設定されています。

HttpSource でサポートされているプロパティの一覧については、[HttpSource](#httpsource-in-copy-activity) に関するページを参照してください。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
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
> ソース データセット列からシンク データセット列へのマッピングについては、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページを参照してください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

