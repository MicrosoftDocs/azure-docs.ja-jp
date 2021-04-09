---
title: マネージド ID を使用してデータへのアクセスを認証する
titleSuffix: Azure Storage
description: Azure リソースのマネージド ID を使用して、Azure VM や関数アプリなどで実行されているアプリケーションからの BLOB とキュー データへのアクセスを認証します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 552d2587f35ed391b470c6d5b1693b79fd57306b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879580"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Azure リソースに対するマネージド ID を使用して BLOB およびキュー データへのアクセスを認証する

Azure の Blob およびキュー ストレージは、Azure Active Directory (Azure AD) 認証を[ Azure リソースのマネージド ID ](../../active-directory/managed-identities-azure-resources/overview.md)を使用してサポートします。 Azure リソースのマネージド ID により、Azure 仮想マシン (VM) で実行されているアプリケーション、関数アプリ、仮想マシン スケール セット、およびその他のサービスから Azure AD 資格情報を使用して、BLOB およびキューのデータへのアクセスを認証することができます。 Azure リソースのマネージド ID を Azure AD 認証と一緒に使用することで、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。  

この記事では、Azure リソースにマネージド ID を使用して Azure VM から Blob またはキュー データへのアクセスを認証する方法について示します。 また、開発環境でコードをテストする方法についても説明します。

## <a name="enable-managed-identities-on-a-vm"></a>VM 上のマネージド ID を有効にする

