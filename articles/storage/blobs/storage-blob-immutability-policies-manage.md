---
title: BLOB ストレージの不変ポリシーを設定および管理する - Azure Storage
description: BLOB (オブジェクト) ストレージの WORM (Write Once Read Many) サポートを使用して、指定された期間に、消去および変更できない状態でデータを保存する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a09dbd7d778a4f7ea2a9aac9ca07b9e6d06bc1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84463622"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>BLOB ストレージの不変ポリシーを設定および管理する

Azure Blob Storage の不変ストレージを使用すると、ユーザーはビジネスに不可欠なデータ オブジェクトを WORM (Write Once Read Many) 状態で保存できます。 この状態では、ユーザーが指定した期間、データを消去および変更できなくなります。 保持間隔の間、BLOB の作成と読み取りは可能ですが、変更や削除を行うことはできません。 不変ストレージは、すべての Azure リージョン内の汎用 V2 および BLOB ストレージ アカウントで使用できます。

この記事では、Azure portal、PowerShell、または Azure CLI を使用して、BLOB ストレージ内のデータの不変ポリシーと訴訟ホールドを設定および管理する方法について説明します。 不変ストレージの詳細については、「[不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](storage-blob-immutable-storage.md)」を参照してください。

## <a name="set-retention-policies-and-legal-holds"></a>保持ポリシーと訴訟ホールドを設定する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. 新しいコンテナーを作成するか、既存のコンテナーを選択して、不変状態に維持する必要がある BLOB を格納します。 コンテナーは、汎用 v2 ストレージ アカウントまたは BLOB ストレージ アカウントに存在する必要があります。

2. [コンテナーの設定] 内にある **[アクセス ポリシー]** を選択します。 次に、**[Immutable blob storage]\(不変 BLOB ストレージ\)** の下で **[Add policy]\(ポリシーの追加\)** を選択します。

    ![ポータルでコンテナーの設定](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. 時間ベースのリテンションを有効にするには、ドロップダウン メニューから **[時間ベースの保持]** を選択します。

    ![[ポリシーの種類] の下で選択されている [時間ベースの保持]](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. 保持間隔を日数で入力します (指定できる値は 1 から 146000 日)。

    ![[Update retention period to]\(保持期間を次に更新\) ボックス](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    ポリシーの初期状態のロックが解除されて、ロックする前に機能をテストし、ポリシーに変更を加えることができます。 ポリシーのロックは、SEC 17a-4 などの規制に準拠するために必要です。

5. ポリシーをロックします。 省略記号 (**...**) を右クリックすると、追加のアクションと共に次のメニューが表示されます。

    ![メニュー上の [ポリシーのロック]](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. **[ポリシーのロック]** を選択し、ロックを確認します。 これでポリシーがロックされて削除できなくなり、保持間隔の延長だけが許可されるようになります。 BLOB の削除とオーバーライドは許可されていません。 

    ![メニューで [ポリシーのロック] を確認する](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. 訴訟ホールドを有効にするには、**[Add Policy]\(ポリシーの追加\)** を選択します。 ドロップダウン メニューから **[訴訟ホールド]** を選択します。

    ![[ポリシーの種類] の下のメニューにある [訴訟ホールド]](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. 1 つ以上のタグを持つ訴訟ホールドを作成します。

    ![[ポリシーの種類] の下にある [タグ名] ボックス](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. 訴訟ホールドをクリアするには、適用された訴訟ホールド ID タグを削除します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

この機能は、`az storage container immutability-policy` および `az storage container legal-hold` コマンド グループに含まれています。 これらに対して `-h` を実行すると、コマンドが表示されます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az.Storage モジュールでは、不変ストレージがサポートされています。  この機能を有効にするには、次の手順を実行します。

1. `Install-Module PowerShellGet –Repository PSGallery –Force` を実行し、最新バージョンの PowerShellGet がインストールされていることを確認します。
2. Azure PowerShell の以前のインストールを削除します。
3. `Install-Module Az –Repository PSGallery –AllowClobber` を実行し、Azure PowerShell をインストールします。

ご参考までに、PowerShell スクリプトの例を次に示します。 このスクリプトでは、新しいストレージ アカウントとコンテナーが作成されます。 そのあと、訴訟ホールドを設定およびクリアする方法、時間ベースのリテンション ポリシー (不変ポリシーとも呼ばれる) を作成およびロックする方法、および保持間隔を延長する方法が示されています。

まず、Azure Storage アカウントを作成します。

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$account = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

訴訟ホールドを設定してクリアします。

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

時間ベースの不変ポリシーを作成または更新します。

```powershell
# Create a time-based immutability policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

不変ポリシーを取得します。

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

不変ポリシーをロックします (プロンプトを無視するには、`-Force` を追加します)。

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

不変ポリシーを拡張します。

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

ロックされていない不変ポリシーを削除します (プロンプトを無視するには、`-Force` を追加します)。

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>保護された追加 BLOB の書き込みを有効にする

### <a name="portal"></a>[ポータル](#tab/azure-portal)

![さらに追加の書き込みを許可する](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

この機能は、`az storage container immutability-policy` および `az storage container legal-hold` コマンド グループに含まれています。 これらに対して `-h` を実行すると、コマンドが表示されます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Create an immutability policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>次のステップ

[不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](storage-blob-immutable-storage.md)
