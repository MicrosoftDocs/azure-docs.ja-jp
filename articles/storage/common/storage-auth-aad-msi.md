---
title: Azure Active Directory と Azure リソースのマネージド ID を使用して BLOB およびキューへのアクセスを承認する - Azure Storage
description: Azure の BLOB とキュー ストレージでは、Azure Active Directory と Azure リソースのマネージド ID を使用したアクセスの承認がサポートされています。 Azure リソースのマネージド ID を使用して、Azure の仮想マシン、関数アプリ、仮想マシン スケール セット、およびその他で実行されているアプリケーションからの BLOB とキューへのアクセスを承認することができます。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bed661873b195694c2fd9b30b1d98a3ecf1fc8a4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671117"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Azure Active Directory と Azure リソースのマネージド ID を使用して BLOB とキューへのアクセスを承認する

Azure の Blob およびキュー ストレージは、Azure Active Directory (Azure AD) 認証を[ Azure リソースのマネージド ID ](../../active-directory/managed-identities-azure-resources/overview.md)を使用してサポートします。 Azure リソースのマネージド ID により、Azure 仮想マシン (VM) で実行されているアプリケーション、関数アプリ、仮想マシン スケール セット、およびその他のサービスから Azure AD 資格情報を使用して、BLOB およびキューのデータへのアクセスを認証することができます。 Azure リソースのマネージド ID を Azure AD 認証と一緒に使用することで、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。  

この記事では、マネージド ID を使用して Azure VM から BLOB またはキュー データへのアクセスを認証する方法について示します。

## <a name="enable-managed-identities-on-a-vm"></a>VM 上のマネージド ID を有効にする

Azure リソースのマネージド ID を使用して VM から BLOB およびキューへのアクセスを認証するには、最初に VM で Azure リソースのマネージド ID を有効にする必要があります。 Azure リソースのマネージド ID を有効にする方法については、次の記事のいずれかを参照してください。

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager クライアント ライブラリ](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Azure AD のマネージド ID にアクセス許可を付与する

お使いの Azure Storage アプリケーションでマネージド ID からの BLOB または Queue サービスへの要求を認証するには、最初にそのマネージド ID に対してロールベースのアクセス制御 (RBAC) の設定を構成します。 BLOB とキュー データへのアクセス許可を含む RBAC ロールは、Azure Storage によって定義されます。 RBAC ロールがマネージド ID に割り当てられている場合は、適切なスコープで BLOB またはキュー データへのこれらのアクセス許可がそのマネージド ID に付与されます。

RBAC ロールの割り当てに関する詳細については、次のいずれかの記事をご覧ください。

- [Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-portal.md)
- [RBAC と Azure CLI を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-cli.md)
- [RBAC と PowerShell を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>Azure Storage リソース ID

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET コード例: ブロック BLOB を作成する

このコード例は、Azure AD から OAuth 2.0 トークンを取得し、それを使用してブロック BLOB の作成要求を承認する方法を示しています。 この例を動作させるには、まず前のセクションで説明されている手順に従ってください。

[!INCLUDE [storage-app-auth-lib-include](../../../includes/storage-app-auth-lib-include.md)]

### <a name="add-the-callback-method"></a>コールバック メソッドを追加する

コールバック メソッドは、トークンの有効期限を確認して、必要に応じて更新します。

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

### <a name="get-a-token-and-create-a-block-blob"></a>トークンを取得し、ブロック BLOB を作成する

App Authentication ライブラリにより **AzureServiceTokenProvider** クラスが提供されます。 このクラスのインスタンスは、トークンを取得して有効期限が切れる前にトークンを更新するコールバックに渡すことができます。

次の例は、トークンを取得して、それを使用して新しい BLOB を作成し、同じトークンを使用して BLOB を読み取ります。

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = await TokenRenewerAsync(azureServiceTokenProvider,CancellationToken.None);

// Create storage credentials using the initial token, and connect the callback function
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token,
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider,
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName),
                                            storageCredentials);

// Upload text to the blob.
await blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage.
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}",
                        DateTimeOffset.UtcNow,
                        await blob.DownloadTextAsync());

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

App Authentication ライブラリに関する詳細については、「[.NET を使用した Azure Key Vault に対するサービス間認証](../../key-vault/service-to-service-authentication.md)」を参照してください。

アクセス トークンを取得する方法についてさらに学習するには、「[アクセストークンを取得するために Azure VM 上の Azure リソースのマネージド ID を使用する方法](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)」を参照してください。

> [!NOTE]
> Azure AD を使用して BLOB またはキュー データに対する要求を承認するには、それらの要求に HTTPS を使用する必要があります。

## <a name="next-steps"></a>次の手順

- Azure Storage の RBAC ロールについては、[RBAC を使用したストレージ データへのアクセス権の管理](storage-auth-aad-rbac.md)に関するページをご覧ください。
- ストレージ アプリケーション内からコンテナーやキューへのアクセスを承認する方法については、[ストレージ アプリケーションで Azure AD を使用する](storage-auth-aad-app.md)方法に関するページを参照してください。
- Azure AD 資格情報を使用して Azure CLI と PowerShell のコマンドを実行する方法については、「[Azure AD ID を使用し、CLI または PowerShell で BLOB とキューのデータにアクセスする](storage-auth-aad-script.md)」を参照してください。
