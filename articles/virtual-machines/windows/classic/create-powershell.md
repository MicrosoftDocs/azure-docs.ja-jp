---
title: "PowerShell を使用した Windows VM の作成 | Microsoft Docs"
description: "Azure PowerShell とクラシック デプロイ モデルを使用して Windows 仮想マシンを作成します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 42c0d4be-573c-45d1-b9b0-9327537702f7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: d9b713460f98104017ae73ea27f30b0d8d1ca7ee
ms.lasthandoff: 03/27/2017


---
# <a name="create-a-windows-virtual-machine-with-powershell-and-the-classic-deployment-model"></a>PowerShell とクラシック デプロイメント モデルを使用した Windows 仮想マシンの作成
> [!div class="op_single_selector"]
> * [Azure クラシック ポータル - Windows](tutorial.md)
> * [PowerShell - Windows](create-powershell.md)
> 
> 

<br>

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager モデルを使用してこれらの手順を実行する](../../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法について説明します。

以下の手順では、構成ブロック手法を使用して、Azure PowerShell コマンド セットをカスタマイズする方法を示します。このコマンド セットでは、Windows ベースの Azure 仮想マシンを作成および事前構成します。 このプロセスを使用すると、新しい Windows ベースの仮想マシンのコマンド セットを迅速に作成して既存のデプロイメントを拡張することや、複数のコマンド セットを作成してカスタムの開発とテスト環境または IT プロの環境をすばやく構築することもできます。

これらの手順では、空白に記入する方式に従って Azure PowerShell コマンド セットを作成します。 この方法は、PowerShell を初めて使う場合や、構成を正しく行うためにどの値を指定するとよいかを知りたい場合に役立ちます。 PowerShell に慣れているユーザーは、コマンドの変数を独自の値で置き換えることができます ("$" で始まる行)。

まだ完了していない場合は、 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs) に関するページの手順に従って、Azure PowerShell をご使用のローカル コンピューターにインストールします。 次に、Windows PowerShell コマンド プロンプトを開きます。

## <a name="step-1-add-your-account"></a>手順 1: アカウントを追加する
1. PowerShell プロンプトで、「**Add-AzureAccount**」と入力し、**Enter** キーを押します。 
2. お使いの Azure サブスクリプションに関連付けられている電子メール アドレスを入力し、 **[続行]**をクリックします。 
3. アカウントのパスワードを入力します。 
4. **[サインイン]**をクリックします。

## <a name="step-2-set-your-subscription-and-storage-account"></a>手順 2. サブスクリプションとストレージ アカウントを設定する
Windows PowerShell コマンド プロンプトで次のコマンドを実行して、Azure サブスクリプションとストレージ アカウントを設定します。 引用符内のすべての文字 (< および > を含む) を、正しい名前に置き換えます。

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

**Get-AzureSubscription** コマンドで出力される SubscriptionName プロパティで正しいサブスクリプション名を取得できます。 **Select-AzureSubscription** コマンドの実行後、**Get-AzureStorageAccount** コマンドを実行して出力される Label プロパティで正しいストレージ アカウント名を取得できます。

## <a name="step-3-determine-the-imagefamily"></a>手順 3. ImageFamily を特定する
次に、作成する Azure 仮想マシンに対応する特定のイメージで使用するために、ImageFamily または Label の値を特定する必要があります。 このコマンドで、利用可能な ImageFamily 値の一覧を取得できます。

    Get-AzureVMImage | select ImageFamily -Unique

Windows ベースのコンピューターで使用する ImageFamily 値の例は次のとおりです。

* Windows Server 2012 R2 Datacenter
* Windows Server 2008 R2 SP1
* Windows Server 2016 Technical Preview 4
* SQL Server 2012 SP1 Enterprise on Windows Server 2012

