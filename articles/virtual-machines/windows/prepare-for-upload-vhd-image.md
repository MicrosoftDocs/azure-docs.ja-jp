---
title: "Windows VHD の Azure へのアップロードの準備 | Microsoft Docs"
description: "Azure にアップロードする前に Windows VHD または VHDX を準備する方法"
services: virtual-machines-windows
documentationcenter: 
author: glimoli
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: aa1cec2ef11da6aa8a8c4089be36994ab5f61682
ms.contentlocale: ja-jp
ms.lasthandoff: 08/12/2017

---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Azure にアップロードする Windows VHD または VHDX を準備する
Windows 仮想マシン (VM) をオンプレミスから Microsoft Azure にアップロードする前に、仮想ハード ディスク (VHD または VHDX) を準備する必要があります。 Azure では、VHD ファイル形式で容量固定ディスクの第 1 世代の VM のみがサポートされています。 VHD のサイズの上限は、1,023 GB です。 第 1 世代の VM は、VHDX ファイル システムから VHD ファイル システムに、また容量可変ディスクから容量固定ディスクに変換できます。 ただし、VM の世代を変更することはできません。 詳細については、[Hyper-V で第 1 世代と第 2 世代のどちらの VM を作成する必要があるか](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)に関するページを参照してください。

Azure VM のサポート ポリシーの詳細については、[Microsoft Azure VM のマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)に関するページを参照してください。

> [!Note]
> この記事の手順は、Windows Server 2008 R2 以降 （64 ビット版） の Windows サーバー オペレーティング システムに適用されます。 Azure での 32 ビット版オペレーティング システムの実行については、「[Support for 32-bit operating systems in Azure virtual machines (Azure 仮想マシンでの 32 ビット版オペレーティング システムのサポート)](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)」を参照してください。

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>仮想ディスクを VHD および容量固定ディスクに変換する 
仮想ディスクを Azure に必要な形式に変換する必要がある場合は、このセクションのいずれかのメソッドを使用します。 仮想ディスクの変換プロセスを実行する前に仮想マシンをバックアップして、Windows VHD がローカル サーバー上で正しく動作するかどうかを確認します。 Azure に変換またはアップロードする前に、VM 自体に発生しているすべてのエラーを解決します。

ディスクを変換した後は、変換したディスクを使用する VM を作成します。 VM を起動してサインインし、VM アップロードの準備を完了します。

### <a name="convert-disk-using-hyper-v-manager"></a>Hyper-V マネージャーを使用したディスクの変換
1. Hyper-V マネージャーを開いて、左側のローカル コンピューターを選択します。 コンピューター リストの上にあるメニューで、**[アクション]**、 > **[ディスクの編集]** の順にクリックします。
2. **[仮想ハード ディスクの場所]** 画面で、お使いの仮想ディスクを見つけて選択します。
3. **[操作の選択]** 画面で、**[変換]** を選択し、**[次へ]** をクリックします。
4. VHDX から変換する必要がある場合は、**[VHD]** を選択し、**[次へ]** をクリックします。
5. 容量可変ディスクから変換する必要がある場合は、**[容量固定]** を選択し、**[次へ]** をクリックします。
6. 新しい VHD ファイルの保存先となるパスを見つけて選択します。
7. **[完了]**をクリックします。

>[!NOTE]
>この記事のコマンドは、管理者特権の PowerShell セッションで実行する必要があります。

### <a name="convert-disk-by-using-powershell"></a>PowerShell を使用したディスクの変換
仮想ディスクは、Windows PowerShell で [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) コマンドを使用して変換できます。 PowerShell の起動時に、**[管理者として実行]** を選択します。 

