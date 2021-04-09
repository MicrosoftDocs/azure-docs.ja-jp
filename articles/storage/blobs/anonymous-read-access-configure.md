---
title: コンテナーと BLOB の匿名パブリック読み取りアクセスを構成する
titleSuffix: Azure Storage
description: ストレージ アカウントの BLOB データへの匿名アクセスを許可または禁止する方法について説明します。 コンテナーのパブリック アクセス設定を変更して、コンテナーと BLOB に匿名でアクセスできるようにします。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: feac7b890c973b1541c5362f860432687082953f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96533878"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>コンテナーと BLOB の匿名パブリック読み取りアクセスを構成する

Azure Storage では、コンテナーと BLOB へのオプションの匿名パブリック読み取りアクセスがサポートされています。 既定では、データへの匿名アクセスは許可されません。 匿名アクセスを明示的に有効にしない限り、コンテナーとその BLOB へのすべての要求には承認が必要です。 匿名アクセスを許可するようにコンテナーのパブリック アクセス レベル設定を構成すると、クライアントは要求の承認なしでそのコンテナー内のデータを読み取ることができます。

> [!WARNING]
> コンテナーがパブリック アクセス用に構成されている場合、任意のクライアントがそのコンテナー内のデータを読み取ることができます。 パブリック アクセスは潜在的なセキュリティ リスクをもたらすため、Microsoft では、シナリオで必要な場合を除き、ストレージ アカウントに対してこれを禁止するようお勧めします。 詳細については、「[コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ](anonymous-read-access-prevent.md)」を参照してください。

この記事では、コンテナーとその BLOB の匿名パブリック読み取りアクセスを構成する方法について説明します。 クライアント アプリケーションから匿名で BLOB データにアクセスする方法については、「[.NET を使用してパブリックのコンテナーと BLOB に匿名でアクセスする](anonymous-read-access-client.md)」を参照してください。

## <a name="about-anonymous-public-read-access"></a>匿名のパブリック読み取りアクセスについて

データへのパブリック アクセスは、既定では常に禁止されています。 パブリック アクセスに影響を与える 2 つの別個の設定があります。

1. **ストレージ アカウントのパブリック アクセスを許可する。** 既定では、ストレージ アカウントにおいて、適切なアクセス許可を持つユーザーは、コンテナーへのパブリック アクセスを有効にすることが許可されます。 そのユーザーが追加の手順によってコンテナーのパブリック アクセスの設定を明示的に構成しない限り、BLOB データへのパブリック アクセスは利用できません。
1. **コンテナーのパブリック アクセス設定を構成する。** 既定では、コンテナーのパブリック アクセスの設定は無効になっています。つまり、コンテナーまたはそのデータへのすべての要求には承認が必要となります。 ストレージ アカウントの匿名アクセスが許可されている場合のみ、適切なアクセス許可を持つユーザーは、コンテナーのパブリック アクセスの設定を変更して、匿名アクセスを有効にできます。

次の表に、これらの両方の設定がコンテナーのパブリック アクセスにどのように影響を与えるかをまとめます。

| パブリック アクセスの設定 | コンテナーのパブリック アクセスが無効 (既定の設定) | コンテナーのパブリック アクセスがコンテナーに対して設定されている | コンテナーのパブリック アクセスが BLOB に対して設定されている |
|--|--|--|--|
| ストレージ アカウントのパブリック アクセスが禁止されている | ストレージ アカウントのどのコンテナーにもパブリック アクセスはない。 | ストレージ アカウントのどのコンテナーにもパブリック アクセスはない。 ストレージ アカウントの設定は、コンテナーの設定をオーバーライドする。 | ストレージ アカウントのどのコンテナーにもパブリック アクセスはない。 ストレージ アカウントの設定は、コンテナーの設定をオーバーライドする。 |
| ストレージ アカウントのパブリック アクセスが許可されている (既定の設定) | このコンテナーへのパブリック アクセスはない (既定の構成)。 | このコンテナーとその BLOB へのパブリック アクセスが許可される。 | このコンテナーの BLOB へのパブリック アクセスは許可されるが、コンテナーそのものに対しては許可されない。 |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>ストレージ アカウントのパブリック読み取りアクセスを許可または禁止する

