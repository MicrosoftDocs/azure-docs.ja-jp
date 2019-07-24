---
title: Azure 統合リソースを Azure Germany からグローバル Azure に移行する
description: この記事では、Azure 統合リソースの Azure Germany からグローバル Azure への移行に関する情報を提供します。
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 8/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: d85c2e7c1aa3738ce6a9d3130d2ddc400c333a9d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490060"
---
# <a name="migrate-integration-resources-to-global-azure"></a>統合リソースをグローバル Azure に移行する

この記事には、Azure 統合リソースの Azure Germany からグローバル Azure への移行に役立つ可能性のある情報が含まれています。

## <a name="service-bus"></a>Service Bus

Azure Service Bus サービスには、データのエクスポートまたはインポート機能がありません。 Service Bus リソースを Azure Germany からグローバル Azure に移行するには、リソースを [Azure Resource Manager テンプレートとして](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)エクスポートできます。 エクスポートしたテンプレートをグローバル Azure に合わせて調整し、リソースを再作成します。

> [!NOTE]
> Resource Manager テンプレートのエクスポートでは、データ (メッセージなど) がコピーされません。 テンプレートのエクスポートでは、メタデータのみ再作成されます。

> [!IMPORTANT]
> 場所、Azure Key Vault シークレット、証明書、および、その他の GUID を新しいリージョンと一致するように、変更します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="service-bus-metadata"></a>Service Bus メタデータ

Resource Manager テンプレートをエクスポートすると、次の Service Bus メタデータ要素が再作成されます。

- 名前空間
- キュー
- トピック
- サブスクリプション
- ルール
- 承認規則

### <a name="keys"></a>構成する

エクスポートし、再作成する前述の手順では、承認規則に関連付けられている Shared Access Signature キーがコピーされません。 Shared Access Signature キーを保持する必要がある場合、オプションのパラメーター `-Keyvalue` で `New-AzServiceBuskey` コマンドレットを使用して、キーを文字列として受け付けます。 [Azure PowerShell Az モジュール](/powershell/azure/install-az-ps)で、更新されたコマンドレットを使用できます。

### <a name="usage-example"></a>使用例

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Queue <queuename> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Topic <topicname> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

> [!NOTE]
> キーを保持する場合でも、新しい接続文字列を使用するようにアプリケーションを更新する必要があります。 DNS ホスト名は、Azure Germany とグローバル Azure で異なります。

### <a name="sample-connection-strings"></a>接続文字列の例

**Azure Germany**

```cmd
Endpoint=sb://myBFProdnamespaceName.**servicebus.cloudapi.de**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

**グローバル Azure**

```cmd
Endpoint=sb://myProdnamespaceName.**servicebus.windows.net**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

詳細:

- [Service Bus のチュートリアル](https://docs.microsoft.com/azure/service-bus-messaging/#step-by-step-tutorials)を完了することによって知識を更新します。
- [Resource Manager テンプレートのエクスポート](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)方法を理解するか、または [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) の概要をご覧ください。
- [Service Bus の概要](../service-bus-messaging/service-bus-messaging-overview.md)を確認します。

## <a name="logic-apps"></a>Logic Apps

Azure Logic Apps サービスは Azure Germany で使用できません。 ただし、使用可能な Azure Scheduler は非推奨とされています。 グローバル Azure で、Logic Apps を使用してスケジュール ジョブを作成します。

詳細:

- [Logic Apps のチュートリアル](https://docs.microsoft.com/azure/logic-apps/#step-by-step-tutorials)を完了して、Azure Logic Apps の機能の理解を深めます。
- [Azure Logic Apps の概要](../logic-apps/logic-apps-overview.md)を確認します。

## <a name="next-steps"></a>次の手順

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [データベース](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [ID](./germany-migration-identity.md)
- [セキュリティ](./germany-migration-security.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)
