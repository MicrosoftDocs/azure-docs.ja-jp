---
title: Azure PowerShell を使用して Azure 外部ロード バランサーを別の Azure リージョンに移動する
description: Azure PowerShell を使って Azure 外部ロード バランサーを Azure リージョン間で移動するには、Azure Resource Manager テンプレートを使用します。
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 94cb46536bcf029a9e71a7238772ccc7b186b1dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500276"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Azure PowerShell を使用して Azure 外部ロード バランサーを別のリージョンに移動する

既存の外部ロード バランサーをリージョン間で移動する必要があるさまざまなシナリオがあります。 たとえば、テスト用に同じ構成で外部ロード バランサーを作成する必要がある場合があります。 ディザスター リカバリー計画の一部として、外部ロード バランサーを別のリージョンに移動する必要がある場合もあります。

Azure 外部ロード バランサーをリージョン間で移動することはできません。 しかし、Azure Resource Manager テンプレートを使用して、外部ロード バランサーの既存の構成とパブリック IP をエクスポートすることはできます。  その後、ロード バランサーとパブリック IP をテンプレートにエクスポートして別のリージョンにリソースをステージし、宛先リージョンに合わせてパラメーターを変更してから、新しいリージョンにテンプレートをデプロイできます。  Resource Manager とテンプレートの詳細については、「[リソース グループをテンプレートにエクスポートする](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)」を参照してください


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに Azure 外部ロード バランサーが存在することを確認します。

- Azure 外部ロード バランサーは、リージョン間で移動することはできません。  新しいロード バランサーをターゲット リージョンのリソースに関連付ける必要があります。

- 外部ロード バランサーの構成をエクスポートし、別のリージョンに外部ロード バランサーを作成するためにテンプレートをデプロイするには、ネットワーク共同作成者ロール以上が必要です。
   
- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトには、ロード バランサー、ネットワーク セキュリティ グループ、パブリック IP、仮想ネットワークが含まれますが、この限りではありません。

- Azure サブスクリプションで、使用するターゲット リージョンに外部ロード バランサーを作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

- 自分のサブスクリプションに、このプロセスでロード バランサーの追加をサポートするのに十分なリソースがあることを確認してください。  「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)」をご覧ください。


## <a name="prepare-and-move"></a>準備と移動
次の手順では、Resource Manager テンプレートを使って外部ロード バランサーの移動を準備し、Azure PowerShell を使用して外部ロード バランサーの構成をターゲット リージョンに移動する方法を示します。  このプロセスの一部として、外部ロード バランサーのパブリック IP 構成を含める必要があります。これは、外部ロード バランサーを移動する前にまず行う必要があります。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>パブリック IP テンプレートをエクスポートして Azure PowerShell からデプロイする

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) を使用して、ターゲット リージョンに移動するパブリック IP のリソース ID を取得し、変数に配置します。

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) コマンドを実行するディレクトリの .json ファイルに、パブリック IP をエクスポートします。
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. ダウンロードされるファイルの名前は、リソースのエクスポート元のリソース グループの名前に基づいて付けられます。  コマンドからエクスポートされた **\<resource-group-name>.json** という名前のファイルを探し、任意のエディターで開きます。
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. パブリック IP 名のパラメーターを編集するには、ソース パブリック IP 名の **defaultValue** プロパティをターゲット パブリック IP の名前に変更し、名前が引用符で囲まれていることを確認します。
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. パブリック IP を移動するターゲット リージョンを編集するには、resources の下の **location** プロパティを変更します。

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
  
