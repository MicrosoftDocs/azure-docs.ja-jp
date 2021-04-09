---
title: 暗号化スコープの作成と管理 (プレビュー)
description: コンテナーまたは BLOB レベルで BLOB データを分離するための暗号化スコープを作成する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d5590ff275ce821c81f5751f4d92972c49adaafc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102209593"
---
# <a name="create-and-manage-encryption-scopes-preview"></a>暗号化スコープの作成と管理 (プレビュー)

暗号化スコープ (プレビュー) を使用すると、個々の BLOB またはコンテナー レベルで暗号化を管理できます。 暗号化スコープでは、ストレージ アカウント内のセキュリティで保護されたエンクレーブで BLOB データを分離します。 暗号化スコープを使用すると、異なる顧客が所有する、同じストレージ アカウントに存在するデータの間にセキュリティで保護された境界を作成できます。 暗号化スコープの詳細については、[Blob Storage の暗号化スコープ (プレビュー)](encryption-scope-overview.md) に関する記事を参照してください。

この記事では、暗号化スコープを作成する方法について説明します。 また、BLOB またはコンテナーを作成するときに暗号化スコープを指定する方法についても説明します。

> [!IMPORTANT]
> 暗号化スコープは現在、**プレビュー** の段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>
> 予期しないコストを回避するには、現在必要ではない暗号化スコープを必ず無効にしてください。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>暗号化スコープの作成

暗号化スコープは、Microsoft マネージド キーを使用して作成することも、Azure Key Vault または Azure Key Vault Managed Hardware Security Model (HSM) (プレビュー) に格納されているカスタマー マネージド キーを使用して作成することもできます。 カスタマー マネージド キーを使用して暗号化スコープを作成するには、まずキー コンテナーまたは Managed HSM を作成し、スコープで使用するキーを追加する必要があります。 キー コンテナーまたは Managed HSM では、消去保護が有効になっている必要があり、これがストレージ アカウントと同じリージョンに存在する必要があります。

暗号化スコープは、作成時に自動的に有効になります。 作成した暗号化スコープは、BLOB の作成時に指定できます。 コンテナーを作成するときに既定の暗号化スコープを指定することもできます。これは、コンテナー内のすべての BLOB に自動的に適用されます。

# <a name="portal"></a>[ポータル](#tab/portal)

Azure portal で暗号化スコープを作成するには、次の手順を実行します。

1. Azure Portal のストレージ アカウントに移動します。
1. **[暗号化]** 設定を選択します。
1. **[Encryption Scopes]\(暗号化スコープ\)** タブを選択します。
1. **[追加]** ボタンをクリックして、新しい暗号化スコープを追加します。
1. **[Encryption Scopes]\(暗号化スコープ\)** の作成ウィンドウで、新しいスコープの名前を入力します。
1. 暗号化の種類として、 **[Microsoft-managed keys]\(Microsoft マネージド キー\)** または **[Customer-managed keys]\(カスタマー マネージド キー\)** を選択します。
    - **[Microsoft-managed keys]\(Microsoft マネージド キー\)** を選択した場合は、 **[作成]** をクリックして暗号化スコープを作成します。
    - **[Customer-managed keys]\(カスタマー マネージド キー\)** を選択した場合は、次の図に示すように、この暗号化スコープで使用するキー コンテナーまたは Managed HSM、キー、およびキーのバージョンを指定します。

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Azure portal で暗号化スコープを作成する方法を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して暗号化スコープを作成するには、まず Az.Storage プレビュー モジュール バージョンをインストールします。 最新のプレビュー バージョンを使用することをお勧めしますが、暗号化スコープはバージョン 1.13.4-preview 以降でサポートされています。 Az. Storage モジュールの他のバージョンをすべて削除します。

