---
title: チュートリアル:単一の AD フォレスト環境を Azure に統合する | Microsoft Docs
description: フェデレーションを使用してハイブリッド ID 環境を設定する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/16/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4410708905610562feb15804277021950eb1edeb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96176292"
---
# <a name="tutorial-federate-a-single-ad-forest-environment-to-the-cloud"></a>チュートリアル:単一の AD フォレスト環境をクラウドに統合する

![作成](media/tutorial-federation/diagram.png)

このチュートリアルでは、フェデレーションを使用してハイブリッド ID 環境を作成する手順を説明します。  この環境は、テストを行うためや、ハイブリッド ID のしくみを詳しく理解するために使用できます。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するために必要な前提条件を次に示します。
- [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) がインストールされたコンピューター。  これは、[Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) または [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) コンピューターに対して行うことをお勧めします。
- [Azure サブスクリプション](https://azure.microsoft.com/free)
- - 仮想マシンがインターネットで通信できるようにするための[外部ネットワーク アダプター](/virtualization/hyper-v-on-windows/quick-start/connect-to-network)。
- Windows Server 2016 のコピー
- 確認可能な[カスタム ドメイン](../../active-directory/fundamentals/add-custom-domain.md)

> [!NOTE]
> 最短時間でチュートリアル環境を作成できるよう、このチュートリアルでは PowerShell スクリプトを使用します。  各スクリプトでは、その先頭で宣言された変数が使用されます。  変数はお客様の環境に合わせて変更することができ、そうする必要があります。
>
>使用されるスクリプトでは、Azure AD Connect のインストール前に一般的な Active Directory 環境が作成されます。  これらはすべてのチュートリアルに関連しています。
>
> このチュートリアルで使用される PowerShell スクリプトのコピーは、[こちら](https://github.com/billmath/tutorial-phs)の GitHub で入手できます。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成
ハイブリッド ID 環境を稼働させるには、まず、オンプレミスの Active Directory サーバーとして使用される仮想マシンを作成する必要があります。  

>[!NOTE]
>ホスト コンピューター上で PowerShell のスクリプトを実行したことがない場合は、スクリプトを実行する前に、PowerShell で `Set-ExecutionPolicy remotesigned` を実行して "はい" を選択する必要があります。

次の操作を行います。

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
7. **[ライセンス条項に同意します]** をオンにし、 **[次へ]** をクリックします。
8. **[カスタム: Windows のみをインストールする (詳細設定)]** を選択します。
9. **[次へ]** をクリックします。
10. インストールが完了したら、仮想マシンを再起動してサインインし、Windows の更新プログラムを実行して VM を最新の状態にします。  最新の更新プログラムをインストールします。

## <a name="install-active-directory-pre-requisites"></a>Active Directory の前提条件をインストールする
仮想マシンを稼働させたところで、Active Directory のインストール前にいくつかの作業を行う必要があります。  言い換えると、仮想マシンの名前を変更し、静的 IP アドレスと DNS 情報を設定して、リモート サーバー管理ツールをインストールする必要があります。   次の操作を行います。

1. PowerShell ISE を管理者として起動します。
2. `Set-ExecutionPolicy remotesigned` を実行し、"すべて続行 (A)" を選択します。  Enter キーを押します。
3. 次のスクリプトを実行します。

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
$DomainNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = ConvertTo-SecureString "Passw0rd" -AsPlainText -Force

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Windows Server AD ユーザーを作成する
Active Directory 環境を作成したところで、テスト アカウントが必要になります。  このアカウントは、オンプレミスの AD 環境で作成されて Azure AD に同期されます。  次の操作を行います。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

```powershell 
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

## <a name="create-a-certificate-for-ad-fs"></a>AD FS の証明書を作成する
次に、AD FS によって使用される TLS/SSL 証明書を作成します。  これは自己署名証明書であり、テスト目的にのみ使用されます。  Microsoft は、運用環境で自己署名証明書を使用することをお勧めしません。 次の操作を行います。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

```powershell 
#Declare variables
$DNSname = "adfs.contoso.com"
$Location = "cert:\LocalMachine\My"

#Create certificate
New-SelfSignedCertificate -DnsName $DNSname -CertStoreLocation $Location
```

## <a name="create-an-azure-ad-tenant"></a>Azure AD テナントを作成する
次に、ユーザーをクラウドに同期できるよう、Azure AD テナントを作成する必要があります。  新しい Azure AD テナントを作成するには、以下を実行します。

1. [Azure portal](https://portal.azure.com) に移動し、Azure サブスクリプションがあるアカウントを使ってサインインします。
2. **プラス (+) アイコン** を選択し、**Azure Active Directory** を検索します。
3. 検索結果で **[Azure Active Directory]** を選択します。
4. **［作成］** を選択します</br>
![Azure AD テナントの作成方法を示すスクリーンショット。](media/tutorial-password-hash-sync/create1.png)</br>
5. **組織の名前** と **初期ドメイン名** を入力します。 **[作成]** を選択します。 これにより、ディレクトリが作成されます。
6. これが完了したら、**こちら** のリンクをクリックし、ディレクトリを管理します。

## <a name="create-a-global-administrator-in-azure-ad"></a>Azure AD でグローバル管理者を作成する
Azure AD テナントを作成したので、次は全体管理者アカウントを作成します。  このアカウントは、Azure AD Connect のインストール時に Azure AD コネクタ アカウントを作成するために使用されます。  Azure AD コネクタ アカウントは、Azure AD に情報を書き込むために使用されます。   全体管理者アカウントを作成するには、以下を実行します。

1.  **[管理]** にある **[ユーザー]** を選択します。</br>
![[管理] セクションの [ユーザー] オプションが選択されているスクリーンショット。ここで、Azure AD の全体管理者を作成します。](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  **[すべてのユーザー]** を選択し、 **+ [新しいユーザー]** を選択します。
3.  このユーザーの名前およびユーザー名を入力します。 これがテナントのグローバル管理者になります。 また、 **[ディレクトリ ロール]** を **[全体管理者]** に変更してください。 一時パスワードを表示することもできます。 完了したら、 **[作成]** を選択します。</br>
![スクリーンショットには、Azure AD の全体管理者を作成するときに選択する [作成] ボタンが表示されています。](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. これが完了したら、新しい Web ブラウザーを開き、新しい全体管理者アカウントと一時パスワードを使用して myapps.microsoft.com にサインインします。
5. 全体管理者のパスワードを覚えやすいものに変更します。

## <a name="add-the-custom-domain-name-to-your-directory"></a>カスタム ドメイン名をディレクトリに追加する
テナントと全体管理者を作成したところで、Azure で確認できるようカスタム ドメインを追加する必要があります。  次の操作を行います。

1. [Azure portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) に戻って、 **[すべてのユーザー]** ブレードを閉じてください。
2. 左側で **[カスタム ドメイン名]** を選択します。
3. **[カスタム ドメインの追加]** を選択します。</br>
![[カスタムドメインの追加] ボタンが強調表示されているスクリーンショット。](media/tutorial-federation/custom1.png)</br>
4. **[カスタム ドメイン名]** で、ボックスにカスタム ドメインの名前を入力し、 **[ドメインの追加]** をクリックします。
5. [カスタム ドメイン名] 画面では、TXT 情報または MX 情報が表示されます。  この情報は、ドメインのドメイン レジストラーの DNS 情報に追加する必要があります。  そのため、ドメイン レジストラーに移動して、ドメインの DNS 設定で TXT 情報または MX 情報を入力します。  これにより、Azure でドメインを確認できるようになります。  Azure による確認には最大で 24 時間かかる可能性があります。  詳細については、[カスタム ドメインの追加](../../active-directory/fundamentals/add-custom-domain.md)に関するドキュメントを参照してください。</br>
![TXT または MX 情報を追加する場所を示すスクリーンショット。](media/tutorial-federation/custom2.png)</br>
6. 確認が確実に行われるよう、[確認] ボタンをクリックします。</br>
![[確認] を選択した後の確認メッセージが表示されているスクリーンショット。](media/tutorial-federation/custom3.png)</br>

## <a name="download-and-install-azure-ad-connect"></a>Azure AD Connect をダウンロードしてインストールする
次に、Azure AD Connect をダウンロードしてインストールします。  インストールが完了したら、高速インストールを実行します。  次の操作を行います。

1. [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) をダウンロードします。
2. **AzureADConnect.msi** を検索し、ダブルクリックします。
3. [ようこそ] 画面で、ライセンス条項に同意するチェック ボックスをオンにし、 **[続行]** をクリックします。  
4. [簡単設定] 画面で、 **[カスタマイズ]** をクリックします。  
5. [必須コンポーネントのインストール] 画面で、 **[インストール]** をクリックします。  
6. [ユーザー サインイン] 画面で **[AD FS とのフェデレーション]** を選択し、 **[次へ]** をクリックします。
![[AD FS とのフェデレーション] を選択する場所を示すスクリーンショット。](media/tutorial-federation/fed1.png)

1. [Azure AD に接続] 画面で、上で作成した全体管理者のユーザー名とパスワードを入力し、 **[次へ]** をクリックします。
2. [ディレクトリの接続] 画面で、 **[ディレクトリの追加]** をクリックします。  次に **[新しい AD アカウントを作成]** を選択し、contoso\Administrator のユーザー名とパスワードを入力して **[OK]** をクリックします。
3. **[次へ]** をクリックします。
4. [Azure AD サインインの構成] 画面で、 **[一部の UPN サフィックスが確認済みドメインに一致していなくても続行する]** を選択し、 **[次へ]** をクリックします。
5. [ドメインと OU のフィルタリング] 画面で、 **[次へ]** をクリックします。
6. [一意のユーザー識別] 画面で、 **[次へ]** をクリックします。
7. [ユーザーおよびデバイスのフィルタリング] 画面で、 **[次へ]** をクリックします。
8. [オプション機能] 画面で、 **[次へ]** をクリックします。
9. [ドメイン管理者の資格情報] ページで、contoso\Administrator のユーザー名とパスワードを入力し、 **[次へ]** をクリックします。
10. AD FS ファーム画面で、 **[新しい AD FS ファームを構成する]** をオンにします。
11. **[フェデレーション サーバーにインストールされている証明書を使用します]** を選択し、 **[参照]** をクリックします。
12. 検索ボックスに「DC1」と入力し、見つかったら選択します。  **[OK]** をクリックします。
13. **[証明書ファイル]** ドロップ ダウンから、以前に作成した証明書の **adfs.contoso.com** を選択します。  **[次へ]** をクリックします。
![作成した証明書ファイルを選択する場所を示すスクリーンショット。](media/tutorial-federation/fed2.png)

1. AD FS サーバー画面で、 **[参照]** をクリックして検索ボックスに「DC1」と入力し、見つかったら選択します。  **[OK]** をクリックします。  **[次へ]** をクリックします。
![フェデレーション](media/tutorial-federation/fed3.png)

1. Web アプリケーションの [プロキシ サーバー] 画面で **[次へ]** をクリックします。
2. AD FS サービス アカウント画面で、contoso\Administrator のユーザー名とパスワードを入力し、 **[次へ]** をクリックします。
3. [Azure AD ドメイン] 画面で、ドロップ ダウンから検証済みのカスタム ドメインを選択し、 **[次へ]** をクリックします。
4. [構成の準備完了] 画面で、 **[インストール]** をクリックします。
5. インストールが完了したら、 **[終了]** をクリックします。
6. インストールの完了後、Sychronization Service Manager または同期規則エディターを使用する前に、サインアウトしてもう一度サインインします。


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>ユーザーが作成され、同期が実行されていることを確認する
オンプレミスのディレクトリに存在していたユーザーが同期され、現在 Azure AD テナントに存在することを確認します。  これが完了するまでに数時間かかる場合があることに注意してください。  ユーザーが同期されていることを確認するには、以下を実行します。


1. [Azure portal](https://portal.azure.com) に移動し、Azure サブスクリプションがあるアカウントを使ってサインインします。
2. 左側の **[Azure Active Directory]** を選択します。
3. **[管理]** にある **[ユーザー]** を選択します。
4. テナントの ![[同期]](media/tutorial-password-hash-sync/synch1.png) に新しいユーザーが表示されていることを確認します。

## <a name="test-signing-in-with-one-of-our-users"></a>いずれかのユーザーでサインインをテストする

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) に移動します。
2. 新しいテナントで作成されたユーザー アカウントを使用してサインインします。  user@domain.onmicrosoft.com の形式を使用してサインインする必要があります。 ユーザーがオンプレミスでのサインインに使用するのと同じパスワードを使用します。
   ![確認](media/tutorial-password-hash-sync/verify1.png)

これでハイブリッド ID 環境を正常に設定できました。この環境は、Azure で提供されるサービスをテストしたり理解したりするために使用できます。

## <a name="next-steps"></a>次の手順

- [ハードウェアおよび前提条件](how-to-connect-install-prerequisites.md) 
- [カスタマイズした設定](how-to-connect-install-custom.md)
- [Azure AD Connect とフェデレーション](how-to-connect-fed-whatis.md)
