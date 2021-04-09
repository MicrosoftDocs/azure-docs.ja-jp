---
title: Azure portal を使用して Azure 内部ロード バランサーを別の Azure リージョンに移動する
description: Azure portal を使用して Azure 内部ロード バランサーを Azure リージョン間で移動するには、Azure Resource Manager テンプレートを使用します
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 68a2cb6926cb41956711a9e3c15d21c250d27f0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94698491"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Azure portal を使用して Azure 内部ロード バランサーを別のリージョンに移動する

既存の内部ロード バランサーをリージョン間で移動することが必要になるさまざまなシナリオがあります。 たとえば、テスト用に同じ構成で内部ロード バランサーを作成したい場合があります。 また、ディザスター リカバリー計画の一環として、内部ロード バランサーを別のリージョンに移動したい場合もあります。

Azure 内部ロード バランサーは、リージョン間で移動することはできません。 ただし、Azure Resource Manager テンプレートを使用すると、内部ロード バランサーの既存の構成と仮想ネットワークをエクスポートできます。  その後、そのロード バランサーと仮想ネットワークをテンプレートにエクスポートし、宛先リージョンに合わせてパラメーターを変更して、そのテンプレートを新しいリージョンにデプロイすることで、リソースを別のリージョンにステージングすることができます。  Resource Manager とテンプレートの詳細については、「[クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに Azure 内部ロード バランサーが存在することを確認します。

- Azure 内部ロード バランサーは、リージョン間で移動することはできません。  新しいロード バランサーをターゲット リージョンのリソースに関連付ける必要があります。

- 内部ロード バランサーの構成をエクスポートし、別のリージョンに内部ロード バランサーを作成するためのテンプレートをデプロイするには、ネットワーク共同作成者ロール以上が必要です。

- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトには、ロード バランサー、ネットワーク セキュリティ グループ、仮想マシン、仮想ネットワークが含まれますが、その限りではありません。

- 自分の Azure サブスクリプションで、使用するターゲット リージョンに内部ロード バランサーを作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

- 自分のサブスクリプションに、このプロセスでロード バランサーの追加をサポートするのに十分なリソースがあることを確認してください。  「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)」をご覧ください。


## <a name="prepare-and-move"></a>準備と移動
次の手順では、Resource Manager テンプレートを使用して、内部ロード バランサーの移動を準備し、Azure portal を使用して内部ロード バランサーの構成をターゲット リージョンに移動する方法を示します。  このプロセスの一環として、内部ロード バランサーの仮想ネットワークの構成を含める必要があり、内部ロード バランサーを移動する前に、最初にこれを実行する必要があります。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>仮想ネットワーク テンプレートをエクスポートし、Azure portal からデプロイする

