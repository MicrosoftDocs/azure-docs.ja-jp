---
title: クライアント アプリケーションのトランスポート層セキュリティ (TLS) を構成する
titleSuffix: Azure Storage
description: 最小バージョンのトランスポート層セキュリティ (TLS) を使用して Azure Storage と通信するようにクライアント アプリケーションを構成します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 37b8c79df5b208feea185292fa09c323b64fa27d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89001809"
---
# <a name="configure-transport-layer-security-tls-for-a-client-application"></a>クライアント アプリケーションのトランスポート層セキュリティ (TLS) を構成する

セキュリティのため、Azure Storage アカウントでは、クライアントが最小バージョンのトランスポート層セキュリティ (TLS) を使用して要求を送信することが必要となる場合があります。 クライアントで、必要とされる最小バージョンよりも古いバージョンの TLS が使用されている場合、Azure Storage の呼び出しは失敗します。 たとえば、ストレージ アカウントに TLS 1.2 が必要な場合、TLS 1.1 を使用しているクライアントから送信された要求は失敗します。

この記事では、特定のバージョンの TLS を使用するようにクライアント アプリケーションを構成する方法について説明します。 Azure Storage アカウントに必要な最小バージョンの TLS を構成する方法の詳細については、「[ストレージ アカウントに必要な最小バージョンのトランスポート層セキュリティ (TLS) を構成する](transport-layer-security-configure-minimum-version.md)」を参照してください。

## <a name="configure-the-client-tls-version"></a>クライアント TLS バージョンを構成する

クライアントが特定のバージョンの TLS を使用して要求を送信するには、オペレーティング システムでそのバージョンがサポートされている必要があります。

次の例では、PowerShell または .NET からクライアントの TLS バージョンを 1.2 に設定する方法を示します。 クライアントによって使用される .NET Framework で、TLS 1.2 がサポートされている必要があります。 詳細については、「[TLS 1.2 のサポート](/dotnet/framework/network-programming/tls#support-for-tls-12)」を参照してください。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次のサンプルは、PowerShell クライアントで TLS 1.2 を有効にする方法を示しています。

```powershell
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

# Create a new container.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context
New-AzStorageContainer -Name "sample-container" -Context $ctx
```

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

次のサンプルは、Azure Storage クライアント ライブラリのバージョン 12 を使用して .NET クライアントで TLS 1.2 を有効にする方法を示しています。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Networking.cs" id="Snippet_ConfigureTls12":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

次のサンプルは、Azure Storage クライアント ライブラリのバージョン 11 を使用して .NET クライアントで TLS 1.2 を有効にする方法を示しています。

```csharp
static void EnableTls12()
{
    // Enable TLS 1.2 before connecting to Azure Storage
    System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

    // Add your connection string here.
    string connectionString = "";

    // Connect to Azure Storage and create a new container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
}
```

---

## <a name="verify-the-tls-version-used-by-a-client"></a>クライアントによって使用される TLS のバージョンを確認する

指定されたバージョンの TLS がクライアントによって要求の送信に使用されたことを確認するには、[Fiddler](https://www.telerik.com/fiddler) または同様のツールを使用できます。 Fiddler を開いてクライアントのネットワーク トラフィックのキャプチャを開始してから、前のセクションの例から 1 つを実行します。 次の図に示すように、Fiddler トレースを調べて、正しいバージョンの TLS が要求の送信に使用されたことを確認します。

:::image type="content" source="media/transport-layer-security-configure-client-version/fiddler-trace-tls-version.png" alt-text="要求により使用される TLS バージョンを示す Fiddler トレースを示すスクリーンショット":::

## <a name="next-steps"></a>次のステップ

- [ストレージ アカウントに必要な最小バージョンのトランスポート層セキュリティ (TLS) を構成する](transport-layer-security-configure-minimum-version.md)
- [BLOB ストレージのセキュリティに関する推奨事項](../blobs/security-recommendations.md)
