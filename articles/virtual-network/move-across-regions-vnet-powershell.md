---
title: Azure PowerShell を使用して Azure 仮想ネットワークを別の Azure リージョンに移動する
description: Resource Manager テンプレートと Azure PowerShell を使用して Azure 仮想ネットワークを Azure リージョン間で移動します。
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fa89a458b23d18bc6fddfca9cf6d9f2a16f91669
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934931"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Azure PowerShell を使用して Azure 仮想ネットワークを別のリージョンに移動する

既存の Azure 仮想ネットワークをリージョン間で移動する場合、さまざまなシナリオがあります。 たとえば、テストや可用性のために、既存の仮想ネットワークと同じ構成で仮想ネットワークを作成することが必要になる場合があります。 または、ディザスター リカバリー計画の一環として、運用仮想ネットワークを別のリージョンに移動する必要がある場合もあります。

Azure Resource Manager テンプレートを使用して、別のリージョンへの仮想ネットワークの移動を実行できます。 これを行うには、仮想ネットワークをテンプレートにエクスポートし、移動先リージョンに合わせてパラメーターを変更した後、新しいリージョンにテンプレートをデプロイします。 Resource Manager テンプレートの詳細については、「[リソース グループをテンプレートにエクスポートする](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)」を参照してください。


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに仮想ネットワークが存在することを確認します。

- 仮想ネットワークをエクスポートし、テンプレートをデプロイして別のリージョンに仮想ネットワークを作成するには、ネットワーク共同作成者ロール以上が必要です。

- 仮想ネットワーク ピアリングは再作成されず、テンプレートにまだ存在する場合は失敗します。 テンプレートをエクスポートする前に、仮想ネットワーク ピアをすべて削除する必要があります。 それらは、仮想ネットワークを移動した後に再確立できます。
    
- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトにはロード バランサー、ネットワーク セキュリティ グループ (NSG)、パブリック IP が含まれますが、それらに限定されません。

- お使いの Azure サブスクリプションで、ターゲット リージョンに仮想ネットワークを作成できることを確認します。 必要なクォータを有効にするには、サポートに連絡してください。

- お使いのサブスクリプションに、このプロセス用の仮想ネットワークの追加をサポートするための十分なリソースがあることを確認します。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)」をご覧ください。


## <a name="prepare-for-the-move"></a>移動を準備する
このセクションでは、Resource Manager テンプレートを使用して、移動するために仮想ネットワークを準備します。 その後に、Azure PowerShell コマンドを使用して、仮想ネットワークをターゲット リージョンに移動します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell を使用して仮想ネットワークをエクスポートし、ターゲット仮想ネットワークをデプロイするには、次の操作を行います。

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) を使用して、ターゲット リージョンに移動する仮想ネットワークのリソース ID を取得し、変数に設定します。

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) コマンドを実行するディレクトリの .json ファイルに、ソース仮想ネットワークをエクスポートします。
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. ダウンロードされたファイルの名前は、リソースのエクスポート元のリソース グループの名前と同じになります。 コマンドでエクスポートした *\<resource-group-name>.json* ファイルを見つけて、エディターで開きます。
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. 仮想ネットワーク名のパラメーターを編集するには、ソース仮想ネットワーク名の **defaultValue** プロパティをターゲット仮想ネットワークの名前に変更します。 名前は、必ず引用符で囲みます。
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. 仮想ネットワークの移動先となるターゲット リージョンを編集するには、resources の下の **location** プロパティを変更します。

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
  
1. リージョンの場所コードを取得するには、次のコマンドを実行して、Azure PowerShell コマンドレットの [Get-AzLocation](/powershell/module/az.resources/get-azlocation) を使用できます。

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (省略可能) 必要に応じて、 *\<resource-group-name>.json* ファイル内の他のパラメーターを変更することもできます。

    * **[アドレス空間]** : ファイルを保存する前に、仮想ネットワークのアドレス空間を変更できます。これを行うには、**resources** > **addressSpace** セクションを変更し、**addressPrefixes** プロパティを変更します。

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

    * **サブネット**:ファイルの **subnets** セクションを変更して、サブネット名とサブネット アドレス空間の変更やこれらに対する追加を行うことができます。 サブネットの名前を変更するには、**name** プロパティを変更します。 また、サブネット アドレス空間を変更するには、**addressPrefix** プロパティを変更します。

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

        アドレス プレフィックスを変更するには、2 つの場所 (前のセクションのコード内と、次のコードの **type** セクション内) で編集します。 前のセクションのコード内の **addressPrefix** プロパティと一致するように、次のコードの **addressPrefix** プロパティを変更します。

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

1. *\<resource-group-name>.json* ファイルを保存します。

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、デプロイするターゲット仮想ネットワークのターゲット リージョンにリソース グループを作成します。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. 編集した *\<resource-group-name>.json* ファイルを、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用して、前の手順で作成したリソース グループにデプロイします。

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. ターゲット リージョンにリソースが作成されたことを確認するには、[Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) と [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) を使用します。
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>仮想ネットワークまたはリソース グループを削除する 

仮想ネットワークのデプロイ後に、ターゲット リージョン内の仮想ネットワークを最初からやり直すか破棄する場合は、ターゲット リージョンに作成したリソース グループを削除します。これにより、移動した仮想ネットワークが削除されます。 

リソース グループを削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用します。

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、仮想ネットワークの移動を完了するには、次のいずれかの操作を行います。

* [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループを削除します。

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) を使用して、ソース仮想ネットワークを削除します。  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、PowerShell を使用して仮想ネットワークをあるリージョンから別のリージョンに移動してから、不要なソース リソースをクリーンアップしました。 リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。

- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure 仮想マシンを別のリージョンに移動する](../site-recovery/azure-to-azure-tutorial-migrate.md)