目的のイメージが見つかったら、任意のテキスト エディターの最新インスタンスまたは PowerShell Integrated Scripting Environment (ISE) を開きます。 新しいテキスト ファイルまたは PowerShell ISE に次のコードをコピーし、ImageFamily 値を置き換えます。

    $family="<ImageFamily value>"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

イメージの名前は、ImageFamily 値ではなく Label プロパティに存在する場合があります。 ImageFamily プロパティを使用しても目的のイメージが見つからなかった場合は、次のコマンドを実行して Label プロパティのイメージを一覧表示します。

    Get-AzureVMImage | select Label -Unique

このコマンドで目的のイメージが見つかった場合は、任意のテキスト エディターの最新インスタンスまたは PowerShell ISE を開きます。 新しいテキスト ファイルまたは PowerShell ISE に次のコードをコピーし、Label 値を置き換えます。

    $label="<Label value>"
    $image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## <a name="step-4-build-your-command-set"></a>手順 4. コマンド セットを構築する
残りのコマンド セットを構築します。具体的には、下の該当するブロック セットを新しいテキスト ファイルまたは ISE にコピーし、変数の値を入力した後、< および > を削除します。 この記事の末尾にある 2 つの [例](#examples) を、最終結果のアイデアとしてご覧ください。

この 2 つのコマンド ブロックのいずれかを選択することからコマンド セットを開始します (必須)。

オプション 1. 仮想マシンの名前とサイズを指定します。

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

オプション 2. 名前、サイズ、可用性セット名を指定します。

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $availset="<set name>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

D、DS、または G シリーズの各仮想マシンの InstanceSize 値の詳細については、「 [Azure の仮想マシンおよびクラウド サービスのサイズ](https://msdn.microsoft.com/library/azure/dn197896.aspx)」を参照してください。

> [!NOTE]
> ソフトウェア アシュアランス付きのマイクロソフト エンタープライズ契約をしていて、Windows Server [Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) を利用する予定の場合、一般的なユース ケースでは、**- LicenseType** パラメーターを **New-AzureVMConfig** コマンドレットに追加することで、値 **Windows_Server** を渡します。  アップロードしたイメージを使用していることを確認します。ギャラリーの標準イメージと Hybrid Use Benefit は併用できません。
> 
> 

スタンドアロンの Windows コンピューターの場合は、必要に応じて、ローカル管理者のアカウントとパスワードを指定します。

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

強力なパスワードを選択してください。 強度を確認するには、 [パスワード チェッカーの強力なパスワードの使用](https://www.microsoft.com/security/pc-security/password-checker.aspx)に関するページを参照してください。

また、必要に応じて Windows コンピューターを既存の Active Directory ドメインに追加するには、ローカル管理者のアカウントとパスワード、ドメイン名、ドメイン アカウントの名前とパスワードを指定します。

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="<FQDN of the domain that the machine is joining>"
    $domacctdomain="<domain of the account that has permission to add the machine to the domain>"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

これ以外の Windows ベースの事前構成の追加方法については、「[Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx)」で **Windows** パラメーター セットと **WindowsDomain** パラメーター セットの構文を参照してください。

必要に応じて、次のように、"静的 DIP" と呼ばれる特定の IP アドレスを仮想マシンに割り当てます。

    $vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

次のように実行すると、特定の IP アドレスが利用可能かどうかを調べることができます。

    Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

必要に応じて、次のように、Azure 仮想ネットワークの特定のサブネットに仮想マシンを割り当てます。

    $vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

必要に応じて、次のように、単一データ ディスクを仮想マシンに追加します。

    $disksize=<size of the disk in GB>
    $disklabel="<the label on the disk>"
    $lun=<Logical Unit Number (LUN) of the disk>
    $hcaching="<Specify one: ReadOnly, ReadWrite, None>"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Active Directory ドメイン コントローラーでは、$hcaching を "None" に設定します。

必要に応じて、外部トラフィックを処理するために、既存の負荷分散セットに仮想マシンを次のように追加します。

    $protocol="<Specify one: tcp, udp>"
    $localport=<port number of the internal port>
    $pubport=<port number of the external port>
    $endpointname="<name of the endpoint>"
    $lbsetname="<name of the existing load-balanced set>"
    $probeprotocol="<Specify one: tcp, http>"
    $probeport=<TCP or HTTP port number of probe traffic>
    $probepath="<URL path for probe traffic>"
    $vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $protocol -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

最後に、仮想マシンの作成に必要な以下のコマンド ブロックの 1 つを選択します。

オプション 1. 既存のクラウド サービスに仮想マシンを作成します。

    New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

クラウド サービスの短い名前が、Azure クラシック ポータルの [Cloud Services] ボックスの一覧か、Azure ポータルの [リソース グループ] ボックスの一覧に表示されます。

オプション 2. 仮想マシンを既存のクラウド サービスと仮想ネットワークに作成します。

    $svcname="<short name of the cloud service>"
    $vnetname="<name of the virtual network>"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## <a name="step-5-run-your-command-set"></a>手順 5. コマンド セットを実行する
手順 4. でテキスト エディターまたは PowerShell ISE を使用して作成した、複数のコマンド ブロックで構成される Azure PowerShell コマンド セットを確認します。 必要なすべての変数が指定され、それらの値がすべて正しいことを確認します。 さらに、< と > がすべて削除されていることも確認します。

テキスト エディターを使用している場合は、コマンド セットをクリップボードにコピーしてから、開いている Windows PowerShell コマンド プロンプトを右クリックします。 この操作により、コマンド セットが一連の PowerShell コマンドとして実行され、Azure 仮想マシンが作成されます。 または、PowerShell ISE でコマンド セットを実行します。

この仮想マシンまたは同様のマシンを再び作成する場合は、次のことができます。

* このコマンド セットを PowerShell スクリプト ファイル (*.ps1) として保存する。
* Azure クラシック ポータルの **[Automation]** セクションで、このコマンド セットを Azure Automation Runbook として保存する。

## <a id="examples"></a>例
次に、Windows ベースの Azure Virtual Machines を作成するために、前の手順を使用して Azure PowerShell コマンド セットを構築する例を 2 つ示します。

### <a name="example-1"></a>例 1
次の条件で Active Directory ドメイン コントローラーに最初の仮想マシンを作成する PowerShell コマンド セットが必要な場合。

* Windows Server 2012 R2 Datacenter イメージを使用する。
* 名前は AZDC1。
* スタンドアロン コンピューターである。
* 20 GB の追加データ ディスク容量。
* 静的 IP アドレスは 192.168.244.4。
* AZDatacenter 仮想ネットワークの BackEnd サブネットに属している。
* Azure-TailspinToys クラウド サービスに属している。

この仮想マシンを作成するための対応する Azure PowerShell コマンド セットは次のとおりです。読みやすくするために各ブロックの間に空白行を入れてあります。

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### <a name="example-2"></a>例 2
次の条件で基幹業務サーバー用に仮想マシンを作成する PowerShell コマンド セットが必要な場合。

* Windows Server 2012 R2 Datacenter イメージを使用する。
* 名前は LOB1。
* corp.contoso.com ドメインのメンバーである。
* 200 GB の追加データ ディスク容量。
* AZDatacenter 仮想ネットワークの FrontEnd サブネットに属している。
* Azure-TailspinToys クラウド サービスに属している。

この仮想マシンを作成するための対応する Azure PowerShell コマンド セットは次のとおりです。

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="LOB1"
    $vmsize="Large"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="corp.contoso.com"
    $domacctdomain="CORP"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

    $disksize=200
    $disklabel="LOBData"
    $lun=0
    $hcaching="ReadWrite"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="next-steps"></a>次のステップ
127 GB より大きい OS ディスクが必要な場合は、 [OS ドライブを展開](../../virtual-machines-windows-expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)することができます。


