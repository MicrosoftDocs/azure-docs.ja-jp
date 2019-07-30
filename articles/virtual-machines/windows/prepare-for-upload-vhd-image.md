---
title: Windows VHD の Azure へのアップロードの準備 | Microsoft Docs
description: Azure にアップロードする Windows VHD または VHDX の準備方法について説明します
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: cc942aeb34d17e8dff064c6a21a3c7b2099c742a
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151025"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Azure にアップロードする Windows VHD または VHDX を準備する

Windows 仮想マシン (VM) をオンプレミスから Azure にアップロードする前に、仮想ハード ディスク (VHD または VHDX) を準備する必要があります。 Azure では、VHD ファイル形式で容量固定ディスクの第 1 世代および第 2 世代 VM の両方がサポートされています。 VHD のサイズの上限は、1,023 GB です。 

第 1 世代 VM では、VHDX ファイル システムを VHD に変換できます。 また、容量可変ディスクを容量固定ディスクに変換することもできます。 ただし、VM の世代を変更することはできません。 詳細については、「[Hyper-V で第 1 世代と第 2 世代のどちらの VM を作成すべきか](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)」および「[Azure での第 2 世代 VM (プレビュー) のサポート](generation-2.md)」を参照してください。

Azure VM のサポート ポリシーについては、「[Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)」を参照してください。

> [!NOTE]
> この記事の手順は、Windows Server 2008 R2 以降 (64 ビット版) の Windows サーバー オペレーティング システムに適用されます。 Azure での 32 ビットのオペレーティング システムの実行については、「[Azure 仮想マシンでの 32 ビット オペレーティング システムのサポート](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)」を参照してください。

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>仮想ディスクを容量固定および VHD に変換する 
仮想ディスクを Azure に必要な形式に変換する必要がある場合は、このセクションのいずれかのメソッドを使用します。 仮想ディスクを変換する前に VM をバックアップします。 Windows VHD がローカル サーバーで正しく動作していることを確認します。 次に、Azure に変換またはアップロードする前に、VM 自体に発生しているすべてのエラーを解決します。

ディスクを変換した後は、そのディスクを使用する VM を作成します。 VM を起動してサインインし、アップロードの準備を完了します。

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Hyper-V マネージャーを使用してディスクを変換する 
1. Hyper-V マネージャーを開いて、左側のローカル コンピューターを選択します。 コンピューター リストの上にあるメニューで、 **[アクション]**  >  **[ディスクの編集]** の順に選択します。
2. **[仮想ハード ディスクの場所]** ページで、お使いの仮想ディスクを選択します。
3. **[アクションの選択]** ページで、 **[変換]**  >  **[次へ]** の順に選択します。
4. VHDX から変換する必要がある場合は、 **[VHD]**  >  **[次へ]** の順に選択します。
5. 容量可変ディスクから変換する必要がある場合は、 **[容量固定]**  >  **[次へ]** の順に選択します。
6. 新しい VHD ファイルの保存先となるパスを見つけて選択します。
7. **[完了]** を選択します。

> [!NOTE]
> この記事のコマンドを実行するには、管理者特権の PowerShell セッションを使用してください。

### <a name="use-powershell-to-convert-the-disk"></a>PowerShell を使用してディスクを変換する 
仮想ディスクは、Windows PowerShell で [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) コマンドを使用して変換できます。 PowerShell の起動時に、 **[管理者として実行]** を選択します。 

次のコマンド例は、ディスクを VHDX から VHD に変換します。 また、このコマンドは、ディスクを容量可変ディスクを容量固定ディスクに変換します。

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