Azure リソースのマネージド ID を使用して VM から BLOB およびキューへのアクセスを認証するには、最初に VM で Azure リソースのマネージド ID を有効にする必要があります。 Azure リソースのマネージド ID を有効にする方法については、次の記事のいずれかを参照してください。

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager クライアント ライブラリ](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

マネージド ID の詳細については、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

## <a name="authenticate-with-the-azure-identity-library"></a>Azure ID ライブラリを使用した認証

Azure ID クライアント ライブラリでは、[Azure SDK](https://github.com/Azure/azure-sdk) に対する Azure AD トークン認証のサポートが提供されています。 .NET、Java、Python、および JavaScript 用の最新バージョンの Azure Storage クライアント ライブラリは、Azure ID ライブラリと統合され、Azure Storage 要求を承認するための OAuth 2.0 トークンを取得するための簡単で安全な手段を提供します。

Azure ID クライアント ライブラリの利点は、アプリケーションが開発環境または Azure のどちらで実行されているかにかかわらず、同じコードを使用して認証できることです。 .NET 対応の Azure ID クライアント ライブラリでは、セキュリティ プリンシパルが認証されます。 コードが Azure 上で実行されている場合、セキュリティ プリンシパルは Azure リソースに対するマネージド ID です。 開発環境では、マネージド ID が存在しないため、クライアント ライブラリはテスト目的でユーザーまたはサービス プリンシパルのいずれかを認証します。

認証後、Azure ID クライアント ライブラリでは、トークン資格情報を取得します。 このトークン資格情報は、Azure Storage に対して操作を実行するために作成するサービス クライアント オブジェクトにカプセル化されます。 このライブラリは、適切なトークン資格情報を取得することにより、これをユーザーに代わってシームレスに処理します。

.NET 用 Azure ID クライアント ライブラリの詳細については、「[.NET 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)」を参照してください。 Azure ID クライアント ライブラリのリファレンス ドキュメントについては、「[Azure.Identity 名前空間](/dotnet/api/azure.identity)」を参照してください。

### <a name="assign-azure-roles-for-access-to-data"></a>データにアクセスするための Azure ロールを割り当る

Azure AD セキュリティ プリンシパルが Blob またはキュー データにアクセスしようとする場合、そのセキュリティ プリンシパルはリソースへのアクセス許可を保持している必要があります。 セキュリティ プリンシパルが Azure 内のマネージド ID であるか、開発環境でコードを実行している Azure AD ユーザー アカウントであるかにかかわらず、Azure Storage での BLOB またはキュー データへのアクセスを許可する Azure ロールをセキュリティ プリンシパルに割り当てる必要があります。 Azure RBAC 経由でのアクセス許可の割り当てについては、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)」の「**アクセス権に Azure ロールを割り当てる**」というタイトルのセクションを参照してください。

> [!NOTE]
> Azure ストレージ アカウントを作成するとき、Azure AD を介してデータにアクセスするためのアクセス許可は自動的に割り当てられません。 Azure Storage の Azure ロールを自分自身に明示的に割り当てる必要があります。 これは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいはコンテナーまたはキューのレベルで割り当てることができます。
>
> データ アクセスのためのロールを自分に割り当てる前に、Azure portal 経由でストレージ アカウントのデータにアクセスできるようになります。これは、Azure portal もデータ アクセスにアカウント キーを使用できるためです。 詳細については、「[Azure portal で BLOB データへのアクセスの承認方法を選択する](../blobs/authorize-data-operations-portal.md)」を参照してください。

### <a name="authenticate-the-user-in-the-development-environment"></a>開発環境でユーザーを認証する

開発環境でコードを実行している場合は、認証が自動的に処理されるか、使用しているツールに応じてブラウザー ログインが必要になることがあります。 たとえば、Microsoft Visual Studio では、アクティブな Azure AD ユーザー アカウントが自動的に認証に使用されるように、シングル サインオン (SSO) がサポートされています。 SSO の詳細については、[アプリケーションへのシングル サインオン](../../active-directory/manage-apps/what-is-single-sign-on.md)に関するページを参照してください。

他の開発ツールでは、Web ブラウザー経由でログインするように求められる場合があります。

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>開発環境でサービス プリンシパルを認証する

開発環境で Web ブラウザーによるシングル サインオンまたはログインがサポートされていない場合は、サービス プリンシパルを使用して開発環境から認証を受けることができます。

#### <a name="create-the-service-principal"></a>サービス プリンシパルを作成する

Azure CLI を使用してサービス Azure ロールを割り当てるには、[az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを呼び出します。 新しいサービス プリンシパルに割り当てる Azure Storage データ アクセス ロールを指定します。 さらに、ロール割り当て用のスコープを指定します。 Azure Storage 用に提供されている組み込みロールの詳細については、「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

サービス プリンシパルにロールを割り当てるための十分なアクセス許可がない場合は、アカウント所有者または管理者にロールの割り当ての実行を依頼しなければならない可能性があります。

次の例では、Azure CLI を使用して新しいサービス プリンシパルを作成し、**ストレージ BLOB データ閲覧者ロール** をアカウント スコープで割り当てています。

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
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
> Azure ロールの割り当ての反映には数分かかることがあります。

#### <a name="set-environment-variables"></a>環境変数の設定

Azure ID クライアント ライブラリでは、実行時に 3 つの環境変数から値を読み取って、サービス プリンシパルが認証されます。 次の表で、各環境変数に設定する値について説明します。

|環境変数|値
|-|-
|`AZURE_CLIENT_ID`|サービス プリンシパル用のアプリ ID
|`AZURE_TENANT_ID`|サービス プリンシパルの Azure AD テナント ID
|`AZURE_CLIENT_SECRET`|サービス プリンシパル用に生成されたパスワード

> [!IMPORTANT]
> 環境変数を設定したら、コンソール ウィンドウを閉じて再度開きます。 Visual Studio または他の開発環境を使用している場合は、新しい環境変数を登録するために開発環境の再起動が必要である可能性があります。

詳細については、[ポータルでの Azure アプリ用の ID 作成](../../active-directory/develop/howto-create-service-principal-portal.md)に関するページを参照してください。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET コード例: ブロック BLOB の作成

Azure ID と Azure Storage クライアント ライブラリを使用するために、次の `using` ディレクティブをコードに追加します。

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Azure Storage への要求を承認するためにコード上で使用できるトークン資格情報を取得するには、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) クラスのインスタンスを作成します。 次のコード例では、認証済みのトークン資格情報を取得し、それを使用してサービス クライアント オブジェクトを作成した後、サービス クライアントを使用して新しい Blob をアップロードする方法を示します。

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Azure AD を使用して BLOB またはキュー データに対する要求を承認するには、それらの要求に HTTPS を使用する必要があります。

## <a name="next-steps"></a>次のステップ

- [Azure RBAC を使用してストレージ データへのアクセス権を管理する](./storage-auth-aad-rbac-portal.md)。
- [ストレージ アプリケーションで Azure AD を使用する](storage-auth-aad-app.md)。
- [Azure AD 資格情報を使用して PowerShell コマンドを実行して BLOB データにアクセスする](../blobs/authorize-data-operations-powershell.md)
- [チュートリアル: マネージド ID を使用して App Service からストレージにアクセスする](../../app-service/scenario-secure-app-access-storage.md)