---
title: オブジェクト レプリケーションを構成する (プレビュー)
titleSuffix: Azure Storage
description: オブジェクト レプリケーションを構成して、ブロック BLOB を 1 つのストレージ アカウントのコンテナーから別のコンテナーに非同期でコピーする方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: dd85504ac2321310288efe5d0a1ef7dfcde60f21
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566065"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>ブロック BLOB のオブジェクト レプリケーションを構成する (プレビュー)

オブジェクト レプリケーション (プレビュー) を使用すると、ソース ストレージ アカウントと宛先アカウント間でブロック BLOB を非同期にコピーできます。 オブジェクト レプリケーションの詳細については、[オブジェクト レプリケーション (プレビュー)](object-replication-overview.md) に関するページを参照してください。

オブジェクト レプリケーションを構成するときに、ソース ストレージ アカウントと宛先アカウントを指定するレプリケーション ポリシーを作成します。 レプリケーション ポリシーには、ソース コンテナーと宛先コンテナーを指定し、ソース コンテナー内のどのブロック BLOB をレプリケートするかを示す 1 つ以上のルールが含まれています。

この記事では、Azure portal、PowerShell、または Azure CLI を使用して、ストレージ アカウントのオブジェクト レプリケーションを構成する方法について説明します。 Azure Storage リソース プロバイダーのクライアント ライブラリのいずれかを使用して、オブジェクト レプリケーションを構成することもできます。

## <a name="create-a-replication-policy-and-rules"></a>レプリケーション ポリシーとルールを作成する

オブジェクト レプリケーションを構成する前に、ソースと宛先のストレージ アカウントがまだ存在しない場合は作成します。 どちらのアカウントも汎用 v2 ストレージ アカウントである必要があります。 詳細については、[Azure Storage アカウントの作成](../common/storage-account-create.md)に関するページを参照してください。

1 つのストレージ アカウントを、最大 2 つの宛先アカウントのソース アカウントとして使用できます。 また、宛先アカウントにはソース アカウントを最大 2 つまで含めることができます。 ソースと宛先のアカウントがすべて異なるリージョンに存在していてもかまいません。 それぞれの宛先アカウントにデータをレプリケートするように、個別のレプリケーション ポリシーを構成できます。

開始する前に、次の機能プレビューに登録していることを確認してください。

- [オブジェクト レプリケーション (プレビュー)](object-replication-overview.md)
- [BLOB のバージョン管理 (プレビュー)](versioning-overview.md)
- [Azure Blob Storage の変更フィードのサポート (プレビュー)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でオブジェクト レプリケーションを構成する前に、ソース コンテナーと宛先コンテナーが存在しない場合は、それぞれのストレージ アカウントに作成します。 また、ソース アカウントで BLOB のバージョン管理と変更フィードを有効にし、宛先アカウントで BLOB のバージョン管理を有効にします。

Azure portal でレプリケーション ポリシーを作成するには、次の手順を実行します。

1. Azure portal でソース ストレージ アカウントに移動します。
1. **[Blob service]** で、 **[オブジェクト レプリケーション]** を選択します。
1. **[レプリケーション規則の設定]** を選択します。
1. 宛先サブスクリプションとストレージ アカウントを選択します。
1. **[Container pairs]\(コンテナー ペア\)** セクションで、ソース アカウントからソース コンテナーを選択し、宛先アカウントから宛先コンテナーを選択します。 1 つのレプリケーション ポリシーにつき最大 10 個のコンテナー ペアを作成できます。

    次の図は、一連のレプリケーション ルールを示しています。

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Azure portal のレプリケーション ルールを示すスクリーンショット":::

1. 必要に応じて、1 つ以上のフィルターを指定し、プレフィックス パターンに一致する BLOB のみをコピーします。 たとえば、プレフィックス `b` を指定すると、その文字で始まる名前の BLOB のみがレプリケートされます。 プレフィックスの一部として仮想ディレクトリを指定できます。 プレフィックス文字列では、ワイルドカード文字はサポートされません。

    次の図は、レプリケーション ルールの一部としてコピーされる BLOB を制限するフィルターを示しています。

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="レプリケーション ルールのフィルターを示すスクリーンショット":::

1. 既定では、コピー スコープは新しいオブジェクトのみをコピーするように設定されています。 コンテナー内のすべてのオブジェクトをコピーするか、カスタムの日付と時刻からオブジェクトをコピーするには、**変更**リンクを選択し、コンテナー ペアのコピー スコープを構成します。

    次の図は、カスタム コピー スコープを示しています。

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="オブジェクト レプリケーションのカスタム コピー スコープを示すスクリーンショット":::

1. **[保存して適用する]** を選択してレプリケーション ポリシーを作成し、データのレプリケーションを開始します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell でレプリケーション ポリシーを作成するには、まず Az.Storage PowerShell モジュールのバージョン [2.0.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) 以降をインストールします。 次の手順に従って、プレビュー モジュールをインストールします。

1. **[設定]** の **[アプリと機能]** 設定を使用して、Windows から Azure PowerShell の以前のインストールをアンインストールします。

1. 最新バージョンの PowerShellGet がインストールされていることを確認します。 Windows PowerShell ウィンドウを開き、次のコマンドを実行して最新バージョンをインストールします。

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    PowerShellGet のインストール後、PowerShell ウィンドウを閉じて再び開きます。

1. 最新バージョンの Azure PowerShell をインストールします。

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Az.Storage プレビュー モジュールをインストールします。

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Azure PowerShell のインストールの詳細については、[PowerShellGet を使用した Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

次の例は、ソース アカウントと宛先アカウントにレプリケーション ポリシーを作成する方法を示しています。 山かっこ内の値は、実際の値に置き換えてください。

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI でレプリケーション ポリシーを作成するには、まず Azure Storage のプレビュー拡張機能をインストールします。

```azurecli
az extension add -n storage-or-preview
```

次に、Azure の資格情報を使用してサインインします。

```azurecli
az login
```

ソースおよび宛先のストレージ アカウントで BLOB のバージョン管理を有効にし、ソース アカウントで変更フィードを有効にします。 山かっこ内の値は、実際の値に置き換えてください。

```azurecli
az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

ソースと宛先のコンテナーをそれぞれのストレージ アカウントに作成します。

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container4 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name source-container4 \
    --auth-mode login
```

宛先アカウントに新しいレプリケーション ポリシーと関連するルールを作成します。

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

ポリシー ID を使用して、ソース アカウントにポリシーを作成します。

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>レプリケーション ポリシーを削除する

レプリケーション ポリシーとそれに関連するルールを削除するには、Azure portal、PowerShell、または CLI を使用します。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でレプリケーション ポリシーを削除するには、次の手順を実行します。

1. Azure portal でソース ストレージ アカウントに移動します。
1. **[設定]** で、 **[オブジェクト レプリケーション]** を選択します。
1. ポリシー名の横にある **[詳細]** ボタンをクリックします。
1. **[Delete Rules]\(ルールの削除\)** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

レプリケーション ポリシーを削除するには、ソース アカウントと宛先アカウントの両方からポリシーを削除します。 ポリシーを削除すると、それに関連付けられているルールも削除されます。

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

レプリケーション ポリシーを削除するには、ソース アカウントと宛先アカウントの両方からポリシーを削除します。 ポリシーを削除すると、それに関連付けられているルールも削除されます。

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>次のステップ

- [オブジェクト レプリケーションの概要 (プレビュー)](object-replication-overview.md)
