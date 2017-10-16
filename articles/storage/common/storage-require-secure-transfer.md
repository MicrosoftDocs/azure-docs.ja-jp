---
title: "Azure Storage で安全な転送が必要 | Microsoft Docs"
description: "Azure Storage の \"安全な転送が必須\" 機能、およびこの機能を有効にする方法について説明します。"
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 5ec50ca23d9f7c92365492dfab42dc14a38699e2
ms.contentlocale: ja-jp
ms.lasthandoff: 09/28/2017

---
# <a name="require-secure-transfer-in-azure-storage"></a>Azure Storage で安全な転送が必要

[安全な転送が必須] オプションは、セキュリティで保護された接続からアカウントへの要求を許可するだけで、ストレージ アカウントのセキュリティを強化することができます。 たとえば、ストレージ アカウントにアクセスするために REST API を呼び出している場合、HTTPS を使用して接続する必要があります。 [安全な転送が必須] では、HTTP を使用する要求は拒否されます。

Azure Files サービスを使用する場合、[安全な転送が必須] を有効にすると、暗号化をしない接続は失敗します。 これには、暗号化なしの SMB 2.1、SMB 3.0、および Linux SMB クライアントの一部のバージョンを使用するシナリオが含まれます。 

既定では、[安全な転送が必須] オプションは無効になっています。

> [!NOTE]
> Azure Storage ではカスタム ドメイン名の HTTPS はサポートされないため、カスタム ドメイン名を使用している場合、このオプションは適用されません。

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Azure Portal で [安全な転送が必須] を有効にする

[Azure Portal](https://portal.azure.com) でストレージ アカウントを作成する場合は、[安全な転送が必須] 設定を有効にすることができます。 既存のストレージ アカウントの場合も有効にすることができます。

### <a name="require-secure-transfer-for-a-new-storage-account"></a>新しいストレージ アカウントの安全な転送が必須

1. Azure Portal で **[ストレージ アカウントの作成]** ウィンドウを開きます。
1. [**安全な転送が必須**] で、[**有効**] を選択します。

  ![[ストレージ アカウントの作成] ブレード](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>既存のストレージ アカウントの安全な転送が必須

1. Azure Portal で既存のストレージ アカウントを選択します。
1. ストレージ アカウント メニュー ウィンドウの **[設定]** で、**[構成]** を選択します。
1. [**安全な転送が必須**] で、[**有効**] を選択します。

  ![ストレージ アカウント メニュー ウィンドウ](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>プログラムを使用して [安全な転送が必須] を有効にする

プログラムで安全な転送を必須にするには、以下の REST API、ツール、またはライブラリを利用して、ストレージ アカウント プロパティの _supportsHttpsTrafficOnly_ 設定を使用します。

* [REST API](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts) (バージョン: 2016-12-01)
* [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) (バージョン: 4.1.0)
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (バージョン: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (バージョン: 1.1.0)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (バージョン: 6.3.0)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (バージョン: 1.1.0)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (バージョン: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-rest-api"></a>REST API で [安全な転送が必須] の設定を有効にする

REST API でのテストを簡略化するには、[ArmClient](https://github.com/projectkudu/ARMClient) を使用してコマンド ラインから呼び出します。

 以下のコマンド ラインを使用して、REST API の設定を確認します。

```
# Login Azure and proceed with your credentials
> armclient login

> armclient GET  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01
```

応答には、_supportsHttpsTrafficOnly_ 設定があります。 For example:

```Json

{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}",
  "kind": "Storage",
  ...
  "properties": {
    ...
    "supportsHttpsTrafficOnly": false
  },
  "type": "Microsoft.Storage/storageAccounts"
}

```

以下のコマンド ラインを使用して、REST API の設定を有効にします。

```

# Login Azure and proceed with your credentials
> armclient login

> armclient PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01 < Input.json

```

Input.json の例を次に示します。
```Json

{
  "location": "westus",
  "properties": {
    "supportsHttpsTrafficOnly": true
  }
}

```

## <a name="next-steps"></a>次のステップ
Azure Storage で提供される包括的なセキュリティ機能のセットを利用して、開発者はセキュリティで保護されたアプリケーションを構築できます。 詳細については、「[Azure Storage セキュリティ ガイド](storage-security-guide.md)」を参照してください。

