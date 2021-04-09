---
title: 大きなファイル共有の有効化と作成 - Azure Files
description: この記事では、大きなファイル共有を有効化して作成する方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c22b3f3164cbb7c1a7ed150d093f77777c7b1023
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501296"
---
# <a name="enable-and-create-large-file-shares"></a>大きなファイル共有の有効化と作成

ストレージ アカウントで大きなファイル共有を有効にすると、ファイル共有を 100 TiB までスケール アップでき、標準共有の IOPS とスループットの上限も上がります。 既存のファイル共有のために、既存のストレージ アカウントに対してこのスケーリングを有効にすることもできます。 詳細については、「[ファイル共有とファイルのスケール ターゲット](storage-files-scale-targets.md#azure-files-scale-targets)」を参照してください。 

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
- Azure CLI を使用する場合は、[最新バージョンをインストールしてください](/cli/azure/install-azure-cli)。
- Azure PowerShell モジュールを使用する場合は、[最新バージョンをインストール](/powershell/azure/install-az-ps)してください。

## <a name="restrictions"></a>制限

現時点では、大きなファイル共有を有効にしたアカウントでは、ローカル冗長ストレージ (LRS) またはゾーン冗長ストレージ (ZRS) のみを使用できます。 geo ゾーン冗長ストレージ (GZRS)、geo 冗長ストレージ (GRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS)、読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) はいずれも使用できません。

アカウントで大きなファイル共有を有効にすると、元に戻すことができません。 有効にした後、アカウントを GZRS、GRS、RA-GRS、または RA-GZRS に変換することはできません。

