---
title: "PowerShell を使用して複数 NIC を持つ VM (クラシック) を作成する | Microsoft Docs"
description: "PowerShell を使用して複数 NIC を持つ VM を作成して構成する方法について説明します。"
services: virtual-network, virtual-machines
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: a1a3952c-2dcc-4977-bd7a-52d623c1fb07
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: d10ae92ba79014d9700613c06ef37a437089f5ac


---
# <a name="create-a-vm-classic-with-multiple-nics"></a>複数 NIC を持つ VM (クラシック) を作成する
Azure に仮想マシン (VM) を作成し、複数のネットワーク インターフェイス (NIC) を各 VM にアタッチできます。 複数 NIC は、アプリケーションの配布や WAN の最適化ソリューションなど、多くのネットワーク仮想アプライアンスに必要です。 複数 NIC では、NIC 間のトラフィックの分離させることもできます。

![VM の複数の NIC](./media/virtual-networks-multiple-nics/IC757773.png)

図は、3 つの NIC を持つ VM を示しており、各 NIC は異なるサブネットに接続されています。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、Resource Manager を使用することをお勧めします。

* インターネットに接続された VIP (従来のデプロイ) は、"既定" の NIC でのみサポートされています。 既定の NIC の IP アドレスに対して VIP は 1 つしかありません。
* 現時点では、複数 NIC の VM のインスタンスレベル パブリック IP (LPIP) アドレス (従来のデプロイ) はサポートされていません。
* VM 内の NIC の順序はランダムになり、Azure インフラストラクチャの更新によって変更される場合もあります。 ただし、IP アドレスと、対応するイーサネットの MAC アドレスは変更されません。 たとえば、**Eth1** の IP アドレスが 10.1.0.100 で、MAC アドレスが 00-0D-3A-B0-39-0D だとします。Azure インフラストラクチャが更新されて再起動された後、これを **Eth2** に変更できますが、IP アドレスと MAC アドレスの組み合わせは同じままになります。 お客様が再起動を開始した場合、NIC の順序は変更されません。
* 各 VM の NIC のアドレスはそれぞれサブネットに存在する必要があるため、1 つの VM 上の複数 NIC それぞれには、同じサブネット内のアドレスに割り当てることができます。
* VM のサイズによって、VM に作成できる NIC の数が決まります。 各サイズの VM でサポートされる NIC の数を確認するには、[Windows Server](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) と [Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) の VM のサイズに関する記事を参照してください。 

## <a name="network-security-groups-nsgs"></a>ネットワーク セキュリティ グループ (NSG)
リソース マネージャー デプロイでは、複数 NIC が有効になっている VM 上の NIC など、VM 上の任意の NIC をネットワーク セキュリティ グループ (NSG) に関連付けることができます。 NIC にサブネット内のアドレスが割り当てられ、そのサブネットが NSG に関連付けられている場合、サブネットの NSG のルールがその NIC にも適用されます。 サブネットを NSG に関連付けるだけでなく、NIC を NSG に関連付けることもできます。

サブネットが NSG に関連付けられていて、そのサブネット内の NIC が個別に NSG に関連付けられている場合、関連付けられた NSG のルールは、NIC で受け渡されるトラフィックの方向に従って **フロー順序** で適用されます。

* **着信トラフィック** は、送信先が対象の NIC であり、サブネット経由で最初に送信され、サブネットの NSG ルールをトリガーし、NIC へ渡される前に NIC の NSG ルールをトリガーします。
* **送信トラフィック** は、送信元が対象の NIC であり、NIC から最初に送信され、NIC の NSG ルールをトリガーし、サブネット経由で渡される前にサブネットの NSG ルールをトリガーします。

[ネットワーク セキュリティ グループ](virtual-networks-nsg.md) の詳細、およびサブネット、VM、NIC への関連付けに基づいてそれらを適用する方法についてご確認ください。

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>従来のデプロイに複数 NIC の VM を構成する方法
3 つの NIC (既定の NIC と追加の 2 つの NIC) を含む複数 NIC の VM を作成するには、次の手順が役立ちます。 この構成手順によって、次のサービス構成ファイルの一部に従って構成される VM が作成されます。

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


この例の PowerShell コマンドを実行するには、次の前提条件を満たしておく必要があります。

* Azure サブスクリプション。
* 構成済みの仮想ネットワーク。 Vnet の詳細については、「 [仮想ネットワークの概要](virtual-networks-overview.md) 」を参照してください。
* Azure PowerShell の最新バージョンをダウンロードしてインストールしていること。 「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。

複数 NIC を持つ VM を作成するには、1 つの PowerShell セッションに各コマンドを入力して次の手順を実行します。

