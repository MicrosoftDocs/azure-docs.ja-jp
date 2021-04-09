---
title: 仮想ネットワーク サービス エンドポイント - Azure Event Hubs | Microsoft Docs
description: この記事では、Microsoft.EventHub サービス エンドポイントを仮想ネットワークに追加する方法について説明します。
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: 1deef5b8bb4b883ec9c01c50a2a603d254b9caef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556525"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-virtual-networks"></a>特定の仮想ネットワークから Azure Event Hubs 名前空間へのアクセスを許可する 

Event Hubs と[仮想ネットワーク (VNet) サービス エンドポイント][vnet-sep]の統合により、仮想ネットワークにバインドされた仮想マシンなどのワークロードからメッセージング機能へのセキュリティで保護されたアクセスが可能になり、ネットワーク トラフィック パスは両端でセキュリティ保護されます。 仮想ネットワークは、**Standard** レベルと **Dedicated** レベルの Event Hubs でサポートされます。 **Basic** レベルではサポートされません。

少なくとも 1 つの仮想ネットワーク サブネット サービス エンドポイントにバインドするように構成した後、それぞれの Event Hubs 名前空間は、仮想ネットワークの承認されたサブネットを除くどこからのトラフィックも受け入れなくなります。 仮想ネットワークの観点からは、サービス エンドポイントに Event Hubs 名前空間をバインドすると、仮想ネットワーク サブネットからメッセージング サービスへの分離されたネットワーク トンネルが構成されます。 

その結果、メッセージング サービス エンドポイントの監視可能なネットワーク アドレスがパブリック IP 範囲内にあるにもかかわらず、サブネットにバインドされたワークロードとそれぞれの Event Hubs 名前空間の間にプライベートな分離された関係が確立されます。 この動作には例外があります。 サービス エンドポイントを有効にすると、既定では、仮想ネットワークに関連付けられた [IP ファイアウォール](event-hubs-ip-filtering.md)で `denyall` 規則が有効になります。 イベント ハブ パブリック エンドポイントへのアクセスを有効にするために、IP ファイアウォールで特定の IP アドレスを追加できます。 

