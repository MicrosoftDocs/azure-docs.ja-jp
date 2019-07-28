---
title: 仮想ネットワーク サービス エンドポイント - Azure Event Hubs | Microsoft Docs
description: この記事では、Microsoft.EventHub サービス エンドポイントを仮想ネットワークに追加する方法について説明します。
services: event-hubs
documentationcenter: ''
author: shubhavijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 15912ce2e100a4317e775d72972ca6eacfac0d42
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080535"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Azure Event Hubs で仮想ネットワーク サービス エンドポイントを使用する

Event Hubs と[仮想ネットワーク (VNet) サービス エンドポイント][vnet-sep]の統合により、メッセージング機能へのアクセスを、仮想ネットワークにバインドされている仮想マシンなどのワークロードから保護し、ネットワーク トラフィック パスを両端で保護できます。

少なくとも 1 つの仮想ネットワーク サブネット サービス エンドポイントにバインドするように構成した後、それぞれの Event Hubs 名前空間は、仮想ネットワークの承認されたサブネットを除くどこからのトラフィックも受け入れなくなります。 仮想ネットワークの観点からは、サービス エンドポイントに Event Hubs 名前空間をバインドすると、仮想ネットワーク サブネットからメッセージング サービスへの分離されたネットワーク トンネルが構成されます。 

その結果、メッセージング サービス エンドポイントの監視可能なネットワーク アドレスがパブリック IP 範囲内にあるにもかかわらず、サブネットにバインドされたワークロードとそれぞれの Event Hubs 名前空間の間にプライベートな分離された関係が確立されます。 この動作には例外があります。 サービス エンドポイントを有効にすると、デフォルトでは、仮想ネットワークに関連付けらる IP ファイアウォールで denyall ルールが有効にされます。 イベント ハブ パブリック エンドポイントへのアクセスを有効にするために、IP ファイアウォールで特定の IP アドレスを追加できます。 


>[!WARNING]
> 仮想ネットワーク統合を実装すると、他の Azure サービスが Event Hubs と対話するのを防ぐことができます。
>
> 仮想ネットワークが実装されているときは、信頼できる Microsoft サービスはサポートされません。
>
> 仮想ネットワークでは動作しない Azure の一般的なシナリオは次のとおりです (網羅的なリストでは**ない**ことに注意してください)
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

> [!IMPORTANT]
> 仮想ネットワークは、**Standard** レベルと **Dedicated** レベルの Event Hubs でサポートされます。 Basic レベルではサポートされません。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet の統合によって有効になる高度なセキュリティのシナリオ 

厳格でコンパートメント化されたセキュリティを要求し、仮想ネットワーク サブネットがコンパートメント化されたサービス間のセグメント化を提供するソリューションでも、それらのコンパートメント内に存在するサービス間の通信パスが必要です。

TCP/IP 上で HTTPS を搬送するものを含め、コンパートメント間の直接の IP ルートには、ネットワーク レイヤーから上のレイヤーの脆弱性を悪用されるリスクがあります。 メッセージング サービスは完全に隔離された通信パスを提供し、そこではメッセージがパーティ間を移動するときにディスクにも書き込まれます。 両方とも同じ Event Hubs インスタンスにバインドされている 2 つの異なる仮想ネットワーク内のワークロードは、それぞれのネットワークの分離境界の整合性を維持しながら、メッセージを介して効率よく確実に通信できます。
 
つまり、セキュリティ対策されたクラウド ソリューションは、信頼性が高くスケーラブルな Azure の業界をリードする非同期メッセージング機能にアクセスできるだけでなく、メッセージングを使用して、セキュリティで保護されたソリューション コンパートメント間に通信パスを作成できます。この通信パスは、HTTPS やその他の TLS で保護されたソケット プロトコルを含むピアツーピア通信モードよりも本質的に安全です。

## <a name="bind-event-hubs-to-virtual-networks"></a>仮想ネットワークへの Event Hubs のバインド

"*仮想ネットワーク規則*" は、Azure Event Hubs 名前空間が特定の仮想ネットワーク サブネットからの接続を許可するかどうかを制御するファイアウォール セキュリティ機能です。

仮想ネットワークへの Event Hubs 名前空間のバインドは、2 ステップのプロセスです。 まず、仮想ネットワーク サブネットに**仮想ネットワーク サービス エンドポイント**を作成し、[サービス エンドポイントの概要][vnet-sep]に関する記事で説明しているように、"Microsoft.EventHub" に対して有効にする必要があります。 サービス エンドポイントを追加した後、Event Hubs 名前空間を "*仮想ネットワーク規則*" にバインドします。

仮想ネットワーク規則は、Event Hubs 名前空間と仮想ネットワーク サブネットの関連付けです。 ルールが存在する間、サブネットにバインドされているすべてのワークロードには、Event Hubs 名前空間へのアクセス権が付与されます。 Event Hubs 自体は送信接続を確立することはなく、アクセス許可を取得する必要はないので、このルールを有効にすることでサブネットへのアクセス権が付与されることはありません。

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して仮想ネットワーク規則を作成する

次の Resource Manager テンプレートでは、既存の Event Hubs 名前空間に仮想ネットワーク規則を追加できます。

テンプレート パラメーター:

* **namespaceName**:Event Hubs 名前空間。
* **vnetRuleName**:作成する仮想ネットワーク規則の名前。
* **virtualNetworkingSubnetId**:仮想ネットワーク サブネットの Resource Manager の完全修飾パス。たとえば、仮想ネットワークの既定のサブネットの場合は `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` です。

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

テンプレートをデプロイするには、[Azure Resource Manager][lnk-deploy] の手順に従います。

## <a name="next-steps"></a>次の手順

仮想ネットワークについて詳しくは、以下のリンクをご覧ください。

- [Azure 仮想ネットワーク サービス エンドポイント][vnet-sep]
- [Azure Event Hubs IP フィルタリング][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
