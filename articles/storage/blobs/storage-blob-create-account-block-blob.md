---
title: ブロック BLOB ストレージ アカウントを作成する - Azure Storage |Microsoft Docs
description: Premium のパフォーマンス特性を持つ Azure ブロック BLOB ストレージ アカウントを作成する方法について説明します。
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
author: twooley
ms.author: twooley
ms.subservice: blobs
ms.openlocfilehash: f4d3e3ad923b6a603902bc007107cb41dae8cf6c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400464"
---
# <a name="create-a-block-blob-storage-account"></a>ブロック BLOB ストレージ アカウントの作成

ブロック BLOB ストレージ アカウントの種類では、Premium のパフォーマンス特性を持つブロック BLOB を作成できます。 この種類のストレージ アカウントは、トランザクション レートが高い、または非常に高速なアクセス時間を必要とするワークロードに最適化されています。 この記事では、Azure portal、Azure CLI、または Azure PowerShell を使用して、ブロック BLOB ストレージ カウントを作成する方法について説明します。

ブロック BLOB ストレージ アカウントの詳細については、「[Azure ストレージ アカウントの概要](https://docs.microsoft.com/azure/storage/common/storage-account-overview)」を参照してください。

## <a name="create-account-in-the-azure-portal"></a>Azure portal でアカウントを作成する

Azure potal でブロック BLOB ストレージ アカウントを作成するには、次の手順に従います。

1. Azure portal で、**[すべてのサービス]** > **[ストレージ]** カテゴリ > **[ストレージ アカウント]** の順に選択します。

1. **[ストレージ アカウント]** の下で **[追加]** を選択します。

1. **[サブスクリプション]** フィールドで、ストレージ アカウントを作成するサブスクリプションを選択します。

1. **[リソース グループ]** フィールドで、既存のリソース グループを選択するか、**[新規作成]** を選択し、新しいリソース グループの名前を入力します。

1. **[ストレージ アカウント名]** で、アカウントの名前を入力します。 次のガイドラインに注意してください。

   - この名前は Azure 全体で一意である必要があります。
   - 名前は 3 から 24 文字の範囲でなければなりません。
   - 名前には、数字と小文字のみを含めることができます。

1. **[場所]** フィールドで、ストレージ アカウントの場所を選択するか、または既定の場所を使います。

1. 残りの設定については、次のように構成します。

   |フィールド     |値  |
   |---------|---------|
   |**パフォーマンス**    |  **[Premium]** を選択します。   |
   |**アカウントの種類**    | **[BlockBlobStorage]** を選択します。      |
   |**レプリケーション**    |  **[ローカル冗長ストレージ (LRS)]** の既定の設定のままにします。      |

   ![ブロック BLOB ストレージ アカウントを作成するポータル UI を表示する](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. **[確認および作成]** を選択して、ストレージ アカウントの設定を確認します。

1. **作成**を選択します。

## <a name="create-account-using-azure-powershell"></a>Azure PowerShell を使用してアカウントを作成する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 管理者特権の Windows PowerShell セッション (管理者として実行) を開きます。

1. 次のコマンドを実行して、最新バージョンの `Az` PowerShell モジュールがインストールされていることを確認します。

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. 新しい PowerShell コンソールを開き、ご使用の Azure アカウントでサインインします。

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. 必要な場合は、新しいリソース グループを作成します。 引用符内の値を置き換えて、次のコマンドを実行します。

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. ブロック BLOB ストレージ アカウントを作成します。 引用符内の値を置き換えて、次のコマンドを実行します。

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Azure CLI を使用してアカウントを作成する

Azure CLI を使用してブロック BLOB アカウントを作成するには、最初に Azure CLI v.2.0.46 以降のバージョンをインストールする必要があります。 2.0.46 or a later version. バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

1. Azure サブスクリプションにサインインします。

   ```azurecli
   az login
   ```

1. 必要な場合は、新しいリソース グループを作成します。 角かっこ内の値 (角かっこを含む) を置き換えて、次のコマンドを実行します。

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. ブロック BLOB ストレージ アカウントを作成します。 角かっこ内の値 (角かっこを含む) を置き換えて、次のコマンドを実行します。

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>次の手順

- ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](https://docs.microsoft.com/azure/storage/common/storage-account-overview)」をご覧ください。

- リソース グループの詳細については、「[Azure Resource Manager の概要](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)」を参照してください。
