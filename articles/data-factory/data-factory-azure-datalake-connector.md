---
title: "Azure Data Lake Store の間でのデータの移動 | Microsoft Docs"
description: "Azure Data Factory を使用して Azure Data Lake Store に、または Azure Data Lake Store からデータを移動する方法を説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 1d7ba169675e822ae08a6c679301a0db01e6e338
ms.lasthandoff: 03/24/2017


---
# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Azure Data Factory を使用した Azure Data Lake Store との間でのデータの移動
この記事では、Azure Data Factory のコピー アクティビティを使って、Azure Data Lake Store との間でデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。 

サポートされる任意のソース データ ストアのデータを、Azure Data Lake Store にコピーしたり、Azure Data Lake Store のデータを、サポートされる任意のシンク データ ストアにコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表をご覧ください。  

> [!NOTE]
> Azure Data Lake Store との間でデータを移動するには、コピー アクティビティを含むパイプラインを作成する前に Azure Data Lake Store アカウントを作成します。 Azure Data Lake Store の詳細については、 [Azure Data Lake Store の概要](../data-lake-store/data-lake-store-get-started-portal.md)に関する記事をご覧ください。

## <a name="getting-started"></a>使用の開始
さまざまなツール/API を使用して、Azure Data Lake Store との間でデータを移動するコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」をご覧ください。

**Azure Portal**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、**.NET API**、**REST API** などのツールを使ってパイプラインを作成することもできます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。 

