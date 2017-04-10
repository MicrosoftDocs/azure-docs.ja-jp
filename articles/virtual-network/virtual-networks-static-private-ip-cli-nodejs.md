---
title: "VM のプライベート IP アドレスの構成 - Azure CLI 1.0 | Microsoft Docs"
description: "Azure コマンド ライン インターフェイス (CLI) 1.0 を使用して仮想マシンのプライベート IP アドレスを構成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9179319095c31d5eb454860e173ffa7c65fc9f73
ms.lasthandoff: 03/21/2017


---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli-10"></a>Azure CLI 1.0 を使用して仮想マシンのプライベート IP アドレスを構成する


## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン 

次のいずれかの CLI バージョンを使用してタスクを完了できます。 

- [Azure CLI 1.0](#how-to-specify-a-static-private-ip-address-when-creating-a-vm) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-networks-static-private-ip-arm-cli.md) - Resource Manager デプロイメント モデル用の次世代 CLI 

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、リソース マネージャーのデプロイ モデルについて説明します。 [クラシック デプロイ モデルで静的プライベート IP アドレスを管理する](virtual-networks-static-private-ip-classic-cli.md)こともできます。

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

次のサンプル Azure CLI コマンドでは、単純な環境が既に作成されていると想定します。 このドキュメントに表示されているコマンドを実行する場合は、まず、 [vnet の作成](virtual-networks-create-vnet-arm-cli.md)に関する記事に示されているテスト環境を構築します。

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>VM 作成時に静的プライベート IP アドレスを指定する方法
静的プライベート IP *192.168.1.101* を使用して、*TestVNet* という名前の VNet の *FrontEnd* サブネットで *DNS01* という名前の VM を作成するには、以下の手順に従います。

1. Azure CLI を初めて使用する場合は、「 [Azure CLI のインストール](../cli-install-nodejs.md) 」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、 **azure config mode** コマンドを実行してリソース マネージャー モードに切り替えます。
   
        azure config mode arm
   
    予想される出力:
   
        info:    New mode is arm
3. **azure network public-ip create** を実行して、VM のパブリック IP を作成します。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。
   
        azure network public-ip create -g TestRG -n TestPIP -l centralus
   
    予想される出力:
   
        info:    Executing command network public-ip create
        + Looking up the public ip "TestPIP"
        + Creating public ip address "TestPIP"
        + Looking up the public ip "TestPIP"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
        data:    Name                            : TestPIP
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        info:    network public-ip create command OK
   
   * **-g (または --resource-group)**。 パブリック IP の作成場所となるリソース グループの名前です。
   * **-n (または --name)**。 パブリック IP の名前です。
   * **-l (または --location)**。 パブリック IP が作成される Azure リージョンです。 ここでは、 *centralus*です。
4. **azure network nic create** コマンドを実行し、静的プライベート IP を使用して NIC を作成します。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。
   
        azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd
   
    予想される出力:
   
        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK
   
   * **-a (または --private-ip-address)**。 NIC の静的プライベート IP アドレスです。
   * **-m (または --subnet-vnet-name)**。 NIC の作成先となる VNet の名前です。
   * **-k (または --subnet-name)**。 NIC の作成先となるサブネットの名前です。
5. **azure vm create** コマンドを実行し、パブリック IP および上記で作成した NIC を使用して VM を作成します。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。
   
        azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd
   
    予想される出力:
   
        info:    Executing command vm create
        + Looking up the VM "DNS01"
        info:    Using the VM Size "Standard_A1"
        warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account vnetstorage
        + Looking up the NIC "TestNIC"
        info:    Found an existing NIC "TestNIC"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
        info:    Found public ip parameters, trying to setup PublicIP profile
        + Looking up the public ip "TestPIP"
        info:    Found an existing PublicIP "TestPIP"
        info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
        + Updating NIC "TestNIC"
        + Looking up the NIC "TestNIC"
        + Creating VM "DNS01"
        info:    vm create command OK
   
   * **-y (または --os-type)**。 VM のオペレーティング システムのタイプ (*Windows* または *Linux*) です。
   * **-f (または --nic-name)**。 VM が使用する NIC の名前です。
   * **-i (または --public-ip-name)**。 VM が使用するパブリック IP の名前です。
   * **-F (または --vnet-name)**。 VM の作成先となる VNet の名前です。
   * **-j (または --vnet-subnet-name)**。 VM の作成先となるサブネットの名前です。

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>VM 用の静的プライベート IP アドレス情報を取得する方法
上記のスクリプトで作成された VM の静的プライベート IP アドレス情報を表示するには、次の Azure CLI コマンドを実行し、*Private IP alloc-method* と *Private IP address* の値を確認します。

    azure vm show -g TestRG -n DNS01

予想される出力:

    info:    Executing command vm show
    + Looking up the VM "DNS01"
    + Looking up the NIC "TestNIC"
    + Looking up the public ip "TestPIP
    data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    ProvisioningState               :Succeeded
    data:    Name                            :DNS01
    data:    Location                        :centralus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Source image:
    data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
    data:
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :DNS01
    data:      User Name                     :adminuser
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-90-1A-A8
    data:          Provisioning State        :Succeeded
    data:          Name                      :TestNIC
    data:          Location                  :centralus
    data:            Private IP alloc-method :Static
    data:            Private IP address      :192.168.1.101
    data:            Public IP address       :40.122.213.159
    info:    vm show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>VM から静的プライベート IP アドレスを削除する方法
リソース マネージャーの Azure CLI では、NIC から静的プライベート IP アドレスを削除することはできません。 動的 IP を使用する新しい NIC を作成し、VM から以前の NIC を削除して、VM に新しい NIC を追加する必要があります。 上記のコマンドで使用された VM の NIC を変更するには、次の手順に従います。

1. **azure network nic create** コマンドを実行し、動的 IP 割り当てを使用して新しい NIC を作成します。 ここでは IP アドレスを指定する必要がないことに注意してください。
   
        azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd
   
    予想される出力:
   
        info:    Executing command network nic create
        + Looking up the network interface "TestNIC2"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC2"
        + Looking up the network interface "TestNIC2"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
        data:    Name                            : TestNIC2
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.6
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK
2. **azure vm set** コマンドを実行し、VM によって使用される NIC を変更します。
   
        azure vm set -g TestRG -n DNS01 -N TestNIC2
   
    予想される出力:
   
        info:    Executing command vm set
        + Looking up the VM "DNS01"
        + Looking up the NIC "TestNIC2"
        + Updating VM "DNS01"
        info:    vm set command OK
3. 必要に応じて、 **azure network nic delete** コマンドを実行し、古い NIC を削除します。
   
        azure network nic delete -g TestRG -n TestNIC --quiet
   
    予想される出力:
   
        info:    Executing command network nic delete
        + Looking up the network interface "TestNIC"
        + Deleting network interface "TestNIC"
        info:    network nic delete command OK

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>既存の VM に静的プライベート IP アドレスを追加する方法
上記のスクリプトを使用して作成された VM によって使用される NIC に静的プライベート IP アドレスを追加するには、次のコマンドを実行します。

    azure network nic set -g TestRG -n TestNIC2 -a 192.168.1.101

予想される出力:

    info:    Executing command network nic set
    + Looking up the network interface "TestNIC2"
    + Updating network interface "TestNIC2"
    + Looking up the network interface "TestNIC2"
    data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
    data:    Name                            : TestNIC2
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : centralus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-90-29-25
    data:    Enable IP forwarding            : false
    data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    IP configurations:
    data:      Name                          : NIC-config
    data:      Provisioning state            : Succeeded
    data:      Private IP address            : 192.168.1.101
    data:      Private IP Allocation Method  : Static
    data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

## <a name="next-steps"></a>次のステップ
* [予約済みパブリック IP](virtual-networks-reserved-public-ip.md) アドレスについて理解する。
* [インスタンスレベル パブリック IP (ILPIP)](virtual-networks-instance-level-public-ip.md) アドレスについて理解する。
* [予約済み IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)を確認する。


