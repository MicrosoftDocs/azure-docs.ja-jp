---
title: Azure PowerShell を使用して Azure 内部ロード バランサーを別の Azure リージョンに移動する
description: Azure Resource Manager テンプレートを使用すると、Azure PowerShell を使用して Azure 内部ロード バランサーを Azure リージョン間で移動できます
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 2c89ad69207a51a92b56d268c685aa2be4118cf1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507587"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>PowerShell を使用して Azure 内部ロード バランサーを別のリージョンに移動する

既存の内部ロード バランサーをリージョン間で移動することが必要になるさまざまなシナリオがあります。 たとえば、テスト用に同じ構成で内部ロード バランサーを作成したい場合があります。 また、ディザスター リカバリー計画の一環として、内部ロード バランサーを別のリージョンに移動したい場合もあります。

Azure 内部ロード バランサーは、リージョン間で移動することはできません。 ただし、Azure Resource Manager テンプレートを使用すると、内部ロード バランサーの既存の構成と仮想ネットワークをエクスポートできます。  その後、そのロード バランサーと仮想ネットワークをテンプレートにエクスポートし、宛先リージョンに合わせてパラメーターを変更して、そのテンプレートを新しいリージョンにデプロイすることで、リソースを別のリージョンにステージングすることができます。  Resource Manager とテンプレートの詳細については、「[リソース グループをテンプレートにエクスポートする](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)」を参照してください


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに Azure 内部ロード バランサーが存在することを確認します。

- Azure 内部ロード バランサーは、リージョン間で移動することはできません。  新しいロード バランサーをターゲット リージョンのリソースに関連付ける必要があります。

- 内部ロード バランサーの構成をエクスポートし、別のリージョンに内部ロード バランサーを作成するためのテンプレートをデプロイするには、ネットワーク共同作成者ロール以上が必要です。
   
- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトには、ロード バランサー、ネットワーク セキュリティ グループ、仮想マシン、仮想ネットワークが含まれますが、その限りではありません。

- 自分の Azure サブスクリプションで、使用するターゲット リージョンに内部ロード バランサーを作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

- 自分のサブスクリプションに、このプロセスでロード バランサーの追加をサポートするのに十分なリソースがあることを確認してください。  「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)」をご覧ください。


## <a name="prepare-and-move"></a>準備と移動
次の手順では、Resource Manager テンプレートを使用した移動に向けて内部ロード バランサーの準備を行い、Azure PowerShell を使用して内部ロード バランサーの構成をターゲット リージョンに移動する方法を示します。  このプロセスの一環として、内部ロード バランサーの仮想ネットワークの構成を含める必要があり、内部ロード バランサーを移動する前に、最初にこれを実行する必要があります。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>仮想ネットワーク テンプレートをエクスポートし、Azure PowerShell からデプロイする

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) を使用して、ターゲット リージョンに移動する仮想ネットワークのリソース ID を取得し、変数に設定します。

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) コマンドを実行するディレクトリの .json ファイルに、ソース仮想ネットワークをエクスポートします。
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. ダウンロードされるファイルの名前は、リソースのエクスポート元のリソース グループの名前に基づいて付けられます。  コマンドからエクスポートされた **\<resource-group-name>.json** という名前のファイルを探し、任意のエディターで開きます。
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 仮想ネットワーク名のパラメーターを編集するには、ソース仮想ネットワーク名の **defaultValue** プロパティをターゲット仮想ネットワークの名前に変更します。名前は、引用符で囲みます。
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  VNET の移動先となるターゲット リージョンを編集するには、resources の下の **location** プロパティを変更します。

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
  
