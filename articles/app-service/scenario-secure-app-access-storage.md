---
title: チュートリアル - マネージド ID を使用して Web アプリからストレージにアクセスする | Azure
description: このチュートリアルでは、マネージド ID を使用して、アプリに代わって Azure Storage にアクセスする方法について説明します。
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 72b1d4fe864c23c0ac065e47d96ab0c78866defa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435843"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>チュートリアル:Web アプリから Azure Storage にアクセスする

マネージド ID を使用して、Azure App Service で実行されている Web アプリ (サインインしているユーザーではありません) に代わって Azure Storage にアクセスする方法について説明します。

:::image type="content" alt-text="ストレージにアクセスする方法を示す図。" source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Web アプリから Azure データ プレーン (Azure Storage、Azure SQL Database、Azure Key Vault、またはその他のサービス) へのアクセスを追加するとします。 共有キーを使用することもできますが、その場合、シークレットを作成、デプロイ、および管理できるユーザーの運用上のセキュリティについて配慮する必要があります。 また、キーが GitHub にチェックインされる可能性もあります。ハッカーはそのスキャン方法を知っています。 Web アプリにデータへのアクセスを許可するより安全な方法では、[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用します。

Azure Active Directory (Azure AD) のマネージド ID を使用すると、App Service は、アプリの資格情報を必要とせずに、ロールベースのアクセス制御 (RBAC) を使用してリソースにアクセスできます。 マネージド ID を対象の Web アプリに割り当てると、Azure では証明書の作成と配布が行われます。 シークレットまたはアプリの資格情報の管理について心配する必要はありません。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Web アプリ上でシステム割り当てマネージド ID を作成する。
> * ストレージ アカウントと Azure Blob Storage コンテナーを作成する。
> * マネージド ID を使用して Web アプリからストレージにアクセスする。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* [App Service の認証および承認モジュールが有効になっている](scenario-secure-app-authentication-app-service.md) Azure App Service で実行されている Web アプリケーション。

## <a name="enable-managed-identity-on-an-app"></a>アプリでマネージド ID を有効にする

Visual Studio を使用して Web アプリを作成して発行すると、アプリでマネージド ID が有効になります。 App Service で、左ペインの **[ID]** を選択し、 **[システム割り当て済み]** を選択します。 **[Status]\(状態\)** が **[オン]** に設定されていることを確認します。 そうでない場合は、 **[保存]** を選択し、 **[はい]** を選択して、システム割り当てマネージド ID を有効にします。 マネージド ID が有効になると、状態が **[オン]** に設定され、オブジェクト ID が使用可能になります。

:::image type="content" alt-text="[システム割り当て済み] ID オプションを示すスクリーンショット。" source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

この手順により、 **[認証/承認]** ペインで作成されたアプリ ID とは異なる新しいオブジェクト ID が作成されます。 システム割り当てマネージド ID のオブジェクト ID をコピーしておきます。 この情報は後で必要になります。

## <a name="create-a-storage-account-and-blob-storage-container"></a>ストレージ アカウントと Blob Storage コンテナーを作成する

これで、ストレージ アカウントと Blob Storage コンテナーを作成する準備が整いました。

すべてのストレージ アカウントは、Azure リソース グループに属している必要があります。 リソース グループは、Azure サービスをグループ化するための論理コンテナーです。 ストレージ アカウントを作成するときに、新しいリソース グループを作成するか、既存のリソース グループを使用するかを選択できます。 この記事では、新しいリソース グループを作成する方法を示します。

汎用 v2 ストレージ アカウントでは、すべての Azure Storage サービス (BLOB、ファイル、キュー、テーブル、ディスク) へのアクセスが提供されます。 ここで説明する手順では汎用 v2 ストレージ アカウントを作成しますが、作成手順はどの種類のストレージ アカウントでも似ています。

Azure Storage 内の BLOB はコンテナーにまとめられます。 このチュートリアルの後半で BLOB をアップロードする前に、まずコンテナーを作成する必要があります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で汎用 v2 ストレージ アカウントを作成するには、次の手順に従います。

1. Azure portal のメニューで、**[すべてのサービス]** を選択します。 リソースの一覧で、「**Storage Accounts**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[ストレージ アカウント]** を選択します。

1. 表示された **[ストレージ アカウント]** ウィンドウで、 **[追加]** を選択します。

1. ストレージ アカウントを作成するサブスクリプションを選択します。

1. **[リソース グループ]** フィールドで、ドロップダウン メニューから、対象の Web アプリが含まれているリソース グループを選択します。

1. 次に、ストレージ アカウントの名前を入力します。 選択する名前は Azure 全体で一意である必要があります。 また、名前の長さは 3 から 24 文字とし、数字と小文字のみを使用できます。

1. ストレージ アカウントの場所を選択するか、または既定の場所を使います。

1. 以下のフィールドは既定値に設定されたままにします。

    |フィールド|値|
    |--|--|
    |デプロイメント モデル|リソース マネージャー|
    |パフォーマンス|Standard|
    |アカウントの種類|StorageV2 (汎用 v2)|
    |レプリケーション|読み取りアクセス地理冗長ストレージ (RA-GRS)|
    |アクセス層|ホット|

1. **[確認および作成]** を選択して、ストレージ アカウントの設定を確認し、アカウントを作成します。

1. **［作成］** を選択します

Azure Storage で Blob Storage コンテナーを作成するには、次の手順に従います。

1. Azure portal で新しいストレージ アカウントに移動します。

1. ストレージ アカウントの左側のメニューで、 **[Blob service]** セクションまでスクロールし、 **[コンテナー]** を選択します。

1. **[+ コンテナー]** ボタンを選択します。

1. 新しいコンテナーの名前を入力します。 コンテナー名は小文字である必要があり、英文字または数字で始まる必要があり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。

1. コンテナーにパブリック アクセスのレベルを設定します。 既定のレベルは **[ プライベート (匿名アクセスなし)]** です。

1. **[OK]** を選択してコンテナーを作成します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

汎用 v2 ストレージ アカウントと Blob Storage コンテナーを作成するには、次のスクリプトを実行します。 対象の Web アプリが含まれているリソース グループの名前を指定します。 ストレージ アカウントの名前を入力します。 選択する名前は Azure 全体で一意である必要があります。 また、名前の長さは 3 から 24 文字とし、数字と小文字のみを使用できます。

対象のストレージ アカウントの場所を指定します。 対象のサブスクリプションに有効な場所のリストを表示するには、```Get-AzLocation | select Location``` を実行します。 コンテナー名は小文字である必要があり、英文字または数字で始まる必要があり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。

山かっこ内のプレースホルダーの値は、忘れずに実際の値に置き換えてください。

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

汎用 v2 ストレージ アカウントと Blob Storage コンテナーを作成するには、次のスクリプトを実行します。 対象の Web アプリが含まれているリソース グループの名前を指定します。 ストレージ アカウントの名前を入力します。 選択する名前は Azure 全体で一意である必要があります。 また、名前の長さは 3 から 24 文字とし、数字と小文字のみを使用できます。 

対象のストレージ アカウントの場所を指定します。 コンテナー名は小文字である必要があり、英文字または数字で始まる必要があり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。

次の例では、Azure AD アカウントを使用して、コンテナーの作成操作を承認します。 コンテナーを作成する前に、ストレージ BLOB データ共同作成者ロールを自分に割り当てます。 自分がアカウント オーナーである場合でも、ストレージ アカウントに対してデータ操作を実行するための明示的なアクセス許可が必要となります。

山かっこ内のプレースホルダーの値は、忘れずに実際の値に置き換えてください。

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>ストレージ アカウントへのアクセスを許可する

BLOB の作成、読み取り、または削除を行う前に、ストレージ アカウントへのアクセスを Web アプリに許可する必要があります。 前の手順では、App Service で実行されている Web アプリをマネージド ID を使用して構成しました。 Azure RBAC を使用すると、セキュリティ プリンシパルと同様に、別のリソースへのアクセス権をマネージド ID に付与できます。 ストレージ BLOB データ共同作成者ロールにより、(システム割り当てマネージド ID で表される) Web アプリに、BLOB コンテナーとデータへの読み取り、書き込み、削除アクセス権が付与されます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[Azure portal](https://portal.azure.com) で、Web アプリにアクセスを許可するストレージ アカウントに移動します。 左ペインで **[アクセス制御 (IAM)]** を選択し、 **[ロールの割り当て]** を選択します。 ストレージ アカウントへのアクセス権を持つユーザーのリストが表示されます。 ここで、ストレージ アカウントへのアクセスを必要とするアプリ サービスであるロボットに、ロールの割り当てを追加します。 **[追加]**  >  **[ロール割り当ての追加]** の順に選択します。

**[ロール]** で、 **[ストレージ BLOB データ共同作成者]** を選択して、対象の Web アプリにストレージ BLOB の読み取りアクセス権を付与します。 **[アクセスの割り当て先]** で、 **[App Service]** を選択します。 **[サブスクリプション]** で、対象のサブスクリプションを選択します。 次に、アクセスを提供する App Service を選択します。 **[保存]** を選択します。

:::image type="content" alt-text="[ロールの割り当ての追加] 画面を示すスクリーンショット。" source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

これで、Web アプリからストレージ アカウントにアクセスできるようになりました。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次のスクリプトを実行して、(システム割り当てマネージド ID で表される) Web アプリに、ストレージ アカウントのストレージ BLOB データ共同作成者ロールを割り当てます。

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次のスクリプトを実行して、(システム割り当てマネージド ID で表される) Web アプリに、ストレージ アカウントのストレージ BLOB データ共同作成者ロールを割り当てます。

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Blob Storage にアクセスする (.NET)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) クラスは、Azure Storage に対する要求をコードで承認するためにトークン資格情報を取得する際に使用されます。 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) クラスのインスタンスを作成します。これは、マネージド ID を使用し、トークンを取得してサービス クライアントにアタッチします。 次のコード例では、認証済みのトークン資格情報を取得し、それを使用して、新しい BLOB をアップロードするサービス クライアント オブジェクトを作成します。