既定では、ストレージ アカウントの構成において、適切なアクセス許可を持つユーザーは、コンテナーへのパブリック アクセスを有効にすることが許可されます。 パブリック アクセスが許可されている場合、適切なアクセス許可を持つユーザーは、コンテナーのパブリック アクセスの設定を変更して、そのコンテナーのデータへの匿名のパブリック アクセスを有効にできます。 そのユーザーが追加の手順によってコンテナーのパブリック アクセスの設定を明示的に構成しない限り、BLOB データへのパブリック アクセスは利用できません。

コンテナーへのパブリック アクセスは既定で常に無効になっており、匿名の要求を許可するには明示的に構成する必要があることにご注意ください。 ストレージ アカウントの設定に関わりなく、適切なアクセス許可を持つユーザーがこの追加手順によってコンテナー上でパブリック アクセスを有効にしない限り、データへのパブリック アクセスが可能になることはありません。

ストレージ アカウントのパブリック アクセスを禁止すると、そのアカウントのすべてのコンテナーと BLOB への匿名アクセスが妨げられます。 アカウントのパブリック アクセスが禁止されると、コンテナーのパブリック アクセスの設定を構成して、匿名アクセスを許可することはできなくなります。 Microsoft では、ユーザーが匿名で BLOB リソースにアクセスすることが必要なシナリオを除き、セキュリティを向上させるため、ストレージ アカウントのパブリック アクセスは禁止することをお勧めします。

> [!IMPORTANT]
> ストレージ アカウントでパブリック アクセスを禁止すると、そのストレージ アカウント内のすべてのコンテナーのパブリック アクセス設定がオーバーライドされます。 ストレージ アカウントでパブリック アクセスを禁止すると、以後、そのアカウントに対する匿名要求は失敗します。 この設定を変更する前に、ストレージ アカウントのデータに匿名でアクセスしている可能性があるクライアント アプリケーションへの影響を理解しておいてください。 詳細については、「[コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ](anonymous-read-access-prevent.md)」を参照してください。

ストレージ アカウントのパブリック アクセスを許可または禁止するには、アカウントの **AllowBlobPublicAccess** プロパティを構成します。 このプロパティは、Azure Resource Manager デプロイ モデルで作成されたすべてのストレージ アカウントで使用できます。 詳細については、「[ストレージ アカウントの概要](../common/storage-account-overview.md)」を参照してください。

既定では、ストレージ アカウントに **AllowBlobPublicAccess** プロパティは設定されておらず、明示的に設定されるまで値を返しません。 プロパティ値が **null** か **true** の場合、ストレージ アカウントによってパブリック アクセスが許可されます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でストレージ アカウントのパブリック アクセスを許可または禁止するには、次の手順を実行します。

1. Azure Portal のストレージ アカウントに移動します。
1. **[設定]** から **[構成]** 設定を探します。
1. **[Blob public access]\(BLOB パブリック アクセス\)** を **[有効]** または **[無効]** に設定します。

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="アカウントの BLOB パブリック アクセスを許可または禁止する方法を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用したストレージ アカウントのパブリック アクセスを許可または禁止するには、[Azure PowerShell バージョン 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) 以降をインストールします。 次に、新規または既存のストレージ アカウントに **AllowBlobPublicAccess** プロパティを構成します。

次の例では、ストレージ アカウントを作成し、明示的に **AllowBlobPublicAccess** プロパティを **true** に設定します。 次に、ストレージ アカウントを更新して **AllowBlobPublicAccess** プロパティを **false** に設定します。 この例では、各ケースのプロパティ値も取得します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with AllowBlobPublicAccess set to true (or null).
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property for the newly created storage account.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess

# Set AllowBlobPublicAccess set to false
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用したストレージ アカウントのパブリック アクセスを許可または禁止するには、Azure CLI バージョン 2.9.0 以降をインストールします。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。 次に、新規または既存のストレージ アカウントに **allowBlobPublicAccess** プロパティを構成します。

