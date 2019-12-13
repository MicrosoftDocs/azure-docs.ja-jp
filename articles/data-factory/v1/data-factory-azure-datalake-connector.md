---
title: Azure Data Lake Storage Gen1 との間でデータをコピーする
description: Azure Data Factory を使って Data Lake Store に、または Data Lake Store からデータをコピーする方法を説明します
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b6a60536bae6fbedf01eda7aa340e90ced58e004
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930111"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Data Factory を使用して Azure Data Lake Storage Gen1 との間でデータをコピーする
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-azure-datalake-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-azure-data-lake-store.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の Azure Data Lake Storage Gen1 コネクター](../connector-azure-data-lake-store.md)に関するページを参照してください。

この記事では、Azure Data Factory のコピー アクティビティを使って、Azure Data Lake Storage Gen1 (旧称 Azure Data Lake Store) との間でデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の概要についての記事「[コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md)」が基になっています。

## <a name="supported-scenarios"></a>サポートされるシナリオ
**Azure Data Lake Store から**以下のデータ ストアにデータをコピーできます。

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

以下のデータ ストアから **Azure Data Lake Store に**データをコピーできます。

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> コピー アクティビティを含むパイプラインを作成する前に、Data Lake Store アカウントを作成します。 詳細については、[Azure Data Lake Store の使用](../../data-lake-store/data-lake-store-get-started-portal.md)に関するページをご覧ください。

## <a name="supported-authentication-types"></a>サポートされている認証の種類
Data Lake Store コネクタは、以下の認証の種類に対応しています。
* サービス プリンシパルの認証
* ユーザー資格情報 (OAuth) 認証

