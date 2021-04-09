---
title: Azure Relay 名前空間に対する IP ファイアウォールを構成する
description: この記事では、ファイアウォール規則を使用して、特定の IP アドレスから Azure Relay 名前空間への接続を許可する方法を説明します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ad8feed5df49dcc4503226a5fae50195bb9d48aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91999503"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Azure Relay 名前空間に対する IP ファイアウォールを構成する
既定では、要求に有効な認証と承認がある限り、Relay 名前空間にはインターネットからアクセスできます。 これは IP ファイアウォールを使用して、さらに [CIDR (クラスレス ドメイン間ルーティング)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 表記の一連の IPv4 アドレスまたは IPv4 アドレス範囲のみに制限できます。

この機能は、Azure Relay へのアクセスを特定の既知のサイトからのみに制限するシナリオに役立ちます。 ファイアウォール規則を使用すると、特定の IPv4 アドレスから送信されたトラフィックを受け入れる規則を構成できます。 たとえば、[Azure ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) で Relay を使用する場合、オンプレミス インフラストラクチャ IP アドレスからのトラフィックのみ許可する **ファイアウォール規則** を作成できます。 


> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 


## <a name="enable-ip-firewall-rules"></a>IP ファイアウォール規則を有効にする
IP ファイアウォール規則は、名前空間レベルで適用されます。 したがって、規則は、サポートされているプロトコルを使用するクライアントからのすべての接続に適用されます。 名前空間で許可されている IP 規則と一致しない IP アドレスからの接続試行は、未承認として拒否されます。 IP 規則に関する記述は応答に含まれません。 IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

### <a name="use-azure-portal"></a>Azure Portal の使用
このセクションでは、Azure portal を使用して、名前空間に対する IP ファイアウォール規則を作成する方法について説明します。 

1. [Azure portal](https://portal.azure.com) で **[リレー名前空間]** に移動します。
2. 左側のメニューで、 **[ネットワーク]** オプションを選択します。 **[許可するアクセス元]** セクションで **[すべてのネットワーク]** オプションを選択した場合、Relay 名前空間では任意の IP アドレスからの接続が受け入れられます。 この設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。 

    ![[すべてのネットワーク] オプションが選択されている [ネットワーク] ページを示すスクリーンショット。](./media/ip-firewall/all-networks-selected.png)
1. アクセスを特定のネットワークと IP アドレスに制限するには、 **[選択されたネットワーク]** オプションを選択します。 **[ファイアウォール]** セクションで、次の手順のようにします。
    1. 現在のクライアント IP にその名前空間へのアクセスを許可するには、 **[クライアント IP アドレスを追加する]** オプションを選択します。 
    2. **[アドレス範囲]** に、特定の IPv4 アドレスまたは IPv4 アドレスの範囲を CIDR 表記で入力します。 

        ![[ファイアウォール] - [すべてのネットワーク] オプションが選択されている](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. ツール バーの **[保存]** を選択して設定を保存します。 ポータルの通知に確認が表示されるまで、数分間お待ちください。


### <a name="use-resource-manager-template"></a>Resource Manager テンプレートの使用
次の Resource Manager テンプレートでは、既存の Relay 名前空間に IP フィルター規則を追加できます。

テンプレートが受け取る 1 つのパラメーター **ipMask** は、1 つの IPv4 アドレスか、または CIDR 表記法で記述した IP アドレス ブロックです。 たとえば、CIDR 表記では、70.37.104.0/24 は 70.37.104.0 から 70.37.104.255 までの 256 個の IPv4 アドレスを表し、24 は範囲に対する有効プレフィックス ビット数を示します。

> [!NOTE]
> 可能な拒否ルールはありませんが、Azure Resource Manager テンプレートには、接続を制限しない **"Allow"** に設定された既定のアクション セットがあります。
> 仮想ネットワークまたはファイアウォールのルールを作成するときは、***"defaultAction"*** を変更する必要があります。
> 
> from
> ```json
> "defaultAction": "Allow"
> ```
> to
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.Relay/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "virtualNetworkRules": [],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

テンプレートをデプロイするには、[Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md) の手順に従います。



## <a name="next-steps"></a>次のステップ
ネットワーク セキュリティ関連の他の機能の詳細については、[ネットワーク セキュリティ](network-security.md)に関するページを参照してください。


<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
