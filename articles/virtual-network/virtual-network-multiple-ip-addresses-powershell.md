---
title: Azure 仮想マシンの複数の IP アドレス - PowerShell | Microsoft Docs
description: PowerShell を使用して仮想マシンに複数の IP アドレスを割り当てる方法を説明します。 | Resource Manager
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: kumud
ms.reviewer: annahar
ms.openlocfilehash: e9bad6ad614855c543ee6d75d4e6f4dc8e2255aa
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876220"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>PowerShell を使用して仮想マシンに複数の IP アドレスを割り当てる

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

この記事では、PowerShell を使用して Azure Resource Manager デプロイ モデルで仮想マシン (VM) を作成する方法を説明します。 クラシック デプロイ モデルで作成されたリソースには、複数の IP アドレスを割り当てることはできません。 Azure のデプロイ モデルの詳細については、[デプロイ モデルの概要](../resource-manager-deployment-model.md)に関する記事をご覧ください。

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>複数の IP アドレスを持つ VM を作成する

以下の手順は、シナリオの説明に従って複数の IP アドレスを持つ VM を作成する方法を示しています。 変数値は実際の実装に合わせて変更してください。

1. PowerShell コマンド プロンプトを開き、1 つの PowerShell セッション内で、このセクションの残りの手順を完了します。 まだ PowerShell をインストール、構成していない場合は、「 [Azure PowerShell のインストールと構成](/powershell/azure/overview) 」の手順を実行してください。
2. `Connect-AzAccount` コマンドを使用してアカウントにログインします。
3. *myResourceGroup* と *westus* を任意の名前と場所に置き換えます。 リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. リソース グループと同じ場所に、仮想ネットワーク (VNet) とサブネットを作成します。

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. ネットワーク セキュリティ グループ (NSG) と規則を作成します。 NSG は、受信と送信の規則を使用して VM をセキュリティで保護します。 この場合、受信規則はポート 3389 に作成され、受信リモート デスクトップ接続を許可します。

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. NIC のプライマリ IP 構成を定義します。 以前に定義した値を使用しなかった場合は、10.0.0.4 を作成済みのサブネットの有効なアドレスに変更します。 静的 IP アドレスを割り当てる前に、そのアドレスがまだ使用されていないことを確認しておくことをお勧めします。 `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet` コマンドを入力します。 このアドレスを使用できる場合、出力で *True* が返されます。 使用できない場合は、出力で *False* が返され、使用可能なアドレスの一覧が返されます。 

    次のコマンドで、 **\<replace-with-your-unique-name> を使用する一意の DNS 名に置き換えます。** この名前は、Azure リージョン内のすべてのパブリック IP アドレス間で一意である必要があります。 これは省略可能なパラメーターです。 パブリック IP アドレスを使用して VM に接続するだけである場合は削除してかまいません。

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    NIC に複数の IP 構成を割り当てるときは、1 つの構成を *-Primary* として割り当てる必要があります。

    > [!NOTE]
    > パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。

7. NIC のセカンダリ IP 構成を定義します。 構成は、必要に応じて追加または削除できます。 各 IP 構成には、プライベート IP アドレスが割り当てられている必要があります。 必要に応じて、各構成にパブリック IP アドレスを 1 つ割り当てることもできます。

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. NIC を作成し、3 つの IP 構成を関連付けます。

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >この記事では、すべての構成を 1 つの NIC に割り当てていますが、複数の IP 構成を VM に接続された各 NIC に割り当てることもできます。 複数の NIC が接続された VM の作成方法については、[複数の NIC が接続された VM の作成](../virtual-machines/windows/multiple-nics.md)に関する記事をご覧ください。

9. 次のコマンドを入力して VM を作成します。

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載されたご使用のオペレーティング システム用の手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。 オペレーティング システムにパブリック IP アドレスは追加しないでください。

## <a name="add"></a>VM に IP アドレスを追加する

