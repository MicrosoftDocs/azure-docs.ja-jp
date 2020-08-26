---
title: Azure Event Hubs のファイアウォール ルール | Microsoft Docs
description: ファイアウォール ルールを使用して、特定の IP アドレスから Azure Event Hubs への接続を許可します。
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: fbf3e67cdde43dbe3d5e02cd4b044d5473f409ac
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185130"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>特定の IP アドレスまたは範囲から Azure Event Hubs 名前空間へのアクセスを許可します
既定では、要求が有効な認証と承認を受けている限り、Event Hubs 名前空間にはインターネットからアクセスできます。 これは IP ファイアウォールを使用して、さらに [CIDR (クラスレス ドメイン間ルーティング)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 表記の一連の IPv4 アドレスまたは IPv4 アドレス範囲のみに制限できます。

この機能は、Azure Event Hubs へのアクセスを特定の既知のサイトからのみに制限したいシナリオで役立ちます。 ファイアウォール規則を使用すると、特定の IPv4 アドレスから送信されたトラフィックを受け入れる規則を構成できます。 たとえば、[Azure ExpressRoute][express-route] で Event Hubs を使用する場合、オンプレミス インフラストラクチャ IP アドレスからのトラフィックのみ許可する**ファイアウォール規則**を作成できます。 

>[!IMPORTANT]
> 許可されているパブリック IP アドレスで稼働中のサービスから要求が送信される場合を除き、Event Hubs 名前空間に対してファイアウォール規則を有効にすると、着信要求は既定でブロックされます。 ブロックされる要求には、他の Azure サービスからの要求、Azure portal からの要求、ログおよびメトリック サービスからの要求などが含まれます。 
>
> 以下に、IP フィルタリングが有効になっていると Event Hubs リソースにアクセスできないサービスの一部を示します。 この一覧はすべてを網羅しているわけでは**ない**ことに注意してください。
>
> - Azure Stream Analytics
> - Azure IoT Hub ルート
> - Azure IoT Device Explorer
> - Azure Event Grid
> - Azure Monitor (診断設定)
>
> 例外として、IP フィルターが有効になっている場合でも、特定の信頼できるサービスからの Event Hubs リソースへのアクセスを許可できます。 信頼できるサービスの一覧については、「[信頼できる Microsoft サービス](#trusted-microsoft-services)」を参照してください。

## <a name="ip-firewall-rules"></a>IP ファイアウォール規則
IP ファイアウォール規則は、Event Hubs 名前空間レベルで適用されます。 そのため、規則は、サポートされているプロトコルを使用するクライアントからのすべての接続に適用されます。 Event Hubs 名前空間上の許可 IP 規則に合致しない IP アドレスからの接続試行はすべて、未承認として拒否されます。 その応答に、IP 規則に関する記述は含まれません。 IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

## <a name="use-azure-portal"></a>Azure Portal の使用
このセクションでは、Azure portal を使用して、Event Hubs 名前空間に対する IP ファイアウォール規則を作成する方法について説明します。 

1. [Azure portal](https://portal.azure.com) で **Event Hubs 名前空間**に移動します。
4. 左側のメニューの **[設定]** で **[ネットワーク]** を選択します。 **Standard** または **Dedicated** 名前空間のみの **[ネットワーク]** タブが表示されます。 
    > [!NOTE]
    > 既定では、次の図に示すように、 **[選択されたネットワーク]** オプションが選択されています。 このページで IP ファイアウォール規則を指定しない、または仮想ネットワークを追加しない場合は、**パブリック インターネット**から (アクセス キーを使用して) 名前空間にアクセスできます。  

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="[ネットワーク] タブ - [選択されたネットワーク] オプション" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    **[すべてのネットワーク]** オプションを選択した場合、イベント ハブはあらゆる IP アドレスからの (アクセス キーを使用した) 接続を受け入れます。 この設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。 

    ![ファイアウォールで [すべてのネットワーク] のオプションが選択されている](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. アクセスを特定の IP アドレスに制限するには、 **[選択されたネットワーク]** オプションが選択されていることを確認します。 **[ファイアウォール]** セクションで、次の手順のようにします。
    1. 現在のクライアント IP にその名前空間へのアクセスを許可するには、 **[クライアント IP アドレスを追加する]** オプションを選択します。 
    2. **[アドレス範囲]** に、特定の IPv4 アドレスまたは IPv4 アドレスの範囲を CIDR 表記で入力します。 
3. **信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する**かどうかを指定します。 詳細については、「[信頼できる Microsoft サービス](#trusted-microsoft-services)」を参照してください。 

      ![[ファイアウォール] - [すべてのネットワーク] オプションが選択されている](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. ツール バーの **[保存]** を選択して設定を保存します。 ポータルの通知に確認が表示されるまで、数分間お待ちください。

    > [!NOTE]
    > 特定の仮想ネットワークへのアクセスを制限するには、[特定のネットワークからのアクセスの許可](event-hubs-service-endpoints.md)に関する記事をご覧ください。

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]


## <a name="use-resource-manager-template"></a>Resource Manager テンプレートの使用

> [!IMPORTANT]
> ファイアウォール ルールは、**Standard** レベルと **Dedicated** レベルの Event Hubs でサポートされます。 Basic レベルではサポートされません。

次の Resource Manager テンプレートでは、既存の Event Hubs 名前空間に IP フィルター規則を追加できます。

テンプレート パラメーター:

- **ipMask** は、1 つの IPv4 アドレスか、または CIDR 表記法で記述した IP アドレス ブロックです。 たとえば、CIDR 表記では、70.37.104.0/24 は 70.37.104.0 から 70.37.104.255 までの 256 個の IPv4 アドレスを表し、24 は範囲に対する有効プレフィックス ビット数を示します。

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
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
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
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
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
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

テンプレートをデプロイするには、[Azure Resource Manager][lnk-deploy] の手順に従います。

## <a name="next-steps"></a>次のステップ

Event Hubs へのアクセスを Azure 仮想ネットワークに制約するには、次のリンクをご覧ください。

- [Event Hubs の仮想ネットワーク サービス エンドポイント][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
