---
title: Azure 管理ツールを Azure Germany からグローバル Azure に移行する
description: この記事では、Azure Germany からグローバル Azure への Azure 管理ツールの移行に関する情報を提供します。
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: bc237d0f78c77a6fbc89db4061ca38403c3d777b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "58408432"
---
# <a name="migrate-management-tool-resources-to-global-azure"></a>管理ツール リソースをグローバル Azure に移行する

この記事には、Azure Germany からグローバル Azure への Azure ストレージ リソースの移行に役立つ情報が含まれています。

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager は、スムーズな移行を完了するために役立ちます。 ただし、Azure Germany で作成した Traffic Manager プロファイルをグローバル Azure に移行することはできません  (移行中に Traffic Manager エンドポイントをターゲット環境に移行するため、いずれにしても Traffic Manager プロファイルを更新する必要があります)。

ソース環境で引き続き実行されている Traffic Manager を使用して、ターゲット環境で追加のエンドポイントを定義できます。 Traffic Manager が新しい環境で実行されている場合も引き続き、ソース環境でまだ移行されていないエンドポイントを定義できます。 このシナリオは、[ブルーグリーン シナリオ](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/)と呼ばれます。 このシナリオには、次の手順が必要です。

1. グローバル Azure で、新しい Traffic Manager プロファイルを作成します。
1. Azure Germany でエンドポイントを定義します。
1. ご利用の DNS CNAME レコードを新しい Traffic Manager プロファイルに変更します。
1. 古い Traffic Manager プロファイルを無効にします。
1. エンドポイントを移行して構成します。 Azure Germany 内のエンドポイントごとに、次の操作を行います。
   1. エンドポイントをグローバル Azure に移行します。
   1. 新しいエンドポイントを使用するように Traffic Manager プロファイルを変更します。

詳細:

- [Traffic Manager のチュートリアル](https://docs.microsoft.com/azure/traffic-manager/#step-by-step-tutorials)を完了して、新しい知識を得ましょう。
- [Traffic Manager の概要](../traffic-manager/traffic-manager-overview.md)を確認してください。
- [Traffic Manager プロファイルの作成](../traffic-manager/traffic-manager-create-profile.md)方法を確認します。
- [ブルーグリーン シナリオ](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/)について確認してください。

## <a name="backup"></a>バックアップ

Azure Backup ジョブおよびスナップショットは、Azure Germany からグローバル Azure に移行することができません。

詳細: 

- [Backup のチュートリアル](https://docs.microsoft.com/azure/backup/#step-by-step-tutorials)を完了して、新しい知識を得ましょう。
- [Azure Backup の概要](../backup/backup-introduction-to-azure-backup.md)を確認してください。

## <a name="scheduler"></a>Scheduler

Azure Scheduler は非推奨とされています。 Azure Logic Apps を使用して、スケジュール ジョブを作成します。

詳細:

- [Logic Apps のチュートリアル](https://docs.microsoft.com/azure/logic-apps/#step-by-step-tutorials)を完了して Azure Logic Apps の機能について理解を深めてください。
- [Logic Apps の概要](../logic-apps/logic-apps-overview.md)を確認してください。

## <a name="network-watcher"></a>Network Watcher

現時点では、Azure Network Watcher インスタンスの Azure Germany からグローバル Azure への移行はサポートされていません。 グローバル Azure で、新しい Network Watcher インスタンスを作成および構成することをお勧めします。 次に、古い環境と新しい環境の間で結果を比較します。 

詳細:

- [Network Watcher のチュートリアル](https://docs.microsoft.com/azure/network-watcher/#step-by-step-tutorials)を完了して、新しい知識を得ましょう。
- [Network Watcher の概要](../network-watcher/network-watcher-monitoring-overview.md)を確認してください。
- [ネットワーク セキュリティ グループのフロー ログ](../network-watcher/network-watcher-nsg-flow-logging-portal.md)について学習してください。
- [接続モニター](../network-watcher/connection-monitor.md)について確認してください。

## <a name="site-recovery"></a>Site Recovery

現在の Azure Site Recovery の設定をグローバル Azure に移行することはできません。 グローバル Azure で新しい Site Recovery ソリューションを設定する必要があります。

Site Recovery の詳細について、また、Azure Germany からグローバル Azure に VM を移行する方法については、[Site Recovery の使用方法](./germany-migration-compute.md#compute-iaas)に関するページを参照してください。

次のステップ バイ ステップ チュートリアルを完了して、新しい知識を得ましょう。

- [Azure から Azure へのディザスター リカバリー](https://docs.microsoft.com/azure/site-recovery/#azure-to-azure)
- [VMware から Azure へのディザスター リカバリー](https://docs.microsoft.com/azure/site-recovery/#vmware)
- [Hyper-V から Azure へのディザスター リカバリー](https://docs.microsoft.com/azure/site-recovery/#hyper-v)

## <a name="azure-policies"></a>Azure のポリシー

Azure Germany からグローバル Azure にポリシーは直接移行することはできません。 移行中、割り当て済みのポリシーのスコープは、通常、変更されます。 特に、このシナリオのように、サブスクリプションがターゲット環境で異なる場合にそのようになります。 ただし、ポリシー定義を保持し、それをグローバル Azure で再利用することができます。

Azure CLI では、次のコマンドを実行して、現在ご利用の環境内のポリシーをすべて一覧表示することができます。

> [!NOTE]
> 次のコマンドを実行する前に、必ず、Azure CLI 内で AzureGermanCloud 環境に切り替えてください。


```azurecli
az policy definition list --query '[].{Type:policyType,Name:name}' --output table
```

**PolicyType** の値が **Custom** となっているポリシーのみをエクスポートします。 **policyRule** をファイルにエクスポートします。 次の例では、カスタム ポリシー "Allow Germany Central Only" (短縮形: `allowgconly`) を現在のフォルダーにあるファイルにエクスポートしています。 

```azurecli
az policy definition show --name allowgconly --output json --query policyRule > policy.json
```

エクスポート ファイルは、次の例のようになります。

```json
{
  "if": {
    "not": {
      "equals": "germanycentral",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

次に、グローバル Azure 環境に切り替えます。 ファイルを編集してポリシー規則を変更します。 たとえば、`germanycentral` を `westeurope` に変更します。

```json
{
  "if": {
    "not": {
      "equals": "westeurope",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

新しいポリシーの作成: 

```azurecli
cat policy.json |az policy definition create --name "allowweonly" --rules @-
```

これで、`allowweonly` という名前の新しいポリシーが用意できました。 このポリシーでは、リージョンとして西ヨーロッパのみが許可されます。

必要に応じて、新しい環境でスコープにポリシーを割り当てます。 次のコマンドを実行することで、Azure Germany 内の古い割り当てを文書化することができます。

```azurecli
az policy assignment list
```

詳細:

- [Azure DNS ポリシーのチュートリアル](../governance/policy/tutorials/create-and-manage.md)を完了して、新しい知識を得ましょう。
- [Azure CLI を使用してポリシーを表示](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-azure-cli)する方法または[PowerShell を使用してポリシーを表示](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-powershell)する方法を確認してください。
- [Azure CLI を使用してポリシーの定義を作成](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-azure-cli)する方法または [PowerShell を使用してポリシーの定義を作成](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-powershell)する方法を確認してください。

## <a name="next-steps"></a>次の手順

次のサービス カテゴリ内のリソースを移行するためのツール、テクニック、および推奨事項を確認します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [データベース](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [セキュリティ](./germany-migration-security.md)
- [メディア](./germany-migration-media.md)
