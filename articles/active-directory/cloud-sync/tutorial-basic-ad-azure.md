---
title: チュートリアル - オンプレミスの Active Directory と Azure AD の基本的な環境。
services: active-directory
description: AD と Azure AD の基本的な環境を作成する方法について説明します。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: de59b3f26a9a4696c721cab94cd5b96bd0200e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612127"
---
# <a name="tutorial-basic-active-directory-environment"></a>チュートリアル:Active Directory の基本的な環境

このチュートリアルでは、Active Directory の基本的な環境を作成する手順について説明します。 

![基本的 Azure AD 環境の図。](media/tutorial-single-forest/diagram-2.png)

チュートリアルで作成した環境を使用して、ハイブリッド ID シナリオのさまざまな側面をテストし、いくつかのチュートリアルの前提条件となります。  既存の Active Directory 環境がある場合は、代替として使用できます。  この情報は、新規に作成するユーザーのために提供されています。

このチュートリアルは次のもので構成されます。
## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するために必要な前提条件を次に示します。
- [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) がインストールされたコンピューター。  これは、[Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) または [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) コンピューターに対して行うことをお勧めします。
- 仮想マシンがインターネットで通信できるようにするための[外部ネットワーク アダプター](/virtualization/hyper-v-on-windows/quick-start/connect-to-network)。
- [Azure サブスクリプション](https://azure.microsoft.com/free)
- Windows Server 2016 のコピー
- [Microsoft .NET framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> 最短時間でチュートリアル環境を作成できるよう、このチュートリアルでは PowerShell スクリプトを使用します。  各スクリプトでは、その先頭で宣言された変数が使用されます。  変数はお客様の環境に合わせて変更することができ、そうする必要があります。
>
>使用されるスクリプトでは、Azure AD Connect クラウド プロビジョニング エージェントのインストール前に一般的な Active Directory 環境が作成されます。  これらはすべてのチュートリアルに関連しています。
>
> このチュートリアルで使用される PowerShell スクリプトのコピーは、[こちら](https://github.com/billmath/tutorial-phs)の GitHub で入手できます。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成
ハイブリッド ID 環境を稼働させるには、まず、オンプレミスの Active Directory サーバーとして使用される仮想マシンを作成する必要があります。  次の操作を行います。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

    ```powershell
    #Declare variables
    $VMName = 'DC1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\DC1\DC1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
    ```

## <a name="complete-the-operating-system-deployment"></a>オペレーティング システムの展開を完了する
仮想マシンの作成を完了するには、オペレーティング システムのインストールを完了する必要があります。

1. Hyper-V マネージャーで仮想マシンをダブルクリックします。
2. [スタート] ボタンをクリックします。
3. "Press any key to boot from CD or DVD" というメッセージが表示されます。 キーを押して続行します。
4. Windows Server の起動画面で言語を選択し、 **[次へ]** をクリックします。
5. **[今すぐインストール]** をクリックします。
6. ライセンス キーを入力し、 **[次へ]** をクリックします。
7. [ライセンス条項に同意します] をオンにし、 **[次へ]** をクリックします。
8. **[カスタム: Windows のみをインストールする (詳細設定)]** を選択します。
9. **[次へ]** をクリックします。
10. インストールが完了したら、仮想マシンを再起動してサインインし、Windows の更新プログラムを実行して VM を最新の状態にします。  最新の更新プログラムをインストールします。

## <a name="install-active-directory-prerequisites"></a>Active Directory の前提条件をインストールする
仮想マシンを稼働させたところで、Active Directory のインストール前にいくつかの作業を行う必要があります。  言い換えると、仮想マシンの名前を変更し、静的 IP アドレスと DNS 情報を設定して、リモート サーバー管理ツールをインストールする必要があります。   次の操作を行います。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

    ```powershell
    #Declare variables
    $ipaddress = "10.0.1.117" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.117"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "DC1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    # Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```

## <a name="create-a-windows-server-ad-environment"></a>Windows Server AD 環境を作成する
VM を作成して、名前の変更と静的 IP アドレスの設定を行いました。これで、Active Directory Domain Services のインストールと構成に進むことができます。  次の操作を行います。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

    ```powershell 
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "contoso.com"
    $DomaninNetBIOSName = "CONTOSO"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

## <a name="create-a-windows-server-ad-user"></a>Windows Server AD ユーザーを作成する
Active Directory 環境を作成したところで、テスト アカウントが必要になります。  このアカウントは、オンプレミスの AD 環境で作成されて Azure AD に同期されます。  次の操作を行います。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Allie"
    $Surname = "McCray"
    $Displayname = "Allie McCray"
    $Name = "amccray"
    $Password = "Pass1w0rd"
    $Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```


## <a name="create-an-azure-ad-tenant"></a>Azure AD テナントを作成する
次に、ユーザーをクラウドに同期できるよう、Azure AD テナントを作成する必要があります。  新しい Azure AD テナントを作成するには、以下を実行します。

1. [Azure portal](https://portal.azure.com) に移動し、Azure サブスクリプションがあるアカウントを使ってサインインします。
2. **プラス (+) アイコン** を選択し、**Azure Active Directory** を検索します。
3. 検索結果で **[Azure Active Directory]** を選択します。
4. **［作成］** を選択します</br>
![Azure portal の Azure Active Directory ページのスクリーンショット。](media/tutorial-single-forest/create-1.png)</br>
5. **組織の名前** と **初期ドメイン名** を入力します。 **[作成]** を選択します。 これにより、ディレクトリが作成されます。
6. これが完了したら、**こちら** のリンクをクリックし、ディレクトリを管理します。

## <a name="create-a-global-administrator-in-azure-ad"></a>Azure AD でグローバル管理者を作成する
Azure AD テナントを作成したので、次は全体管理者アカウントを作成します。  全体管理者アカウントを作成するには、以下を実行します。

1.  **[管理]** にある **[ユーザー]** を選択します。</br>
![[概要] メニューのスクリーンショット。[ユーザー] が選択されています。](media/tutorial-single-forest/administrator-1.png)</br>
2.  **[すべてのユーザー]** を選択し、 **+ [新しいユーザー]** を選択します。
3.  このユーザーの名前およびユーザー名を入力します。 これがテナントのグローバル管理者になります。 また、 **[ディレクトリ ロール]** を **[全体管理者]** に変更してください。 一時パスワードを表示することもできます。 完了したら、 **[作成]** を選択します。</br>
![作成](media/tutorial-single-forest/administrator-2.png)</br>
4. これが完了したら、新しい Web ブラウザーを開き、新しい全体管理者アカウントと一時パスワードを使用して myapps.microsoft.com にサインインします。
5. 全体管理者のパスワードを覚えやすいものに変更します。

## <a name="optional--additional-server-and-forest"></a>省略可能:その他のサーバーとフォレスト
次に示すのは、追加のサーバーまたはフォレストを作成する手順を説明するオプションのセクションです。  これは、[Azure AD Connect クラウド同期のパイロットの実施](tutorial-pilot-aadc-aadccp.md)など、より高度なチュートリアルで使用できます。

追加のサーバーのみが必要な場合は、**仮想マシンの作成** の手順で停止し、上記で作成した既存のドメインにサーバーを参加させることができます。  

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive
    ```

### <a name="complete-the-operating-system-deployment"></a>オペレーティング システムの展開を完了する
仮想マシンの作成を完了するには、オペレーティング システムのインストールを完了する必要があります。

1. Hyper-V マネージャーで仮想マシンをダブルクリックします。
2. [スタート] ボタンをクリックします。
3. "Press any key to boot from CD or DVD" というメッセージが表示されます。 キーを押して続行します。
4. Windows Server の起動画面で言語を選択し、 **[次へ]** をクリックします。
5. **[今すぐインストール]** をクリックします。
6. ライセンス キーを入力し、 **[次へ]** をクリックします。
7. [ライセンス条項に同意します] をオンにし、 **[次へ]** をクリックします。
8. **[カスタム: Windows のみをインストールする (詳細設定)]** を選択します。
9. **[次へ]** をクリックします。
10. インストールが完了したら、仮想マシンを再起動してサインインし、Windows の更新プログラムを実行して VM を最新の状態にします。  最新の更新プログラムをインストールします。

### <a name="install-active-directory-prerequisites"></a>Active Directory の前提条件をインストールする
仮想マシンを稼働させたところで、Active Directory のインストール前にいくつかの作業を行う必要があります。  言い換えると、仮想マシンの名前を変更し、静的 IP アドレスと DNS 情報を設定して、リモート サーバー管理ツールをインストールする必要があります。   次の操作を行います。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```
### <a name="create-a-windows-server-ad-environment"></a>Windows Server AD 環境を作成する
VM を作成して、名前の変更と静的 IP アドレスの設定を行いました。これで、Active Directory Domain Services のインストールと構成に進むことができます。  次の操作を行います。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD environment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

### <a name="create-a-windows-server-ad-user"></a>Windows Server AD ユーザーを作成する
Active Directory 環境を作成したところで、テスト アカウントが必要になります。  このアカウントは、オンプレミスの AD 環境で作成されて Azure AD に同期されます。  次の操作を行います。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>まとめ
これで、既存のチュートリアルや、クラウド同期で提供される追加機能のテストに使用できる環境が用意されました。

## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)