次の例では、ストレージ アカウントを作成し、明示的に **allowBlobPublicAccess** プロパティを **true** に設定します。 次に、ストレージ アカウントを更新して **allowBlobPublicAccess** プロパティを **false** に設定します。 この例では、各ケースのプロパティ値も取得します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --allow-blob-public-access true

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-blob-public-access false

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv
```

# <a name="template"></a>[テンプレート](#tab/template)

テンプレートを使用したストレージ アカウントのパブリック アクセスを許可または禁止するには、**AllowBlobPublicAccess** プロパティを **true** または **false** に設定してテンプレートを作成します。 次の手順は、Azure portal でテンプレートを作成する方法について説明しています。

1. Azure portal で、 **[リソースの作成]** を選択します。
1. **[Marketplace を検索]** で「**template deployment**」と入力し、**Enter** キーを押します。
1. **[テンプレートのデプロイ] (カスタム テンプレートを使用したデプロイ) (プレビュー)** 、 **[作成]** 、 **[エディターで独自のテンプレートを作成する]** の順に選択します。
1. テンプレート エディターで、次の JSON を貼り付けて新しいアカウントを作成し、**AllowBlobPublicAccess** プロパティを **true** または **false** に設定します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'template')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "allowBlobPublicAccess": false
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. テンプレートを保存します。
1. リソース グループ パラメーターを指定し、 **[レビューと作成]** ボタンを選択してテンプレートをデプロイし、**allowBlobPublicAccess** プロパティが構成されたストレージ アカウントを作成します。

---

> [!NOTE]
> ストレージ アカウントのパブリック アクセスを禁止しても、そのストレージ アカウントでホストされている静的な Web サイトには影響しません。 **$web** コンテナーは常にパブリック アクセスが可能です。
>
> ストレージ アカウントのパブリック アクセス設定を更新した後、変更が完全に反映されるまでに最大で 30 秒かかることがあります。

BLOB パブリック アクセスを許可または禁止するには、Azure Storage リソース プロバイダーのバージョンが 2019-04-01 以降である必要があります。 詳細については、「[Azure Storage Resource Provider REST API](/rest/api/storagerp/)」 (Azure ストレージ リソース プロバイダー REST API) をご覧ください。

このセクションの例では、ストレージ アカウントの **AllowBlobPublicAccess** プロパティを読み取り、パブリック アクセスが現在許可されているのか、禁止されているかを確認する方法を示しました。 匿名アクセスが禁止されるようにアカウントのパブリック アクセス設定が構成されていることを確認する方法の詳細については、「[匿名パブリック アクセスの修正](anonymous-read-access-prevent.md#remediate-anonymous-public-access)」を参照してください。

## <a name="set-the-public-access-level-for-a-container"></a>コンテナーのパブリック アクセス レベルの設定

匿名ユーザーにコンテナーとその BLOB に対する読み取りアクセスを許可するには、まずストレージ アカウントのパブリック アクセスを許可してから、コンテナーのパブリック アクセス レベルを設定します。 ストレージ アカウントのパブリック アクセスが拒否されている場合、コンテナーのパブリック アクセスを構成することはできません。

ストレージ アカウントのパブリック アクセスが許可されている場合、次のアクセス許可を使用してコンテナーを構成できます。

- **パブリック読み取りアクセスなし:** コンテナーとその BLOB には、承認された要求を使用しなければアクセスできません。 このオプションは、すべての新しいコンテナーの既定値です。
- **BLOB に限定したパブリック読み取りアクセス:** コンテナー内の BLOB は匿名要求で読み取り可能ですが、コンテナー データは匿名では使用できません。 匿名クライアントはコンテナー内の BLOB を列挙することはできません。
- **コンテナーとその BLOB に対するパブリック読み取りアクセス:** コンテナーと BLOB のデータは、コンテナーのアクセス許可設定とコンテナーのメタデータを除いて、匿名要求によって読み取ることができます。 クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。

個々の BLOB のパブリック アクセス レベルを変更することはできません。 パブリック アクセス レベルは、コンテナー レベルでのみ設定されます。 コンテナーの作成時にコンテナーのパブリック アクセス レベルを設定することも、既存のコンテナーで設定を更新することもできます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal で 1 つ以上の既存のコンテナーのパブリック アクセス レベルを更新するには、次の手順を実行します。

1. Azure portal でご利用のストレージ アカウントの概要に移動します。
1. メニュー ブレードの **[BLOB サービス]** で、 **[コンテナー]** を選択します。
1. パブリック アクセス レベルを設定するコンテナーを選択します。
1. **[アクセス レベルの変更]** ボタンを使用して、パブリック アクセスの設定を表示します。
1. **[パブリック アクセス レベル]** ドロップダウンから目的のパブリック アクセス レベルを選択し、[OK] ボタンをクリックして選択したコンテナーに変更を適用します。

    ![ポータルでパブリック アクセス レベルを設定する方法を示すスクリーンショット](./media/anonymous-read-access-configure/configure-public-access-container.png)

ストレージ アカウントのパブリック アクセスが禁止されている場合、コンテナーのパブリック アクセス レベルは設定できません。 コンテナーのパブリック アクセス レベルを設定しようとすると、アカウントでパブリック アクセスが禁止されているため、設定が無効になります。

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="パブリック アクセスが禁止されているときはコンテナーのパブリック アクセス レベルの設定がブロックされることを示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して 1 つ以上のコンテナーのパブリック アクセス レベルを更新するには、[Set-AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) コマンドを呼び出します。 アカウント キー、接続文字列、または Shared Access Signature (SAS) を渡すことによって、この操作を承認します。 コンテナーのパブリック アクセス レベルを設定する[コンテナー ACL の設定](/rest/api/storageservices/set-container-acl)操作では、Azure AD による承認はサポートされていません。 詳細については、「[Blob および queue データ操作を呼び出す権限](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」を参照してください。

次の例では、パブリック アクセスが無効なコンテナーを作成し、コンテナーとその BLOB への匿名アクセスが許可されるようにコンテナーのパブリック アクセス設定を更新します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
# Set variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Get context object.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

# Create a new container with public access setting set to Off.
$containerName = "<container>"
New-AzStorageContainer -Name $containerName -Permission Off -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx

# Update the container's public access setting to Container.
Set-AzStorageContainerAcl -Container $containerName -Permission Container -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx
```

