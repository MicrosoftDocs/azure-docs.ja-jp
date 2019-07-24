---
title: Azure Event Hubs のファイアウォール ルール | Microsoft Docs
description: ファイアウォール ルールを使用して、特定の IP アドレスから Azure Event Hubs への接続を許可します。
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: ccb2fa7b0805b332957513c52c0c1051d068d2cc
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507537"
---
# <a name="use-firewall-rules"></a>ファイアウォール ルールを使用する

Azure Event Hubs が特定の既知のサイトからのみアクセスできるシナリオでは、ファイアウォール ルールにより、特定の IPv4 アドレスからのトラフィックの受け入れのルールを構成することができます。 たとえば、これらのアドレスは、企業の NAT ゲートウェイのアドレスである可能性があります。

## <a name="when-to-use"></a>いつ使用するか

指定した IP アドレス範囲のみからトラフィックを受信し、それ以外のすべてを拒否するように、Event Hubs 名前空間を設定する必要がある場合は、*ファイアウォール ルール*を利用して Event Hub エンドポイントを他の IP アドレスからブロックすることができます。 たとえば、[Azure Express Route][express-route] で Event Hubs を使用する場合、*ファイアウォール ルール*を作成して、オンプレミス インフラストラクチャ IP アドレスからのトラフィックを制限できます。

## <a name="how-filter-rules-are-applied"></a>フィルター規則の適用方法

IP フィルター規則は、Event Hubs 名前空間レベルで適用されます。 したがって、規則は、サポートされているプロトコルを使用するクライアントからのすべての接続に適用されます。

Event Hubs 名前空間上の許可 IP 規則に一致しない IP アドレスからの接続試行は、未承認として拒否されます。 IP 規則に関する記述は応答に含まれません。

## <a name="default-setting"></a>既定の設定

既定では、ポータルの Event Hubs の **[IP フィルター]** は空白になっています。 この既定の設定は、イベント ハブが任意の IP アドレスからの接続を受け入れることを意味します。 この既定の設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。

## <a name="ip-filter-rule-evaluation"></a>IP フィルター規則の評価

IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

>[!WARNING]
> ファイアウォールを実装すると、他の Azure サービスが Event Hubs と対話するのを禁止できます。
>
> IP フィルター処理 (ファイアウォール) が実装されているときは信頼できる Microsoft サービスはサポートされませんが、近日中に使用できるようになります。
>
> IP フィルター処理では動作しない Azure の一般的なシナリオは次のとおりです (網羅的なリストでは**ない**ことに注意してください)
> - Azure Monitor
> - Azure Stream Analytics
> - Azure Event Grid との統合
> - Azure IoT Hub ルート
> - Azure IoT Device Explorer
> - Azure データ エクスプローラー
>
> 次の Microsoft サービスが仮想ネットワーク上に存在する必要があります
> - Azure Web Apps 
> - Azure Functions

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用してファイアウォール ルールを作成する

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
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

テンプレートをデプロイするには、[Azure Resource Manager][lnk-deploy] の手順に従います。

## <a name="next-steps"></a>次の手順

Event Hubs へのアクセスを Azure 仮想ネットワークに制約するには、次のリンクをご覧ください。

- [Event Hubs の仮想ネットワーク サービス エンドポイント][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