プライベート IP アドレスとパブリック IP アドレスを Azure ネットワーク インターフェイスに追加するには、次の手順を実行します。 次のセクションの例では、この記事の[シナリオ](#scenario)で説明している 3 つの IP 構成を使用した VM を既に所有していることを前提としていますが、必須ではありません。

1. PowerShell コマンド プロンプトを開き、1 つの PowerShell セッション内で、このセクションの残りの手順を完了します。 まだ PowerShell をインストール、構成していない場合は、「 [Azure PowerShell のインストールと構成](/powershell/azure/overview) 」の手順を実行してください。
2. 次の $ 変数の "値" を、IP アドレスを追加する NIC の名前、NIC が属するリソース グループ、NIC の場所に変更します。

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   変更する NIC の名前がわからない場合は、次のコマンドを入力してから、前述の変数の値を変更します。

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. 変数を作成し、次のコマンドを入力して、既存の NIC に設定します。

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. 次のコマンドで、*MyVNet* と *MySubnet* を VNet の名前と NIC の接続先のサブネットの名前にそれぞれ変更します。 VNet オブジェクトと NIC の接続先のサブネット オブジェクトを取得するコマンドを入力します。

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   VNet 名または NIC の接続先のサブネット名がわからない場合は、次のコマンドを入力します。

   ```powershell
   $MyNIC.IpConfigurations
   ```

   出力で、次の出力例のようなテキストを探します。

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    この出力では、*MyVnet* が VNet、*MySubnet* が NIC の接続先のサブネットです。

5. 要件に基づいて、以下のいずれかのセクションの手順を実行します。

   **プライベート IP アドレスを追加する**

   NIC にプライベート IP アドレスを追加するには、IP 構成を作成する必要があります。 次のコマンドでは、静的 IP アドレス 10.0.0.7 が割り当てられた構成を作成します。 静的 IP アドレスを指定するときは、サブネットの未使用のアドレスを指定する必要があります。 まず `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` コマンドを入力してアドレスをテストし、このアドレスが使用可能かどうかを確認することをお勧めします。 この IP アドレスを使用できる場合、出力で *True* が返されます。 使用できない場合は、出力で *False* が返され、使用可能なアドレスの一覧が返されます。

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   一意の構成名とプライベート IP アドレス (静的 IP アドレスを使用する構成の場合) を使用して、必要な数だけ構成を作成します。

   この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載された、お使いのオペレーティング システム用の手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。

   **パブリック IP アドレスを追加する**

   パブリック IP アドレスを追加するには、新しい IP 構成または既存の IP 構成にパブリック IP アドレス リソースを関連付けます。 必要に応じて、以下のいずれかのセクションの手順を実行します。

   > [!NOTE]
   > パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。
   >

   **パブリック IP アドレス リソースを新しい IP 構成に関連付ける**

   すべての IP 構成にプライベート IP アドレスが必要であるため、パブリック IP アドレスを新しい IP 構成に追加するときは、必ずプライベート IP アドレスも追加する必要があります。 既存のパブリック IP アドレス リソースを追加することも、新しいリソースを作成することもできます。 新しいパブリック IP アドレス リソースを作成するには、次のコマンドを入力します。

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   静的プライベート IP アドレスと、関連付けられた *myPublicIp3* パブリック IP アドレス リソースで新しい IP 構成を作成するには、次のコマンドを入力します。

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **パブリック IP アドレス リソースを既存の IP 構成に関連付ける**

   パブリック IP アドレス リソースは、このリソースがまだ関連付けられていない IP 構成にのみ関連付けることができます。 IP 構成にパブリック IP アドレスが関連付けられているかどうかを確認するには、次のコマンドを入力します。

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   次のような出力が表示されます。

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   *IpConfig-3* の **PublicIpAddress** 列が空白であるため、現在、この構成にはパブリック IP アドレス リソースは関連付けられていません。 IpConfig-3 に既存のパブリック IP アドレス リソースを追加することも、次のコマンドを入力して新しいリソースを作成することもできます。

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   パブリック IP アドレス リソースを、*IPConfig-3* という名前の既存の IP 構成に関連付けるには、次のコマンドを入力します。

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. 次のコマンドを入力して、新しい IP 構成で NIC を設定します。

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. 次のコマンドを入力して、NIC に割り当てられたプライベート IP アドレスとパブリック IP アドレス リソースを表示します。

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載された、お使いのオペレーティング システム用の手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。 オペレーティング システムにパブリック IP アドレスは追加しないでください。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]