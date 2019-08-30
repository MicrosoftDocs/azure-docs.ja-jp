---
title: .NET を使用してコンテナーまたは BLOB 用のユーザー委任 SAS を作成する (プレビュー) - Azure Storage
description: .NET クライアント ライブラリを使用して、Azure Storage で Azure Active Directory 資格情報を使用するユーザー委任 SAS を作成する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 98ab93bbec8da17dde93c9c343703838b0279994
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900427"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>.NET を使用してコンテナーまたは BLOB 用のユーザー委任 SAS を作成する (プレビュー)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

この記事では、[.NET 用 Azure Storage クライアント ライブラリ](https://www.nuget.org/packages/Azure.Storage.Blobs)を使用するコンテナーまたは BLOB 用のユーザー委任 SAS を Azure Active Directory (Azure AD) 資格情報を使用して作成する方法について説明します。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>プレビュー パッケージをインストールする

この記事の例では、BLOB ストレージ用の Azure Storage クライアント ライブラリの最新のプレビュー バージョンが使用されます。 プレビュー パッケージをインストールするには、NuGet パッケージ マネージャー コンソールから次のコマンドを実行します。

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

この記事の例では、Azure AD の資格情報で認証するために、[.NET 用 Azure ID クライアント ライブラリ](https://www.nuget.org/packages/Azure.Identity/)の最新のプレビュー バージョンも使用されます。 Azure ID クライアント ライブラリによって、セキュリティ プリンシパルが認証されます。 その後、認証されたセキュリティ プリンシパルによって、ユーザー委任 SAS を作成できます。 Azure ID クライアント ライブラリの詳細については、「[.NET 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)」を参照してください。

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

Azure ID クライアント ライブラリ経由で Azure AD の資格情報を使用して認証するには、コードの実行場所に応じて、サービス プリンシパルまたはマネージド ID をセキュリティ プリンシパルとして使用します。 コードが開発環境で実行される場合は、テスト目的でサービス プリンシパルを使用します。 コードが Azure で実行される場合は、マネージド ID を使用します。 この記事では、開発環境からコードを実行することを前提として、サービス プリンシパルを使用してユーザー委任 SAS を作成する方法を示します。

Azure CLI を使用してサービス プリンシパルを作成し、RBAC ロールを割り当てるには、[az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを呼び出します。 新しいサービス プリンシパルに割り当てる Azure Storage データ アクセス ロールを指定します。 ロールには、**Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションが含まれている必要があります。 Azure Storage 用に提供されている組み込みロールの詳細については、「[Azure リソースの組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

さらに、ロール割り当て用のスコープを指定します。 サービス プリンシパルでは、ストレージ アカウント レベルで実行される操作であるユーザー委任キーが作成されるため、ロールの割り当てには、ストレージ アカウント、リソース グループ、またはサブスクリプションのレベルでスコープを設定する必要があります。 ユーザー委任 SAS を作成するための RBAC アクセス許可の詳細については、「[ユーザー委任 SAS を作成する (REST API)](/rest/api/storageservices/create-user-delegation-sas)」の「**RBAC によるアクセス許可の割り当て**」セクションを参照してください。

サービス プリンシパルにロールを割り当てるための十分なアクセス許可がない場合は、アカウント所有者または管理者にロールの割り当ての実行を依頼しなければならない可能性があります。

次の例では、Azure CLI を使用して新しいサービス プリンシパルを作成し、**ストレージ BLOB データ閲覧者ロール**をアカウント スコープで割り当てています。

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

`az ad sp create-for-rbac` コマンドによって、サービス プリンシパルのプロパティの一覧が JSON 形式で返されます。 これらの値をコピーして、次の手順で必要な環境変数を作成するために使用できるようにします。

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> RBAC ロールの割り当ての反映には数分かかることがあります。

## <a name="set-environment-variables"></a>環境変数の設定

Azure ID クライアント ライブラリでは、実行時に 3 つの環境変数から値を読み取って、サービス プリンシパルが認証されます。 次の表で、各環境変数に設定する値について説明します。

|環境変数|値
|-|-
|`AZURE_CLIENT_ID`|サービス プリンシパル用のアプリ ID
|`AZURE_TENANT_ID`|サービス プリンシパルの Azure AD テナント ID
|`AZURE_CLIENT_SECRET`|サービス プリンシパル用に生成されたパスワード

> [!IMPORTANT]
> 環境変数を設定したら、コンソール ウィンドウを閉じて再度開きます。 Visual Studio または他の開発環境を使用している場合は、新しい環境変数を登録するために開発環境の再起動が必要である可能性があります。

## <a name="add-using-directives"></a>using ディレクティブを追加する

Azure ID と Azure Storage クライアント ライブラリのプレビューバージョンを使用するために、次の `using` ディレクティブをコードに追加します。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="authenticate-the-service-principal"></a>サービス プリンシパルを認証する

サービス プリンシパルを認証するには、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) クラスのインスタンスを作成します。 `DefaultAzureCredential` コンストラクター によって、先ほど作成した環境変数が読み取られます。

次のコード スニペットでは、認証された資格情報を取得し、それを使用して BLOB ストレージ用のサービス クライアントを作成する方法が示されています。

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>ユーザー委任キーを取得する

すべての SAS はキーによって署名されます。 ユーザー委任 SAS を作成するには、まず、SAS に署名するために使用されるユーザー委任キーを要求する必要があります。 ユーザー委任キーは、サービス SAS またはアカウント SAS に署名するために使用されるアカウント キーに似ていますが、Azure AD の資格情報に依存している点が異なります。 クライアントで OAuth 2.0 トークンを使用してユーザー委任キーが要求されると、ユーザーに代わって Azure Storage によってユーザー委任キーが返されます。

ユーザー委任キーを取得したら、そのキーを使用して、キーの有効期間にわたって、任意の数のユーザー委任共有アクセス署名を作成できます。 ユーザー委任キーは、それを取得するために使用された OAuth 2.0 トークンに依存しないため、キーが有効である限り、トークンを更新する必要はありません。 キーの有効期間として、最大 7 日間を指定できます。

ユーザー委任キーを要求するには、次のいずれかの方法を使用します。

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

次のコード スニペットでは、ユーザー委任キーが取得され、そのプロパティが書き出されます。

```csharp
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>SAS トークンを作成する

次のコード スニペットで、新しい[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) を作成し、ユーザー委任 SAS のパラメーターを指定する方法を示します。 その後、スニペットでは [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) を呼び出して、SAS トークン文字列が取得されます。 最後に、このコードでは、リソース アドレスと SAS トークンを含む完全な URI がビルドされます。

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>例:ユーザー委任 SAS を取得する

次の例は、セキュリティ プリンシパルを認証し、ユーザー委任 SAS を作成するための完全なコードを示しています。

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>例:ユーザー委任 SAS を使用して BLOB を読み取る

次の例では、前の例で作成されたユーザー委任 SAS を、シミュレートされたクライアント アプリケーションからテストします。 SAS が有効であれば、クライアント アプリケーションで BLOB の内容を読み取ることができます。 SAS が無効な場合 (たとえば、有効期限が切れている場合)、Azure Storage によってエラー コード 403 (禁止) が返されます。

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (StorageRequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid, 
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>関連項目

- [ユーザー委任キー取得操作](/rest/api/storageservices/get-user-delegation-key)
- [ユーザー委任 SAS を作成する (REST API)](/rest/api/storageservices/create-user-delegation-sas)
