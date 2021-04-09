---
title: ARM テンプレートを使用すると、Azure Cosmos DB で継続的バックアップと特定の時点への復元を構成できます。
description: Azure Resource Manager テンプレートを使用して、継続的バックアップとデータの復元を行うアカウントをプロビジョニングする方法について説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 4abfdd0209bd9f13fb7bd902b27a53f65156da2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381819"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-resource-manager-templates"></a>継続的バックアップと特定の時点への復元の構成と管理 (プレビュー) - Azure Resource Manager テンプレートの使用
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB の特定の時点への復元機能 (継続的バックアップ モード) は、現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Cosmos DB の特定の時点への復元機能 (プレビュー) を使用すると、コンテナー内で誤って行われた変更から回復し、削除されたアカウント、データベース、またはコンテナーの復元や、任意のリージョン (バックアップが存在していた場所) への復元が行えます。 継続的バックアップ モードでは、過去 30 日以内の任意の時点に復元することができます。

この記事では、Azure Resource Manager テンプレートを使用して、継続的バックアップとデータの復元を使用してアカウントをプロビジョニングする方法について説明します。

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>継続的バックアップを使用してアカウントをプロビジョニングする

Azure Resource Manager テンプレートを使用して、継続的モードで Azure Cosmos DB アカウントをデプロイできます。 アカウントをプロビジョニングするためのテンプレートを定義する場合は、次の例に示すように `backupPolicy` パラメーターを含めます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

次に、Azure PowerShell または CLI を使用してテンプレートをデプロイします。 次の例は、CLI コマンドを使用してテンプレートをデプロイする方法を示しています。

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Resource Manager テンプレートを使用した復元

また、Resource Manager テンプレートを使用してアカウントを復元することもできます。 テンプレートを定義するときは、次のパラメーターを指定します。

* `createMode` パラメーターを *Restore* に設定します。
* `restoreParameters` を定義します。`restoreSource` の値は、ソース アカウントの `az cosmosdb restorable-database-account list` コマンドの出力から抽出されます。 アカウント名のインスタンス ID 属性は、復元の実行に使用されます。
* `restoreMode` パラメーターを *PointInTime* に設定し、`restoreTimestampInUtc` 値を構成します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

次に、Azure PowerShell または CLI を使用してテンプレートをデプロイします。 次の例は、CLI コマンドを使用してテンプレートをデプロイする方法を示しています。  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>次のステップ

* [Azure CLI](continuous-backup-restore-command-line.md)、[PowerShell](continuous-backup-restore-command-line.md)、または [Azure portal](continuous-backup-restore-portal.md) を使用して、継続的バックアップを構成および管理します。
* [継続的バックアップ モードのリソース モデル](continuous-backup-restore-resource-model.md)
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。