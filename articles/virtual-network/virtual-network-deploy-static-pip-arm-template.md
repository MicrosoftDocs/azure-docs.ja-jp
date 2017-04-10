---
title: "静的パブリック IP アドレスを持つ VM の作成 - Azure Resource Manager テンプレート | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用して、静的パブリック IP アドレスを持つ VM を作成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d551085a-c7ed-4ec6-b4c3-e9e1cebb774c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 1bb49c0c03a3103194d068b511fa92ecb996caf3
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-vm-with-a-static-public-ip-address-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して静的パブリック IP アドレスを持つ VM を作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [テンプレート](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (クラシック)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、クラシック デプロイメント モデルではなくこのモデルをお勧めします。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="public-ip-address-resources-in-a-template-file"></a>テンプレート ファイル内のパブリック IP アドレス リソース
[サンプル テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json)を表示してダウンロードすることができます。

次のセクションは、上記のシナリオに基づいたパブリック IP リソースの定義を示しています。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('webVMSetting').pipName]",
  "location": "[variables('location')]",
  "properties": {
    "publicIPAllocationMethod": "Static"
  },
  "tags": {
    "displayName": "PublicIPAddress - Web"
  }
},
```

**publicIPAllocationMethod** プロパティが *Static* に設定されていることに注意してください。 このプロパティには、*Dynamic* (既定値) または *Static* のいずれかを設定できます。 Static に設定すると、割り当てられたパブリック IP アドレスが変更されないことが保証されます。

次のセクションは、パブリック IP アドレスとネットワーク インターフェイスの関連付けを示しています。

```json
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[variables('webVMSetting').nicName]",
    "location": "[variables('location')]",
    "tags": {
    "displayName": "NetworkInterface - Web"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
      "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
    ],
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
          "privateIPAllocationMethod": "Static",
          "privateIPAddress": "[variables('webVMSetting').ipAddress]",
          "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
          },
          "subnet": {
            "id": "[variables('frontEndSubnetRef')]"
          }
        }
      }
    ]
  }
},
```

**publicIPAddress** プロパティが、**variables('webVMSetting').pipName** という名前のリソースの **ID** を指していることに注意してください。 これは上記のパブリック IP リソースの名前です。

最後に、作成される VM の **networkProfile** プロパティに、上記のネットワーク インターフェイスが示されます。

```json
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }
```

## <a name="deploy-the-template-by-using-click-to-deploy"></a>[クリックしてデプロイ] を使用してテンプレートをデプロイする

パブリック リポジトリで使用できるサンプル テンプレートは、上記のシナリオの生成に使用した既定値を含むパラメーター ファイルを使用します。 [クリックしてデプロイ] を使用してこのテンプレートをデプロイするには、 **VM with static PIP** テンプレートの Readme.md ファイルで [[Azure へのデプロイ]](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP) をクリックします。 必要に応じて既定のパラメーター値を置き換え、空白のパラメーターの値を入力します。  ポータルの指示に従って、静的パブリック IP アドレスを持つ仮想マシンを作成します。

## <a name="deploy-the-template-by-using-powershell"></a>PowerShell を使用してテンプレートをデプロイする

PowerShell を使用してダウンロードしたテンプレートをデプロイするには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」の手順を実行します。
2. 必要に応じて、PowerShell コンソールで `New-AzureRmResourceGroup` コマンドレットを実行して、新しいリソース グループを作成します。 既に作成したリソース グループがある場合は、手順 3. に進みます。

    ```powershell
    New-AzureRmResourceGroup -Name PIPTEST -Location westus
    ```

    予想される出力:
   
        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. PowerShell コンソールで、`New-AzureRmResourceGroupDeployment` コマンドレットを実行してテンプレートをデプロイします。

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
        -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
        -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json
    ```

    予想される出力:
   
        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0
   
        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         
   
        Outputs           :

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Azure CLI を使用してテンプレートをデプロイする
Azure CLI を使用してテンプレートをデプロイするには、次の手順を実行します。

1. Azure CLI を初めて使用する場合は、[Azure CLI のインストールと構成](../cli-install-nodejs.md)に関する記事の手順に従って、Azure CLI をインストールして構成します。
2. 次に示すように、 `azure config mode` コマンドを実行して、リソース マネージャー モードに切り替えます。

    ```azurecli
    azure config mode arm
    ```

    上記のコマンドの予想される出力を次に示します。

        info:    New mode is arm

3. [パラメーター ファイル](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json)を開き、内容を選択してコンピューター内のファイルに保存します。 この例では、パラメーターは *parameters.json* という名前のファイルに保存されます。 必要に応じて、このファイル内のパラメーター値を変更します。ただし、少なくとも、adminPassword パラメーターの値を一意の複雑なパスワードに変更することをお勧めします。
4. 上記でダウンロードして変更したテンプレート ファイルとパラメーター ファイルを使用して、`azure group deployment create` コマンドレットを実行して新しい VNet をデプロイします。 次のコマンドで、<path> をファイルの保存先のパスに置き換えます。 

    ```azurecli
    azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json
    ```

    予想される出力 (使用されているパラメーター値の表示):

        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/[Subscription ID]/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK


