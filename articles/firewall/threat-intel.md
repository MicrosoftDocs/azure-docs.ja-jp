---
title: Azure Firewall の脅威インテリジェンスベースのフィルター処理
description: Azure Firewall の脅威インテリジェンスのフィルター処理について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 4ef9089c94d9e806cc519c4f8243cdcb7e73953a
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730535"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>Azure Firewall の脅威インテリジェンスベースのフィルター処理 - パブリック プレビュー

ファイアウォール用に脅威インテリジェンスベースのフィルター処理を有効にして、既知の悪意のある IP アドレスやドメインとの間のトラフィックの警告と拒否を行うことができます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。 [インテリジェント セキュリティ グラフ](https://www.microsoft.com/en-us/security/operations/intelligence)は、Microsoft の脅威インテリジェンスを動作させる機能です。Azure Security Center を含む複数のサービスによって使用されます。

![ファイアウォールの脅威インテリジェンス](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> 脅威インテリジェンス ベースのフィルター処理は、現在パブリック プレビュー段階であり、プレビューのサービス レベル契約が付属します。 特定の機能はサポート対象ではなく、機能が制限されることがあります。  詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

脅威インテリジェンスベースのフィルター処理を有効にすると、NAT ルール、ネットワーク ルール、またはアプリケーション ルールの前に、関連付けられているルールが処理されます。 プレビュー中は、信頼度の高いレコードのみが含まれます。

ルールがトリガーされるときのアラートのみを記録するかどうかと、アラートおよび拒否モードを選択できます。

既定では、脅威インテリジェンスベースのフィルター処理は、アラート モードで有効になっています。 この機能の無効化やモードの変更は、ポータルのインターフェイスがお住まいの地域で使用可能になるまで実行できません。

![脅威インテリジェンス ベースのフィルタリング ポータルのインターフェイス](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>ログ

次のログの抜粋は、トリガーされたルールを示しています。

```
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

## <a name="next-steps"></a>次の手順

- [Azure Firewall Log Analytics のサンプル](log-analytics-samples.md)を参照する
- [Azure Firewall のデプロイおよび構成](tutorial-firewall-deploy-portal.md)方法について説明する
- [マイクロソフト セキュリティ インテリジェンス レポート](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)を確認する