1. Azure VM イメージ ギャラリーから VM イメージを選択します。 イメージは頻繁に変更され、リージョンごとに利用できることに注意してください。 次の例で指定されたイメージは変更されたり、リージョンによって提供されていない可能性があるため、必ず必要なイメージを指定してください。

    ```powershell
    $image = Get-AzureVMImage `
    -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"
    ```

2. VM 構成を作成します。

    ```powershell
    $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
    -Image $image.ImageName –AvailabilitySetName "MyAVSet"
    ```

3. 既定の管理者ログインを作成します。

    ```powershell
    Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
    -Password "<YourAdminPassword>"
    ```

4. VM 構成に NIC を追加します。

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
    -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
    Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
    -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm
    ```

5. 既定の NIC のサブネットと IP アドレスを指定します。

    ```powershell
    Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
    Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm
    ```

6. 仮想ネットワーク内に VM を作成します。

    ```powershell
    New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm
    ```

    > [!NOTE]
    > ここでは既存の VNet を指定する必要があります (前提条件で説明)。 次の例では **MultiNIC-VNet**という名前の仮想ネットワークを指定します。
    >

## <a name="limitations"></a>制限事項
複数 NIC 機能を使用する場合、次の制限が適用されます。

* 複数 NIC を持つ VM は、Azure の仮想ネットワーク (Vnet) 上に作成する必要があります。 複数 NIC では、非 VNet VM を構成できません。
* 可用性セットのすべての VM は、複数 NIC または単一 NIC を使用する必要があります。 可用性セット内に複数 NIC VM と単一 NIC VM を混在させることはできません。 同じ規則がクラウド サービス内の VM に適用されます。 複数 NIC を持つ VM では、それぞれ少なくとも 2 つ以上の NIC があればよく、NIC の数が同じである必要はありません。
* 単一 NIC の VM をデプロイ後に、複数 NIC で構成することはできません (その逆の場合も構成できません)。構成するには、VM を削除してから、再作成する必要があります。

## <a name="secondary-nics-access-to-other-subnets"></a>他のサブネットへのセカンダリ NIC アクセス
セカンダリ NIC のトラフィック フローは、同じサブネット内に制限されるため、既定では、セカンダリ NIC に既定のゲートウェイは構成されません。 ユーザーは、セカンダリ NIC がユーザーのサブネットの外部と通信できるようにする場合は、以下に示すように、ルーティング テーブルにエントリを追加してゲートウェイを構成する必要があります。

> [!NOTE]
> 2015 年 7 月より前に作成された VM の場合、すべての NIC に既定のゲートウェイが構成されている可能性があります。 セカンダリ NIC の既定のゲートウェイは、その VM が再起動されるまで削除されません。 Linux などの弱いホスト ルーティング モデルを使用するオペレーティング システムでは、受信トラフィックと送信トラフィックが異なる NIC を使用している場合、インターネット接続が中断する可能性があります。
> 

### <a name="configure-windows-vms"></a>Windows VM の構成
次のような 2 つの NIC を備えた Windows VM があるものとします。

* プライマリ NIC の IP アドレス: 192.168.1.4
* セカンダリ NIC の IP アドレス: 192.168.2.5

この VM の IPv4 ルーティング テーブルは、次のようになります。

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

既定のルート (0.0.0.0) はプライマリ NIC だけが使用できることに注意してください。 次に示すように、セカンダリ NIC のサブネットの外部にあるリソースにはアクセスできません。

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

セカンダリ NIC で既定のルートを追加するには、次の手順に従います。

1. コマンド プロンプトから次のコマンドを実行して、セカンダリ NIC のインデックス番号を識別します。
   
        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================
2. インデックスが 27 (この例の場合) である表の 2 番目のエントリに注意してください。
3. 次に示すように、コマンド プロンプトから **route add** コマンドを実行します。 この例では、セカンダリ NIC のデフォルト ゲートウェイとして 192.168.2.1 を指定します。
   
        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27
4. 接続をテストするには、コマンド プロンプトに戻り、次の例で示すように、セカンダリ NIC から別のサブネットに対して ping を試みます。
   
        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
   
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
5. 次に示すように、ルート テーブルで新しく追加されたルートを確認することもできます。
   
        C:\Users\Administrator>route print
   
        ...
   
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>Linux VM を構成します。
Linux VM の場合、既定の動作では弱いホスト ルーティングが使用されるので、セカンダリ NIC を同じサブネット内のトラフィック フローだけに制限することをお勧めします。 ただし、特定のシナリオでサブネット外部への接続が必要な場合は、送信と受信のトラフィックが同じ NIC を使用するようにポリシー ベースのルーティングを有効にする必要があります。

## <a name="next-steps"></a>次のステップ
* [リソース マネージャー デプロイの 2 層アプリケーションのシナリオで複数 NIC の VM](virtual-network-deploy-multinic-arm-template.md)をデプロイします。
* [従来のデプロイの 2 層アプリケーションのシナリオで複数 NIC の VM](virtual-network-deploy-multinic-classic-ps.md)をデプロイします。




<!--HONumber=Dec16_HO1-->