次に示すのは、Az.Storage の [2.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) モジュールをインストールするコマンドです。

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.1.1-preview -AllowPrerelease
```

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Microsoft マネージド キーによって保護される暗号化スコープを作成する

Microsoft マネージド キーによって保護される新しい暗号化スコープを作成するには、`-StorageEncryption` パラメーターを使用して **New-AzStorageEncryptionScope** コマンドを呼び出します。

例中のプレースホルダーを独自の値に置き換えてください。

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>カスタマー マネージド キーによって保護される暗号化スコープを作成する

キー コンテナーまたは Managed HSM に格納されているカスタマー マネージド キーによって保護される新しい暗号化スコープを作成するには、まず、ストレージ アカウントのカスタマー マネージド キーを構成します。 マネージド ID をストレージ アカウントに割り当ててから、そのマネージド ID を使用してキー コンテナーまたは Managed HSM のアクセス ポリシーを構成して、ストレージ アカウントにそれへのアクセス許可が付与されるようにする必要があります。

暗号化スコープで使用するためにカスタマー マネージド キーを構成するには、消去保護がキー コンテナーまたは Managed HSM で有効になっている必要があります。 キー コンテナーまたは Managed HSM は、ストレージ アカウントと同じリージョンに存在する必要があります。

例中のプレースホルダーを独自の値に置き換えてください。

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri-with-version>"
$scopeName2 = "customer2scope"


# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

次に、`-KeyvaultEncryption` パラメーターを使用して **New-AzStorageEncryptionScope** コマンドを呼び出し、キー URI を指定します。 キーのバージョンは必ずキー URI に含めてください。 例中のプレースホルダーを独自の値に置き換えてください。

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI で暗号化スコープを作成するには、最初に Azure CLI バージョン 2.4.0 以降をインストールします。

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Microsoft マネージド キーによって保護される暗号化スコープを作成する

Microsoft マネージド キーによって保護される新しい暗号化スコープを作成するには、`--key-source` パラメーターを `Microsoft.Storage` に指定して、[az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) コマンドを呼び出します。 プレースホルダー値をお客様独自の値に置き換えてください。

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>カスタマー マネージド キーによって保護される暗号化スコープを作成する

Microsoft マネージド キーによって保護される新しい暗号化スコープを作成するには、`--key-source` パラメーターを `Microsoft.Storage` に指定して、[az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) コマンドを呼び出します。 プレースホルダー値をお客様独自の値に置き換えてください。

キー コンテナーまたは Managed HSM にあるカスタマー マネージド キーによって保護される新しい暗号化スコープを作成するには、まず、ストレージ アカウントのカスタマー マネージド キーを構成します。 ストレージ アカウントにアクセスするためのアクセス許可が付与されるように、マネージド ID をストレージ アカウントに割り当ててから、そのマネージド ID を使用してキー コンテナーのアクセス ポリシーを構成する必要があります。 詳細については、「[Azure Storage 暗号化のカスタマー マネージド キー](../common/customer-managed-keys-overview.md)」を参照してください。

暗号化スコープで使用するためにカスタマー マネージド キーを構成するには、消去保護がキー コンテナーまたは Managed HSM で有効になっている必要があります。 キー コンテナーまたは Managed HSM は、ストレージ アカウントと同じリージョンに存在する必要があります。

例中のプレースホルダーを独自の値に置き換えてください。

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

次に、`--key-uri` パラメーターを使用して **az storage account encryption-scope create** コマンドを呼び出し、キー URI を指定します。 キーのバージョンは必ずキー URI に含めてください。 例中のプレースホルダーを独自の値に置き換えてください。

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

キー コンテナーにあるカスタマー マネージド キーを使用して Azure Storage 暗号化を構成する方法については、「[Azure Key Vault に格納されているカスタマー マネージド キーによる暗号化を構成する](../common/customer-managed-keys-configure-key-vault.md)」を参照してください。 Managed HSM にあるカスタマー マネージド キーを構成する方法については、「[Azure Key Vault Managed HSM (プレビュー) に格納されているカスタマー マネージド キーによる暗号化を構成する](../common/customer-managed-keys-configure-key-vault-hsm.md)」を参照してください。

## <a name="list-encryption-scopes-for-storage-account"></a>ストレージ アカウントの暗号化スコープを一覧表示する

# <a name="portal"></a>[ポータル](#tab/portal)

Azure portal でストレージ アカウントの暗号化スコープを表示するには、ストレージ アカウントの **[Encryption Scopes]\(暗号化スコープ\)** 設定に移動します。 このウィンドウでは、暗号化スコープを有効または無効にしたり、暗号化スコープのキーを変更したりできます。

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Azure portal の暗号化スコープの一覧を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してストレージ アカウントで使用可能な暗号化スコープを一覧表示するには、**Get-AzStorageEncryptionScope** コマンドを呼び出します。 例中のプレースホルダーを独自の値に置き換えてください。

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

リソース グループ内のすべての暗号化スコープをストレージ アカウントごとに一覧表示するには、パイプラインの構文を次のように使用します。

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI を使用してストレージ アカウントで使用可能な暗号化スコープを一覧表示するには、[az storage account encryption-scope list](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) コマンドを呼び出します。 例中のプレースホルダーを独自の値に置き換えてください。

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>既定の暗号化スコープを持つコンテナーを作成する

コンテナーを作成するときに、既定の暗号化スコープを指定できます。 そのコンテナー内の BLOB は、既定でそのスコープを使用します。

すべての BLOB で既定のスコープを使用するようにコンテナーが構成されている場合を除き、独自の暗号化スコープを使用して個々の BLOB を作成できます。

# <a name="portal"></a>[ポータル](#tab/portal)

Azure portal で既定の暗号化スコープを持つコンテナーを作成するには、まず、「[暗号化スコープの作成](#create-an-encryption-scope)」の説明に従って暗号化スコープを作成します。 次に、以下の手順に従ってコンテナーを作成します。

1. ストレージ アカウント内のコンテナーの一覧に移動し、 **[追加]** ボタンをクリックして、新しいコンテナーを作成します。
1. **[新しいコンテナー]** ウィンドウで **[詳細]** 設定を展開します。
1. **[Encryption Scopes]\(暗号化スコープ\)** ドロップダウンで、コンテナーの既定の暗号化スコープを選択します。
1. コンテナー内のすべての BLOB で既定の暗号化スコープを使用することを要求するには、 **[Use this encryption scope for all blobs in the container]\(コンテナー内のすべての BLOB に対してこの暗号化スコープを使用する\)** チェックボックスをオンにします。 このチェックボックスをオンにした場合、コンテナー内の個々の BLOB によって既定の暗号化スコープが上書きされることはありません。

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="既定の暗号化スコープが表示されているコンテナーを示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して既定の暗号化スコープを持つコンテナーを作成するには、[New-AzRmStorageContainer](/powershell/module/az.storage/new-azrmstoragecontainer) コマンドを呼び出して、`-DefaultEncryptionScope` パラメーターのスコープを指定します。 **New-AzRmStorageContainer** コマンドでは、Azure Storage リソース プロバイダーを使用してコンテナーが作成されます。これにより、暗号化スコープとその他のリソース管理操作を構成できます。

コンテナー内のすべての BLOB でコンテナーの既定のスコープが使用されるように強制するには、`-PreventEncryptionScopeOverride` パラメーターを `true` に設定します。

```powershell
$containerName1 = "container1"
$containerName2 = "container2"

