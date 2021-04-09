---
title: .NET を使用してアカウント SAS を作成する
titleSuffix: Azure Storage
description: .NET クライアント ライブラリを使用して、アカウントの Shared Access Signature (SAS) を作成する方法を学習します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2918b845430a6fc6dc59eca7041c114fc9d06515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97092212"
---
# <a name="create-an-account-sas-with-net"></a>.NET を使用してアカウント SAS を作成する

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

この記事では、ストレージ アカウント キーを使用して、[.NET 用の Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage)でアカウント SAS を作成する方法を示します。

## <a name="create-an-account-sas"></a>アカウント SAS を作成する

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

アカウント SAS は、アカウント アクセス キーを使用して署名されます。 SAS の署名に使用される資格情報を作成するには、[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) クラスを使用します。 次に、新しい [AccountSasBuilder](/dotnet/api/azure.storage.sas.accountsasbuilder) オブジェクトを作成し、[ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters) を呼び出して SAS トークン文字列を取得します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

コンテナーのアカウント SAS を作成するには、[CloudStorageAccount.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) メソッドを呼び出します。

次のコード例では、BLOB およびファイル サービスに有効なアカウント SAS を作成します。これでは、クライアントに、サービスレベルの API にアクセスするための、読み取り、書き込み、および一覧表示のアクセス許可を付与します。 アカウント SAS では、プロトコルを HTTPS に制限しているため、要求は HTTPS で行う必要があります。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

---

## <a name="use-an-account-sas-from-a-client"></a>クライアントからアカウント SAS を使用する

Blob service 用にサービスレベルの API にアクセスするためにアカウント SAS を使用するには、SAS を使って Blob service クライアント オブジェクトを構築し、ストレージ アカウント用に BLOB ストレージ エンドポイントを構築します。

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

このスニペットでは、`<storage-account>` プレースホルダーを実際のストレージ アカウントの名前に置き換えます。

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

---

## <a name="next-steps"></a>次の手順

- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](storage-sas-overview.md)
- [アカウント SAS を作成する](/rest/api/storageservices/create-account-sas)