次の例では、VHDX から VHD に、および容量可変ディスクから容量固定ディスクに変換するコマンドを示します。

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
このコマンドでは、"-Path" の値を、変換する仮想ハードディスクへのパスで置き換え、"-DestinationPath" の値を、新しいパスと変換されたディスクの名前で置き換えます。

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK ディスク フォーマットからの変換
[VMDK ファイル形式](https://en.wikipedia.org/wiki/VMDK)の Windows VM イメージがある場合は、[Microsoft VM Converter](https://www.microsoft.com/download/details.aspx?id=42497) を使用して VHD に変換できます。 詳細については、ブログ記事「[How to Convert a VMware VMDK to Hyper-V VHD (VMware VMDK から Hyper-V VHD への変換方法)](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)」を参照してください。

## <a name="set-windows-configurations-for-azure"></a>Azure 用の Windows 構成を設定する

Azure にアップロードする予定の VM で、[管理者特権でのコマンド プロンプト ウィンドウ](https://technet.microsoft.com/library/cc947813.aspx)から、以下の手順にあるすべてのコマンドを実行します。

1. ルーティング テーブルの静的な固定ルートを削除します。
   
   * ルート テーブルを表示するには、コマンド プロンプト ウィンドウで `route print` を実行します。
   * **[Persistence Routes (固定ルート)]** セクションを確認します。 固定ルートがある場合は、 [ルートの削除](https://technet.microsoft.com/library/cc739598.apx) を使ってルートを削除します。
2. WinHTTP プロキシを削除します。
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. ディスク SAN ポリシーを [Onlineall](https://technet.microsoft.com/library/gg252636.aspx) に設定します。 
   
    ```PowerShell
    diskpart 
    ```
    開いているコマンド プロンプト ウィンドウに、次のコマンドを入力します。

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Windows を世界協定時刻 (UTC) の時間に設定して、Windows タイム (w32time) サービスのスタートアップの種類を**自動**に設定します。
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" 1 -Type DWord

    Set-Service -Name w32time -StartupType Auto
    ```
5. 電源プロファイルを **[高パフォーマンス]** に設定します。

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```

## <a name="check-the-windows-services"></a>Windows サービスの確認
次の各 Windows サービスが **Windows の既定値**に設定されていることを確認します。 これらは、VM を確実に接続可能にするためにセットアップする必要がある最小数のサービスです。 スタートアップの設定をリセットするには、次のコマンドを実行します。
   
```PowerShell
Set-Service -Name bfe -StartupType Auto
Set-Service -Name dhcp -StartupType Auto
Set-Service -Name dnscache -StartupType Auto
Set-Service -Name IKEEXT -StartupType Auto
Set-Service -Name iphlpsvc -StartupType Auto
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Auto
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Auto
Set-Service -Name RemoteRegistry -StartupType Auto
```

## <a name="update-remote-desktop-registry-settings"></a>リモート デスクトップのレジストリ設定を更新する
リモート デスクトップ接続に関して以下の設定が正しく構成されていることを確認します。

>[!Note] 
>以下の手順で **Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;オブジェクト名&gt; &lt;値&gt;** を実行すると、エラー メッセージを受け取ることがあります。 このエラー メッセージは安全に無視することができます。 このメッセージは、ドメインが、グループ ポリシー オブジェクトを介してその構成をプッシュしていないことのみを意味しています。
>
>

1. リモート デスクトップ プロトコル (RDP) が有効になっていることを確認します。
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord
    ```
   
2. RDP ポートが正しくセットアップされている (既定のポート 3389) ことを確認します。
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" 3389 -Type DWord
    ```
    VM をデプロイすると、ポート 3389 に対する既定の規則が作成されます。 ポート番号を変更する場合は、VM が Azure にデプロイされた後で行ってください。

3. リスナーがすべてのネットワーク インターフェイスでリッスンしていることを確認します。
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" 0 -Type DWord
   ```
4. RDP 接続のネットワーク レベル認証モードを構成します。
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" 1 -Type DWord
     ```

5. キープアライブ値を設定します。
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" 1 -Type DWord
    ```
6. 再接続します。
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" 0 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" 0 -Type DWord
    ```
7. 同時接続数を制限します。
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" 4294967295 -Type DWord
    ```
8. RDP リスナーに関連付けられている自己署名証明書がある場合は削除します。
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash"
    ```
    これは、VM のデプロイ時に最初から接続できるようにするためです。 必要に応じて、Azure に VM をデプロイした後に、後続のステージでこれをレビューすることもできます。

9. VM がドメインの一部になる場合は、以下の設定をすべてチェックし、前の設定が元に戻されていないことを確認します。 確認が必要なポリシーは以下のとおりです。
    
    - RDP が有効になっている：

         Computer Configuration\Policies\Windows Settings\Administrative Templates\ Components\Remote Desktop Services\Remote Desktop Session Host\Connections:
         
         **ユーザーがリモート デスクトップを使用してリモートで接続できるようにする**

    - NLA グループ ポリシー:

        Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Security: 
        
        **ネットワーク レベル認証を使用してリモート接続用のユーザー認証を要求する**
    
    - キープアライブ設定:

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections: 
        
        **キープアライブ接続間隔を構成する**

    - 再接続設定:

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections: 
        
        **自動再接続**

    - 接続数制限の設定:

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections: 
        
        **接続数を制限する**

## <a name="configure-windows-firewall-rules"></a>Windows ファイアウォール規則の構成
1. 3 つのプロファイル (ドメイン、標準、パブリック) で Windows ファイアウォールを有効にします。

   ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 1 -Type DWord
   ```

2. PowerShell で次のコマンドを実行して、WinRM に 3 つのファイアウォール プロファイル (ドメイン、プライベート、パブリック) の通過を許可し、PowerShell リモート サービスを有効にします。
   
   ```PowerShell
    Enable-PSRemoting -force
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
3. RDP トラフィックを許可するために以下のファイアウォール規則を有効にします。 

   ```PowerShell
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   ```   
4. VM がVirtual Network 内部の ping コマンドに応答できるように、"ファイルとプリンターの共有" 規則を有効にします。

   ```PowerShell
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   ``` 
5. VM がドメインの一部になる場合は、以下の設定をチェックし、前の設定が元に戻されていないことを確認します。 確認が必要な AD ポリシーは以下のとおりです。

    - Windows ファイアウォール プロファイルを有効にする

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall: **すべてのネットワーク接続の保護**

       Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall: **すべてのネットワーク接続の保護**

    - RDP を有効にする 

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall: **着信リモート デスクトップの例外を許可する**

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall: **着信リモート デスクトップの例外を許可する**

    - ICMP-V4 を有効にする

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall: **ICMP の例外を許可する**

        Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall: **ICMP の例外を許可する**

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>VM が正常で安全であり、RDP でアクセスできることを確認する 
1. ディスクが正常で一貫性があることを確認するには、次回の VM 再起動時にチェック ディスク操作を実行します。

    ```PowerShell
    Chkdsk /f
    ```
    クリーンで正常なディスクがレポートに示されていることを確認してください。

2. ブート構成データ (BCD) を設定します。 

    > [!Note]
    > PowerShell **ではなく**、必ず管理者特権のコマンド ウィンドウでこれらのコマンドを実行してください。
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Windows Management Instrumentation リポジトリに一貫性があることを確認します。 これを行うには、次のコマンドを実行します。

    ```PowerShell
    winmgmt /verifyrepository
    ```
    リポジトリが破損している場合は、「[WMI: Repository Corruption, or Not? (WMI: リポジトリの破損かどうかの確認)](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)」を参照してください。

4. 他のアプリケーションでポート 3389 を使用していないことを確認します。 このポートは、Azure の RDP サービスに使用します。 **netstat -anob** を実行して、VM でどのポートが使用されているかを確認できます。

    ```PowerShell
    netstat -anob
    ```

5. アップロードする Windows VHD がドメイン コントローラーである場合は、以下の手順に従ってください。

    A. [この追加の手順](https://support.microsoft.com/kb/2904015)に従って、ディスクを準備します。

    B. ある時点で DSRM 内の VM を起動する必要がある場合は、DSRM パスワードがわかっていることを確認します。 このリンクを参照して、[DSRM パスワード](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)を設定することもできます。

6. あらかじめ登録された Administrator アカウントとパスワードがわかっていることを確認します。 現在のローカル Administrator パスワードをリセットし、このアカウントを使用して RDP 接続を介して Windows にサインイン可能であることを確認することもできます。 このアクセス許可は、"リモート デスクトップ サービスを使ったログオンを許可" グループ ポリシー オブジェクトによって制御されています。 このオブジェクトは、次のリンクにあるローカル グループ ポリシー エディターで確認することができます。

    Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment

7. 次の AD ポリシーで、RDP 経由とネットワークからの自分の RDP アクセスをブロックしていないことを確認します。

    - [コンピューターの構成]\[Windows の設定]\[セキュリティ設定]\[ローカル ポリシー]\[ユーザー権利の割り当て]\[ネットワーク経由のアクセスを拒否]

    - [コンピューターの構成]\[Windows の設定]\[セキュリティ設定]\[ローカル ポリシー]\[ユーザー権利の割り当て]\[リモート デスクトップ サービスを使ったログオンを拒否]


8. VM を再起動して、Windows が引き続き正常であり、RDP 接続を使用してアクセス可能であることを確認します。 この時点で、ローカル Hyper-V に VM を作成して、VM が完全に開始されていることを確認し、この VM が RDP でアクセス可能であるかどうかをテストできます。

9. TCP パケットまたは追加のファイアウォールを分析するソフトウェアなど、余分な Transport Driver Interface フィルターを削除します。 必要に応じて、Azure に VM をデプロイした後に、後続のステージでこれをレビューすることもできます。

10. 物理コンポーネントまたはその他の仮想化テクノロジに関連する、その他のサードパーティ ソフトウェアとドライバーをアンインストールします。

### <a name="install-windows-updates"></a>Windows 更新プログラムのインストール
理想的な構成は、**マシンのパッチ レベルが最新である**構成です。 これが不可能である場合は、以下の構成プログラムがインストールされていることを確認してください。

|                       |                   |           |                                       最小限のファイル バージョン x64       |                                      |                                      |                            |
|-------------------------|-------------------|------------------------------------|---------------------------------------------|--------------------------------------|--------------------------------------|----------------------------|
| コンポーネント               | Binary            | Windows 7 および Windows Server 2008 R2 | Windows 8 および Windows Server 2012             | Windows 8.1 および Windows Server 2012 R2 | Windows 10 および Windows Server 2016 RS1 | Windows 10 RS2             |
| Storage                 | disk.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061           | -                                    | -                          |
|                         | storport.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.332             |
|                         | ntfs.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.447             |
|                         | Iologmsg.dll      | 6.1.7601.23403 - KB3125574         | 6.2.9200.16384 - KB2995387                  | -                                    | -                                    | -                          |
|                         | Classpnp.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614           | 10.0.14393.953 - KB4022715           | -                          |
|                         | Volsnap.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384           | -                                    | 10.0.15063.0               |
|                         | partmgr.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | volmgr.sys        |                                    |                                             |                                      |                                      | 10.0.15063.0               |
|                         | Volmgrx.sys       | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | 10.0.15063.0               |
|                         | Msiscsi.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726           | 10.0.14393.1066 - KB4022715          | 10.0.15063.447             |
|                         | Msdsm.sys         | 6.1.7601.23403 - KB3125574         | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726           | -                                    | -                          |
|                         | Mpio.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726           | 10.0.14393.1198 - KB4022715          | -                          |
|                         | Fveapi.dll        | 6.1.7601.23311 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614           | 10.0.14393.576 - KB4022715           | -                          |
|                         | Fveapibase.dll    | 6.1.7601.23403 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614           | 10.0.14393.206 - KB4022715           | -                          |
| ネットワーク                 | netvsc.sys        | -                                  | -                                           | -                                    | 10.0.14393.1198 - KB4022715          | 10.0.15063.250 - KB4020001 |
|                         | mrxsmb10.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726           | 10.0.14393.479 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb20.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb.sys        | 6.1.7601.23816 - KB4022722         | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | tcpip.sys         | 6.1.7601.23761 - KB4022722         | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.447             |
|                         | http.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726           | 10.0.14393.251 - KB4022715           | 10.0.15063.483             |
|                         | vmswitch.sys      | 6.1.7601.23727 - KB4022719         | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.138             |
| コア                    | ntoskrnl.exe      | 6.1.7601.23807 - KB4022719         | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.483             |
| リモート デスクトップ サービス | rdpcorets.dll     | 6.2.9200.21506 - KB4022719         | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.0               |
|                         | termsrv.dll       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850           | 10.0.14393.0 - KB4022715             | 10.0.15063.0               |
|                         | termdd.sys        | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | win32k.sys        | 6.1.7601.23807 - KB4022719         | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726           | 10.0.14393.594 - KB4022715           | -                          |
|                         | rdpdd.dll         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | rdpwd.sys         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
| セキュリティ                | WannaCrypt に起因 | KB4012212                          | KB4012213                                   | KB4012213                            | KB4012606                            | KB4012606                  |
|                         |                   |                                    | KB4012216                                   |                                      | KB4013198                            | KB4013198                  |
|                         |                   | KB4012215                          | KB4012214                                   | KB4012216                            | KB4013429                            | KB4013429                  |
|                         |                   |                                    | KB4012217                                   |                                      | KB4013429                            | KB4013429                  |
       
### sysprep をいつ使用するか <a id="step23"></a>    

sysrep は、Windows インストールに組み込むことができるプロセスです。個人データをすべて削除し、いくつかのコンポーネントをリセットすることによって、システムのインストールをリセットし、"out of the box experience" を提供します。 このプロセスは通常、特定の構成を持つ複数の他の VM のデプロイ元となるテンプレートを作成する場合に行います。 このテンプレートは**一般化されたイメージ**と呼ばれます。

そうではなく、1 つのディスクから 1 つの VM のみを作成する場合、sysprep を使用する必要はありません。 この状況では、**特殊化イメージ**として知られるイメージから VM を作成するだけです。

特殊化されたディスクから VM を作成する方法の詳細については、以下を参照してください。

- [特殊化されたディスクからの VM の作成](create-vm-specialized.md)
- [特殊化された VHD ディスクからの VM の作成](https://azure.microsoft.com/resources/templates/201-vm-specialized-vhd/)

一般化されたイメージを作成する場合は、sysprep を実行する必要があります。 Sysprep の詳細については、「[Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。 

Windows ベースのコンピューターにインストールされているロールまたはアプリケーションには、この一般化をサポートしていないものもあります。 そのため、この手順を実行する前に、以下の記事を参照して、そのコンピューターのロールが sysprep でサポートされていることを確認してください。 詳しくは、「[Sysprep Support for Server Roles (サーバー ロールの sysprep サポート)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)」を参照してください。

### <a name="steps-to-generalize-a-vhd"></a>VHD を一般化する手順

>[!NOTE]
> 以下の手順で示されているように sysprep.exe を実行した後、VM をオフにしたら、Azure でその VM からイメージを作成するまで、その VM を再度オンにしないでください。

1. Windows VM にサインインします。
2. 管理者として**コマンド プロンプト**を実行します。 
3. ディレクトリを **%windir%\system32\sysprep** に変更し、**sysprep.exe** を実行します。
3. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。

    ![システム準備ツール](media/prepare-for-upload-vhd-image/syspre.png)
4. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
5. **[OK]**をクリックします。
6. sysprep が完了したら、VM をシャットダウンします。 VM をシャットダウンするために **[再起動]** を使用しないでください。
7. これで VHD をアップロードする準備ができました。 一般化されたディスクから VM を作成する方法の詳細については、[一般化した VHD のアップロードと Azure での新しい VM の作成](sa-upload-generalized.md)に関するページをご覧ください。


## <a name="complete-recommended-configurations"></a>推奨される構成を完了する
次の設定は、VHD のアップロードに影響しません。 ただし、これらを構成しておくことを強くお勧めします。

* [Azure VM エージェント](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をインストールします。 その後で、VM 拡張機能を有効にできます。 VM 拡張機能によって、パスワードのリセットや RDP の構成など、VM で使用する重要な機能のほとんどが実装されます。 詳細については、次を参照してください。

    - [VM エージェントおよび拡張機能 – パート 1](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/)
    - [VM エージェントおよび拡張機能 – パート 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)
* Dump ログは、Windows のクラッシュの問題をトラブルシューティングするのに役立つ場合があります。 Dump ログ コレクションを有効にします。
  
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "AutoReboot" -Value 0 -Type DWord
    New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
    この記事にある手順のいずれかでエラーが発生した場合、これはレジストリ キーがすでに存在することを意味します。 そのような場合は、代わりに以下のコマンドを使用してください。

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
*  Azure で VM を作成した後は、パフォーマンスを向上させるために、ページ ファイルを "テンポラル ドライブ" ボリューム上に置くことをお勧めします。 これは以下のようにしてセットアップできます。

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile"
    ```
VM に接続されているデータ ディスクがある場合、テンポラル ドライブ ボリュームのドライブ文字は通常 "D" になります。 この文字は、使用可能なドライブ数や行った設定に応じて異なる場合があります。

## <a name="next-steps"></a>次のステップ
* [Resource Manager デプロイメント向けに Windows VM イメージを Azure にアップロードする](upload-generalized-managed.md)