このコードをサンプル アプリケーションの一部として見る場合は、[GitHub 上のサンプル](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity)を参照してください。

### <a name="install-client-library-packages"></a>クライアント ライブラリ パッケージをインストールする

Blob Storage を使用するための [Blob Storage NuGet パッケージ](https://www.nuget.org/packages/Azure.Storage.Blobs/)と、Azure AD の資格情報で認証するための [.NET 用 Azure Identity クライアント ライブラリ NuGet パッケージ](https://www.nuget.org/packages/Azure.Identity/)をインストールします。 クライアント ライブラリは、.NET Core コマンド ライン インターフェイスまたは Visual Studio のパッケージ マネージャー コンソールを使用してインストールします。

# <a name="command-line"></a>[コマンド ライン](#tab/command-line)

コマンド ラインを開き、プロジェクト ファイルが含まれているディレクトリに切り替えます。

インストール コマンドを実行します。

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[パッケージ マネージャー](#tab/package-manager)

Visual Studio でプロジェクトまたはソリューションを開き、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** コマンドを使用してコンソールを開きます。

インストール コマンドを実行します。
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>例

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
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
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了し、Web アプリや関連するリソースが不要になった場合は、[作成したリソースをクリーンアップ](scenario-secure-app-clean-up-resources.md)します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
>
> * システム割り当てマネージド ID を作成する。
> * ストレージ アカウントと Blob Storage コンテナーを作成する。
> * マネージド ID を使用して Web アプリからストレージにアクセスする。

> [!div class="nextstepaction"]
> [ユーザーに代わって App Service から Microsoft Graph にアクセスする](scenario-secure-app-access-microsoft-graph-as-user.md)