7. リージョンの場所コードを取得するには、次のコマンドを実行して、Azure PowerShell コマンドレットの [Get-AzLocation](/powershell/module/az.resources/get-azlocation) を使用できます。

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. また、必要に応じて、テンプレート内の他のパラメーターも変更できます。これらは、実際の要件に応じて省略可能です。

    * **SKU** - 構成のパブリック IP の SKU を、Standard から Basic、または Basic から Standard に変更できます。そのためには、 **\<resource-group-name>.json** ファイルの **sku** > **name** プロパティを変更します。

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

         Basic と Standard SKU のパブリック IP の違いについて詳しくは、「[パブリック IP アドレスの作成、変更、削除](../virtual-network/virtual-network-public-ip-address.md)」を参照してください。

    * **パブリック IP の割り当て方法** と **アイドル タイムアウト** - **publicIPAllocationMethod** プロパティを **Dynamic** から **Static** または **Static** から **Dynamic** に変更することで、テンプレートのこれらのオプションの両方を変更できます。 アイドル タイムアウトは、**idleTimeoutInMinutes** プロパティを目的の量に変えることで変更できます。  既定値は **4** です。

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
         ```

        割り当て方法とアイドル タイムアウト値の詳細については、「[パブリック IP アドレスの作成、変更、削除](../virtual-network/virtual-network-public-ip-address.md)」を参照してください。


9. **\<resource-group-name>.json** ファイルを保存します。

10. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、デプロイするターゲット パブリック IP のターゲット リージョンにリソース グループを作成します。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 編集した **\<resource-group-name>.json** ファイルを、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用して、前の手順で作成したリソース グループにデプロイします。

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. ターゲット リージョンにリソースが作成されたことを確認するには、[Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) と [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) を使用します。
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>外部ロード バランサー テンプレートをエクスポートして Azure PowerShell からデプロイする

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) を使用して、ターゲット リージョンに移動する外部ロード バランサーのリソース ID を取得し、変数に配置します。

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) コマンドを実行するディレクトリの .json ファイルに、ソース外部ロード バランサー構成をエクスポートします。
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. ダウンロードされるファイルの名前は、リソースのエクスポート元のリソース グループの名前に基づいて付けられます。  コマンドからエクスポートされた **\<resource-group-name>.json** という名前のファイルを探し、任意のエディターで開きます。
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 外部ロード バランサー名のパラメーターを編集するには、ソース外部ロード バランサー名の **defaultValue** プロパティをターゲット外部ロード バランサーの名前に変更し、名前が引用符で囲まれていることを確認します。

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  上で移動したターゲット パブリック IP の値を編集するには、まず、リソース ID を取得し、それをコピーして **\<resource-group-name>.json** ファイルに貼り付ける必要があります。  ID を取得するには、[Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) を使用します。

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    リソース ID を表示するには、変数を入力し、Enter キーを押します。  ID パスを強調表示してクリップボードにコピーします。

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  **\<resource-group-name>.json** ファイルで、パブリック IP の外部 ID に対する 2 つ目のパラメーターの **defaultValue** の代わりに、変数の **リソース ID** を貼り付け、パスが引用符で囲まれていることを確認します。

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  ロード バランサー用にアウトバウンド NAT 規則とアウトバウンド規則を構成した場合、このファイルには、アウトバウンド パブリック IP 用の外部 ID の 3 つ目のエントリが示されます。  **ターゲット リージョン** で上記の手順を繰り返してアウトバウンド パブリック IP 用の ID を取得し、そのエントリを **\<resource-group-name>.json** ファイルに貼り付けます。

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. 外部ロード バランサー構成を移動するターゲット リージョンを編集するには、 **\<resource-group-name>.json** ファイルで **resources** の下の **location** プロパティを変更します。

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

11. リージョンの場所コードを取得するには、次のコマンドを実行して、Azure PowerShell コマンドレットの [Get-AzLocation](/powershell/module/az.resources/get-azlocation) を使用できます。

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. また、必要に応じて、テンプレート内の他のパラメーターも変更できます。これらは、実際の要件に応じて省略可能です。
    
    * **SKU** - 構成の外部ロード バランサーの SKU を、Standard から Basic、または Basic から Standard に変更できます。そのためには、 **\<resource-group-name>.json** ファイルの **sku** > **name** プロパティを変更します。

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
      Basic SKU と Standard SKU のロード バランサーの違いの詳細については、「[Azure Standard Load Balancer の概要](./load-balancer-overview.md)」を参照してください。

    * **負荷分散規則** - 構成の負荷分散規則を追加または削除できます。そのためには、 **\<resource-group-name>.json** ファイルの **loadBalancingRules** セクションでエントリを追加または削除します。

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

    * **プローブ** - 構成のロード バランサーのプローブを追加または削除できます。そのためには、 **\<resource-group-name>.json** ファイルの **probes** セクションでエントリを追加または削除します。

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

    * **インバウンド NAT 規則** - ロード バランサーのインバウンド NAT 規則を追加または削除できます。そのためには、 **\<resource-group-name>.json** ファイルの **inboundNatRules** セクションでエントリを追加または削除します。

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
        インバウンド NAT 規則の追加または削除を完了するには、 **\<resource-group-name>.json** ファイルの末尾の **type** プロパティとして、規則が存在するか削除されている必要があります。

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

    * **アウトバウンド規則** - 構成のアウトバウンド規則を追加または削除できます。そのためには、 **\<resource-group-name>.json** ファイルの **outboundRules** プロパティを編集します。

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

         アウトバウンド規則の詳細については、「[Load Balancer のアウトバウンド規則](./load-balancer-outbound-connections.md#outboundrules)」を参照してください

13. **\<resource-group-name>.json** ファイルを保存します。
    
10. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、デプロイするターゲット外部ロード バランサーのターゲット リージョンにリソース グループを作成します。 このプロセスの一環として、上記の既存のリソース グループを再利用することもできます。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 編集した **\<resource-group-name>.json** ファイルを、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用して、前の手順で作成したリソース グループにデプロイします。

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. ターゲット リージョンにリソースが作成されたことを確認するには、[Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) と [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) を使用します。
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>破棄 

デプロイ後に、ターゲット内のパブリック IP とロード バランサーを最初からやり直すか破棄する場合は、ターゲットで作成されたリソース グループを削除します。これにより、移動したパブリック IP とロード バランサーが削除されます。  リソース グループを削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用します。

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、NSG の移動を完了するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) または [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) および [Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer) を使用して、ソース NSG またはリソース グループを削除します

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure ネットワーク セキュリティ グループをあるリージョンから別のリージョンに移動し、移動元リソースをクリーンアップしました。  リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM を別のリージョンに移動する](../site-recovery/azure-to-azure-tutorial-migrate.md)