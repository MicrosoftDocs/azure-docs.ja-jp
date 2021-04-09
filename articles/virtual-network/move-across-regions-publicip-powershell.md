---
title: Azure PowerShell を使用して Azure パブリック IP 構成を別の Azure リージョンに移動する
description: Azure PowerShell を使用して Azure パブリック IP 構成を Azure リージョン間で移動するには、Azure Resource Manager テンプレートを使用します。
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: a21d088680855b74e7259028ed7ef55165707c56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938689"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>Azure PowerShell を使用して Azure パブリック IP 構成を別のリージョンに移動する

既存の Azure パブリック IP 構成を別のリージョンに移動することが必要になるさまざまなシナリオがあります。 たとえば、テスト用に同じ構成と SKU を使用してパブリック IP を作成することが必要な場合があります。 ディザスター リカバリー計画の一部として、パブリック IP 構成を別のリージョンに移動することが必要な場合もあります。

**Azure パブリック IP はリージョン固有であり、あるリージョンから別のリージョンに移動することはできません。** ただし、Azure Resource Manager テンプレートを使用して、パブリック IP の既存の構成をエクスポートすることはできます。  その後、パブリック IP をテンプレートにエクスポートすることで別のリージョンにリソースをステージし、移動先リージョンに合わせてパラメーターを変更してから、新しいリージョンにテンプレートをデプロイできます。  Resource Manager とテンプレートの詳細については、「[リソース グループをテンプレートにエクスポートする](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)」を参照してください


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに Azure パブリック IP が存在することを確認します。

- Azure パブリック IP をリージョン間で移動することはできません。  新しいパブリック IP を移動先リージョンのリソースに関連付ける必要があります。

- パブリック IP の構成をエクスポートし、テンプレートをデプロイして別のリージョンにパブリック IP を作成するには、ネットワーク共同作成者ロール以上が必要です。
   
- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトには、ロード バランサー、ネットワーク セキュリティ グループ (NSG)、仮想ネットワークが含まれますが、この限りではありません。

- 自分の Azure サブスクリプションで、使用される移動先リージョンにパブリック IP を作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

- 使用するサブスクリプションに、このプロセスでのパブリック IP の追加をサポートするのに十分なリソースがあることを確認します。  「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)」をご覧ください。


## <a name="prepare-and-move"></a>準備と移動
次の手順では、Resource Manager テンプレートを使用して構成の移動用にパブリック IP を準備し、Azure PowerShell を使用してパブリック IP の構成を移動先リージョンに移動する方法を示します。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>テンプレートをエクスポートし、スクリプトからデプロイする

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) を使用して、ターゲット リージョンに移動するパブリック IP のリソース ID を取得し、変数に配置します。

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) コマンドを実行するディレクトリの .json ファイルに、ソース仮想ネットワークをエクスポートします。
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. ダウンロードされるファイルの名前は、リソースのエクスポート元のリソース グループの名前に基づいて付けられます。  コマンドからエクスポートされた **\<resource-group-name>.json** という名前のファイルを探し、任意のエディターで開きます。
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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

         Basic と Standard SKU のパブリック IP の違いについて詳しくは、「[パブリック IP アドレスの作成、変更、削除](./virtual-network-public-ip-address.md)」を参照してください。

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

        割り当て方法とアイドル タイムアウト値の詳細については、「[パブリック IP アドレスの作成、変更、削除](./virtual-network-public-ip-address.md)」を参照してください。


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
## <a name="discard"></a>破棄 

デプロイ後に、ターゲット内のパブリック IP を最初からやり直すか破棄する場合は、ターゲットで作成されたリソース グループを削除します。これにより、移動したパブリック IP が削除されます。  リソース グループを削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用します。

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、仮想ネットワークの移動を完了するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) または [Remove-AzPublicIPAddress](/powershell/module/az.network/remove-azpublicipaddress) を使用して、ソース仮想ネットワークまたはリソース グループを削除します

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure パブリック IP をあるリージョンから別のリージョンに移動し、移動元リソースをクリーンアップしました。  リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM を別のリージョンに移動する](../site-recovery/azure-to-azure-tutorial-migrate.md)
