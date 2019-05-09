---
title: Azure Storage で安全な転送が必要 | Microsoft Docs
description: Azure Storage の "安全な転送が必須" 機能、およびこの機能を有効にする方法について説明します。
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 06/20/2017
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: e75cd873b780f514b24ee254dd491b6aa779c420
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58101926"
---
# <a name="require-secure-transfer-in-azure-storage"></a>Azure Storage で安全な転送が必要

[安全な転送が必須] オプションは、セキュリティで保護された接続からアカウントへの要求を許可するだけで、ストレージ アカウントのセキュリティを強化することができます。 たとえば、ストレージ アカウントにアクセスするために REST API を呼び出している場合、HTTPS を使用して接続する必要があります。 [安全な転送が必須] では、HTTP を使用する要求は拒否されます。

Azure Files サービスを使用する場合、[安全な転送が必須] を有効にすると、暗号化をしない接続は失敗します。 これには、暗号化なしの SMB 2.1、SMB 3.0、および Linux SMB クライアントの一部のバージョンを使用するシナリオが含まれます。 

SDK を使用してストレージ アカウントを作成した場合、既定で [安全な転送が必須] オプションは無効です。 また、Azure portal でストレージ アカウントを作成した場合は、既定で有効です。

> [!NOTE]
> Azure Storage ではカスタム ドメイン名の HTTPS はサポートされないため、カスタム ドメイン名を使用している場合、このオプションは適用されません。 また、クラシック ストレージ アカウントはサポートされていません。

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Azure Portal で [安全な転送が必須] を有効にする

[Azure Portal](https://portal.azure.com) でストレージ アカウントを作成する場合は、[安全な転送が必須] 設定を有効にすることができます。 既存のストレージ アカウントの場合も有効にすることができます。

### <a name="require-secure-transfer-for-a-new-storage-account"></a>新しいストレージ アカウントの安全な転送が必須

1. Azure Portal で **[ストレージ アカウントの作成]** ウィンドウを開きます。
1. **[安全な転送が必須]** で、**[有効]** を選択します。

   ![[ストレージ アカウントの作成] ブレード](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>既存のストレージ アカウントの安全な転送が必須

1. Azure Portal で既存のストレージ アカウントを選択します。
1. ストレージ アカウント メニュー ウィンドウの **[設定]** で、**[構成]** を選択します。
1. **[安全な転送が必須]** で、**[有効]** を選択します。

   ![ストレージ アカウント メニュー ウィンドウ](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>プログラムを使用して [安全な転送が必須] を有効にする

プログラムで安全な転送を必須にするには、以下の REST API、ツール、またはライブラリを利用して、ストレージ アカウント プロパティの _supportsHttpsTrafficOnly_ 設定を使用します。

* [REST API](https://docs.microsoft.com/rest/api/storagerp/storageaccounts) (バージョン:2016-12-01)
* [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) (バージョン:0.7)
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (バージョン:2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (バージョン:1.1.0)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (バージョン:6.3.0)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (バージョン:1.1.0)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (バージョン:0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>PowerShell で [安全な転送が必須] の設定を有効にする

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

このサンプルには、Azure PowerShell モジュール Az バージョン 0.7 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。

`Connect-AzAccount` を実行して、Azure との接続を作成します。

 以下のコマンド ラインを使って、設定を確認します。

```powershell
> Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

以下のコマンド ラインを使って、設定を有効にします。

```powershell
> Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>CLI で [安全な転送が必須] の設定を有効にする

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 以下のコマンド ラインを使って、設定を確認します。

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

以下のコマンド ラインを使って、設定を有効にします。

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>次の手順
Azure Storage で提供される包括的なセキュリティ機能のセットを利用して、開発者はセキュリティで保護されたアプリケーションを構築できます。 詳細については、「[Azure Storage セキュリティ ガイド](storage-security-guide.md)」を参照してください。
