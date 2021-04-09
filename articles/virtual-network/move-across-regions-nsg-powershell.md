---
title: Azure PowerShell を使用して Azure ネットワーク セキュリティ グループ (NSG) を別の Azure リージョンに移動する
description: Azure PowerShell を使用して Azure ネットワーク セキュリティ グループを Azure リージョン間で移動するには、Azure Resource Manager テンプレートを使用します。
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: ad73ef03aa9623fb724f1397697fac18f659a90c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935000"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Azure PowerShell を使用して Azure ネットワーク セキュリティ グループ (NSG) を別のリージョンに移動する

既存の NSG をリージョン間で移動することが必要になるさまざまなシナリオがあります。 たとえば、テスト用に同じ構成とセキュリティ規則で NSG を作成したい場合があります。 ディザスター リカバリー計画の一部として、NSG を別のリージョンに移動したい場合もあります。

Azure セキュリティ グループは、あるリージョンから別のリージョンに移動することができません。 ただし、Azure Resource Manager テンプレートを使用して、NSG の既存の構成とセキュリティ規則をエクスポートすることはできます。  その後、NSG をテンプレートにエクスポートすることで別のリージョンにリソースを公開し、宛先リージョンに合わせてパラメーターを変更してから、新しいリージョンにテンプレートをデプロイできます。  Resource Manager とテンプレートの詳細については、「[リソース グループをテンプレートにエクスポートする](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)」を参照してください。


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに Azure ネットワーク セキュリティ グループが存在することを確認します。

- Azure ネットワーク セキュリティ グループはリージョン間で移動できません。  新しい NSG を移動先リージョンのリソースに関連付ける必要があります。

- NSG 構成をエクスポートし、テンプレートをデプロイして別のリージョンで NSG を作成するには、ネットワーク共同作成者ロール以上が必要です。
   
- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトには、ロード バランサー、パブリック IP、仮想ネットワークが含まれますが、この限りではありません。

- 自分の Azure サブスクリプションで、使用される移動先リージョンに NSG を作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

- 使用するサブスクリプションに、このプロセスでの NSG の追加をサポートするのに十分なリソースがあることを確認します。  「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)」をご覧ください。


## <a name="prepare-and-move"></a>準備と移動
次の手順では、構成とセキュリティ規則を移動する目的で Resource Manager テンプレートを使用してネットワーク セキュリティ グループの準備を行い、Azure PowerShell を使用して NSG の構成とセキュリティ規則を移動先リージョンに移動する方法を示します。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>テンプレートをエクスポートし、スクリプトからデプロイする

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) を使用し、移動先リージョンに移動する NSG のリソース ID を取得し、変数に配置します。

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) コマンドを実行するディレクトリの .json ファイルに NSG をエクスポートします。
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. ダウンロードされるファイルの名前は、リソースのエクスポート元のリソース グループの名前に基づいて付けられます。  コマンドからエクスポートされた **\<resource-group-name>.json** という名前のファイルを探し、任意のエディターで開きます。
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. NSG 名のパラメーターを編集するには、移動元 NSG の名前の **defaultValue** プロパティを移動先 NSG の名前に変更し、名前を確実に引用符で囲みます。
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. NSG の構成とセキュリティ規則を移動する移動先リージョンを編集するには、**resources** の下の **location** プロパティを変更します。

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. リージョンの場所コードを取得するには、次のコマンドを実行して、Azure PowerShell コマンドレットの [Get-AzLocation](/powershell/module/az.resources/get-azlocation) を使用できます。

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. また、必要に応じて、 **\<resource-group-name>.json** 内の他のパラメーターも変更できます。これらは、実際の要件に応じて省略可能です。

    * **セキュリティ規則** - **\<resource-group-name>.json** ファイルの **securityRules** セクションで規則を追加または削除することで、移動先 NSG にデプロイされた規則を編集できます。

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        移動先 NSG で規則を追加したり、削除したりするには、下の例の形式で、 **\<resource-group-name>.json** ファイルの末尾でカスタム規則の種類を変更する必要もあります。

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. **\<resource-group-name>.json** ファイルを保存します。

10. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、デプロイする移動先 NSG の移動先リージョンにリソース グループを作成します。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. 編集した **\<resource-group-name>.json** ファイルを、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用して、前の手順で作成したリソース グループにデプロイします。

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 移動先リージョンでリソースが作成されたことを確認するには、[Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) と [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) を使用します。
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>破棄 

デプロイ後に、移動先で NSG を最初からやり直すか破棄する場合、移動先で作成されたリソース グループを削除します。それにより、移動した NSG が削除されます。  リソース グループを削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用します。

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、NSG の移動を完了するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) または [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) を使用し、移動元 NSG またはリソース グループを削除します。

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure ネットワーク セキュリティ グループをあるリージョンから別のリージョンに移動し、移動元リソースをクリーンアップしました。  リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM を別のリージョンに移動する](../site-recovery/azure-to-azure-tutorial-migrate.md)
