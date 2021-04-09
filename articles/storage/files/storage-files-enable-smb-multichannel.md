---
title: SMB マルチチャネルを有効にする
description: Azure Premium ファイル共有で SMB マルチチャネルを有効にする方法を説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2f867fa6d4b7e1d864a85106b5d957a53d38eb76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732542"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>FileStorage アカウントで SMB マルチチャネルを有効にする (プレビュー) 

Azure FileStorage アカウントは、SMB マルチチャネル (プレビュー) をサポートしています。これにより、Premium ファイル共有への複数のネットワーク接続を確立することによって、SMB 3.x クライアントからのパフォーマンスが向上します。 この記事では、既存のストレージ アカウントで SMB マルチチャネルを有効にする手順をわかりやすく説明します。 Azure Files SMB マルチチャネルの詳細については、SMB マルチチャネルのパフォーマンスに関するページを参照してください。

## <a name="limitations"></a>制限事項

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>リージョン別の提供状況

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>前提条件

- [FileStorage アカウントを作成](./storage-how-to-create-file-share.md)します。
- Azure PowerShell モジュールを使用する場合は、[3.0.1-preview バージョンのモジュールをインストール](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview)してください。

## <a name="getting-started"></a>作業の開始

PowerShell ウィンドウを開き、Azure サブスクリプションにサインインします。 そこから、次のコマンドを使用して、SMB マルチチャネル プレビューに登録できます。

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> 登録には、最大で 1 時間かかります。

### <a name="verify-that-feature-registration-is-complete"></a>機能の登録が完了したことを確認する

ストレージ アカウントでこの機能を有効にするまでに最大で 1 時間かかるため、次のコマンドを使用して、サブスクリプションに登録されたことを検証することをお勧めします。

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>SMB マルチチャネルを有効にする 
FileStorage アカウントを作成したら、次の手順に従って、ストレージ アカウントの SMB マルチチャネル設定を更新することができます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
1. Azure portal にサインインし、SMB マルチチャネルを構成する FileStorage ストレージ アカウントに移動します。
1. **[ファイル サービス]** の下の **[ファイル共有]** を選択し、 **[ファイル共有の設定]** を選択します。
1. **[SMB マルチチャネル]** を **[オン]** にし (または、 **[オフ]** にして無効にし)、 **[保存]** を選択します。

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="ストレージ アカウントのスクリーンショット、SMB マルチチャネルがオンになっています。":::

[SMB マルチチャネル] オプションが **[ファイル共有の設定]** の下に表示されないか、構成の更新中に設定更新失敗のエラーが発生した場合は、サブスクリプションが登録されていることと、アカウントの種類とレプリケーションがサポートされている [サポート対象リージョン](#regional-availability)のいずれかに自分のアカウントが存在することを確認してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell モジュールを使用して SMB マルチチャネルを有効にするには、[3.0.1-preview バージョンのモジュールをインストールする](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview)必要があります。

変数 `$resourceGroupName` および `$storageAccountName` を、リソース グループとストレージ アカウントに設定した後に、以下の PowerShell コマンドを実行します。

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI では、SMB マルチチャネルの構成はまだサポートされていません。 portal の手順を参照して、ストレージ アカウントで SMB マルチチャネルを構成してください。

---

> [!NOTE]
> SMB マルチチャネルの構成設定の変更はすべて、ストレージ アカウントのすべてのファイル共有に適用されます。 ただし、変更を有効にするには、クライアントで共有を再マウントする必要があります。


## <a name="next-steps"></a>次のステップ 

- SMB マルチチャネルを利用するには、[ファイル共有を再マウント](storage-how-to-use-files-windows.md)します。
- [SMB マルチチャネルに関連する問題のトラブルシューティングを行います](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings)。
- この機能強化の詳細については、[SMB マルチチャネルのパフォーマンス](storage-files-smb-multichannel-performance.md)に関するページを参照してください。
 - Windows SMB マルチチャネル機能の詳細については、「[SMB マルチチャネルを管理する](/azure-stack/hci/manage/manage-smb-multichannel)」を参照してください。