ツールまたは API のいずれを使用した場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。 

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。 
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 ツール/API (.NET API を除く) を使う場合、こうした Data Factory エンティティは、JSON 形式で定義します。  Azure Data Lake Store との間でデータをコピーするときに使用する Data Factory エンティティの JSON 定義のサンプルについては、この記事の「[JSON の使用例](#json-examples)」を参照してください。 

次のセクションでは、Azure Data Lake Store に固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。 

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
リンクされたサービスは、データ ストアをデータ ファクトリにリンクします。 Azure Data Lake Store データ ストアをデータ ファクトリにリンクするには、**AzureDataLakeStore** 型のリンクされたサービスを作成します。 次の表では、Azure Data Lake Store のリンクされたサービスに固有の JSON 要素について説明します。**サービス プリンシパル**または**ユーザー資格情報**のいずれかの認証を選択できます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **AzureDataLakeStore** | はい |
| dataLakeStoreUri | Azure Data Lake Store アカウントの情報を指定します。 `https://[accountname].azuredatalakestore.net/webhdfs/v1` または `adl://[accountname].azuredatalakestore.net/` という形式で指定します。 | はい |
| subscriptionId | Data Lake Store が所属する Azure サブスクリプション ID。 | シンクでは必須 |
| resourceGroupName | Data Lake Store が所属する Azure リソース グループの名前。 | シンクでは必須 |

### <a name="using-service-principal-authentication-recommended"></a>サービス プリンシパル認証の使用 (推奨)
サービス プリンシパル認証を使用するには、Azure Active Directory (AAD) でアプリケーション エンティティを登録して、Data Lake Store へのアクセス権を付与します。 詳細な手順については、[サービス間認証](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)に関するページをご覧ください。 **アプリケーション ID**、**アプリケーション キー**、および**テナント ID** の値をメモします。 この情報は、リンクされたサービスを定義するときに使用します。 

> [!IMPORTANT]
> コピー ウィザードを使用してデータ パイプラインを作成する場合は、サービス プリンシパルに、少なくとも Data Lake Store アカウントに対するアクセス制御 (IAM) の閲覧者ロールと、Data Lake Store ルート ("/") とその子に対する読み取りおよび実行アクセス許可を必ず付与します。 付与されていない場合、"提供された資格情報が無効" であることを示すエラーが表示されることがあります。
>
> AAD でサービス プリンシパルを作成/更新した後、その変更が実際に有効になるまで数分かかる場合があります。 まず、サービス プリンシパルと Data Lake Store の ACL 構成を再確認します。 "指定された資格情報が無効です" というエラーが引き続き表示される場合は、しばらく待ってからやり直してください。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 | はい |
| テナント | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅にマウスを置くことで取得できます。 | はい |

**例: サービス プリンシパル認証の使用**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="using-user-credential-authentication"></a>ユーザー資格情報認証の使用
また、次のプロパティを指定することで、ユーザー資格情報認証を使用して、Data Lake Store との間でデータをコピーすることもできます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| authorization | **Data Factory エディター**で **[承認する]** をクリックし、資格情報を入力すると、自動生成された承認 URL がこのプロパティに割り当てられます。 | はい |
| sessionId | OAuth 承認セッションの OAuth セッション ID。 各セッション ID は一意であり、1 回のみ使用できます。 Data Factory エディターを使用すると、この設定が自動的に生成されます。 | はい |

**例: ユーザー資格情報認証の使用**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>トークンの有効期限
**[承認する]** ボタンを使用して生成した承認コードは、いずれ有効期限が切れます。 さまざまな種類のユーザー アカウントの有効期限については、次の表を参照してください。 認証**トークンの有効期限が切れる**と、次のエラー メッセージが表示される場合があります。
 
```
"Credential operation error: invalid_grant - AADSTS70002: Error validating credentials. AADSTS70008: The provided access grant is expired or revoked. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z".
```

| ユーザー タイプ | 有効期限 |
|:--- |:--- |
| Azure Active Directory で管理されていないユーザー アカウント (@hotmail.com、@live.com など)。 |12 時間 |
| Azure Active Directory (AAD) で管理されているユーザー アカウント |スライスの最後の実行から 14 日後。 <br/><br/>OAuth ベースのリンクされたサービスに基づいて、少なくとも 14 日間に 1 回スライスが実行する場合、90 日です。 |

このトークンの有効期限の前にパスワードを変更すると、トークンが即座に期限切れとなり、前述のエラーが表示されます。

このエラーを回避または解決するには、**トークンの有効期限が切れた**ときに、**[承認する]** ボタンを使用して再承認し、リンクされたサービスを再デプロイします。 次のセクションのコードを使用して、**sessionId** と **authorization** プロパティの値をプログラムで生成することもできます。 

#### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>プログラムを使用して sessionId と authorization の値を生成するには

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
コードで使用する Data Factory クラスの詳細については、「[AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)」、「[AzureDataLakeAnalyticsLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)」、および「[AuthorizationSessionGetResponse クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)」をご覧ください。 このコードで使用されている WindowsFormsWebAuthenticationDialog クラスの **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** のバージョン **2.9.10826.1824** に参照を追加します。

## <a name="dataset-properties"></a>データセットのプロパティ
データセットを指定して Azure Blob Storage の入力データを表すには、そのデータセットの type プロパティを **AzureDataLakeStore** に設定します。 また、データセットの **linkedServiceName** プロパティは、Azure Data Lake Store のリンクされたサービスの名前に設定します。 データセットの定義に利用できる JSON のセクションとプロパティの完全一覧については、[データセットの作成](data-factory-create-datasets.md)に関する記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。 **typeProperties** セクションは、データセットの型ごとに異なり、データ ストアのデータの場所や書式などに関する情報を提供します。 **AzureDataLakeStore** 型のデータセットの typeProperties セクションには、次のプロパティがあります。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| folderPath |Azure Data Lake Store のコンテナーとフォルダーのパス。 |はい |
| fileName |Azure Data Lake Store 内のファイルの名前。 fileName は省略可能で、大文字と小文字を区別します。 <br/><br/>fileName を指定すると、アクティビティ (コピーを含む) は特定のファイルで動作します。<br/><br/>fileName が指定されていない場合、コピーには入力データセットの folderPath のすべてのファイルが含まれます。<br/><br/>出力データセットに fileName が指定されていない場合、生成されるファイル名は次の形式になります: Data<Guid>.txt (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |いいえ |
| partitionedBy |partitionedBy は任意のプロパティです。 これを使用し、時系列データに動的な folderPath と fileName を指定できます。 たとえば、1 時間ごとのデータに対して folderPath をパラメーター化できます。 詳細と例については、「 [partitionedBy プロパティの使用](#using-partitionedby-property) 」をご覧ください。 |なし |
| BlobSink の format | 次のファイル形式がサポートされます: **TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |なし |

### <a name="using-partitionedby-property"></a>partitionedBy プロパティの使用
前のセクションで説明したように、**partitionedBy** プロパティ、[Data Factory 関数、およびシステム変数](data-factory-functions-variables.md)を使用して、時系列データに動的な folderPath および filename を指定できます。

時系列データセット、スケジュール設定、スライスの詳細については、[データセットの作成](data-factory-create-datasets.md)に関する記事および[スケジュール設定と実行](data-factory-scheduling-and-execution.md)に関する記事をご覧ください。

#### <a name="sample-1"></a>サンプル 1

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
この例では、{Slice} は、指定された形式 (YYYYMMDDHH) で、Data Factory システム変数の SliceStart の値に置き換えられます。 SliceStart はスライスの開始時刻です。 folderPath はスライスごとに異なります。 例: wikidatagateway/wikisampledataout/2014100103 または wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>サンプル 2
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
この例では、SliceStart の年、月、日、時刻が folderPath プロパティと fileName プロパティで使用される個別の変数に抽出されます。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります

**AzureDataLakeStoreSource** の **typeProperties** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True (既定値)、False |いいえ |

**AzureDataLakeStoreSink** の **typeProperties** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| copyBehavior |コピー動作を指定します。 |<b>PreserveHierarchy</b>: ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーに対するソース ファイルの相対パスと、ターゲット フォルダーに対するターゲット ファイルの相対パスが一致します。<br/><br/><b>FlattenHierarchy</b>: ソース フォルダーのすべてのファイルがターゲット フォルダーの最初のレベルに作成されます。 ターゲット ファイルは、自動生成された名前で作成されます。<br/><br/><b>MergeFiles</b>: ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル/Blob の名前を指定した場合、マージされたファイル名は指定した名前になります。それ以外は自動生成されたファイル名になります。 |いいえ |

## <a name="json-examples"></a>JSON の使用例
以下の例は、[Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 ここでは、Azure Data Lake Store と Azure BLOB Storage との間でデータをコピーする方法を示します。 ただし、任意のソースのデータを、サポートされている任意のシンクに**直接**コピーできます。 詳細については、「[コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md)」の「サポートされるデータ ストアと形式」のセクションを参照してください。  
## <a name="example-copy-data-from-azure-blob-to-azure-data-lake-store"></a>例: Azure BLOB から Azure Data Lake Store へのデータのコピー
次のサンプルは以下を示しています。

1. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
2. [AzureDataLakeStore](#linked-service-properties)型のリンクされたサービス。
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureDataLakeStore](#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) と [AzureDataLakeStoreSink](#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、Azure BLOB ストレージから Azure Data Lake Store に時系列データを 1 時間おきに コピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure Storage のリンクされたサービス:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Azure Data Lake Store のリンクされたサービス**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> 構成の詳細については、「[Azure Data Lake Store のリンクされたサービスのプロパティ](#linked-service-properties)」セクションの手順を参照してください。
>

**Azure BLOB の入力データセット:**

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスでは開始時間の年、月、日の部分を使用し、ファイル名では開始時間の時刻部分を使用します。 "external": "true" の設定により、このテーブルが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Data Lake Store の出力データセット:**

このサンプルは Azure Data Lake Store にデータをコピーします。 新しいデータが 1 時間おきに Data Lake Store にコピーされます。

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**BLOB ソースおよび Azure Data Lake Store シンクを使用するパイプラインでのコピー アクティビティ:** 

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **BlobSource** に設定され、**sink** 型が **AzureDataLakeStoreSink** に設定されています。

```JSON
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
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

## <a name="example-copy-data-from-azure-data-lake-store-to-azure-blob"></a>例: Azure Data Lake Store から Azure BLOB へのデータのコピー
次のサンプルは以下を示しています。

1. [AzureDataLakeStore](#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [AzureDataLakeStore](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [AzureDataLakeStoreSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、Azure Data Lake Store から Azure BLOB に時系列データを 1 時間おきにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure Data Lake Store のリンクされたサービス:**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> 構成の詳細については、「[Azure Data Lake Store のリンクされたサービスのプロパティ](#linked-service-properties)」セクションの手順を参照してください。
>

**Azure Storage のリンクされたサービス:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Data Lake の入力データセット:**

**"external": true** の設定により、このテーブルが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```JSON
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
              "interval": 1
        },
        "policy": {
              "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
              }
        }
      }
}
```
**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure Data Lake Store ソースおよび BLOB シンクを使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** の型が **AzureDataLakeStoreSource** に設定され、**sink** の型が **BlobSink** に設定されています。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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

コピー アクティビティ定義で、ソース データセットの列をシンク データセットの列にマップすることもできます。 詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページを参照してください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

