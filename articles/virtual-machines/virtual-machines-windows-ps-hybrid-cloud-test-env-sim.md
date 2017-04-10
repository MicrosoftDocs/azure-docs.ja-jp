---
title: "シミュレートされたハイブリッド クラウドのテスト環境 | Microsoft Docs"
description: "2 つの Azure 仮想ネットワークと 1 つの VNet 間接続を使用して、IT プロや開発テスト用のシミュレートされたハイブリッド クラウド環境を作成します。"
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ca5bf294-8172-44a9-8fed-d6f38d345364
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 1322af70f643bcc191e3d69fe4a174adc9cd7e8d


---
# <a name="set-up-a-simulated-hybrid-cloud-environment-for-testing"></a>テスト用のシミュレートされたハイブリッド クラウド環境の設定
この記事では、2 つの Azure 仮想ネットワークを使用して、Microsoft Azure でシミュレートされたハイブリッド クラウド環境を作成する手順について説明します。 完成すると次のような構成になります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

これはハイブリッド クラウド運用環境をシミュレートされており、次で構成されています。

* Azure 仮想ネットワークでホストされているシミュレートされ、簡素化されたオンプレミス ネットワーク (TestLab 仮想ネットワーク)。
* Azure でホストされているシミュレートされたクロスプレミス仮想ネットワーク (TestVNET)。
* 2 つの仮想ネットワークをつなぐ VNet 間接続。
* TestVNET 仮想ネットワークのセカンダリ ドメイン コントローラー。

この構成を基盤や共通の出発点にして以下を実行できます。

* シミュレートされたハイブリッド クラウド環境でアプリケーションを開発およびテストする。
* コンピューターのテスト構成 (TestLab 仮想ネットワークと TestVNET 仮想ネットワーク内にそれぞれコンピューターを配置) を作成して、ハイブリッド クラウド ベースの IT ワークロードをシミュレートする。

このハイブリッド クラウド テスト環境を設定する手順は、大きく次の 4 つのフェーズに分かれています。

1. TestLab 仮想ネットワークを構成する。
2. クロスプレミス仮想ネットワークを作成する。
3. VNet 間 VPN 接続を作成する。
4. DC2 を構成する。 

この構成には、Azure サブスクリプションが必要です。 MSDN または Visual Studio サブスクリプションをお持ちの場合は、「 [Visual Studio サブスクライバー向けの月単位の Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。

> [!NOTE]
> Azure の仮想マシンと仮想ネットワーク ゲートウェイは、稼働していると継続的に費用が発生します。 その費用は、MSDN サブスクリプションまたは有料のサブスクリプションに対して請求されます。 Azure VPN ゲートウェイは、2 台 1 組みの Azure の仮想マシンとして実装されます。 費用を最小限に抑えるためには、テスト環境を作成し、できる限り迅速に必要なテストとデモンストレーションを行います。
> 
> 

## <a name="phase-1-configure-the-testlab-virtual-network"></a>フェーズ 1: TestLab 仮想ネットワークを構成する
[基本構成テスト環境](https://technet.microsoft.com/library/mt771177.aspx) に関するトピックの手順を使用して、TestLab という Azure Virtual Network で DC1、APP1、および CLIENT1 の各コンピューターを構成します。 

次に、Azure PowerShell プロンプトを開始します。

> [!NOTE]
> 次のコマンド セットは、Azure PowerShell 1.0 以降を使用します。
> 
> 

ご使用のアカウントにサインインします。

    Login-AzureRMAccount

次のコマンドを使用して、サブスクリプション名を取得します。

    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Azure サブスクリプションを設定します。 フェーズ 1 で基本構成をビルドするために使用したものと同じサブスクリプションを使用します。 引用符内のすべての文字 (< および > を含む) を、正しい名前に置き換えます。

    $subscr="<subscription name>"
    Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

次に、Azure ゲートウェイをホストするために使用される、基本構成の TestLab 仮想ネットワークにゲートウェイ サブネットを追加します。

    $rgName="<name of your resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
    Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

その次に、TestLab 仮想ネットワーク用のゲートウェイに割り当てるパブリック IP アドレスを要求します。

    $gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

その後、ゲートウェイを作成します。

    $vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
    New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

新しいゲートウェイは、作成に 20 分程度かかる場合があることに注意してください。

ローカル コンピューターで Azure ポータルから CORP\User1 の資格情報を使用して DC1 に接続します。 コンピューターとユーザーが認証にローカル ドメイン コントローラーを使用するよう CORP ドメインを構成するために、DC1 で管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

    New-ADReplicationSite -Name "TestLab" 
    New-ADReplicationSite -Name "TestVNET"
    New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
    New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

現在の構成は次のようになります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)

## <a name="phase-2-create-the-testvnet-virtual-network"></a>フェーズ 2: TestVNET 仮想ネットワークを作成する
まず、TestVNET 仮想ネットワークを作成し、ネットワーク セキュリティ グループで保護します。

    $rgName="<name of the resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
    $testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
    $gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
    New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
    $rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
    New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
    $vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
    $nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
    Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

次に、TestVNET 仮想ネットワーク用のゲートウェイに割り当てられるパブリック IP アドレスを要求し、ゲートウェイを作成します。

    $gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
    New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

現在の構成は次のようになります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)

