---
title: Azure PowerShell を使用して Azure 仮想ネットワークを別の Azure リージョンに移動する
description: Azure PowerShell を使用して Azure 仮想ネットワークを Azure リージョン間で移動するには、Azure Resource Manager テンプレートを使用します。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fe4c3fe131eb763ef8875cced91ab3ae22abca08
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077955"
---
# <a name="move-azure-virtual-network-to-another-region-using-azure-powershell"></a>Azure PowerShell を使用して Azure 仮想ネットワークを別のリージョンに移動する

既存の Azure 仮想ネットワーク (VNET) を別のリージョンに移動することが必要になるさまざまなシナリオがあります。 たとえば、既存の仮想ネットワークのテストや可用性のために、同じ構成で仮想ネットワークを作成することが必要になる場合があります。 ディザスター リカバリー計画の一部として、運用仮想ネットワークを別のリージョンに移動する必要がある場合もあります。

Azure Resource Manager テンプレートを使用して、別のリージョンへの仮想ネットワークの移動を実行できます。 これを行うには、仮想ネットワークをテンプレートにエクスポートし、移動先リージョンに合わせてパラメーターを変更した後、新しいリージョンにテンプレートをデプロイします。  Resource Manager とテンプレートの詳細については、「[リソース グループをテンプレートにエクスポートする](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)」を参照してください


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに Azure 仮想ネットワークが存在することを確認します。

- 仮想ネットワークをエクスポートし、テンプレートをデプロイして別のリージョンに仮想ネットワークを作成するには、ネットワーク共同作成者ロール以上が必要です。

- 仮想ネットワーク ピアリングは再作成されず、テンプレートにまだ存在する場合は失敗します。  テンプレートをエクスポートする前に仮想ネットワーク ピアを削除し、仮想ネットワークの移動後にピアを再確立する必要があります。
    
- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトにはロード バランサー、ネットワーク セキュリティ グループ (NSG)、パブリック IP が含まれますが、それらに限定されません。

- 自分の Azure サブスクリプションで、使用されるターゲット リージョンに仮想ネットワークを作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

- お使いのサブスクリプションに、このプロセス用の仮想ネットワークの追加をサポートするための十分なリソースがあることを確認します。  「[Azure サブスクリプションとサービスの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)」をご覧ください。


## <a name="prepare-and-move"></a>準備と移動
次の手順では、Resource Manager テンプレートを使用した仮想ネットワークの移動を準備し、Azure PowerShell コマンドを使用して仮想ネットワークをターゲット リージョンに移動する方法を示します。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-and-deploy-the-target-virtual-network-with-powershell"></a>PowerShell を使用して仮想ネットワークをエクスポートし、ターゲット仮想ネットワークをデプロイする

1. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0) を使用して、ターゲット リージョンに移動する仮想ネットワークのリソース ID を取得し、変数に設定します。

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) コマンドを実行するディレクトリの .json ファイルに、ソース仮想ネットワークをエクスポートします。
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. ダウンロードされるファイルの名前は、リソースのエクスポート元のリソース グループの名前に基づいて付けられます。  コマンドからエクスポートされた **\<resource-group-name>.json** という名前のファイルを探し、任意のエディターで開きます。
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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

6.  VNET を移動するターゲット リージョンを編集するには、resources の下の **location** プロパティを変更します。

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
  
7. リージョンの場所コードを取得するには、次のコマンドを実行して、Azure PowerShell コマンドレットの [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) を使用できます。

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  また、必要に応じて、 **\<resource-group-name>.json** ファイル内の次のような他のパラメーターも変更できます。これらは、要件に基づくオプションです。

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

         **\<resource-group-name>.json** ファイルでアドレス プレフィックスを変更するには、上で示したセクションと、後で示す **type** セクションの 2 か所を変更する必要があります。  **addressPrefix** プロパティを上記のものと一致するように変更します。

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

10. [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) を使用して、デプロイするターゲット VNET のターゲット リージョンにリソース グループを作成します
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. 編集した **\<resource-group-name>.json** ファイルを、[New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0) を使用して、前の手順で作成したリソース グループにデプロイします。

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. ターゲット リージョンにリソースが作成されたことを確認するには、[Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) と [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0) を使用します。
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>破棄 

デプロイ後に、ターゲット内の仮想ネットワークを最初からやり直すか破棄する場合は、ターゲットで作成されたリソース グループを削除します。これにより、移動した仮想ネットワークが削除されます。  リソース グループを削除するには、[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) を使用します。

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、仮想ネットワークの移動を完了するには、[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) または [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) を使用して、ソース仮想ネットワークまたはリソース グループを削除します

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure 仮想ネットワークをあるリージョンから別のリージョンに移動し、ソース リソースをクリーンアップしました。  リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure VM を別のリージョンに移動する](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
