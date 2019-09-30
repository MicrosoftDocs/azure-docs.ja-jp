---
title: Azure portal を使用して Azure 外部ロード バランサーを別の Azure リージョンに移動する
description: Azure portal を使って Azure 外部ロード バランサーを Azure リージョン間で移動するには、Azure Resource Manager テンプレートを使用します。
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: eda0d6e8fe56b985c3b29fa80cee880444d63741
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105300"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-the-azure-portal"></a>Azure portal を使用して Azure 外部ロード バランサーを別のリージョンに移動する

既存の外部ロード バランサーをリージョン間で移動する必要があるさまざまなシナリオがあります。 たとえば、テスト用に同じ構成で外部ロード バランサーを作成する必要がある場合があります。 ディザスター リカバリー計画の一部として、外部ロード バランサーを別のリージョンに移動する必要がある場合もあります。

Azure 外部ロード バランサーをリージョン間で移動することはできません。 しかし、Azure Resource Manager テンプレートを使用して、外部ロード バランサーの既存の構成とパブリック IP をエクスポートすることはできます。  その後、ロード バランサーとパブリック IP をテンプレートにエクスポートして別のリージョンにリソースをステージし、宛先リージョンに合わせてパラメーターを変更してから、新しいリージョンにテンプレートをデプロイできます。  Resource Manager とテンプレートの詳細については、「[リソース グループをテンプレートにエクスポートする](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)」を参照してください。


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに Azure 外部ロード バランサーが存在することを確認します。

- Azure 外部ロード バランサーは、リージョン間で移動することはできません。  新しいロード バランサーをターゲット リージョンのリソースに関連付ける必要があります。

- 外部ロード バランサーの構成をエクスポートし、別のリージョンに外部ロード バランサーを作成するためにテンプレートをデプロイするには、ネットワーク共同作成者ロール以上が必要です。
   
- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトには、ロード バランサー、ネットワーク セキュリティ グループ、パブリック IP、仮想ネットワークが含まれますが、この限りではありません。

- Azure サブスクリプションで、使用するターゲット リージョンに外部ロード バランサーを作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

- サブスクリプションに、このプロセス用のロード バランサーの追加をサポートするための十分なリソースがあることを確認してください。  「[Azure サブスクリプションとサービスの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)」をご覧ください。


## <a name="prepare-and-move"></a>準備と移動
次の手順では、Resource Manager テンプレートを使って外部ロード バランサーの移動を準備し、Azure portal を使用して外部ロード バランサーの構成をターゲット リージョンに移動する方法を示します。  このプロセスの一部として、外部ロード バランサーのパブリック IP 構成を含める必要があります。これは、外部ロード バランサーを移動する前にまず行う必要があります。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-the-portal"></a>パブリック IP テンプレートをエクスポートしてポータルからデプロイする