1. [Azure portal](https://portal.azure.com) >  **[リソース グループ]** にログインします。
2. ソース仮想ネットワークが含まれているリソース グループを探し、それをクリックします。
3. **[設定]**  >  **[テンプレートのエクスポート]** を選択します。
4. **[テンプレートのエクスポート]** ブレードで **[デプロイ]** を選択します。
5. **[テンプレート]**  >  **[パラメーターの編集]** をクリックして、オンライン エディターで **parameters.json** ファイルを開きます。
6. 仮想ネットワーク名のパラメーターを編集するには、**parameters** の **value** プロパティを変更します。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. エディターで、ソース仮想ネットワーク名の値を、ターゲット VNET に対して選択した名前に変更します。 名前は確実に引用符で囲みます。

8. エディターで **[保存]** をクリックします。

9. **[テンプレート]**  >  **[テンプレートの編集]** をクリックして、オンライン エディターで **template.json** ファイルを開きます。

10. VNET の移動先となるターゲット リージョンを編集するには、resources の下の **location** プロパティを変更します。

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

11. リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。  リージョンのコードは、スペースを含まないリージョン名です (**Central US** = **centralus**)。

12. また、必要に応じて、**template.json** ファイル内の他のパラメーターも変更できます。これは要件に基づくオプションです。

    * **アドレス空間** - VNET のアドレス空間は、保存する前に変更できます。変更は、**template.json** ファイルの **resources** > **addressSpace** セクションにある **addressPrefixes** プロパティで行います。

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **サブネット** - サブネットの名前とサブネットのアドレス空間は、変更または追加できます。その操作は、**template.json** ファイルの **subnets** セクションで行います。 サブネットの名前を変更するには、**name** プロパティを変更します。 サブネット アドレス空間を変更するには、**template.json** ファイルの **addressPrefix** プロパティを変更します。

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         **template.json** ファイルでアドレス プレフィックスを変更するには、上で示したセクションと、後で示す **type** セクションの 2 か所を変更する必要があります。  **addressPrefix** プロパティを上記と一致するように変更します。

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

13. オンライン エディターで **[保存]** をクリックします。

14. **[基本]**  >  **[サブスクリプション]** をクリックして、ターゲット VNET をデプロイするサブスクリプションを選択します。

15. **[基本]**  >  **[リソース グループ]** をクリックして、ターゲット VNET をデプロイするリソース グループを選択します。  **[新規作成]** をクリックして、ターゲット VNET 用の新しいリソース グループを作成できます。  名前が既存の VNET のソース リソース グループと同じでないことを確認します。

16. **[基本]**  >  **[場所]** が VNET をデプロイするターゲットの場所に設定されていることを確認します。

17. **[設定]** で、名前が上記のパラメーター エディターで入力した名前と一致することを確認します。

18. **[使用条件]** のチェック ボックスをオンにします。

19. **[購入]** ボタンをクリックして、ターゲット仮想ネットワークをデプロイします。

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>内部ロード バランサー テンプレートをエクスポートして Azure PowerShell からデプロイする

1. [Azure portal](https://portal.azure.com) >  **[リソース グループ]** にログインします。
2. ソース内部ロード バランサーが含まれているリソース グループを探し、それをクリックします。
3. **[設定]**  >  **[テンプレートのエクスポート]** を選択します。
4. **[テンプレートのエクスポート]** ブレードで **[デプロイ]** を選択します。
5. **[テンプレート]**  >  **[パラメーターの編集]** をクリックして、オンライン エディターで **parameters.json** ファイルを開きます。

6. 内部ロード バランサー名のパラメーターを編集するには、ソース内部ロード バランサー名の **defaultValue** プロパティをターゲット内部ロード バランサーの名前に変更します。名前は必ず引用符で囲んでください。

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. 上で移動したターゲット仮想ネットワークの値を編集するには、まずリソース ID を取得し、それをコピーして **parameters.json** ファイルに貼り付ける必要があります。 ID を取得するには、次の手順に従います。

    1. 別のブラウザー タブまたはウィンドウで、[Azure portal](https://portal.azure.com) >  **[リソース グループ]** にログインします。
    2. 前の手順で移動した仮想ネットワークが含まれているターゲット リソース グループを探し、それをクリックします。
    3. **[設定]**  >  **[プロパティ]** を選択します。
    4. 右側のブレードで **リソース ID** を強調表示し、それをクリップボードにコピーします。  または、**[リソース ID]** パスの右側にある **[クリップボードにコピー]** ボタンをクリックすることもできます。
    5. 別のブラウザー ウィンドウまたはタブに開いた **[パラメーターの編集]** エディターで、**defaultValue** プロパティにリソース ID を貼り付けます。

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. オンライン エディターで **[保存]** をクリックします。

7. **[テンプレート]**  >  **[テンプレートの編集]** をクリックして、オンライン エディターで **template.json** ファイルを開きます。
8. 内部ロード バランサー構成を移動するターゲット リージョンを編集するには、**template.json** ファイルで **resources** の下の **location** プロパティを変更します。

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。  リージョンのコードは、スペースを含まないリージョン名です (**Central US** = **centralus**)。

10. また、必要に応じて、テンプレート内の他のパラメーターも変更できます。これらは、実際の要件に応じて省略可能です。

    * **SKU** - 構成の内部ロード バランサーの SKU を、Standard から Basic、または Basic から Standard に変更できます。そのためには、**template.json** ファイルの **sku** > **name** プロパティを変更します。

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Basic SKU と Standard SKU のロード バランサーの違いの詳細については、「[Azure Standard Load Balancer の概要](./load-balancer-overview.md)」を参照してください。

    * **負荷分散規則** - 構成の負荷分散規則を追加または削除できます。そのためには、**template.json** ファイルの **loadBalancingRules** セクションでエントリを追加または削除します。

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       負荷分散規則の詳細については、「[Azure Load Balancer とは](./load-balancer-overview.md)」を参照してください。

    * **プローブ** - 構成のロード バランサーのプローブを追加または削除できます。そのためには、**template.json** ファイルの **probes** セクションでエントリを追加または削除します。

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Azure Load Balancer の正常性プローブの詳細については、「[Load Balancer の正常性プローブ](./load-balancer-custom-probe-overview.md)」を参照してください。

    * **インバウンド NAT 規則** - ロード バランサーのインバウンド NAT 規則を追加または削除できます。そのためには、**template.json** ファイルの **inboundNatRules** セクションでエントリを追加または削除します。

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        インバウンド NAT 規則の追加または削除を完了するには、**template.json** ファイルの末尾の **type** プロパティとして、規則が存在するか削除されている必要があります。

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        インバウンド NAT 規則の詳細については、「[Azure Load Balancer とは](./load-balancer-overview.md)」を参照してください。

12. オンライン エディターで **[保存]** をクリックします。

13. **[基本]**  >  **[サブスクリプション]** をクリックして、ターゲット内部ロード バランサーをデプロイするサブスクリプションを選択します。

15. **[基本]**  >  **[リソース グループ]** をクリックして、ターゲット ロード バランサーをデプロイするリソース グループを選択します。  **[新規作成]** をクリックしてターゲット内部ロード バランサー用の新しいリソース グループを作成するか、上記で仮想ネットワーク用に作成した既存のリソース グループを選択できます。  名前が既存のソース内部ロード バランサーのソース リソース グループと同じでないことを確認します。

16. **[基本]**  >  **[場所]** が内部ロード バランサーをデプロイするターゲットの場所に設定されていることを確認します。

17. **[設定]** で、名前が上記のパラメーター エディターで入力した名前と一致することを確認します。  構成内のすべての仮想ネットワークにリソース ID が設定されていることを確認します。

18. **[使用条件]** のチェック ボックスをオンにします。

19. **[購入]** ボタンをクリックして、ターゲット仮想ネットワークをデプロイします。

## <a name="discard"></a>破棄

ターゲット仮想ネットワークと内部ロード バランサーを破棄する場合は、ターゲット仮想ネットワークと内部ロード バランサーが含まれているリソース グループを削除します。  これを行うには、ポータルのダッシュボードでリソース グループを選択し、概要ページの上部にある **[削除]** を選択します。

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、仮想ネットワークと内部ロード バランサーの移動を完了するには、ソース仮想ネットワークと内部ロード バランサーまたはリソース グループを削除します。 これを行うには、ポータルのダッシュボードで仮想ネットワークと内部ロード バランサーまたはリソース グループを選択し、各ページの上部にある **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure 内部ロード バランサーをリージョン間で移動し、ソース リソースをクリーンアップしました。  リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM を別のリージョンに移動する](../site-recovery/azure-to-azure-tutorial-migrate.md)