## <a name="create-a-new-storage-account"></a>新しいストレージ アカウントの作成

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Azure Portal で **[すべてのサービス]** を選択します。 
1. リソースの一覧で、「**Storage Accounts**」と入力します。 入力すると、入力内容に基づいて一覧がフィルター処理されます。 **[ストレージ アカウント]** を選択します。
1. 表示された **[ストレージ アカウント]** ウィンドウで、 **[追加]** を選択します。
1. ストレージ アカウントの作成に使用するサブスクリプションを選択します。
1. **[リソース グループ]** フィールドの下の **[新規作成]** を選択します。 新しいリソース グループの名前を入力します。

    ![ポータルでリソース グループを作成する方法を示すスクリーンショット](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 次に、ストレージ アカウントの名前を入力します。 この名前は Azure 全体で一意である必要があります。 また、名前の長さは 3 ~ 24 文字にする必要があり、数字と小文字のみを使用できます。
1. ストレージ アカウントの場所を選択します。
1. レプリケーションを **[ローカル冗長ストレージ]** または **[ゾーン冗長ストレージ]** に設定します。
1. 以下のフィールドは既定値のままにします。

   |フィールド  |値  |
   |---------|---------|
   |デプロイメント モデル     |リソース マネージャー         |
   |パフォーマンス     |Standard         |
   |アカウントの種類     |StorageV2 (汎用 v2)         |
   |アクセス層     |ホット         |

1. **[詳細]** を選択し、 **[大きなファイル共有]** の右側にある **[有効]** オプション ボタンを選択します。
1. **[確認および作成]** を選択して、ストレージ アカウントの設定を確認し、アカウントを作成します。

    ![Azure portal の新しいストレージ アカウントの [有効] オプション ボタンのスクリーンショット](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. **［作成］** を選択します

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、大きなファイル共有を有効にできるように、[Azure CLI の最新バージョンをインストールします](/cli/azure/install-azure-cli)。

大きなファイル共有が有効なストレージ アカウントを作成するには、次のコマンドを使用します。 `<yourStorageAccountName>`、`<yourResourceGroup>`、および `<yourDesiredRegion>` を実際の情報に置き換えます。

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

まず、大きなファイル共有を有効にできるように、[PowerShell の最新バージョンをインストールします](/powershell/azure/install-az-ps)。

大きなファイル共有が有効なストレージ アカウントを作成するには、次のコマンドを使用します。 `<yourStorageAccountName>`、`<yourResourceGroup>`、および `<yourDesiredRegion>` を実際の情報に置き換えます。

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>既存のアカウントで大きなファイル共有を有効にする

また、既存のアカウントで大きなファイル共有を有効にすることもできます。 大きなファイル共有を有効にした場合、GZRS、GRS、RA-GRS、または RA-GZRS に変換することはできません。 大きなファイル共有を有効にすると、このストレージ アカウントでは元に戻せません。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) を開き、大きなファイル共有を有効にするストレージ アカウントに移動します。
1. ストレージ アカウントを開き、 **[構成]** を選択します。
1. **[Large file shares]\(大きなファイル共有\)** で **[有効]** を選択し、 **[保存]** を選択します。
1. **[概要]** を選択し、 **[最新の情報に更新]** を選択します。

![Azure portal の既存のストレージ アカウントで [有効] オプション ボタンを選択する](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

これで、大きなファイル共有をストレージ アカウントで有効にしました。 次に、増加した容量とスケールを活用するために、[既存の共有のクォータを更新する](#expand-existing-file-shares)必要があります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

既存のアカウントで大きなファイル共有を有効にするには、次のコマンドを使用します。 `<yourStorageAccountName>` と `<yourResourceGroup>` を実際の情報に置き換えます。

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

これで、大きなファイル共有をストレージ アカウントで有効にしました。 次に、増加した容量とスケールを活用するために、[既存の共有のクォータを更新する](#expand-existing-file-shares)必要があります。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

既存のアカウントで大きなファイル共有を有効にするには、次のコマンドを使用します。 `<yourStorageAccountName>` と `<yourResourceGroup>` を実際の情報に置き換えます。

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

これで、大きなファイル共有をストレージ アカウントで有効にしました。 次に、増加した容量とスケールを活用するために、[既存の共有のクォータを更新する](#expand-existing-file-shares)必要があります。

---

## <a name="create-a-large-file-share"></a>大きなファイル共有の作成

ストレージ アカウントで大きなファイル共有を有効にした後、よりクォータが高いアカウントでファイル共有を作成できます。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)

大きなファイル共有の作成は、Standard ファイル共有の作成とほぼ同じです。 主な違いは、クォータを最大 100 TiB に設定できることです。

1. ストレージ アカウントで **[ファイル共有]** を選択します。
1. **[+ ファイル共有]** を選択します。
1. ファイル共有の名前を入力します。 また、必要なクォータ サイズを 100 TiB まで設定することもできます。 **[作成]** を選択します。 

![[名前] ボックスと [クォータ] ボックスを示す Azure portal UI](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

大きなファイル共有を作成するには、次のコマンドを使用します。 `<yourStorageAccountName>`、`<yourStorageAccountKey>`、および `<yourFileShareName>` を実際の情報に置き換えます。

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

大きなファイル共有を作成するには、次のコマンドを使用します。 `<YourStorageAccountName>`、`<YourStorageAccountKey>`、および `<YourStorageAccountFileShareName>` を実際の情報に置き換えます。

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>既存のファイル共有の拡張

ストレージ アカウントで大きなファイル共有を有効にした後は、そのアカウントの既存のファイル共有を高いクォータに拡張することもできます。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. ストレージ アカウントで **[ファイル共有]** を選択します。
1. ファイル共有を右クリックし、 **[クォータ]** を選択します。
1. 希望する新しいサイズを入力し、 **[OK]** を選択します。

![既存のファイル共有のクォータを持つ Azure portal UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

クォータを最大サイズに設定するには、次のコマンドを使用します。 `<yourStorageAccountName>`、`<yourStorageAccountKey>`、および `<yourFileShareName>` を実際の情報に置き換えます。

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

クォータを最大サイズに設定するには、次のコマンドを使用します。 `<YourStorageAccountName>`、`<YourStorageAccountKey>`、および `<YourStorageAccountFileShareName>` を実際の情報に置き換えます。

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>次のステップ

* [Windows でのファイル共有の接続とマウント](storage-how-to-use-files-windows.md)
* [Linux でのファイル共有の接続とマウント](storage-how-to-use-files-linux.md)
* [macOS でのファイル共有の接続とマウント](storage-how-to-use-files-mac.md)