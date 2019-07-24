---
title: Azure Service Bus の Virtual Network サービス エンドポイントと規則 | Microsoft Docs
description: 仮想ネットワークに Microsoft.ServiceBus サービス エンドポイントを追加します。
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: 0801469d586e6f2d6514927cdc7b894900a3aa35
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57843571"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Azure Service Bus で仮想ネットワーク サービス エンドポイントを使用する

Service Bus と[仮想ネットワーク (VNet) サービス エンドポイント][vnet-sep]の統合により、メッセージング機能へのアクセスを、仮想ネットワークにバインドされている仮想マシンなどのワークロードから保護し、ネットワーク トラフィック パスを両端で保護できます。

少なくとも 1 つの仮想ネットワーク サブネット サービス エンドポイントにバインドするように構成した後、それぞれの Service Bus 名前空間は、承認された仮想ネットワークを除くどこからのトラフィックも受け入れなくなります。 仮想ネットワークの観点からは、サービス エンドポイントに Service Bus 名前空間をバインドすると、仮想ネットワーク サブネットからメッセージング サービスへの分離されたネットワーク トンネルが構成されます。

その結果、メッセージング サービス エンドポイントの監視可能なネットワーク アドレスがパブリック IP 範囲内にあるにもかかわらず、サブネットにバインドされたワークロードとそれぞれの Service Bus 名前空間の間にプライベートな分離された関係が確立されます。

>[!WARNING]
> 仮想ネットワーク統合を実装すると、他の Azure サービスが Service Bus と対話するのを防ぐことができます。
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
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> 仮想ネットワークは、[Premium レベル](service-bus-premium-messaging.md)の Service Bus 名前空間でのみサポートされます。

## <a name="enable-service-endpoints-with-service-bus"></a>Service Bus でサービス エンドポイントを有効にする

Service Bus で VNet サービス エンドポイントを使用するときの重要な考慮事項は、Standard レベルと Premium レベルの Service Bus 名前空間が混在するアプリケーションではこれらのエンドポイントを有効にしないほうがよい、ということです。 Standard レベルは VNet をサポートせず、エンドポイントは Premium レベルの名前空間のみに限定されることがその理由です。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet の統合によって有効になる高度なセキュリティのシナリオ 

厳格でコンパートメント化されたセキュリティを要求し、仮想ネットワーク サブネットがコンパートメント化されたサービス間のセグメント化を提供するソリューションでも、一般に、それらのコンパートメント内に存在するサービス間の通信パスが必要です。

TCP/IP 上で HTTPS を搬送するものを含め、コンパートメント間の直接の IP ルートには、ネットワーク レイヤーから上のレイヤーの脆弱性を悪用されるリスクがあります。 メッセージング サービスは完全に隔離された通信パスを提供し、そこではメッセージがパーティ間を移動するときにディスクにも書き込まれます。 両方とも同じ Service Bus インスタンスにバインドされている 2 つの異なる仮想ネットワーク内のワークロードは、それぞれのネットワークの分離境界の整合性を維持しながら、メッセージを介して効率よく確実に通信できます。
 
つまり、セキュリティ対策されたクラウド ソリューションは、信頼性が高くスケーラブルな Azure の業界をリードする非同期メッセージング機能にアクセスできるだけでなく、メッセージングを使用して、セキュリティで保護されたソリューション コンパートメント間に通信パスを作成できます。この通信パスは、HTTPS やその他の TLS で保護されたソケット プロトコルを含むピアツーピア通信モードよりも本質的に安全です。

## <a name="binding-service-bus-to-virtual-networks"></a>Service Bus を仮想ネットワークにバインドする

"*仮想ネットワーク ルール*" は、Azure Service Bus サーバーが特定の仮想ネットワーク サブネットからの接続を許可するかどうかを制御するファイアウォール セキュリティ機能です。

仮想ネットワークへの Service Bus 名前空間のバインドは、2 ステップのプロセスです。 まず、仮想ネットワーク サブネットに**仮想ネットワーク サービス エンドポイント**を作成し、[サービス エンドポイントの概要][vnet-sep]に関する記事で説明しているように、"Microsoft.ServiceBus" に対して有効にする必要があります。 サービス エンドポイントを追加した後、Service Bus 名前空間を "*仮想ネットワーク ルール*" にバインドします。

仮想ネットワーク ルールは、Service Bus 名前空間と仮想ネットワーク サブネットの関連付けです。 ルールが存在する間、サブネットにバインドされているすべてのワークロードには、Service Bus 名前空間へのアクセス権が付与されます。 Service Bus 自体は送信接続を確立することはなく、アクセス許可を取得する必要はないので、このルールを有効にすることでサブネットへのアクセス権が付与されることはありません。

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して仮想ネットワーク ルールを作成する

次の Resource Manager テンプレートでは、既存の Service Bus 名前空間に仮想ネットワーク ルールを追加できます。

テンプレート パラメーター:

* **namespaceName**:Service Bus 名前空間。
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

テンプレート:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
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
                    "service": "Microsoft.ServiceBus"
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
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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
- [Azure Service Bus IP フィルタリング][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md