このコマンドで、`-Path` の値を、変換する仮想ハード ディスクのパスに置き換えてください。 `-DestinationPath` の値を、変換したディスクの新しいパスおよび名前に置き換えます。

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK ディスク フォーマットからの変換
[VMDK ファイル形式](https://en.wikipedia.org/wiki/VMDK)の Windows VM イメージがある場合は、[Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) を使用して VHD 形式に変換できます。 詳細については、「[VMware VMDK から Hyper-V VHD への変換方法](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)」を参照してください。

## <a name="set-windows-configurations-for-azure"></a>Azure 用の Windows 構成を設定する

Azure にアップロードする予定の VM で、[管理者特権でのコマンド プロンプト ウィンドウ](https://technet.microsoft.com/library/cc947813.aspx)から、次のコマンドを実行します。

1. ルーティング テーブルの静的な固定ルートを削除します。
   
   * ルート テーブルを表示するには、コマンド プロンプト ウィンドウで `route print` を実行します。
   * `Persistence Routes` セクションを確認します。 固定ルートがある場合は、`route delete` コマンドを使って削除します。
2. WinHTTP プロキシを削除します。
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    VM で特定のプロキシを使用する必要がある場合は、Azure の IP アドレス ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) にプロキシ例外追加します。これにより VM は Azure に接続できます。
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. ディスク SAN ポリシーを [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx) に設定します。
   
    ```PowerShell
    diskpart 
    ```
    開いているコマンド プロンプト ウィンドウに、次のコマンドを入力します。

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Windows の協定世界時 (UTC) の時刻を設定します。 また、Windows Time サービス (`w32time`) の起動の種類を `Automatic` に設定します。
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. 電源プロファイルを高パフォーマンスに設定します。

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. 環境変数の `TEMP` と `TMP` が既定値に設定されていることを確認します。

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Windows サービスの確認
次の各 Windows サービスが Windows の既定値に設定されていることを確認します。 これらのサービスは、VM の接続を確保するために設定する必要がある最小のものになります。 スタートアップの設定をリセットするには、次のコマンドを実行します。
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>リモート デスクトップのレジストリ設定を更新する
リモート アクセスに関して次の設定が正しく構成されていることを確認します。

>[!NOTE] 
>`Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; -value &lt;value&gt;` を実行するときにエラー メッセージを受け取る場合があります。 このメッセージは無視してかまいません。 これが意味しているのは、ドメインが、グループ ポリシー オブジェクトを介してその構成をプッシュしていないことだけです。

1. リモート デスクトップ プロトコル (RDP) が有効になっていることを確認します。
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. RDP ポートが正しくセットアップされています。 既定のポートは 3389 です。
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    VM をデプロイすると、ポート 3389 に対する既定の規則が作成されます。 ポート番号を変更する場合は、VM が Azure にデプロイされた後で行ってください。

3. リスナーがすべてのネットワーク インターフェイスでリッスンしていることを確認します。
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. RDP 接続のネットワーク レベル認証 (NLA) モードを構成します。
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. キープアライブ値を設定します。
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. 再接続します。
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. コンカレント接続数を制限します。
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. RDP リスナーに関連付けられている自己署名証明書をすべて削除します。
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    このコードは、VM のデプロイ時に最初から接続できるようにします。 これを後で確認する必要がある場合は、VM が Azure にデプロイされた後に行うことができます。

9. VM がドメインの一部になる場合は、次のポリシーをチェックして、前の設定が元に戻されていないことを確認します。 
    
    | 目標                                     | ポリシー                                                                                                                                                       | 値                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP が有効になっている                           | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[コンポーネント]\[リモート デスクトップ サービス]\[リモート デスクトップ セッション ホスト]\[接続]         | ユーザーがリモート デスクトップを使用してリモートで接続できるようにする                                  |
    | NLA グループ ポリシー                         | [設定]\[管理用テンプレート]\[コンポーネント]\[リモート デスクトップ サービス]\[リモート デスクトップ セッション ホスト]\[セキュリティ]                                                    | NLA を使ってリモート アクセスにユーザー認証を要求する |
    | キープ アライブ設定                      | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[Windows コンポーネント]\[リモート デスクトップ サービス]\[リモート デスクトップ セッション ホスト]\[接続] | キープアライブ接続間隔を構成する                                                 |
    | 再接続設定                       | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[Windows コンポーネント]\[リモート デスクトップ サービス]\[リモート デスクトップ セッション ホスト]\[接続] | 自動的に再接続する                                                                   |
    | 限られた数の接続の設定 | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[Windows コンポーネント]\[リモート デスクトップ サービス]\[リモート デスクトップ セッション ホスト]\[接続] | 接続数を制限する                                                              |

## <a name="configure-windows-firewall-rules"></a>Windows ファイアウォール規則の構成
1. 3 つのプロファイル (ドメイン、標準、パブリック) で Windows ファイアウォールを有効にします。

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. PowerShell で次のコマンドを実行して、WinRM に 3 つのファイアウォール プロファイル (ドメイン、プライベート、パブリック) の通過を許可し、PowerShell リモート サービスを有効にします。
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. RDP トラフィックを許可するために以下のファイアウォール規則を有効にします。

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. VM が仮想ネットワーク内部の ping コマンドに応答できるように、"ファイルとプリンターの共有" 規則を有効にします。

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. VM がドメインの一部になる場合は、次の Azure AD ポリシーをチェックして、前の設定が元に戻されていないことを確認します。 

    | 目標                                 | ポリシー                                                                                                                                                  | 値                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Windows ファイアウォール プロファイルを有効にする | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[ネットワーク]\[ネットワーク接続]\[Windows ファイアウォール]\[ドメイン プロファイル]\[Windows ファイアウォール]   | すべてのネットワーク接続を保護する         |
    | RDP を有効にする                           | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[ネットワーク]\[ネットワーク接続]\[Windows ファイアウォール]\[ドメイン プロファイル]\[Windows ファイアウォール]   | 着信リモート デスクトップの例外を許可する |
    |                                      | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[ネットワーク]\[ネットワーク接続]\[Windows ファイアウォール]\[標準プロファイル]\[Windows ファイアウォール] | 着信リモート デスクトップの例外を許可する |
    | ICMP-V4 を有効にする                       | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[ネットワーク]\[ネットワーク接続]\[Windows ファイアウォール]\[ドメイン プロファイル]\[Windows ファイアウォール]   | ICMP の例外を許可する                   |
    |                                      | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[ネットワーク]\[ネットワーク接続]\[Windows ファイアウォール]\[標準プロファイル]\[Windows ファイアウォール] | ICMP の例外を許可する                   |

## <a name="verify-the-vm"></a>VM の確認 

VM が正常であり、セキュリティで保護されており、RDP アクセス可能であることを確認します。 

1. ディスクが正常で一貫性があることを確認するには、次回の VM 再起動時にディスクをチェックします。

    ```PowerShell
    Chkdsk /f
    ```
    クリーンで正常なディスクがレポートに示されていることを確認してください。

2. ブート構成データ (BCD) を設定します。 

    > [!NOTE]
    > 管理者特権の PowerShell ウィンドウを使用してこれらのコマンドを実行します。
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. Dump ログは、Windows のクラッシュの問題をトラブルシューティングするのに役立つ場合があります。 Dump ログ コレクションを有効にします。

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Windows Management Instrumentation (WMI) リポジトリに一貫性があることを確認します。

    ```PowerShell
    winmgmt /verifyrepository
    ```
    リポジトリが破損している場合は、「[WMI:リポジトリが破損しているかどうか](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)」を参照してください。

5. 他にポート 3389 を使用しているアプリケーションがないことを確認します。 このポートは、Azure の RDP サービスに使用します。 VM で使用されているポートを確認するには、`netstat -anob` を実行します。

    ```PowerShell
    netstat -anob
    ```

6. ドメイン コントローラーである Windows VHD をアップロードするには、次のようにします。

   * [この追加の手順](https://support.microsoft.com/kb/2904015)に従って、ディスクを準備します。

   * ある時点で Directory Services Restore Mode (DSRM) の VM を起動しなければならない場合に備えて、DSRM パスワードを把握していることを確認します。 詳細については、「[DSRM パスワードの設定](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)」を参照してください。

7. あらかじめ登録された Administrator アカウントとパスワードを把握していることを確認します。 現在のローカル Administrator パスワードをリセットし、このアカウントを使用して RDP 接続を介して Windows にサインイン可能であることを確認することもできます。 このアクセス許可は、"リモート デスクトップ サービスを使ったログオンを許可" グループ ポリシー オブジェクトによって制御されています。 このオブジェクトは、次の場所からローカル グループ ポリシー エディターで表示します。

    Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment

8. 次の Azure AD ポリシーで、RDP 経由とネットワークからの自分の RDP アクセスをブロックしていないことを確認します。

    - [コンピューターの構成]\[Windows の設定]\[セキュリティ設定]\[ローカル ポリシー]\[ユーザー権利の割り当て]\[ネットワーク経由のアクセスを拒否]

    - [コンピューターの構成]\[Windows の設定]\[セキュリティ設定]\[ローカル ポリシー]\[ユーザー権利の割り当て]\[リモート デスクトップ サービスを使ったログオンを拒否]


9. 次の Azure AD ポリシーで、必要なアクセス アカウントのいずれも削除していないことを確認します。

   - [コンピューターの構成]\[Windows の設定]\[セキュリティ設定]\[ローカル ポリシー]\[ユーザー権利の割り当て]\[ネットワーク経由でコンピューターへアクセス]

   ポリシーには次のグループが一覧表示されるはずです。

   - 管理者

   - Backup Operators

   - Everyone

   - ユーザー

10. VM を再起動して、Windows が引き続き正常であり、RDP 接続を介してアクセス可能であることを確認します。 この時点で、ローカル Hyper-V に VM を作成して、VM が完全に起動することを確認できます。 次に、RDP を介して VM に到達できることを確認するためのテストを実行します。

11. 余分な Transport Driver Interface (TDI) フィルターはすべて削除します。 たとえば、TCP パケットや追加のファイアウォールを分析するソフトウェアは削除します。 これを後で確認する必要がある場合は、VM が Azure にデプロイされた後に行うことができます。

12. 物理コンポーネントまたはその他の仮想化テクノロジに関連する、その他のサードパーティ ソフトウェアまたはドライバーをアンインストールします。

### <a name="install-windows-updates"></a>Windows 更新プログラムのインストール
理想的には、継続して*パッチ レベル*でマシンを更新する必要があります。 これを実行できない場合は、以下の更新プログラムがインストールされていることを確認してください。

| コンポーネント               | Binary         | Windows 7 SP1、Windows Server 2008 R2 SP1 | Windows 8、Windows Server 2012               | Windows 8.1、Windows Server 2012 R2 | Windows 10 v1607、Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709、Windows Server 2016 v1709 | Windows 10 v1803、Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| ネットワーク                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| コア                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| リモート デスクトップ サービス | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| セキュリティ                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### Sysprep を使用する状況の判断 <a id="step23"></a>    

システム準備ツール (Sysprep) は、Windows インストールをリセットするために使用できるプロセスです。 Sysprep は、個人データをすべて削除し、コンポーネントをいくつかリセットすることによって、"すぐに使用できる" エクスペリエンスを提供します。 

Sysprep は通常、特定の構成を持つ他の複数の VM のデプロイ元となるテンプレートを作成するために実行します。 このテンプレートは、*一般化されたイメージ*と呼ばれます。

1 つのディスクから 1 つの VM のみを作成する場合は、Sysprep を使用する必要はありません。 代わりに、*特殊化されたイメージ*から VM を作成することができます。 特殊化されたディスクから VM を作成する方法については、以下を参照してください。

- [特殊化されたディスクからの VM の作成](create-vm-specialized.md)
- [特殊化された VHD ディスクからの VM の作成](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

一般化されたイメージを作成する場合は、Sysprep を実行する必要があります。 詳細については、「[Sysprep の使用方法: 紹介](https://technet.microsoft.com/library/bb457073.aspx)」を参照してください。 

Windows ベースのコンピューターにインストールされているロールまたはアプリケーションには、一般化されたイメージをサポートしていないものもあります。 したがって、この手順を実行する前に、Sysprep でお使いのコンピューターのロールがサポートされていることを確認してください。 詳しくは、「[Sysprep Support for Server Roles (サーバー ロールの sysprep サポート)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)」を参照してください。

### <a name="generalize-a-vhd"></a>VHD の一般化

>[!NOTE]
> 次の手順で `sysprep.exe` を実行した後、VM をオフにします。 Azure でそれからイメージを作成するまでは再びオンにしないでください。

1. Windows VM にサインインします。
1. 管理者として**コマンド プロンプト**を実行します。 
1. ディレクトリを `%windir%\system32\sysprep` に変更します 次に、`sysprep.exe` を実行します。
1. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、 **[一般化する]** チェック ボックスがオンになっていることを確認します。

    ![システム準備ツール](media/prepare-for-upload-vhd-image/syspre.png)
1. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
1. **[OK]** を選択します。
1. Sysprep が完了したら、VM をシャットダウンします。 VM をシャットダウンするために **[再起動]** を使用しないでください。

これで VHD をアップロードする準備ができました。 一般化されたディスクから VM を作成する方法の詳細については、[一般化された VHD のアップロードと Azure での新しい VM の作成](sa-upload-generalized.md)に関するページをご覧ください。


>[!NOTE]
> カスタム *unattend.xml* ファイルはサポートされていません。 `additionalUnattendContent` プロパティはサポートされていますが、提供されるのは、Azure プロビジョニング エージェントで使用される *unattend.xml* ファイルに [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) オプションを追加するための限定サポートのみになります。 たとえば、FirstLogonCommands と LogonCommands を追加するには、[additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) を使用できます。 詳細については、「[additionalUnattendContent FirstLogonCommands の例](https://github.com/Azure/azure-quickstart-templates/issues/1407)」をご覧ください。


## <a name="complete-the-recommended-configurations"></a>推奨される構成を完了する
次の設定は、VHD のアップロードに影響しません。 ただし、これらを構成しておくことを強くお勧めします。

* [Azure 仮想マシン エージェント](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をインストールします。 その後で、VM 拡張機能を有効にできます。 VM 拡張機能によって、VM で使用する重要な機能のほとんどが実装されます。 たとえば、パスワードをリセットしたり、RDP を構成したりするには、拡張機能が必要です。 詳細については、「[Azure 仮想マシン エージェントの概要](../extensions/agent-windows.md)」を参照してください。
* Azure で VM を作成した後は、パフォーマンスを向上させるために、ページ ファイルを "*テンポラル ドライブ ボリューム*" 上に置くことをお勧めします。 次のようにファイルの配置をセットアップできます。

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  データ ディスクが VM に接続されている場合、テンポラル ドライブ ボリュームの文字は通常 *D* になります。この指定は、設定や使用可能なドライブ数に応じて異なる場合があります。

## <a name="next-steps"></a>次の手順
* [Resource Manager デプロイメント向けに Windows VM イメージを Azure にアップロードする](upload-generalized-managed.md)
* [Azure Windows VM のライセンス認証に関する問題のトラブルシューティング](troubleshoot-activation-problems.md)