>[!WARNING]
> 許可されている仮想ネットワークで稼働中のサービスから要求が送信される場合を除き、Event Hubs 名前空間に対して仮想ネットワークを有効にすると、着信要求は既定でブロックされます。 ブロックされる要求には、他の Azure サービスからの要求、Azure portal からの要求、ログおよびメトリック サービスからの要求などが含まれます。 例外として、仮想ネットワークが有効になっている場合でも、特定の信頼できるサービスからの Event Hubs リソースへのアクセスを許可できます。 信頼できるサービスの一覧については、[信頼できるサービス](#trusted-microsoft-services)に関するセクションを参照してください。

> [!IMPORTANT]
> 指定された IP アドレスまたは仮想ネットワークのサブネットからのトラフィックのみを許可するには、名前空間に少なくとも 1 つの IP 規則または仮想ネットワーク規則を指定します。 IP 規則も仮想ネットワーク規則も指定しない場合は、パブリック インターネット経由で (アクセス キーを使用して) 名前空間にアクセスできます。  

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet の統合によって有効になる高度なセキュリティのシナリオ 

厳格でコンパートメント化されたセキュリティを要求し、仮想ネットワーク サブネットがコンパートメント化されたサービス間のセグメント化を提供するソリューションでも、それらのコンパートメント内に存在するサービス間の通信パスが必要です。

TCP/IP 上で HTTPS を搬送するものを含め、コンパートメント間の直接の IP ルートには、ネットワーク レイヤーから上のレイヤーの脆弱性を悪用されるリスクがあります。 メッセージング サービスは、隔離された通信パスを提供し、そこではメッセージがパーティ間を移動するときにディスクにも書き込まれます。 両方とも同じ Event Hubs インスタンスにバインドされている 2 つの異なる仮想ネットワーク内のワークロードは、それぞれのネットワークの分離境界の整合性を維持しながら、メッセージを介して効率よく確実に通信できます。
 
つまり、セキュリティ対策されたクラウド ソリューションは、信頼性が高くスケーラブルな Azure の業界をリードする非同期メッセージング機能にアクセスできるだけでなく、メッセージングを使用して、セキュリティで保護されたソリューション コンパートメント間に通信パスを作成できます。この通信パスは、HTTPS やその他の TLS で保護されたソケット プロトコルを含むピアツーピア通信モードよりも本質的に安全です。

## <a name="bind-event-hubs-to-virtual-networks"></a>仮想ネットワークへの Event Hubs のバインド

"**仮想ネットワーク規則**" は、Azure Event Hubs 名前空間が特定の仮想ネットワーク サブネットからの接続を許可するかどうかを制御するファイアウォール セキュリティ機能です。

仮想ネットワークへの Event Hubs 名前空間のバインドは、2 ステップのプロセスです。 まず、仮想ネットワークのサブネット上に **仮想ネットワーク サービス エンドポイント** を作成し、[サービス エンドポイントの概要][vnet-sep]の記事で説明されているように、それを **Microsoft.EventHub** に対して有効にする必要があります。 サービス エンドポイントを追加した後、Event Hubs 名前空間を "**仮想ネットワーク規則**" にバインドします。

仮想ネットワーク規則は、Event Hubs 名前空間と仮想ネットワーク サブネットの関連付けです。 ルールが存在する間、サブネットにバインドされているすべてのワークロードには、Event Hubs 名前空間へのアクセス権が付与されます。 Event Hubs 自体は送信接続を確立することはなく、アクセス許可を取得する必要はないので、このルールを有効にすることでサブネットへのアクセス権が付与されることはありません。

## <a name="use-azure-portal"></a>Azure Portal の使用
このセクションでは、Azure portal を使用して仮想ネットワーク サービス エンドポイントを追加する方法を示します。 アクセスを制限するには、この Event Hubs 名前空間に対して仮想ネットワーク サービス エンドポイントを統合する必要があります。

1. [Azure portal](https://portal.azure.com) で **Event Hubs 名前空間** に移動します。
4. 左側のメニューの **[設定]** で **[ネットワーク]** を選択します。 **Standard** または **Dedicated** 名前空間のみの **[ネットワーク]** タブが表示されます。 

    > [!WARNING]
    > このページで **[選択されたネットワーク]** オプションを選択し、1 つ以上の IP ファイアウォール規則または仮想ネットワークを追加しない場合は、**パブリック インターネット** 経由で (アクセス キーを使用して) 名前空間にアクセスできます。 

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="[ネットワーク] タブ - [選択されたネットワーク] オプション" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    **[すべてのネットワーク]** オプションを選択した場合、イベント ハブはあらゆる IP アドレスからの (アクセス キーを使用した) 接続を受け入れます。 この設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。 

    ![ファイアウォールで [すべてのネットワーク] のオプションが選択されている](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. 特定のネットワークにアクセスを制限するには、ページの先頭にある **[選択されたネットワーク]** オプションを選択します (まだ選択されていない場合)。
2. ページの **[仮想ネットワーク]** セクションで、 **[+ 既存の仮想ネットワークを追加]** _ を選択します。 新しい VNet を作成する場合は、_ *[+ 新しい仮想ネットワークの作成]* * を選択します。 

    ![既存の仮想ネットワークを追加する](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)

    >[!WARNING]
    > このページで **[選択されたネットワーク]** オプションを選択し、1 つ以上の IP ファイアウォール規則または仮想ネットワークを追加しない場合は、パブリック インターネット経由で (アクセス キーを使用して) 名前空間にアクセスできます。
3. 仮想ネットワークの一覧から仮想ネットワークを選択し、**サブネット** を選択します。 仮想ネットワークを一覧に追加する前に、サービス エンドポイントを有効にする必要があります。 サービス エンドポイントが有効になっていない場合は、有効にするよう求められます。
   
   ![サブネットを選択する](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. **Microsoft.EventHub** に対して、サブネットのサービス エンドポイントが有効になると、次の成功メッセージが表示されます。 ページの下部にある **[追加]** を選択して、ネットワークを追加します。 

    ![サブネットを選択してエンドポイントを有効にする](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > サービス エンドポイントを有効にできない場合は、Resource Manager テンプレートを使用して、不足している仮想ネットワーク サービス エンドポイントを無視してもかまいません。 この機能はポータルでは使用できません。
5. **信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する** かどうかを指定します。 詳細については、「[信頼できる Microsoft サービス](#trusted-microsoft-services)」を参照してください。 
6. ツール バーの **[保存]** を選択して設定を保存します。 ポータルの通知に確認が表示されるまで、数分間お待ちください。

    ![ネットワークを保存する](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)

    > [!NOTE]
    > 特定の IP アドレスまたは範囲にアクセスを制限する方法については、[特定の IP アドレスまたは範囲からのアクセスの許可](event-hubs-ip-filtering.md)に関するページを参照してください。

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Resource Manager テンプレートの使用
次の Resource Manager テンプレートの例では、既存の Event Hubs 名前空間に仮想ネットワーク規則を追加します。 ネットワーク規則では、仮想ネットワーク内のサブネットの ID を指定します。 

この ID は、仮想ネットワーク サブネットの Resource Manager の完全修飾パスです。 たとえば、仮想ネットワークの既定のサブネットの場合は `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` です。

仮想ネットワークまたはファイアウォール規則を追加する場合は、`defaultAction` の値を `Deny` に設定します。


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
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

テンプレートをデプロイするには、[Azure Resource Manager][lnk-deploy] の手順に従います。

> [!IMPORTANT]
> IP 規則も仮想ネットワーク規則も指定されていない場合は、`defaultAction` を `deny` に設定しても、すべてのトラフィックが名前空間に送られます。  名前空間には、パブリック インターネット経由で (アクセス キーを使用して) アクセスできます。 指定された IP アドレスまたは仮想ネットワークのサブネットからのトラフィックのみを許可するには、名前空間に少なくとも 1 つの IP 規則または仮想ネットワーク規則を指定します。  

## <a name="next-steps"></a>次のステップ

仮想ネットワークについて詳しくは、以下のリンクをご覧ください。

- [Azure 仮想ネットワーク サービス エンドポイント][vnet-sep]
- [Azure Event Hubs IP フィルタリング][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