1. [Azure portal](http://portal.azure.com) >  **[リソース グループ]** にログインします。
2. ソース パブリック IP が含まれているリソース グループを探し、それをクリックします。
3. **[設定]**  >  **[テンプレートのエクスポート]** を選択します。
4. **[テンプレートのエクスポート]** ブレードで **[デプロイ]** を選択します。
5. **[テンプレート]**  >  **[パラメーターの編集]** をクリックして、オンライン エディターで **parameters.json** ファイルを開きます。
8. パブリック IP 名のパラメーターを編集するには、**parameters** > **value** の下にあるプロパティを、ソース パブリック IP 名からターゲット パブリック IP 名に変更し、名前が引用符で囲まれていることを確認します。

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

    エディターで **[保存]** をクリックします。

9.  **[テンプレート]**  >  **[テンプレートの編集]** をクリックして、オンライン エディターで **template.json** ファイルを開きます。 

10. パブリック IP を移動するターゲット リージョンを編集するには、**resources** の下の **location** プロパティを変更します。

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
  
11. リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。  リージョンのコードは、スペースを含まないリージョン名です (**Central US** = **centralus**)。
    
12. また、必要に応じて、テンプレート内の次のような他のパラメーターも変更できます。これらは、要件に基づくオプションです。

    * **SKU** - 構成のパブリック IP の SKU を、Standard から Basic、または Basic から Standard に変更できます。そのためには、**template.json** ファイルの **sku** > **name** プロパティを変更します。

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

        Basic と Standard SKU のパブリック IP の違いについて詳しくは、「[パブリック IP アドレスの作成、変更、削除](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)」を参照してください。

    * **パブリック IP の割り当て方法**と**アイドル タイムアウト** - **publicIPAllocationMethod** プロパティを **Dynamic** から**Static** または **Static** から **Dynamic** に変更することで、テンプレートのこれらのオプションの両方を変更できます。 アイドル タイムアウトは、**idleTimeoutInMinutes** プロパティを目的の量に変えることで変更できます。  既定値は **4** です。

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

        割り当て方法とアイドル タイムアウト値の詳細については、「[パブリック IP アドレスの作成、変更、削除](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)」を参照してください。

 
13. オンライン エディターで **[保存]** をクリックします。

14. **[基本]**  >  **[サブスクリプション]** をクリックして、ターゲット パブリック IP をデプロイするサブスクリプションを選択します。

15. **[基本]**  >  **[リソース グループ]** をクリックして、ターゲット パブリック IP をデプロイするリソース グループを選択します。  **[新規作成]** をクリックして、ターゲット パブリック IP の新しいリソース グループを作成できます。  名前が既存のソース パブリック IP のソース リソース グループと同じでないことを確認します。 

16. **[基本]**  >  **[場所]** がパブリック IP をデプロイするターゲットの場所に設定されていることを確認します。

17. **[設定]** で、名前が上のパラメーター エディターで入力した名前と一致することを確認します。

18. **[使用条件]** のチェック ボックスをオンにします。

19. **[購入]** ボタンをクリックして、ターゲット パブリック IP をデプロイします。
20. 移動するロード バランサーの送信 NAT で使用されているパブリック IP がもう 1 つある場合は、上記の手順を繰り返し、2 つ目の送信パブリック IP をターゲット リージョンにエクスポートしてデプロイします。

### <a name="export-the-external-load-balancer-template-and-deploy-from-the-azure-portal"></a>外部ロード バランサー テンプレートをエクスポートして Azure portal からデプロイする

1. [Azure portal](http://portal.azure.com) >  **[リソース グループ]** にログインします。
2. ソース外部ロード バランサーが含まれているリソース グループを探し、それをクリックします。
3. **[設定]**  >  **[テンプレートのエクスポート]** を選択します。
4. **[テンプレートのエクスポート]** ブレードで **[デプロイ]** を選択します。
5. **[テンプレート]**  >  **[パラメーターの編集]** をクリックして、オンライン エディターで **parameters.json** ファイルを開きます。

5. 外部ロード バランサー名のパラメーターを編集するには、ソース外部ロード バランサー名の **value** プロパティをターゲット外部ロード バランサーの名前に変更し、名前が引用符で囲まれていることを確認します。

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

6.  上で移動したターゲット パブリック IP の値を編集するには、まず、リソース ID を取得し、それをコピーして **parameters.json** ファイルに貼り付ける必要があります。 ID を取得するには、次の手順に従います。
    
    1. ブラウザーの別のタブつまりウィンドウで、[Azure portal](http://portal.azure.com) >  **[リソース グループ]** にログインします。
    2. 前の手順で移動したパブリック IP を含むターゲット リソース グループを見つけて、それをクリックします。
    3. **[設定]**  >  **[プロパティ]** を選択します。
    4. 右側のブレードで**リソース ID** を強調表示し、それをクリップボードにコピーします。  または、 **[リソース ID]** パスの右側にある **[クリップボードにコピー]** をクリックしてください。
    5. 別のブラウザー ウィンドウすなわちタブに開いた **[パラメーターの編集]** エディターで、**value** プロパティにリソース ID を貼り付けます。

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
    6. オンライン エディターで **[保存]** をクリックします。
   

7.  ロード バランサー用にアウトバウンド NAT 規則とアウトバウンド規則を構成した場合、このファイルには、アウトバウンド パブリック IP 用の外部 ID の 3 つ目のエントリが示されます。  **ターゲット リージョン**で上記の手順を繰り返してアウトバウンド パブリック IP 用の ID を取得し、そのエントリを **parameters.json** ファイルに貼り付けます。

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

8.  **[テンプレート]**  >  **[テンプレートの編集]** をクリックして、オンライン エディターで **template.json** ファイルを開きます。
9.  外部ロード バランサー構成を移動するターゲット リージョンを編集するには、**template.json**ファイルで **resources** の下の **location** プロパティを変更します。

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

10. リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。  リージョンのコードは、スペースを含まないリージョン名です (**Central US** = **centralus**)。

11. また、必要に応じて、テンプレート内の次のような他のパラメーターも変更できます。これらは、要件に基づくオプションです。
    
    * **SKU** - 構成の外部ロード バランサーの SKU を、Standard から Basic または Basic から Standard に変更できます。そのためには、**template.json** ファイルの **sku** > **name** プロパティを変更します。

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
      Basic SKU と Standard SKU のロード バランサーの違いについて詳しくは、「[Azure Standard Load Balancer の概要](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)」を参照してください

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
       負荷分散規則の詳細については、「[Azure Load Balancer の概要](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)」を参照してください

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
       Azure Load Balancer の正常性プローブの詳細については、「[Load Balancer の正常性プローブ](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)」を参照してください

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
        インバウンド NAT 規則の詳細については、「[Azure Load Balancer とは](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)」を参照してください

    * **アウトバウンド規則** - 構成のアウトバウンド規則を追加または削除できます。そのためには、**template.json** ファイルの **outboundRules** プロパティを編集します。

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

         アウトバウンド規則の詳細については、「[Load Balancer のアウトバウンド規則](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)」を参照してください

12. オンライン エディターで **[保存]** をクリックします。
    
13. **[基本]**  >  **[サブスクリプション]** をクリックして、ターゲット外部ロード バランサーをデプロイするサブスクリプションを選択します。

15. **[基本]**  >  **[リソース グループ]** をクリックして、ターゲット ロード バランサーをデプロイするリソース グループを選択します。  **[新規作成]** をクリックしてターゲット外部ロード バランサー用の新しいリソース グループを作成するか、上記でパブリック IP 用に作成した既存のリソース グループを選択できます。  名前が既存のソース外部ロード バランサーのソース リソース グループと同じでないことを確認します。 

16. **[基本]**  >  **[場所]** が外部ロード バランサーをデプロイするターゲットの場所に設定されていることを確認します。

17. **[設定]** で、名前が上のパラメーター エディターで入力した名前と一致することを確認します。  構成内のすべてのパブリック IP にリソース ID が設定されていることを確認します。

18. **[使用条件]** のチェック ボックスをオンにします。

19. **[購入]** ボタンをクリックして、ターゲット パブリック IP をデプロイします。

## <a name="discard"></a>破棄 

ターゲットのパブリック IP と外部ロード バランサーを破棄する場合は、ターゲットのパブリック IP と外部ロード バランサーを含むリソース グループを削除します。  これを行うには、ポータルのダッシュボードでリソース グループを選択し、概要ページの上部にある **[削除]** を選択します。

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、パブリック IP と外部ロード バランサーの移動を完了するには、ソースのパブリック IP と外部ロード バランサーまたはリソース グループを削除します。 これを行うには、ポータルのダッシュボードでパブリック IP と外部ロード バランサーまたはリソース グループを選択し、各ページの上部にある **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure 外部ロード バランサーをあるリージョンから別のリージョンに移動し、ソース リソースをクリーンアップしました。  リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure VM を別のリージョンに移動する](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