7. リージョンの場所コードを取得するには、次のコマンドを実行して、Azure PowerShell コマンドレットの [Get-AzLocation](/powershell/module/az.resources/get-azlocation) を使用できます。

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  また、必要に応じて、 **\<resource-group-name>.json** ファイル内の他のパラメーターも変更できます。これらは、実際の要件に応じて省略可能です。

    * **アドレス空間** - VNET のアドレス空間は、保存する前に変更できます。変更は、 **\<resource-group-name>.json** ファイルの **resources** > **addressSpace** セクションにある **addressPrefixes** プロパティで行います。

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

    * **サブネット** - サブネットの名前とサブネットのアドレス空間は、変更または追加できます。その操作は、 **\<resource-group-name>.json** ファイルの **subnets** セクションで行います。 サブネットの名前を変更するには、**name** プロパティを変更します。 サブネット アドレス空間を変更するには、 **\<resource-group-name>.json** ファイルの **addressPrefix** プロパティを変更します。

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

         **\<resource-group-name>.json** ファイルでアドレス プレフィックスを変更するには、上で示したセクションと、後で示す **type** セクションの 2 か所を変更する必要があります。  **addressPrefix** プロパティを上記と一致するように変更します。

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

9.  **\<resource-group-name>.json** ファイルを保存します。

10. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、デプロイするターゲット VNET のターゲット リージョンにリソース グループを作成します。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. 編集した **\<resource-group-name>.json** ファイルを、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用して、前の手順で作成したリソース グループにデプロイします。

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. ターゲット リージョンにリソースが作成されたことを確認するには、[Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) と [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) を使用します。
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>内部ロード バランサー テンプレートをエクスポートして Azure PowerShell からデプロイする

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) を使用して、ターゲット リージョンに移動する内部ロード バランサーのリソース ID を取得し、変数に設定します。

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) コマンドを実行するディレクトリの .json ファイルに、ソース内部ロード バランサー構成をエクスポートします。
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. ダウンロードされるファイルの名前は、リソースのエクスポート元のリソース グループの名前に基づいて付けられます。  コマンドからエクスポートされた **\<resource-group-name>.json** という名前のファイルを探し、任意のエディターで開きます。
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 内部ロード バランサー名のパラメーターを編集するには、ソース内部ロード バランサー名の **defaultValue** プロパティをターゲット内部ロード バランサーの名前に変更します。名前は必ず引用符で囲んでください。

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. 上で移動したターゲット仮想ネットワークの値を編集するには、まずリソース ID を取得し、それをコピーして **\<resource-group-name>.json** ファイルに貼り付ける必要があります。  ID を取得するには、[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) を使用します。
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    リソース ID を表示するには、変数を入力し、Enter キーを押します。  ID パスを強調表示してクリップボードにコピーします。

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  **\<resource-group-name>.json** ファイルで、変数の **Resource ID** を、ターゲット仮想ネットワーク ID の 2 つ目のパラメーターの **defaultValue** の代わりに貼り付けます。このパスは引用符で囲みます。
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. 内部ロード バランサー構成を移動するターゲット リージョンを編集するには、 **\<resource-group-name>.json** ファイルで **resources** の下の **location** プロパティを変更します。

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

11. リージョンの場所コードを取得するには、次のコマンドを実行して、Azure PowerShell コマンドレットの [Get-AzLocation](/powershell/module/az.resources/get-azlocation) を使用できます。

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. また、必要に応じて、テンプレート内の他のパラメーターも変更できます。これらは、実際の要件に応じて省略可能です。
    
    * **SKU** - 構成の内部ロード バランサーの SKU を、Standard から Basic、または Basic から Standard に変更できます。そのためには、 **\<resource-group-name>.json** ファイルの **sku** > **name** プロパティを変更します。

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
    
13. **\<resource-group-name>.json** ファイルを保存します。
    
10. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、デプロイするターゲット内部ロード バランサーのターゲット リージョンにリソース グループを作成します。 このプロセスの一環として、上記の既存のリソース グループを再利用することもできます。
    
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

デプロイ後に、ターゲット内の仮想ネットワークとロード バランサーを最初からやり直すか破棄する場合は、ターゲットに作成されたリソース グループを削除します。そうすると、移動した仮想ネットワークとロード バランサーが削除されます。  リソース グループを削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用します。

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、NSG の移動を完了するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) または [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) および [Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer) を使用して、ソース NSG またはリソース グループを削除します。

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure 内部ロード バランサーをリージョン間で移動し、ソース リソースをクリーンアップしました。  リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM を別のリージョンに移動する](../site-recovery/azure-to-azure-tutorial-migrate.md)