## <a name="phase-3-create-the-vnet-to-vnet-connection"></a>フェーズ 3: VNet 間接続を作成する
最初に、ランダムな、暗号強度の高い 32 文字の事前共有キーをネットワーク管理者またはセキュリティ管理者から取得します。 または、「 [Create a random string for an IPsec preshared key (IPsec 事前共有キー用にランダムな文字列を作成する)](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) 」に記載されている情報を使用して事前共有キーを取得します。

次に、以下のコマンドを使用して、VNet 間 VPN 接続を作成します (完了するまでに時間がかかることがあります)。

    $sharedKey="<pre-shared key value>"
    $gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
    $gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
    New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
    New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

数分後に接続が確立されます。

現在の構成は次のようになります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)

## <a name="phase-4-configure-dc2"></a>フェーズ 4: DC2 を構成する
まず、DC2 用の仮想マシンを作成します。 ローカル コンピューターで Azure PowerShell コマンド プロンプトから次のコマンドを実行します。

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<the storage account name for the base configuration>"
    $vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
    $vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

次に、Azure ポータルから新しい DC2 仮想マシンに接続します。

続いて、基本的な接続テストを行うためのトラフィックを許可するよう Windows ファイアウォール規則を構成します。 DC2 で管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc1.corp.contoso.com

ping コマンドで IP アドレス 10.0.0.4 からの応答が 4 回成功する必要があります。 これは VNet 間接続を介したトラフィックのテストです。

次に、DC2 で余っているデータ ディスクを新しいボリュームとして追加し、ドライブ文字 F: を割り当てます。

1. サーバー マネージャーの左側のウィンドウで、**[ファイル サービスと記憶域サービス]** をクリックし、**[ディスク]** をクリックします。
2. コンテンツ ウィンドウの **[ディスク]** グループで、(**[パーティション]** が **[不明]** に設定されている) **[ディスク 2]** をクリックします。
3. **[タスク]** をクリックし**[ボリューム]** をクリックします。
4. 新しいボリューム ウィザードの [開始する前に] ページで **[次へ]**をクリックします。
5. [サーバーとディスクの選択] ページで、**[ディスク 2]** をクリックし、**[次へ]** をクリックします。 メッセージが表示されたら、 **[OK]**をクリックします。
6. [ボリュームのサイズの指定] ページで、 **[次へ]**をクリックします。
7. [ドライブ文字またはフォルダーへの割り当て] ページで、 **[次へ]**をクリックします。
8. [ファイル システム形式の選択] ページで、 **[次へ]**をクリックします。
9. [選択内容の確認] ページで、 **[作成]**をクリックします。
10. 完了したら、 **[閉じる]**をクリックします。

次に、DC2 を corp.contoso.com ドメインのレプリカ ドメイン コントローラーとして構成します。 DC2 で Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

CORP\User1 のパスワードとディレクトリ サービス復元モード (DSRM) のパスワードの両方を入力し、DC2 を再起動するよう求められます。

これで TestVNET 仮想ネットワーク独自の DNS サーバー (DC2) が設定されたので、この DNS サーバーを使用するように TestVNET 仮想ネットワークを構成する必要があります。

1. Azure ポータルの左側のウィンドウで、仮想ネットワーク アイコンをクリックし、 **[TestVNET]**をクリックします。
2. **[設定]** タブで、**[DNS サーバー]** をクリックします。
3. **[プライマリ DNS サーバー]** で、「**192.168.0.4**」と入力して 10.0.0.4 を置き換えます。
4. **[Save]**をクリックします。

現在の構成は次のようになります。 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

これで、シミュレートされたハイブリッド クラウド環境でテストする準備が整いました。

## <a name="next-step"></a>次のステップ
* この環境に [Web ベースの基幹業務アプリケーション](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を設定します。




<!--HONumber=Nov16_HO3-->


