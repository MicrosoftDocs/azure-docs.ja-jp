---
title: Azure portal を使用して Azure 外部ロード バランサーを別の Azure リージョンに移動する
description: Azure portal を使用して外部ロード バランサーを Azure リージョン間で移動するには、Azure Resource Manager テンプレートを使用します。
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f83ff3d1d03354daef3466c1f48eaa505e378634
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94693751"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Azure portal を使用して外部ロード バランサーを別のリージョンに移動する

外部ロード バランサーをリージョン間で移動する必要があるさまざまなシナリオがあります。 たとえば、テスト用に同じ構成でもう 1 つの外部ロード バランサーを作成する必要がある場合があります。 ディザスター リカバリー計画の一部として、外部ロード バランサーを別のリージョンに移動する必要がある場合もあります。

文字どおりの意味では、Azure 外部ロード バランサーをリージョン間で移動することはできません。 しかし、Azure Resource Manager テンプレートを使用して、外部ロード バランサーの既存の構成とパブリック IP アドレスをエクスポートすることはできます。 その後、ロード バランサーとパブリック IP をテンプレートにエクスポートして別のリージョンにリソースをステージし、宛先リージョンに合わせてパラメーターを変更してから、新しいリージョンにテンプレートをデプロイできます。 Resource Manager とテンプレートの詳細については、「[リソース グループをテンプレートにエクスポートする](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)」を参照してください。


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに Azure 外部ロード バランサーが存在することを確認します。

- Azure 外部ロード バランサーは、リージョン間で移動することはできません。 新しいロード バランサーをターゲット リージョンのリソースに関連付ける必要があります。

- 外部ロード バランサーの構成をエクスポートし、別のリージョンに外部ロード バランサーを作成するためにテンプレートをデプロイするには、ネットワーク共同作成者以上のロールを割り当てられている必要があります。

- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトには、ロード バランサー、ネットワーク セキュリティ グループ、パブリック IP、仮想ネットワークが含まれますが、この限りではありません。

- Azure サブスクリプションで、ターゲット リージョンに外部ロード バランサーを作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

- 自分のサブスクリプションに、ロード バランサーの追加をサポートするのに十分なリソースがあることを確認してください。 「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)」をご覧ください。

## <a name="prepare-and-move"></a>準備と移動
次の手順では、Resource Manager テンプレートを使って外部ロード バランサーの移動を準備し、Azure portal を使用して外部ロード バランサーの構成をターゲット リージョンに移動する方法を示します。 まず、外部ロード バランサーのパブリック IP 構成をエクスポートする必要があります。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>パブリック IP テンプレートをエクスポートしてポータルからパブリック IP をデプロイする

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。
2. 移動元のパブリック IP が含まれているリソース グループを探し、それを選択します。
3. **[設定]**  >  **[テンプレートのエクスポート]** の順に選択します。
4. **[テンプレートのエクスポート]** の下の **[デプロイ]** を選択します。
5. **[テンプレート]**  >  **[パラメーターの編集]** の順に選択して、オンライン エディターで parameters.json ファイルを開きます。
8. パブリック IP 名のパラメーターを編集するには、**parameters** の下にある **value** プロパティを、移動元のパブリック IP 名から移動先のパブリック IP 名に変更します。 名前は引用符で囲みます。

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    エディターで **[保存]** を選択します。

9.  **[テンプレート]**  >  **[テンプレートの編集]** の順に選択して、オンライン エディターで template.json ファイルを開きます。

