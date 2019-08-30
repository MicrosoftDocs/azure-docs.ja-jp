---
title: PowerShell を使用した Azure VM での AlwaysOn 可用性グループの構成 | Microsoft Docs
description: このチュートリアルでは、クラシック デプロイ モデルを使用して作成されたリソースを使用します。 PowerShell を使用して、Azure に AlwaysOn 可用性グループを作成します。
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: 89f731062ce46969c73f745d62b289b3b3483d8c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100352"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>PowerShell を使用した Azure VM での AlwaysOn 可用性グループの構成
> [!div class="op_single_selector"]
> * [クラシック:UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [クラシック:PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

開始する前に、Azure Resource Manager モデルでこのタスクを完了できるかを検討してください。 新たにデプロイする場合、Azure Resource Manager モデルを使用することをお勧めします。 [Azure Virtual Machines での SQL Server AlwaysOn 可用性グループ](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md)に関するページをご覧ください。

> [!IMPORTANT]
> ほとんどの新しいデプロイでは、Resource Manager モデルを使用することをお勧めします。 Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。

Azure 仮想マシン (VM) を使用すると、データベース管理者は高可用性の SQL Server システムに要するコストを下げることができます。 このチュートリアルでは、Azure 環境内で SQL Server AlwaysOn をエンド ツー エンドで使用して、可用性グループを実装する方法について説明します。 チュートリアルの最後には、次の要素で構成された SQL Server AlwaysOn ソリューションが Azure で完成します。

* 複数のサブネットから成る仮想ネットワーク (フロントエンドのサブネットとバックエンドのサブネットを含む)。
* Active Directory ドメインを持つドメイン コントローラー。
* バックエンド サブネットにデプロイされ、Active Directory ドメインに参加している 2 つの SQL Server VM。
* 3 ノードの Windows フェールオーバー クラスター (ノード マジョリティ クォーラム モデル)。
* 可用性データベースの 2 つの同期コミット レプリカを含む可用性グループ。

このシナリオは、Azure での費用対効果などの要因ではなく、Azure での単純さを重視して選択されています。 たとえば、Azure でのコンピューティング時間を節約するために、2 ノードのフェールオーバー クラスターでドメイン コントローラーをクォーラム ファイル共有監視として使用することで、2 つのレプリカを持つ可用性グループの VM 数を最小限に抑えることができます。 この方法では、上記の構成よりも VM 数が 1 つ減少します。

このチュートリアルでは、冒頭に挙げたソリューションをセットアップするために必要な手順を紹介します。個々の手順について深く掘り下げて説明することはしません。 したがって、GUI での構成手順は示さず、それぞれの手順を手早く実行できるように PowerShell スクリプトを使用しています。 このチュートリアルでは、次のことを前提としています。

* 仮想マシン サブスクリプション付きの Azure アカウントを既に所有している。
* [Azure PowerShell コマンドレット](/powershell/azure/overview)がインストール済みである。
* オンプレミス ソリューションにおける AlwaysOn 可用性グループについて十分に理解している。 詳細については、「 [AlwaysOn 可用性グループ (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)」をご覧ください。

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Azure サブスクリプションへの接続と仮想ネットワークの作成
1. ローカル コンピューターの PowerShell ウィンドウで、Azure モジュールをインポートし、発行設定ファイルをコンピューターにダウンロードします。その後、ダウンロードした発行設定ファイルをインポートして、該当する Azure サブスクリプションに PowerShell セッションを接続します。

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    **Get-AzurePublishSettingsFile** コマンドを実行すると、管理証明書が自動的に生成され、Azure によってローカル コンピューターにダウンロードされます。 ブラウザーが自動的に開き、Azure サブスクリプションに使用する Microsoft アカウントの資格情報を入力するように求められます。 ダウンロードされた **.publishsettings** ファイルには、Azure サブスクリプションを管理するうえで必要になる情報がすべて含まれています。 このファイルをローカル ディレクトリに保存した後、 **Import-AzurePublishSettingsFile** コマンドを使用してインポートしてください。

   > [!NOTE]
   > publishsettings ファイルには、Azure のサブスクリプションとサービスの管理に使用される資格情報 (デコード済み) が保存されています。 セキュリティを考慮して、このファイルはソース ディレクトリの外 (Libraries\Documents フォルダーなど) に一時的に保存し、インポートが完了したら削除することをお勧めします。 悪意のあるユーザーが .publishsettings ファイルへのアクセス許可を取得すると、Azure サービスを編集、作成、削除できるためです。

2. クラウド IT インフラストラクチャの作成に使用する一連の変数を定義します。

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    後でコマンドを正しく実行できるように、次の点に注意してください。

   * **$storageAccountName** 変数と **$dcServiceName** 変数はそれぞれ、インターネット上でクラウドのストレージ アカウントとクラウド サーバーを識別するために使用されるため、一意の値を指定する必要があります。
   * **$affinityGroupName** 変数と **$virtualNetworkName** 変数に指定した名前は、後で使用する仮想ネットワークの構成ドキュメントに設定されます。
   * **$sqlImageName** には、SQL Server 2012 Service Pack 1 Enterprise Edition が存在する VM イメージの最新の名前を指定します。
   * わかりやすくするために、**Contoso! 000** というパスワードをチュートリアル全体で使用します。

3. アフィニティ グループを作成します。

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. 構成ファイルをインポートして仮想ネットワークを作成します。

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    この構成ファイルの内容は、以下の XML ドキュメントになっています。 簡単に説明すると、**ContosoAG** と呼ばれるアフィニティ グループ内の **ContosoNET** と呼ばれる仮想ネットワークを指定します。 これにはアドレス空間 **10.10.0.0/16** と、**10.10.1.0/24** および **10.10.2.0/24** という 2 つのサブネットが含まれています。これらは、それぞれフロント サブネットとバック サブネットです。 フロント サブネットには、Microsoft SharePoint などのクライアント アプリケーションを配置できます。 バック サブネットには、SQL Server VM を配置します。 **$affinityGroupName** 変数と **$virtualNetworkName** 変数を前の手順で変更した場合は、以下でも対応する名前を変更する必要があります。

        <NetworkConfiguration xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. 作成したアフィニティ グループに関連付けられたストレージ アカウントを作成し、そのアカウントを、サブスクリプション内の現在のストレージ アカウントとして設定します。

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. 新しいクラウド サービスと可用性セットにドメイン コントローラー サーバーを作成します。

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    パイプで接続されたこれらのコマンドによって、次の処理が実行されます。

   * **New-AzureVMConfig** は、VM 構成を作成します。
   * **Add-AzureProvisioningConfig** は、スタンドアロン Windows サーバーの構成パラメーターを設定します。
   * **Add-AzureDataDisk** は、キャッシュ オプションが None に設定されている場合、Active Directory データを格納するために使用するデータ ディスクを追加します。
   * **New-AzureVM** は、新しいクラウド サービスを作成し、そこに新しい Azure VM を作成します。

7. 新しい VM が完全にプロビジョニングされるのを待ち、リモート デスクトップ ファイルを自分の作業ディレクトリにダウンロードします。 新しい Azure VM のプロビジョニングには長時間かかるため、`while` ループによって、使用の準備が整うまで新しい VM に対するポーリングが続けられます。

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

これで、ドメイン コントローラー サーバーは正常にプロビジョニングされました。 次に、このドメイン コントローラー サーバー上に Active Directory ドメインを構成します。 ローカル コンピューターで、PowerShell ウィンドウを開いたままにしておきます。 2 つの SQL Server VM を作成するために、後でまた使用します。

## <a name="configure-the-domain-controller"></a>ドメイン コントローラーの構成
1. リモート デスクトップ ファイルを起動して、ドメイン コントローラー サーバーに接続します。 新しい VM の作成時に指定した、コンピューター管理者のユーザー名 (AzureAdmin) とパスワード (**Contoso!000**) を使用します。
2. 管理者モードで PowerShell ウィンドウを開きます。
3. 次の **DCPROMO.EXE** コマンドを実行して、**corp.contoso.com** ドメインをセットアップし、データ ディレクトリをドライブ M に配置します。

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    コマンドが完了すると、VM が自動的に再起動されます。

4. リモート デスクトップ ファイルを起動して、ドメイン コントローラー サーバーに再接続します。 今回は、**CORP\Administrator** としてサインインします。
5. 管理者モードで PowerShell ウィンドウを開き、次のコマンドを使用して Active Directory PowerShell モジュールをインポートします。

        Import-Module ActiveDirectory

6. 次のコマンドを実行して、ドメインに 3 人のユーザーを追加します。

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** は、SQL Server サービス インスタンス、フェールオーバー クラスター、および可用性グループに関連するすべての構成に使用されます。 **CORP\SQLSvc1** と **CORP\SQLSvc2** は、2 つの SQL Server VM の SQL Server サービス アカウントとして使用されます。
7. 次に、以下のコマンドを実行して、ドメインにコンピューター オブジェクトを作成する権限を **CORP\Install** に付与します。

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    上記で指定した GUID は、コンピューター オブジェクト タイプの GUID です。 フェールオーバー クラスターの Active Directory オブジェクトを作成するために、**CORP\Install** アカウントには、**すべてのプロパティの読み取り**アクセス許可と**コンピューター オブジェクトの作成**アクセス許可が必要です。 **Read All Properties** 権限については、CORP\Install に既定で割り当てられるため、明示的に付与する必要はありません。 フェールオーバー クラスターを作成するために必要なアクセス許可の詳細については、「[Failover Cluster Step-by-Step Guide: Configuring Accounts in Active Directory (フェールオーバー クラスター ステップ バイ ステップ ガイド: Active Directory のアカウントの構成)](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx)」をご覧ください。

    これで Active Directory オブジェクトとユーザー オブジェクトの構成が終了しました。次に、2 つの SQL Server VM を作成し、このドメインに参加させます。

## <a name="create-the-sql-server-vms"></a>SQL Server VM の作成
1. ローカル コンピューターで開いている PowerShell ウィンドウを引き続き使用します。 次の追加の変数を定義します。

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    IP アドレス **10.10.0.4** は、通常、Azure Virtual Network の **10.10.0.0/16** サブネットに作成する最初の VM に割り当てられます。 **IPCONFIG** を実行して、これがドメイン コントローラー サーバーのアドレスであることを確認します。
2. 次のパイプ処理されたコマンドを実行して、フェールオーバー クラスターの最初の VM を **ContosoQuorum** という名前で作成します。

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    上記のコマンドに関して、次の点に注意してください。

   * **New-AzureVMConfig** は、希望する可用性セット名で VM 構成を作成します。 その後作成される VM は、同じ可用性セットに参加するように、同じ可用性セット名を使用します。
   * **Add-AzureProvisioningConfig** は、作成した Active Directory ドメインに VM を参加させます。
   * **Set-AzureSubnet** は、VM をバック サブネットに配置します。
   * **New-AzureVM** は、新しいクラウド サービスを作成し、そこに新しい Azure VM を作成します。 **DnsSettings** パラメーターは、新しいクラウド サービス内にあるサーバーの DNS サーバーが、IP アドレス **10.10.0.4** を持つことを指定します。 これは、ドメイン コント ローラー サーバーの IP アドレスです。 このパラメーターは、クラウド サービスの新しい VM を有効にして、Active Directory ドメインに正常に参加できるようにするために必要です。 このパラメーターを指定しない場合は、VM で IPv4 設定を手動で設定して、VM がプロビジョニングされた後にドメイン コントローラー サーバーをプライマリ DNS サーバーとして使用できるようにしてから、VM を Active Directory ドメインに参加させる必要があります。
3. 以下のパイプ処理されたコマンドを実行して、**ContosoSQL1** および **ContosoSQL2** という名前の SQL Server VM を作成します。

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    上記のコマンドに関して、次の点に注意してください。

   * **New-AzureVMConfig** は、ドメイン コントローラー サーバーと同じ可用性セット名を使用し、仮想マシン ギャラリーの SQL Server 2012 Service Pack 1 Enterprise Edition イメージを使用します。 また、オペレーティング システム ディスクを読み取りキャッシュ専用 (書き込みキャッシュなし) に設定しています。 データベース ファイルは、VM にアタッチする専用のデータ ディスクに移行し、読み取りキャッシュと書き込みキャッシュなしで構成することをお勧めします。 ただし、次善の処理は、オペレーティング システム ディスクで書き込みキャッシュを削除することです。オペレーティング システム ディスクでは、読み取りキャッシュを削除できないためです。
   * **Add-AzureProvisioningConfig** は、作成した Active Directory ドメインに VM を参加させます。
   * **Set-AzureSubnet** は、VM をバック サブネットに配置します。
   * **Add-AzureEndpoint** は、クライアント アプリケーションからインターネット上の SQL Server サービス インスタンスにアクセスできるように、アクセス エンドポイントを追加します。 ContosoSQL1 と ContosoSQL2 には、異なるポートが用意されます。
   * **New-AzureVM** は、ContosoQuorum と同じクラウド サービスに新しい SQL Server VM を作成します。 VM を同じ可用性セットに含める場合は、VM を同じクラウド サービスに配置する必要があります。
4. 各 VM が完全にプロビジョニングされるのを待ち、VM ごとにリモート デスクトップ ファイルを自分の作業ディレクトリにダウンロードします。 `for` ループは 3 つの新しい VM を順に処理し、各 VM に対して、最上位の中かっこ内にあるコマンドを実行します。

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    これで SQL Server VM がプロビジョニングされ、実行されている状態になりましたが、これらは SQL Server と共に既定のオプションでインストールされています。

## <a name="initialize-the-failover-cluster-vms"></a>フェールオーバー クラスター VM の初期化
このセクションでは、フェールオーバー クラスターと SQL Server のインストールで使用する 3 台のサーバーを変更する必要があります。 具体的には次の処理が行われます。

* すべてのサーバー:**フェールオーバー クラスタリング** 機能をインストールする必要があります。
* すべてのサーバー:コンピューターの **管理者** として **CORP\Install** を追加する必要があります。
* ContosoSQL1 と ContosoSQL2 のみ:既定のデータベースの **sysadmin** ロールとして **CORP\Install** を追加する必要があります。
* ContosoSQL1 と ContosoSQL2 のみ:次の権限を持つサインインとして **NT AUTHORITY\System** を追加する必要があります。

  * 可用性グループの変更
  * SQL の接続
  * サーバー状態の表示
* ContosoSQL1 と ContosoSQL2 のみ:SQL Server VM では、 **TCP** プロトコルが既に有効になっています。 ただし、SQL Server にリモート アクセスするためには、ファイアウォールを解放する必要があります。

これで開始する準備ができました。 まず **ContosoQuorum**で、次の手順に従ってください。

1. リモート デスクトップ ファイルを起動して **ContosoQuorum** に接続します。 VM の作成時に指定した、コンピューター管理者のユーザー名 (**AzureAdmin**) とパスワード (**Contoso!000**) を使用します。
2. コンピューターが正常に **corp.contoso.com**に参加していることを確認します。
3. SQL Server のインストールで、自動化された初期化タスクの実行が完了してから、先に進みます。
4. 管理者モードで PowerShell ウィンドウを開きます。
5. Windows のフェールオーバー クラスタリング機能をインストールします。

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. **CORP\Install** をローカル管理者として追加します。

        net localgroup administrators "CORP\Install" /Add
7. ContosoQuorum からサインアウトします。 このサーバーについては、これで終了です。

        logoff.exe

次に、**ContosoSQL1** と **ContosoSQL2** を初期化します。 次の手順に従います。どちらの SQL Server VM でも手順は同じです。

1. リモート デスクトップ ファイルを起動して、2 つの SQL Server VM に接続します。 VM の作成時に指定した、コンピューター管理者のユーザー名 (**AzureAdmin**) とパスワード (**Contoso!000**) を使用します。
2. コンピューターが正常に **corp.contoso.com**に参加していることを確認します。
3. SQL Server のインストールで、自動化された初期化タスクの実行が完了してから、先に進みます。
4. 管理者モードで PowerShell ウィンドウを開きます。
5. Windows のフェールオーバー クラスタリング機能をインストールします。

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. **CORP\Install** をローカル管理者として追加します。

        net localgroup administrators "CORP\Install" /Add
7. SQL Server PowerShell プロバイダーをインポートします。

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. 既定の SQL Server インスタンスの sysadmin ロールとして **CORP\Install** を追加します。

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. 前に説明した 3 つの権限を持つサインインとして **NT AUTHORITY\System** を追加します。

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. ファイアウォールを解放し、SQL Server のリモート アクセスを許可します。

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. 両方の VM からサインアウトします。

         logoff.exe

これで可用性グループを構成する準備が整いました。 **ContosoSQL1**上の作業はすべて、SQL Server PowerShell プロバイダーを使用して行います。

## <a name="configure-the-availability-group"></a>可用性グループの構成
1. リモート デスクトップ ファイルを起動して **ContosoSQL1** に再度接続します。 サインインには、コンピューター アカウントではなく、**CORP\Install** を使用してください。
2. 管理者モードで PowerShell ウィンドウを開きます。
3. 次の変数を定義します。

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. SQL Server PowerShell プロバイダーをインポートします。

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. ContosoSQL1 の SQL Server サービス アカウントを CORP\SQLSvc1 に変更します。

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. ContosoSQL2 の SQL Server サービス アカウントを CORP\SQLSvc2 に変更します。

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. [Azure VM での AlwaysOn 可用性グループのフェールオーバー クラスターの作成](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)に関するページから、**CreateAzureFailoverCluster.ps1** をローカルの作業ディレクトリにダウンロードします。 このスクリプトを使用すると、機能的なフェールオーバー クラスターを作成できます。 Windows フェールオーバー クラスタリングと Azure ネットワークのやり取りに関する重要な情報については、「[Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)」をご覧ください。
8. 作業ディレクトリに移動し、ダウンロードしたスクリプトを使用してフェールオーバー クラスターを作成します。

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. **ContosoSQL1** と **ContosoSQL2** で、既定の SQL Server インスタンスの AlwaysOn 可用性グループを有効にします。

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. バックアップ ディレクトリを作成し、SQL Server サービス アカウントの権限を付与します。 セカンダリ レプリカの可用性データベースを準備するには、このディレクトリを使用します。

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. **ContosoSQL1** に **MyDB1** というデータベースを作成して完全バックアップとログ バックアップを作成し、**ContosoSQL2** で **WITH NORECOVERY** オプションを使用して復元します。

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. SQL Server VM 上に可用性グループのエンドポイントを作成し、それらのエンドポイントに適切な権限を設定します。

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. 可用性レプリカを作成します。

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. 最後に、可用性グループを作成し、それにセカンダリ レプリカを参加させます。

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>次の手順
これで、Azure に可用性グループを作成して、SQL Server AlwaysOn を正常に実装できました。 この可用性グループのリスナーを構成するには、「[Azure での AlwaysOn 可用性グループの ILB リスナーの構成](../classic/ps-sql-int-listener.md)」を参照してください。

Azure での SQL Server の使用に関するその他の情報については、「[Azure Virtual Machines における SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。
