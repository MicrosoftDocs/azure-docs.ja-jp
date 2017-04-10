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
ms.date: 03/07/2017
ms.author: davidmu
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 8bff8d1d3f2a1211fe301e4079bf732544943e92
ms.lasthandoff: 03/08/2017


---

# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Resource Manager テンプレートから Windows 仮想マシンを作成する

この記事では、PowerShell を使用して Azure Resource Manager テンプレートをデプロイする方法を示します。 この[テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)は、1 つのサブネットを持つ新しい仮想ネットワークに、Windows Server を実行する&1; つの仮想マシンをデプロイします。

仮想マシン リソースの詳細については、「[Virtual machines in an Azure Resource Manager template](virtual-machines-windows-template-description.md)」(Azure Resource Manager テンプレートの仮想マシン) をご覧ください。 テンプレート内のすべてのリソースの詳細については、「[Resource Manager テンプレートのチュートリアル](../resource-manager-template-walkthrough.md)」をご覧ください。

この記事の手順を実行するには、約&5; 分かかります。

## <a name="step-1-install-azure-powershell"></a>手順 1: Azure PowerShell をインストールする

最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。

## <a name="step-2-create-a-resource-group"></a>手順 2: リソース グループを作成する

すべてのリソースを[リソース グループ](../azure-resource-manager/resource-group-overview.md)にデプロイする必要があります。

1. リソースを作成できる場所の一覧を取得します。
   
    ```powershell   
    Get-AzureRmLocation | sort DisplayName | Select DisplayName
    ```

2. 選択した場所にリソース グループを作成します。 この例では、**Central US** という場所に **myResourceGroup** という名前のリソース グループを作成します。

    ```powershell   
    New-AzureRmResourceGroup -Name "myResourceGroup" -Location "Central US"
    ```
   
  次のような結果が表示されます。

    ```powershell 
    ResourceGroupName : myResourceGroup
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup
    ```

## <a name="step-3-create-the-resources"></a>手順 3: リソースを作成する
テンプレートをデプロイし、入力を求められたら、パラメーター値を指定します。 この例では、作成したリソース グループに 101-vm-simple-windows テンプレートをデプロイします。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" 
```
VM の管理者アカウントの名前、アカウントのパスワード、および DNS プレフィックスの入力を求められます。

次のような結果が表示されます。

    DeploymentName    : azuredeploy
    ResourceGroupName : myResourceGroup
    ProvisioningState : Succeeded
    Timestamp         : 12/29/2016 8:11:37 PM
    Mode              : Incremental
    TemplateLink      :
       Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/
                        101-vm-simple-windows/azuredeploy.json
       ContentVersion : 1.0.0.0
    Parameters        :
      Name             Type                       Value
      ===============  =========================  ==========
      adminUsername    String                     myAdminUser
      adminPassword    SecureString
      dnsLabelPrefix   String                     myDomain
      windowsOSVersion String                     2016-Datacenter
    Outputs           :
      Name             Type                       Value
      ===============  =========================  ===========
      hostname         String                     myDomain.centralus.cloudapp.azure.com
    DeploymentDebugLogLevel :

> [!NOTE]
> ローカル ファイルからテンプレートとパラメーターをデプロイすることもできます。 詳細については、「[Azure Storage での Azure PowerShell の使用](../storage/storage-powershell-guide-full.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- デプロイに問題がある場合は、次の手順として、「[Troubleshoot common Azure deployment errors with Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)」(Azure Resource Manager を使用した Azure のデプロイで発生する一般的なエラーのトラブルシューティング) を参照してください。
- Azure PowerShell を使用して仮想マシンを作成する方法について、「[Resource Manager と PowerShell を使用して Windows VM を作成する](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
- 「[Resource Manager と PowerShell を使用した Azure Virtual Machines の管理](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」で、作成した仮想マシンを管理する方法を確認します。


