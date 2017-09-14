---
title: "Azure でテンプレートから Windows VM を作成する | Microsoft Docs"
description: "Resource Manager テンプレートと PowerShell を使用して、新しい Windows VM を簡単に作成します。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: davidmu
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ddab80262fe27c1f5995858ec7de75d7c46df081
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Resource Manager テンプレートから Windows 仮想マシンを作成する

この記事では、PowerShell を使用して Azure Resource Manager テンプレートをデプロイする方法を示します。 作成したテンプレートは、単一のサブネットを持つ新しい仮想ネットワークに、Windows Server を実行する単一の仮想マシンをデプロイします。

仮想マシン リソースの詳細については、「[Virtual machines in an Azure Resource Manager template](template-description.md)」(Azure Resource Manager テンプレートの仮想マシン) をご覧ください。 テンプレート内のすべてのリソースの詳細については、「[Resource Manager テンプレートのチュートリアル](../../azure-resource-manager/resource-manager-template-walkthrough.md)」をご覧ください。

この記事の手順を実行するには、約 5 分かかります。

## <a name="install-azure-powershell"></a>Azure PowerShell をインストールする

最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](../../powershell-install-configure.md)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

すべてのリソースを[リソース グループ](../../azure-resource-manager/resource-group-overview.md)にデプロイする必要があります。

1. リソースを作成できる場所の一覧を取得します。
   
    ```powershell   
    Get-AzureRmLocation | sort DisplayName | Select DisplayName
    ```

2. 選択した場所にリソース グループを作成します。 この例では、**West US** という場所に **myResourceGroup** という名前のリソース グループを作成します。

    ```powershell   
    New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
    ```

## <a name="create-the-files"></a>ファイルの作成

この手順では、リソースをデプロイするテンプレート ファイルと、テンプレートにパラメーター値を指定するパラメーター ファイルを作成します。 また、Azure Resource Manager の操作を実行するために使用する認証ファイルも作成します。

1. *CreateVMTemplate.json* という名前のファイルを作成し、そのファイルに次の JSON コードを追加します。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

2. *Parameters.json* という名前のファイルを作成し、そのファイルに次の JSON コードを追加します。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
      "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

3. 次のようにして、新しいストレージ アカウントとコンテナーを作成します。

    ```powershell
    $storageName = "st" + (Get-Random)
    New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" -AccountName $storageName -Location "West US" -SkuName "Standard_LRS" -Kind Storage
    $accountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name $storageName).Value[0]
    $context = New-AzureStorageContext -StorageAccountName $storageName -StorageAccountKey $accountKey 
    New-AzureStorageContainer -Name "templates" -Context $context -Permission Container
    ```

4. 次のようにして、ストレージ アカウントにファイルをアップロードします。

    ```powershell
    Set-AzureStorageBlobContent -File "C:\templates\CreateVMTemplate.json" -Context $context -Container "templates"
    Set-AzureStorageBlobContent -File "C:\templates\Parameters.json" -Context $context -Container templates
    ```

    "-File" パスは、ファイルを実際に保存した場所に変更してください。

## <a name="create-the-resources"></a>リソースの作成

次のようにして、パラメーターを使用してテンプレートをデプロイします。

```powershell
$templatePath = "https://" + $storageName + ".blob.core.windows.net/templates/CreateVMTemplate.json"
$parametersPath = "https://" + $storageName + ".blob.core.windows.net/templates/Parameters.json"
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -Name "myDeployment" -TemplateUri $templatePath -TemplateParameterUri $parametersPath 
```

> [!NOTE]
> ローカル ファイルからテンプレートとパラメーターをデプロイすることもできます。 詳細については、「[Azure Storage での Azure PowerShell の使用](../../storage/common/storage-powershell-guide-full.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- デプロイに問題がある場合は、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](../../resource-manager-common-deployment-errors.md)」を参照してください。
- 仮想マシンを作成して管理する方法については、「[Azure PowerShell モジュールを使用して Windows VM を作成および管理する](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。


