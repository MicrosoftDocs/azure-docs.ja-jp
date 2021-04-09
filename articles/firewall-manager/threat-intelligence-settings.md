---
title: Azure Firewall の脅威インテリジェンスの構成
description: Azure Firewall ポリシー用に脅威インテリジェンスベースのフィルター処理を構成して、既知の悪意のある IP アドレスおよびドメインとの間で送受信されるトラフィックを警告および拒否する方法について説明します。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7ede1c917bb44dd31aa59855a0b7c83eb478700a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651725"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Azure Firewall の脅威インテリジェンスの構成

Azure Firewall ポリシー用に脅威インテリジェンスベースのフィルター処理を構成して、既知の悪意のある IP アドレスおよびドメインとの間で送受信されるトラフィックを警告および拒否できます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。 [インテリジェント セキュリティ グラフ](https://www.microsoft.com/security/operations/intelligence)は、Microsoft の脅威インテリジェンスを動作させる機能です。Azure Security Center を含む複数のサービスによって使用されます。<br>

脅威インテリジェンスベースのフィルター処理が構成されている場合は、すべての NAT ルール、ネットワーク ルール、またはアプリケーション ルールの前に、関連付けられているルールが処理されます。

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="脅威インテリジェンス ポリシー":::

## <a name="threat-intelligence-mode"></a>脅威インテリジェンス モード

脅威インテリジェンスは、次の表で説明する 3 つのモードのいずれかで構成できます。 既定では、脅威インテリジェンスベースのフィルター処理は、アラート モードで有効になっています。

|モード |説明  |
|---------|---------|
|`Off`     | ファイアウォールの脅威インテリジェンス機能が有効になっていません。 |
|`Alert only`     | 悪意のある既知の IP アドレスおよびドメインと通信するトラフィックがファイアウォールを通過すると、信頼度の高い警告を受信します。 |
|`Alert and deny`     | 悪意のある既知の IP アドレスおよびドメインと通信するトラフィックがファイアウォールを通過しようとしていることが検出されると、トラフィックがブロックされ、信頼度の高い警告を受信します。 |

> [!NOTE]
> 脅威インテリジェンス モードは、親ポリシーから子ポリシーに継承されます。 子ポリシーは、親ポリシーと同じか、より厳密なモードで構成する必要があります。

## <a name="allowlist-addresses"></a>許可リスト アドレス

脅威インテリジェンスによって偽陽性がトリガーされ、実際には有効なトラフィックがブロックされる可能性があります。 許可された IP アドレスのリストを構成して、指定したアドレス、範囲、またはサブネットが脅威インテリジェンスによってフィルター処理されないようにすることができます。  

![許可リスト アドレス](media/threat-intelligence-settings/allow-list.png)

CSV ファイルをアップロードすることで、一度に複数のエントリで許可リストを更新できます。 CSV ファイルには、IP アドレスと範囲のみを含めることができます。 ファイルに見出しを含めることはできません。

> [!NOTE]
> 脅威インテリジェンスの許可リスト アドレスは、親ポリシーから子ポリシーに継承されます。 親ポリシーに追加されたすべての IP アドレスまたは範囲は、すべての子ポリシーにも適用されます。

## <a name="logs"></a>ログ

次のログの抜粋は、悪意のあるサイトへの送信トラフィックに対してトリガーされるルールを示しています。

```json
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>テスト

- **送信テスト** - 環境が侵害されたことを意味するため、送信トラフィック アラートがまれに発生します。 テスト送信アラートの動作をサポートするため、アラートをトリガーするテスト用 FQDN が作成されています。 **testmaliciousdomain.eastus.cloudapp.azure.com** を送信テストに使用してください。

- **テスト受信** - DNAT ルールがファイアウォールで構成されている場合、受信トラフィックにアラートが表示されることを想定できます。 これは、特定のソースのみが DNAT ルールで許可されている場合でも該当し、それ以外の場合はトラフィックが拒否されます。 Azure Firewall では、既知のすべてのポート スキャナーでアラートが発行されるわけではありません。悪意のあるアクティビティにも参加していることが知られているスキャナーのみです。

## <a name="next-steps"></a>次のステップ

- [マイクロソフト セキュリティ インテリジェンス レポート](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)を確認する
