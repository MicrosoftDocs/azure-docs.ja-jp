---
title: .NET を使用してアカウント SAS を作成する - Azure Storage
description: .NET クライアント ライブラリを使用して、アカウントの Shared Access Signature (SAS) を作成する方法を学習します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8d627fd905741c76dd34eb4270f49d46b93abb1
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036098"
---
# <a name="create-an-account-sas-with-net"></a>.NET を使用してアカウント SAS を作成する

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

この記事では、ストレージ アカウント キーを使用して、[.NET 用の Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage/client)でアカウント SAS を作成する方法を示します。

## <a name="create-an-account-sas"></a>アカウント SAS を作成する

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

## <a name="use-an-account-sas-from-a-client"></a>クライアントからアカウント SAS を使用する

Blob service 用にサービスレベルの API にアクセスするためにアカウント SAS を使用するには、SAS を使って Blob service クライアント オブジェクトを構築し、ストレージ アカウント用に BLOB ストレージ エンドポイントを構築します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

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

## <a name="next-steps"></a>次の手順

- [Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](storage-sas-overview.md)
- [アカウント SAS を作成する](/rest/api/storageservices/create-account-sas)