ストレージ アカウントのパブリック アクセスが禁止されている場合、コンテナーのパブリック アクセス レベルは設定できません。 コンテナーのパブリック アクセス レベルを設定しようとすると、ストレージ アカウントでパブリック アクセスが許可されていないことを知らせるエラーが Azure Storage から返されます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して 1 つ以上のコンテナーのパブリック アクセス レベルを更新するには、[az storage container set permission](/cli/azure/storage/container#az-storage-container-set-permission) コマンドを実行します。 アカウント キー、接続文字列、または Shared Access Signature (SAS) を渡すことによって、この操作を承認します。 コンテナーのパブリック アクセス レベルを設定する[コンテナー ACL の設定](/rest/api/storageservices/set-container-acl)操作では、Azure AD による承認はサポートされていません。 詳細については、「[Blob および queue データ操作を呼び出す権限](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」を参照してください。

次の例では、パブリック アクセスが無効なコンテナーを作成し、コンテナーとその BLOB への匿名アクセスが許可されるようにコンテナーのパブリック アクセス設定を更新します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage container create \
    --name <container-name> \
    --account-name <account-name> \
    --resource-group <resource-group>
    --public-access off \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key

az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key
```

ストレージ アカウントのパブリック アクセスが禁止されている場合、コンテナーのパブリック アクセス レベルは設定できません。 コンテナーのパブリック アクセス レベルを設定しようとすると、ストレージ アカウントでパブリック アクセスが許可されていないことを知らせるエラーが Azure Storage から返されます。

# <a name="template"></a>[テンプレート](#tab/template)

該当なし。

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>複数のコンテナーのパブリック アクセス設定の確認

コンテナーを一覧表示してパブリック アクセス設定を確認することによって、1 つ以上のストレージ アカウントのどのコンテナーがパブリック アクセス用に構成されているかを確認できます。 この方法は、ストレージ アカウントに多数のコンテナーが含まれていない場合や、少数のストレージ アカウントにまたがって設定を確認する場合に実用的な選択肢です。 ただし、多数のコンテナーを列挙しようとすると、パフォーマンスが低下する可能性があります。

次の例では、PowerShell を使用して、ストレージ アカウントのすべてのコンテナーのパブリック アクセス設定を取得します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>次のステップ

- [コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ](anonymous-read-access-prevent.md)
- [.NET を使用してパブリックのコンテナーと BLOB に匿名でアクセスする](anonymous-read-access-client.md)
- [Azure Storage へのアクセスを承認する](../common/storage-auth.md)
