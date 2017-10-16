---
title: "Azure ネットワークおよびアプリケーション セキュリティ グループ (プレビュー) によるネットワーク トラフィックのフィルター処理 | Microsoft Docs"
description: "仮想マシンとの間で送受信されるネットワーク トラフィックの種類を制限するためにネットワークおよびアプリケーション セキュリティ グループ (プレビュー) を作成する方法について説明します。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 035eb44432081ef52c758a5d311b4d2ba2c6108d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>ネットワークおよびアプリケーション セキュリティ グループ (プレビュー) によるネットワーク トラフィックのフィルター処理

このチュートリアルでは、アプリケーション セキュリティ グループを含む仮想マシンのグループとの間で送受信されるネットワーク トラフィックをフィルター処理するためにネットワーク セキュリティ グループを作成する方法について説明します。 ネットワーク セキュリティ グループおよびアプリケーション セキュリティ グループの詳細については、「[セキュリティの概要](security-overview.md)」を参照してください。 

以降のセクションには、Azure コマンド ライン インターフェイス ([Azure CLI](#azure-cli)) および [Azure PowerShell](#powershell) を使用してネットワーク セキュリティ グループを作成するために実行できる手順が含まれています。 ネットワーク セキュリティ グループを作成するために使用したツールに関係なく、結果は同じです。 このチュートリアルの該当セクションに移動するには、ツール リンクをクリックしてください。 

この記事では、Resource Manager デプロイメント モデルを使用してネットワーク セキュリティ グループを作成する手順を示します。ネットワーク セキュリティ グループを作成するときは、このデプロイメント モデルを使用することをお勧めします。 ネットワーク セキュリティ グループ (クラシック) を作成する必要がある場合は、「[Create a network security group (classic) (ネットワーク セキュリティ グループ (クラシック) の作成)](virtual-networks-create-nsg-classic-ps.md)」を参照してください。 Azure 展開モデルの知識がない場合は、[Azure 展開モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

> [!NOTE]
> このチュートリアルでは、現在プレビュー リリース段階にあるネットワーク セキュリティ グループ機能を利用します。 プレビュー リリース段階にある機能には、一般リリースにある機能と同じ可用性と信頼性はありません。 プレビュー段階にある間、これらの機能は米国中西部の地域でのみ使用できます。 一般リリースにある機能のみを使用してネットワーク セキュリティ グループを実装する場合は、「[Create a network security group (ネットワーク セキュリティ グループの作成)](virtual-networks-create-nsg-arm-pportal.md)」を参照してください。 

## <a name="azure-cli"></a>Azure CLI

Windows、Linux、または macOS のどこからコマンドを実行しても、Azure CLI コマンドは同じです。 ただし、オペレーティング システム シェル間ではスクリプトに違いがあります。 次の手順のスクリプトは、Bash シェルで実行します。 

1. [Azure CLI のインストールと構成](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)
2. `az --version` コマンドを入力して、使用している Azure CLI のバージョンが 2.0.18 以降であることを確認してください。 そうでない場合は、最新バージョンをインストールします。
3. `az login` コマンドを使用して Azure にログインします。
4. 次のコマンドを入力して、プレビューに登録します。
    
    ```azurecli-interactive
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ``` 

5. 次のコマンドを入力して、プレビューに登録されていることを確認します。

    ```azurecli-interactive
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    前のコマンドから返された出力に**状態**を表す "*登録済み*" が表示されるまでは、残りの手順に進まないでください。 登録される前に続行すると、残りの手順が失敗します。
6. 次の bash スクリプトを実行して、リソース グループを作成します。

    ```azurecli-interactive
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. 3 つのアプリケーション セキュリティ グループ (サーバーの種類ごとに 1 つ) を作成します。

    ```azurecli-interactive
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. ネットワーク セキュリティ グループを作成します。

    ```azurecli-interactive
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. NSG 内にセキュリティ規則を作成し、アプリケーション セキュリティ グループを宛先として設定します。
    
    ```azurecli-interactive    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. 仮想ネットワークを作成します。 
    
    ```azurecli-interactive
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. 仮想ネットワークのサブネットにネットワーク セキュリティ グループを関連付けます。

    ```azurecli-interactive
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. 3 つのネットワーク インターフェイス (サーバーの種類ごとに 1 つ) を作成します。 

    ```azurecli-interactive
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --network-security-group myNsg \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --network-security-group myNsg \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --network-security-group myNsg \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    ネットワーク インターフェイスがメンバーになっているアプリケーション セキュリティ グループに基づいて、手順 9. で作成した対応するセキュリティ規則だけがそのネットワーク インターフェイスに適用されます。 たとえば、ネットワーク インターフェイスが *WebServers* アプリケーション セキュリティ グループのメンバーであり、その規則は宛先として *WebServers* アプリケーション セキュリティ グループを指定しているため、*myWebNic* には *WebRule* だけが有効です。 ネットワーク インターフェイスが *AppServers* および *DatabaseServers* アプリケーション セキュリティ グループのメンバーでないため、*AppRule* および *DatabaseRule* 規則は *myWebNic* に適用されません。

13. サーバーの種類ごとに 1 つの仮想マシンを作成し、対応するネットワーク インターフェイスを各仮想マシンに接続します。 この例では Windows 仮想マシンを作成しますが、代わりに Linux 仮想マシンを作成するには、*win2016datacenter* を *UbuntuLTS* に変更できます。

    ```azurecli-interactive
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. **省略可:** 「[リソースの削除](#delete-cli)」の手順を実行して、このチュートリアルで作成したリソースを削除します。

## <a name="powershell"></a>PowerShell

1. [PowerShell](/powershell/azure/install-azurerm-ps) をインストールして構成します。
2. AzureRm モジュールのバージョン 4.4.0 以降がインストールされていることを確認してください。 `Get-Module -ListAvailable AzureRM` コマンドを入力することによって、現在インストールされているバージョンを確認できます。 インストールまたはアップグレードする必要がある場合は、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureRM)から最新バージョンの AzureRM モジュールをインストールします。
3. PowerShell セッションで、[Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)と `login-azurermaccount` コマンドを使用して Azure にログインします。
4. 次のコマンドを入力して、プレビューに登録します。
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. 次のコマンドを入力して、プレビューに登録されていることを確認します。

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    前のコマンドから返された出力の **[RegistrationState]** 列に *[Registered] (登録済み)* が表示されるまでは、残りの手順に進まないでください。 登録される前に続行すると、残りの手順が失敗します。
        
6. リソース グループを作成します。

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. 3 つのアプリケーション セキュリティ グループ (サーバーの種類ごとに 1 つ) を作成します。

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. サーバーの種類ごとにセキュリティ規則を作成します。
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80  

    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443 

    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336    
    ``` 

9. ネットワーク セキュリティ グループを作成します。

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. サブネット構成を作成し、それにネットワーク セキュリティ グループを関連付けます。
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. 仮想ネットワークを作成します。 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. 3 つのネットワーク インターフェイス (サーバーの種類ごとに 1 つ) を作成します。 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -NetworkSecurityGroup $nsg `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -NetworkSecurityGroup $nsg `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -NetworkSecurityGroup $nsg `
      -ApplicationSecurityGroup $databaseAsg
    ```

    ネットワーク インターフェイスがメンバーになっているアプリケーション セキュリティ グループに基づいて、手順 8. で作成した対応するセキュリティ規則だけがそのネットワーク インターフェイスに適用されます。 たとえば、ネットワーク インターフェイスが *WebServers* アプリケーション セキュリティ グループのメンバーであり、その規則は宛先として *WebServers* アプリケーション セキュリティ グループを指定しているため、*myWebNic* には *WebRule* だけが有効です。 ネットワーク インターフェイスが *AppServers* および *DatabaseServers* アプリケーション セキュリティ グループのメンバーでないため、*AppRule* および *DatabaseRule* 規則は *myWebNic* に適用されません。

13. サーバーの種類ごとに 1 つの仮想マシンを作成し、対応するネットワーク インターフェイスを各仮想マシンに接続します。 この例では Windows 仮想マシンを作成しますが、代わりに Linux 仮想マシンを作成するには、スクリプトを実行する前に *-Windows* を *-Linux* に、*MicrosoftWindowsServer* を *Canonical* に、*WindowsServer* を *UbuntuServer* に、さらに *2016-Datacenter* を *14.04.2-LTS* に変更できます。

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. **省略可:** 「[リソースの削除](#delete-cli)」の手順を実行して、このチュートリアルで作成したリソースを削除します。

## <a name="delete"></a>リソースの削除

このチュートリアルが完了したら、使用料がかからないように、作成したリソースを削除することをお勧めします。 リソース グループを削除すると、リソース グループ内にあるリソースもすべて削除されます。

### <a name="delete-portal"></a>Azure Portal

1. ポータルの検索ボックスに、「**myResourceGroup**」と入力します。 検索結果で、**[myResourceGroup]** をクリックします。
2. **myResourceGroup** ブレードで **[削除]** アイコンをクリックします。
3. 削除を確定するには、**[リソース グループ名を入力してください]** ボックスに「**myResourceGroup**」と入力し、**[削除]** をクリックします。

### <a name="delete-cli"></a>Azure CLI

CLI セッションで次のコマンドを入力します。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

PowerShell セッションで次のコマンドを入力します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