10. パブリック IP の移動先リージョンを編集するには、**resources** の下の **location** プロパティを変更します。

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```
  
    リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。 リージョンのコードは、スペースを含まないリージョン名です。 たとえば、米国中部のコードは、**centralus** です。
    
12. また、要件に応じて、次のような、テンプレート内の他のパラメーターも変更できます。

    * **SKU**。 構成のパブリック IP の SKU を、Standard から Basic、または Basic から Standard に変更できます。そのためには、template.json ファイルの **sku** の下の **name** プロパティを変更します。

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Basic と Standard SKU のパブリック IP の違いについては、「[パブリック IP アドレスの作成、変更、削除](../virtual-network/virtual-network-public-ip-address.md)」を参照してください。

    * **パブリック IP の割り当て方法** と **アイドル タイムアウト**。 パブリック IP の割り当て方法を変更するには、**publicIPAllocationMethod** プロパティを **Dynamic** から **Static**、または **Static** から **Dynamic** に変更します。 アイドル タイムアウトを変更するには、**idleTimeoutInMinutes** プロパティを目的の値に変更します。 既定値は **4** です。

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        割り当て方法とアイドル タイムアウト値については、「[パブリック IP アドレスの作成、変更、削除](../virtual-network/virtual-network-public-ip-address.md)」を参照してください。

 
13. オンライン エディターで **[保存]** を選択します。

14. **[基本]**  >  **[サブスクリプション]** の順に選択して、移動先のパブリック IP をデプロイするサブスクリプションを選択します。

15. **[基本]**  >  **[リソース グループ]** を選択して、移動先のパブリック IP をデプロイするリソース グループを選択します。 **[新規作成]** を選択して、移動先パブリック IP の新しいリソース グループを作成できます。 名前が既存の移動元パブリック IP の移動元リソース グループと同じでないことを確認します。

16. **[基本]**  >  **[場所]** が、パブリック IP をデプロイする移動先の場所に設定されていることを確認します。

17. **[設定]** で、名前が前にパラメーター エディターで入力した名前と一致することを確認します。

18. **[使用条件]** ボックスをオンにします。

19. **[購入]** を選択して、移動先パブリック IP をデプロイします。

20. 移動するロード バランサーのアウトバウンド NAT で使用されているパブリック IP がもう 1 つある場合は、前の手順を繰り返して、2 つ目のアウトバウンド パブリック IP を移動先リージョンにエクスポートしてデプロイします。

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>外部ロード バランサー テンプレートをエクスポートして Azure portal からロード バランサーをデプロイする

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。
2. 移動元外部ロード バランサーが含まれているリソース グループを探し、それを選択します。
3. **[設定]**  >  **[テンプレートのエクスポート]** の順に選択します。
4. **[テンプレートのエクスポート]** の下の **[デプロイ]** を選択します。
5. **[テンプレート]**  >  **[パラメーターの編集]** の順に選択して、オンライン エディターで parameters.json ファイルを開きます。

5. 外部ロード バランサー名のパラメーターを編集するには、移動元外部ロード バランサー名の **value** プロパティを移動先外部ロード バランサーの名前に変更します。 名前は引用符で囲みます。

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  前の手順で移動した移動先パブリック IP の値を編集するには、まず、リソース ID を取得し、それを parameters.json ファイルに貼り付ける必要があります。 ID を取得するには、次の手順に従います。

    1. 別のブラウザー タブまたはウィンドウで、[Azure portal](https://portal.azure.com) にサインインし、**[リソース グループ]** を選択します。
    2. 前の手順で移動したパブリック IP が含まれている移動先リソース グループを探します。 それを選択します。
    3. **[設定]** > **[プロパティ]** の順に選択します。
    4. 右側のブレードで **リソース ID** を強調表示し、それをクリップボードにコピーします。 または、**[リソース ID]** パスの右側にある **[クリップボードにコピー]** を選択することもできます。
    5. 別のブラウザー ウィンドウまたはタブで開いた **[パラメーターの編集]** エディターで、**value** プロパティにリソース ID を貼り付けます。

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. オンライン エディターで **[保存]** を選択します。


7.  ロード バランサー用にアウトバウンド NAT とアウトバウンド規則を構成した場合、このファイルには、アウトバウンド パブリック IP 用の外部 ID の 3 つ目のエントリが示されます。 **移動先リージョン** で前の手順を繰り返してアウトバウンド パブリック IP 用の ID を取得します。 その ID を parameters.json ファイルに貼り付けます。

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  **[テンプレート]**  >  **[テンプレートの編集]** の順に選択して、オンライン エディターで template.json ファイルを開きます。
9.  外部ロード バランサー構成の移動先リージョンを編集するには、template.json ファイルで **resources** の下の **location** プロパティを変更します。

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。 リージョンのコードは、スペースを含まないリージョン名です。 たとえば、米国中部のコードは、**centralus** です。

11. また、要件に応じて、次のような、テンプレート内の他のパラメーターも変更できます。

    * **SKU**。 構成の外部ロード バランサーの SKU を、Standard から Basic、または Basic から Standard に変更できます。そのためには、template.json ファイルの **sku** の下の **name** プロパティを変更します。

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Basic SKU と Standard SKU のロード バランサーの違いについては、「[Azure Standard Load Balancer の概要](./load-balancer-overview.md)」を参照してください。

    * **負荷分散規則**。 構成の負荷分散規則を追加または削除できます。そのためには、template.json ファイルの **loadBalancingRules** セクションでエントリを追加または削除します。

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
       負荷分散規則については、「[Azure Load Balancer の概要](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)」を参照してください。

    * **プローブ**。 構成のロード バランサーのプローブを追加または削除できます。そのためには、template.json ファイルの **probes** セクションでエントリを追加または削除します。

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
       詳細については、「[Load Balancer の正常性プローブ](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)」を参照してください。

    * **インバウンド NAT 規則**。 ロード バランサーのインバウンド NAT 規則を追加または削除できます。そのためには、template.json ファイルの **inboundNatRules** セクションでエントリを追加または削除します。

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
        インバウンド NAT 規則の追加または削除を完了するには、template.json ファイルの末尾の **type** プロパティとして、規則が存在するか削除されている必要があります。

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
        インバウンド NAT 規則については、「[Azure Load Balancer の概要](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)」を参照してください。

    * **アウトバウンド規則**。 構成のアウトバウンド規則を追加または削除できます。そのためには、template.json ファイルの **outboundRules** プロパティを編集します。

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         詳細については、「[Load Balancer のアウトバウンド規則](./load-balancer-outbound-connections.md#outboundrules)」を参照してください。

12. オンライン エディターで **[保存]** を選択します。

13. **[基本]**  >  **[サブスクリプション]** の順に選択して、移動先外部ロード バランサーをデプロイするサブスクリプションを選択します。

15. **[基本]**  >  **[リソース グループ]** の順に選択して、移動先ロード バランサーをデプロイするリソース グループを選択します。 **[新規作成]** を選択して、移動先外部ロード バランサーの新しいリソース グループを作成できます。 または、前にパブリック IP のために作成した既存のリソース グループを選択することもできます。 名前が既存の移動元外部ロード バランサーの移動元リソース グループと同じでないことを確認します。

16. **[基本]**  >  **[場所]** が外部ロード バランサーをデプロイする移動先の場所に設定されていることを確認します。

17. **[設定]** で、名前が前にパラメーター エディターで入力した名前と一致することを確認します。 構成内のすべてのパブリック IP にリソース ID が設定されていることを確認します。

18. **[使用条件]** ボックスをオンにします。

19. **[購入]** を選択して、移動先パブリック IP をデプロイします。

## <a name="discard"></a>破棄

移動先のパブリック IP と外部ロード バランサーを破棄する場合は、それらを含むリソース グループを削除します。 そうするには、ポータルのダッシュボードでリソース グループを選択し、概要ページの上部にある **[削除]** を選択します。

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、パブリック IP と外部ロード バランサーの移動を完了するには、ソースのパブリック IP と外部ロード バランサーまたはリソース グループを削除します。 そうするには、ポータルのダッシュボードでそのリソース グループを選択し、各ページの上部にある **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure 外部ロード バランサーをあるリージョンから別のリージョンに移動し、ソース リソースをクリーンアップしました。 リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM を別のリージョンに移動する](../site-recovery/azure-to-azure-tutorial-migrate.md)