サービス プリンシパル認証を使うことをお勧めします (特に、スケジュールされたデータ コピーの場合)。 ユーザー資格情報認証では、トークンの有効期限の動作が発生する可能性があります。 構成の詳細については、「[リンクされたサービスのプロパティ](#linked-service-properties)」セクションを参照してください。

## <a name="get-started"></a>作業開始
さまざまなツール/API を使用して、Azure Data Lake Store との間でデータを移動するコピー アクティビティを含むパイプラインを作成できます。

データをコピーするためのパイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 コピー ウィザードを使ったパイプラインの作成に関するチュートリアルについては、「[チュートリアル:コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」をご覧ください。

また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **Data Factory**を作成します。 データ ファクトリには、1 つまたは複数のパイプラインを設定できます。
2. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。 たとえば、Azure Blob Storage から Azure Data Lake Store にデータをコピーする場合、リンクされたサービスを 2 つ作成して、Azure ストレージ アカウントと Azure Data Lake Store をデータ ファクトリにリンクします。 Azure Data Lake Store に固有のリンクされたサービスのプロパティについては、「[リンクされたサービスのプロパティ](#linked-service-properties)」セクションを参照してください。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。 最後の手順で説明されている例では、データセットを作成して入力データを含む BLOB コンテナーとフォルダーを指定します。 また、もう 1 つのデータセットを作成して、Blob Storage からコピーされたデータを保持する Data Lake Store 内のフォルダーとファイルのパスを指定します。 Azure Data Lake Store に固有のデータセットのプロパティについては、「[データセットのプロパティ](#dataset-properties)」セクションを参照してください。
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 前に説明した例では、コピー アクティビティのソースとして BlobSource を、シンクとして AzureDataLakeStoreSink を使います。 同様に、Azure Data Lake Store から Azure Blob Storage にコピーする場合は、AzureDataLakeStoreSource と BlobSink をコピー アクティビティで使います。 Azure Data Lake Store に固有のコピー アクティビティのプロパティについては、「[コピー アクティビティのプロパティ](#copy-activity-properties)」セクションを参照してください。 ソースまたはシンクとしてデータ ストアを使う方法については、前のセクションのデータ ストアのリンクをクリックしてください。

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。 Azure Data Lake Store との間でデータをコピーするときに使用する Data Factory エンティティの JSON 定義のサンプルについては、この記事の「[JSON の使用例](#json-examples-for-copying-data-to-and-from-data-lake-store)」を参照してください。

次のセクションでは、Data Lake Store に固有の Data Factory エンティティの定義に使われる JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
リンクされたサービスは、データ ストアをデータ ファクトリにリンクします。 Data Lake Store のデータをデータ ファクトリにリンクするには、**AzureDataLakeStore** 型のリンクされたサービスを作成します。 次の表では、Data Lake Store のリンクされたサービスに固有の JSON 要素について説明します。 サービス プリンシパル認証とユーザー資格情報認証のどちらかを選ぶことができます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| **type** | type プロパティは **AzureDataLakeStore** に設定する必要があります。 | はい |
| **dataLakeStoreUri** | Azure Data Lake Store アカウントに関する情報です。 この情報の形式は、`https://[accountname].azuredatalakestore.net/webhdfs/v1` または `adl://[accountname].azuredatalakestore.net/` です。 | はい |
| **subscriptionId** | Data Lake Store アカウントが属している Azure サブスクリプション ID です。 | シンクでは必須 |
| **resourceGroupName** | Data Lake Store アカウントが属している Azure リソース グループ名です。 | シンクでは必須 |

### <a name="service-principal-authentication-recommended"></a>サービス プリンシパル認証 (推奨)
サービス プリンシパル認証を使うには、Azure Active Directory (Azure AD) でアプリケーション エンティティを登録して、Data Lake Store へのアクセス権を付与します。 詳細な手順については、「[サービス間認証](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)」を参照してください。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。
* アプリケーション ID
* アプリケーション キー
* テナント ID

> [!IMPORTANT]
> Azure Data Lake Store でサービス プリンシパルに適切なアクセス許可を付与してください。
>- **Data Lake Store をソースとして使用するには**、少なくともデータの**読み取り + 実行**アクセス許可 (フォルダーの内容を表示およびコピーする場合)、または**読み取り**アクセス許可 (1 つのファイルをコピーする場合) を付与します。 アカウント レベルのアクセスの制御に関する要件はありません。
>- **Data Lake Store をシンクとして使用するには**、少なくともデータの**書き込み + 実行**アクセス許可 (フォルダー内に子項目を作成する場合) を付与します。 また、Azure IR を使用してコピーの権限を付与する (ソースとシンクの両方がクラウドに存在する) 場合は、データ ファクトリで Data Lake Store のリージョンを検出させるために、アカウントのアクセスの制御 (IAM) で少なくとも**閲覧者**ロールを付与します。 この IAM ロールを付与しないようにする場合は、コピー アクティビティで Data Lake Store の場所を使用して [executionLocation を指定](data-factory-data-movement-activities.md#global)します。
>- **コピー ウィザードを使用してパイプラインを作成する**場合は、アカウントのアクセスの制御 (IAM) で少なくとも**閲覧者**ロールを付与します。 また、Data Lake Store ルート ("/") とその子に対する少なくとも**読み取り + 実行**アクセス許可を付与します。 付与しないと、"指定された資格情報が無効です" というメッセージが表示されることがあります。

次のプロパティを指定して、サービス プリンシパル認証を使います。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| **servicePrincipalId** | アプリケーションのクライアント ID を取得します。 | はい |
| **servicePrincipalKey** | アプリケーションのキーを取得します。 | はい |
| **tenant** | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | はい |

**例:サービス プリンシパルの認証**
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

### <a name="user-credential-authentication"></a>ユーザー資格情報認証
または、次のプロパティを指定することで、ユーザー資格情報認証を使って、Data Lake Store との間でデータをコピーすることもできます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| **authorization** | Data Factory エディターで **[承認する]** をクリックし、資格情報を入力すると、自動生成された承認 URL がこのプロパティに割り当てられます。 | はい |
| **sessionId** | OAuth 承認セッションからの OAuth セッション ID です。 各セッション ID は一意であり、1 回のみ使うことができます。 Data Factory エディターを使うと、この設定が自動的に生成されます。 | はい |

> [!IMPORTANT]
> Azure Data Lake Store でユーザーに適切なアクセス許可を付与してください。
>- **Data Lake Store をソースとして使用するには**、少なくともデータの**読み取り + 実行**アクセス許可 (フォルダーの内容を表示およびコピーする場合)、または**読み取り**アクセス許可 (1 つのファイルをコピーする場合) を付与します。 アカウント レベルのアクセスの制御に関する要件はありません。
>- **Data Lake Store をシンクとして使用するには**、少なくともデータの**書き込み + 実行**アクセス許可 (フォルダー内に子項目を作成する場合) を付与します。 また、Azure IR を使用してコピーの権限を付与する (ソースとシンクの両方がクラウドに存在する) 場合は、データ ファクトリで Data Lake Store のリージョンを検出させるために、アカウントのアクセスの制御 (IAM) で少なくとも**閲覧者**ロールを付与します。 この IAM ロールを付与しないようにする場合は、コピー アクティビティで Data Lake Store の場所を使用して [executionLocation を指定](data-factory-data-movement-activities.md#global)します。
>- **コピー ウィザードを使用してパイプラインを作成する**場合は、アカウントのアクセスの制御 (IAM) で少なくとも**閲覧者**ロールを付与します。 また、Data Lake Store ルート ("/") とその子に対する少なくとも**読み取り + 実行**アクセス許可を付与します。 付与しないと、"指定された資格情報が無効です" というメッセージが表示されることがあります。

**例:ユーザー資格情報認証**
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
**[承認する]** ボタンを使って生成した承認コードは、一定の時間が経過すると有効期限が切れます。 次のメッセージは、認証トークンの有効期限が切れたことを意味します。

資格情報の操作エラー: invalid_grant - AADSTS70002:Error validating credentials. (資格情報の検証中にエラーが発生しました。) AADSTS70008:指定されたアクセス権の付与は期限が切れているか、失効しています。 トレース ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 相関 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 タイムスタンプ:2015-12-15 21-09-31Z。

次の表では、さまざまな種類のユーザー アカウントの有効期限を示します。

| ユーザー タイプ | 有効期限 |
|:--- |:--- |
| Azure Active Directory で管理されて "*いない*" ユーザー アカウント (@hotmail.com、@live.com など) |12 時間 |
| Azure Active Directory で管理されているユーザー アカウント |スライスの最後の実行から 14 日後 <br/><br/>OAuth ベースのリンクされたサービスに基づくスライスが 14 日ごとに少なくとも 1 回実行される場合は 90 日 |

トークンの有効期限の前にパスワードを変更すると、トークンは即座に期限切れになります。 このセクションで前に示したメッセージが表示されます。

トークンの有効期限が切れたら、 **[承認する]** ボタンを使ってアカウントを再承認し、リンクされたサービスを再デプロイできます。 次のコードを使って、**sessionId** および **authorization** プロパティの値をプログラムで生成することもできます。


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
コードで使用する Data Factory クラスの詳細については、「[AzureDataLakeStoreLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)」、「[AzureDataLakeAnalyticsLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)」、「[AuthorizationSessionGetResponse クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)」を参照してください。 コードで使用する `WindowsFormsWebAuthenticationDialog` クラスに対する `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` のバージョン `2.9.10826.1824` に対する参照を追加します。

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント

**現象:** データを Azure Data Lake Store **内に**コピーする際、次のエラーによってコピー アクティビティが失敗する。

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**根本原因:** 次の 2 つの原因が考えられます。

1. Azure Data Lake Store のリンクされたサービスで指定された `resourceGroupName` や `subscriptionId` が正しくない。
2. ユーザーまたはサービス プリンシパルに、必要なアクセス許可がない。

**解決策:**

1. リンクされたサービス `typeProperties` で指定した `subscriptionId` と `resourceGroupName` が、 実際にデータ レイク アカウントが属しているものであることを確認します。

2. データ レイク アカウントのユーザーまたはサービス プリンシパルに、少なくとも**閲覧者**ロールが付与されていることを確認します。 具体的な方法を次に示します。

    1. Azure portal にアクセスし、ご自分の Data Lake Store アカウントに移動します
    2. Data Lake Store のブレードで、 **[アクセス制御 (IAM)]** をクリックします
    3. **[ロールの割り当ての追加]** をクリックします
    4. **[ロール]** を **[閲覧者]** に設定し、コピーに使用するユーザーまたはサービス プリンシパルを選択して、アクセス権を付与します

3. ユーザーやサービス プリンシパルに**閲覧者**ロールを付与しないようにする必要がある場合は、コピー アクティビティで[実行場所を明示的に指定](data-factory-data-movement-activities.md#global)して、Data Lake Store の場所を使用する方法もあります。 例:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>データセットのプロパティ
Data Lake Store の入力データを表すデータセットを指定するには、データセットの **type** プロパティを **AzureDataLakeStore** に設定します。 また、データセットの **linkedServiceName** プロパティは、Data Lake Store のリンクされたサービスの名前に設定します。 データセットの定義に利用できる JSON のセクションとプロパティの完全な一覧については、「[Azure Data Factory のデータセット](data-factory-create-datasets.md)」を参照してください。 **構造**、**可用性**、**ポリシー**など、JSON でのデータセットのセクションは、データセットのすべての型 (Azure SQL データベース、Azure BLOB、Azure テーブルなど) でほぼ同じです。 **typeProperties** セクションは、データセットの型ごとに異なり、データ ストアのデータの場所や書式などに関する情報を提供します。

**AzureDataLakeStore** 型の **typeProperties** セクションには、次のプロパティが含まれます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| **folderPath** |Data Lake Store のコンテナーとフォルダーのパスです。 |はい |
| **fileName** |Azure Data Lake Store 内のファイルの名前です。 **fileName** プロパティは省略可能で、大文字と小文字を区別します。 <br/><br/>**fileName** を指定すると、アクティビティ (コピーを含む) は特定のファイルで動作します。<br/><br/>**fileName** が指定されていない場合、コピーには入力データセットの **folderPath** のすべてのファイルが含まれます。<br/><br/>出力データセットに **fileName** が指定されておらず、アクティビティ シンクで **preserveHierarchy** が指定されていない場合は、生成されるファイル名は `Data._Guid_.txt` という形式になります。 例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt。 |いいえ |
| **partitionedBy** |**PartitionedBy** プロパティは省略可能です。 これを使用し、時系列データに動的なパスとファイル名を指定できます。 たとえば、1 時間ごとのデータに対して **folderPath** をパラメーター化できます。 詳細と例については、「partitionedBy プロパティ」を参照してください。 |いいえ |
| **format** | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 **format** の **type** プロパティをいずれかの値に設定します。 詳細については、「[Azure Data Factory でサポートされるファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md)」の「[テキスト形式](data-factory-supported-file-and-compression-formats.md#text-format)」、「[JSON 形式](data-factory-supported-file-and-compression-formats.md#json-format)」、「[Avro 形式](data-factory-supported-file-and-compression-formats.md#avro-format)」、「[ORC 形式](data-factory-supported-file-and-compression-formats.md#orc-format)」、「[Parquet 形式](data-factory-supported-file-and-compression-formats.md#parquet-format)」の各セクションを参照してください。 <br><br> ファイルベースのストア間でファイルをそのままコピー (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で `format` セクションをスキップします。 |いいえ |
| **compression** | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory でサポートされるファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |

### <a name="the-partitionedby-property"></a>partitionedBy プロパティ
**partitionedBy** プロパティ、Data Factory 関数、およびシステム変数を使用して、時系列データに動的な **folderPath** および **fileName** プロパティを指定できます。 詳細については、「[Azure Data Factory - 関数およびシステム変数](data-factory-functions-variables.md)」を参照してください。


この例で、`{Slice}` は、指定された形式 (`yyyyMMddHH`) の Data Factory システム変数 `SliceStart` の値に置き換えられます。 名前 `SliceStart` は、スライスの開始時刻を示します。 `folderPath` プロパティは、`wikidatagateway/wikisampledataout/2014100103` や `wikidatagateway/wikisampledataout/2014100104` のように、スライスごとに異なります。

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

次の例では、`SliceStart` の年、月、日、時刻が、`folderPath` プロパティと `fileName` プロパティで使われる個別の変数に抽出されます。
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
時系列データセット、スケジュール、スライスの詳細については、「[Azure Data Factory のデータセット](data-factory-create-datasets.md)」および「[Data Factory のスケジュール設定と実行](data-factory-scheduling-and-execution.md)」を参照してください。


## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

**AzureDataLakeStoreSource** の **typeProperties** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| **recursive** |データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True (既定値)、False |いいえ |

**AzureDataLakeStoreSink** の **typeProperties** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| **copyBehavior** |コピー動作を指定します。 |<b>PreserveHierarchy</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーに対するソース ファイルの相対パスと、ターゲット フォルダーに対するターゲット ファイルの相対パスが一致します。<br/><br/><b>FlattenHierarchy</b>:ソース フォルダーのすべてのファイルがターゲット フォルダーの最上位レベルに作成されます。 ターゲット ファイルは、自動生成された名前で作成されます。<br/><br/><b>MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイルまたは BLOB の名前を指定した場合、マージされたファイル名は指定した名前になります。 指定しないと、ファイル名は自動生成されます。 |いいえ |

### <a name="recursive-and-copybehavior-examples"></a>recursive と copyBehavior の例
このセクションでは、recursive 値と copyBhavior 値の組み合わせごとに、Copy 操作で行われる動作について説明します。

| recursive | copyBehavior | 行われる動作 |
| --- | --- | --- |
| true |preserveHierarchy |ソース フォルダー Folder1 が次のような構造の場合: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲット フォルダー Folder1 はソースと同じ構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |ソース フォルダー Folder1 が次のような構造の場合: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 の自動生成された名前 |
| true |mergeFiles |ソース フォルダー Folder1 が次のような構造の場合: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2、File3、File4、File5の内容は、自動生成されたファイル名を持つ 1 つのファイルにマージされます。 |
| false |preserveHierarchy |ソース フォルダー Folder1 が次のような構造の場合: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |flattenHierarchy |ソース フォルダー Folder1 が次のような構造の場合:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/><br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |mergeFiles |ソース フォルダー Folder1 が次のような構造の場合:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2 の内容は、自動生成されたファイル名を持つ 1 つのファイルにマージされます。 File1 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |

## <a name="supported-file-and-compression-formats"></a>サポートされているファイル形式と圧縮形式
詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md)」を参照してください。

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Data Lake Store との間でのデータのコピーに関する JSON の例
次の例では、JSON 定義のサンプルを示します。 これらのサンプル定義を使うと、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使ってパイプラインを作成できます。 以下の例では、Data Lake Store と Azure BLOB Storage との間でデータをコピーする方法を示します。 ただし、任意のソースのデータを、サポートされている任意のシンクに _直接_ コピーできます。 詳細については、「[コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md)」の「サポートされるデータ ストアと形式」セクションを参照してください。

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>例:Azure Blob Storage から Azure Data Lake Store にデータをコピーする
このセクションのコード例では以下を示します。

* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
* [AzureDataLakeStore](#linked-service-properties)型のリンクされたサービス。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
* [AzureDataLakeStore](#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
* [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) と [AzureDataLakeStoreSink](#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

以下の例では、Azure Blob Storage ストレージから Data Lake Store に時系列データを 1 時間おきにコピーします。

**Azure Storage のリンクされたサービス**

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
> 構成の詳細については、「[リンクされたサービスのプロパティ](#linked-service-properties)」セクションを参照してください。
>

**Azure BLOB の入力データセット**

次の例では、データは新しい BLOB から 1 時間おきに取得されます (`"frequency": "Hour", "interval": 1`)。 BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは、開始時刻の年、月、日の部分を使います。 ファイル名は、開始時刻の時の部分を使います。 `"external": true` の設定により、このテーブルが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

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

**Azure Data Lake Store の出力データセット**

次の例では、Data Lake Store にデータをコピーします。 新しいデータが 1 時間おきに Data Lake Store にコピーされます。

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

**BLOB ソースおよび Data Lake Store シンクを使用するパイプラインでのコピー アクティビティ**

次の例のパイプラインには、入力データセットと出力データセットを使うように構成されたコピー アクティビティが含まれます。 コピー アクティビティは、1 時間おきに実行するようにスケジュールされています。 パイプラインの JSON 定義では、`source` 型は `BlobSource` に、`sink` 型は `AzureDataLakeStoreSink` に設定されています。

```json
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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>例:Azure Data Lake Store から Azure BLOB にデータをコピーする
このセクションのコード例では以下を示します。

* [AzureDataLakeStore](#linked-service-properties)型のリンクされたサービス。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
* [AzureDataLakeStore](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
* [AzureDataLakeStoreSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

次のコードは、Data Lake Store から Azure BLOB に時系列データを 1 時間おきにコピーします。

**Azure Data Lake Store のリンクされたサービス**

```json
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
> 構成の詳細については、「[リンクされたサービスのプロパティ](#linked-service-properties)」セクションを参照してください。
>

**Azure Storage のリンクされたサービス**

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
**Azure Data Lake の入力データセット**

この例では、`"external"` を `true` に設定することにより、このテーブルが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことを Data Factory サービスに通知します。

```json
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
**Azure BLOB の出力データセット**

次の例では、データは新しい BLOB に 1 時間おきに書き込まれます (`"frequency": "Hour", "interval": 1`)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは、開始時刻の年、月、日、時の部分を使います。

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

**Azure Data Lake Store ソースおよび BLOB シンクを使用するパイプラインでのコピー アクティビティ**

次の例のパイプラインには、入力データセットと出力データセットを使うように構成されたコピー アクティビティが含まれます。 コピー アクティビティは、1 時間おきに実行するようにスケジュールされています。 パイプラインの JSON 定義では、`source` 型は `AzureDataLakeStoreSource` に、`sink` 型は `BlobSink` に設定されています。

```json
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

コピー アクティビティの定義では、ソース データセットの列をシンク データセットの列にマップすることもできます。 詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページを参照してください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
コピー アクティビティのパフォーマンスに影響する要因と、パフォーマンスを最適化する方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。