# Create a container with a default encryption scope that cannot be overridden.
New-AzRmStorageContainer -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Name $containerName1 `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI で既定の暗号化スコープを持つコンテナーを作成するには、[az storage container create](/cli/azure/storage/container#az-storage-container-create) コマンドを呼び出して、`--default-encryption-scope` パラメーターのスコープを指定します。 コンテナー内のすべての BLOB でコンテナーの既定のスコープが使用されるように強制するには、`--prevent-encryption-scope-override` パラメーターを `true` に設定します。

次の例では、Azure AD アカウントを使用して、コンテナーの作成操作を承認します。 アカウント アクセス キーを使用することもできます。 詳細については、「[Azure CLI を使用して BLOB またはキュー データへのアクセスを承認する](./authorize-data-operations-cli.md)」を参照してください。

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

既定の暗号化スコープがあり、BLOB による既定のスコープの上書きを禁止するよう構成されているコンテナーに対して、クライアントから BLOB をアップロードする際にスコープの指定を試みた場合、操作は失敗し、コンテナーの暗号化ポリシーによってその要求が禁止されていることを示すメッセージが表示されます。

## <a name="upload-a-blob-with-an-encryption-scope"></a>暗号化スコープで BLOB をアップロードする

BLOB をアップロードするときに、その BLOB の暗号化スコープを指定したり、コンテナーに既定の暗号化スコープを使用したりできます (指定されている場合)。

# <a name="portal"></a>[ポータル](#tab/portal)

Azure portal を使用して暗号化スコープが指定された BLOB をアップロードするには、まず、「[暗号化スコープの作成](#create-an-encryption-scope)」の説明に従って暗号化スコープを作成します。 次に、以下の手順に従って BLOB を作成します。

1. BLOB をアップロードするコンテナーに移動します。
1. **[アップロード]** ボタンを選択し、アップロードする BLOB を探します。
1. **[BLOB のアップロード]** ウィンドウで **[詳細]** 設定を展開します。
1. **[Encryption Scopes]\(暗号化スコープ\)** ドロップダウン セクションを見つけます。 既定では、コンテナーの既定の暗号化スコープで BLOB が作成されます (指定されている場合)。 BLOB でデフォルトの暗号化スコープを使用するようにコンテナーで要求されている場合、このセクションは無効になります。
1. アップロードする BLOB に別のスコープを指定するには、 **[既存のスコープを選択する]** を選択し、ドロップダウンから目的のスコープを選択します。

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="暗号化スコープを持つ BLOB をアップロードする方法を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して暗号化スコープが指定された BLOB をアップロードするには、[Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) コマンドを呼び出し、BLOB の暗号化スコープを指定します。

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = (Get-AzStorageAccount -ResourceGroupName $rgName -StorageAccountName $accountName).Context

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx
# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx -Container $containerName2 -File $localSrcFile -Blob "helloworld.txt" -BlobType Block -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI を使用して暗号化スコープが指定された BLOB をアップロードするには、[az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload) コマンドを呼び出し、BLOB の暗号化スコープを指定します。

Azure Cloud Shell を使用する場合は、「[BLOB をアップロードする](storage-quickstart-blobs-cli.md#upload-a-blob)」で説明されている手順に従って、ルート ディレクトリにファイルを作成します。 その後、次のサンプルを使用して、このファイルを BLOB にアップロードできます。

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>スコープの暗号化キーを変更する

暗号化スコープを保護するキーを Microsoft マネージド キーからカスタマー マネージド キーに変更するには、まず、カスタマー マネージド キーがストレージ アカウントの Azure Key Vault または Key Vault HSM で有効になっていることを確認します。 詳細については、「[Azure Key Vault に格納されているカスタマー マネージド キーによる暗号化を構成する](../common/customer-managed-keys-configure-key-vault.md)」または「[Azure Key Vault に格納されているカスタマー マネージド キーによる暗号化を構成する](../common/customer-managed-keys-configure-key-vault.md)」を参照してください。

# <a name="portal"></a>[ポータル](#tab/portal)

スコープを保護するキーを Azure portal で変更するには、以下の手順を実行します。

1. **[Encryption Scopes]\(暗号化スコープ\)** タブに移動し、ストレージ アカウントの暗号化スコープの一覧を表示します。
1. 変更するスコープの横にある **[詳細]** ボタンを選択します。
1. **[Edit encryption scope]\(暗号化スコープの編集\)** ウィンドウでは、暗号化の種類を Microsoft マネージド キーからカスタマー マネージド キーに変更したり、その逆に変更したりすることができます。
1. 新しいカスタマー マネージド キーを選択するには、 **[新しいキーを使用する]** を選択し、キー コンテナー、キー、およびキーのバージョンを指定します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して、暗号化スコープを保護するキーをカスタマー マネージド キーから Microsoft マネージド キーに変更するには、**Update-AzStorageEncryptionScope** コマンドを呼び出し、`-StorageEncryption` パラメーターを渡します。

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

次に、**Update-AzStorageEncryptionScope** コマンドを呼び出し、`-KeyUri` と `-KeyvaultEncryption` のパラメーターを渡します。

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI を使用して、暗号化スコープを保護するキーをカスタマー マネージド キーから Microsoft マネージド キーに変更するには、[az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) コマンドを呼び出し、`Microsoft.Storage` の値で `--key-source` パラメーターを渡します。

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

次に、**az storage account encryption-scope update** コマンドを呼び出し、`--key-uri` パラメーターを渡して、`Microsoft.KeyVault` の値で `--key-source` パラメーターを渡します。

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>暗号化スコープを無効にする

暗号化スコープが無効の場合、それに対して課金されることはなくなります。 不要な料金が発生しないように、不要な暗号化スコープを無効にします。 詳細については、「[保存データ向け Azure ストレージの暗号化](../common/storage-service-encryption.md)」をご覧ください。

# <a name="portal"></a>[ポータル](#tab/portal)

Azure portal で暗号化スコープを無効にするには、ストレージ アカウントの **[Encryption Scopes]\(暗号化スコープ\)** 設定に移動し、目的の暗号化スコープを選択して、 **[無効にする]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell で暗号化スコープを無効にするには、次の例に示すように、Update-AzStorageEncryptionScope コマンドを呼び出し、値が `disabled` の `-State` パラメーターを含めます。 暗号化スコープを再び有効にするには、`-State` パラメーターを `enabled` に設定して、同じコマンドを呼び出します。 例中のプレースホルダーを独自の値に置き換えてください。

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI で暗号化スコープを無効にするには、次の例に示すように、[az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) コマンドを呼び出し、値が `Disabled` の `--state` パラメーターを含めます。 暗号化スコープを再び有効にするには、`--state` パラメーターを `Enabled` に設定して、同じコマンドを呼び出します。 例中のプレースホルダーを独自の値に置き換えてください。

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

---

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](../common/storage-service-encryption.md)
- [BLOB ストレージの暗号化スコープ (プレビュー)](encryption-scope-overview.md)
- [Azure Storage の暗号化のためのカスタマー マネージド キー](../common/customer-managed-keys-overview.md)