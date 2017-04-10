---
title: "ネットワーク セキュリティ グループの作成 - Azure Resource Manager テンプレート | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用して、ネットワーク セキュリティ グループを作成してデプロイする方法を説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: f3e7385d-717c-44ff-be20-f9aa450aa99b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9c36ed4ed16b53a7cca2d2eba93ad5063e60569b
ms.lasthandoff: 03/21/2017


---
# <a name="create-network-security-groups-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用してネットワーク セキュリティ グループを作成する

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、リソース マネージャーのデプロイ モデルについて説明します。 [クラシック デプロイ モデルで NSG を作成](virtual-networks-create-nsg-classic-ps.md)することもできます。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## <a name="nsg-resources-in-a-template-file"></a>テンプレート ファイルの NSG リソース
[サンプル テンプレート](https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/NSGs.json)を表示してダウンロードすることができます。

次のセクションは、シナリオに基づいたフロントエンド NSG の定義を示します。

```json
"apiVersion": "2015-06-15",
"type": "Microsoft.Network/networkSecurityGroups",
"name": "[parameters('frontEndNSGName')]",
"location": "[resourceGroup().location]",
"tags": {
  "displayName": "NSG - Front End"
},
"properties": {
  "securityRules": [
    {
      "name": "rdp-rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 100,
        "direction": "Inbound"
      }
    },
    {
      "name": "web-rule",
      "properties": {
        "description": "Allow WEB",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 101,
        "direction": "Inbound"
      }
    }
  ]
}
```
フロントエンドのサブネットに NSG を関連付けるには、テンプレートのサブネットの定義を変更し、NSG の参照 ID を使用する必要があります。

```json
"subnets": [
  {
    "name": "[parameters('frontEndSubnetName')]",
    "properties": {
      "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
      "networkSecurityGroup": {
      "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
      }
    }
  }, 
```

同じことがバックエンド NSG とテンプレートのバックエンドのサブネットに行われていることを確認します。

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>[デプロイ] をクリックして ARM テンプレートをデプロイする
パブリック リポジトリで使用できるサンプル テンプレートは、上記のシナリオの生成に使用した既定値を含むパラメーター ファイルを使用します。 "クリックしてデプロイ" を使用してこのテンプレートをデプロイするには、 [このリンク](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG)に従って、 **[Azure へのデプロイ]**をクリックし、必要に応じて既定のパラメーター値を置き換えて、ポータルの指示に従います。

## <a name="deploy-the-arm-template-by-using-powershell"></a>PowerShell を使用して ARM テンプレートをデプロイする
PowerShell を使用してダウンロードした ARM テンプレートをデプロイするには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してインストール、構成します。
2. テンプレートを使用してリソース グループを作成するには、 **`New-AzureRmResourceGroup`** コマンドレットを実行します。

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location uswest `
    -TemplateFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' `
    -TemplateParameterFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'
    ```

    予想される出力:

        ResourceGroupName : TestRG
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  
   
        Resources         :
                            Name                Type                                     Location
                            ==================  =======================================  ========
                            sqlAvSet            Microsoft.Compute/availabilitySets       westus  
                            webAvSet            Microsoft.Compute/availabilitySets       westus  
                            SQL1                Microsoft.Compute/virtualMachines        westus  
                            SQL2                Microsoft.Compute/virtualMachines        westus  
                            Web1                Microsoft.Compute/virtualMachines        westus  
                            Web2                Microsoft.Compute/virtualMachines        westus  
                            TestNICSQL1         Microsoft.Network/networkInterfaces      westus  
                            TestNICSQL2         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb1         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb2         Microsoft.Network/networkInterfaces      westus  
                            NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
                            NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
                            TestPIPSQL1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPSQL2         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb2         Microsoft.Network/publicIPAddresses      westus  
                            TestVNet            Microsoft.Network/virtualNetworks        westus  
                            testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
                            testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
   
        ResourceId        : /subscriptions/[Subscription Id]/resourceGroups/TestRG

## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Azure CLI を使用して ARM テンプレートをデプロイする
Azure CLI を使用して ARM テンプレートをデプロイするには、次の手順に従います。

1. Azure CLI を初めて使用する場合は、「 [Azure CLI のインストール](../cli-install-nodejs.md) 」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、 **`azure config mode`** コマンドを実行してリソース マネージャー モードに切り替えます。

    ```azurecli
    azure config mode arm
    ```

    次のコマンドで想定される出力は次のとおりです。

        info:    New mode is arm

3. 上記でダウンロードして変更したテンプレート ファイルとパラメーター ファイルを使用して、 **`azure group deployment create`** コマンドレットを実行して新しい VNet をデプロイします。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。

    ```azurecli
    azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' -e 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'
    ```

    予想される出力:
   
        info:    Executing command group create
        info:    Getting resource group TestRG
        info:    Creating resource group TestRG
        info:    Created resource group TestRG
        info:    Initializing template configurations and parameters
        info:    Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    
        info:    group create command OK
   
   * **-n (または --name)**。 作成されるリソース グループの名前です。
   * **-l (または --location)**。 リソース グループが作成される Azure リージョンです。
   * **-f (または --template-file)**。 ARM テンプレート ファイルへのパスです。
   * **-e (または--parameters-file)**。 ARM パラメーター ファイルへのパスです。


