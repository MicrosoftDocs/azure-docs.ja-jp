---
title: Windows VHD の Azure へのアップロードの準備
description: Azure にアップロードする Windows VHD または VHDX の準備方法について説明します
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: genli
ms.openlocfilehash: 12ef839cbbbc69230b314bf7c56a63f57a0d6b20
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556266"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Azure にアップロードする Windows VHD または VHDX を準備する

Windows 仮想マシン (VM) をオンプレミスから Azure にアップロードする前に、仮想ハード ディスク (VHD または VHDX) を準備する必要があります。 Azure では、VHD ファイル形式で容量固定ディスクの第 1 世代および第 2 世代 VM の両方がサポートされています。 第 1 世代 VM で OS VHD に許容される最大サイズは 2 TB です。

VHDX ファイルを VHD に変換し、容量可変ディスクを容量固定ディスクに変換することはできますが、VM の世代を変更することはできません。 詳細については、「[Hyper-V で第 1 世代と第 2 世代のどちらの VM を作成すべきか](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V)」および「[Azure での第 2 世代 VM のサポート](../generation-2.md)」を参照してください。

Azure VM のサポート ポリシーについては、「[Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/)」を参照してください。

> [!NOTE]
> この記事の手順は以下に適用されます。
>
> - Windows Server (64 ビット版) 2008 R2 以降の Windows Server オペレーティング システム。 Azure での 32 ビットのオペレーティング システムの実行については、「[Azure 仮想マシンでの 32 ビット オペレーティング システムのサポート](https://support.microsoft.com/help/4021388/)」を参照してください。
> - Azure Site Recovery や Azure Migrate など、何らかのディザスター リカバリー ツールをワークロードの移行に使用する場合でも、移行前にゲスト OS でこのプロセスを実行してイメージを準備する必要があります。

## <a name="system-file-checker"></a>システム ファイル チェッカー

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>OS イメージを一般化する前に Windows システム ファイル チェッカー ユーティリティを実行する

システム ファイル チェッカー (SFC) は、Windows システム ファイルの確認と置換に使用します。

> [!IMPORTANT]
> この記事の例を実行するには、管理者特権の PowerShell セッションを使用してください。

次の SFC コマンドを実行します。

```powershell
sfc.exe /scannow
```

```Output
Beginning system scan.  This process will take some time.

Beginning verification phase of system scan.
Verification 100% complete.

Windows Resource Protection did not find any integrity violations.
```

SFC スキャンが完了したら、Windows 更新プログラムをインストールし、コンピューターを再起動します。

## <a name="set-windows-configurations-for-azure"></a>Azure 用の Windows 構成を設定する

> [!NOTE]
> 一般化されたイメージから Windows VM が作成されると、Azure プラットフォームは、ISO ファイルを DVD-ROM にマウントします。 このため、一般化されたイメージの OS で DVD-ROM が有効になっている必要があります。 無効になっている場合、Windows VM は OOBE (Out-of-Box Experience) でスタックします。

1. ルーティング テーブル内の静的な固定ルートをすべて削除します。

   - ルーティング テーブルを表示するには、`route.exe print` を実行します。
   - **[Persistence Routes]\(固定ルート\)** セクションを確認します。 固定ルートがある場合は、`route.exe delete` コマンドを使って削除します。

1. WinHTTP プロキシを削除します。

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    VM で特定のプロキシを使用する必要がある場合は、次のように Azure の IP アドレス ([168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)) にプロキシ例外を追加します。これにより VM は Azure に接続できます。

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. 次のように DiskPart を開きます。

   ```powershell
   diskpart.exe
   ```

   ディスク SAN ポリシーを [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11)) に設定します。

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. Windows の協定世界時 (UTC) の時刻を設定します。 また、Windows Time サービス (**w32time**) の起動の種類を **Automatic** に設定します。

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. 電源プロファイルを高パフォーマンスに設定します。

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. 環境変数の **TEMP** と **TMP** が既定値に設定されていることを確認します。

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```

## <a name="check-the-windows-services"></a>Windows サービスの確認

次の各 Windows サービスが Windows の既定値に設定されていることを確認します。 これらのサービスは、VM の接続を確保するために構成する必要がある最小のものになります。 スタートアップの設定をリセットするには、次の例を実行します。

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>リモート デスクトップのレジストリ設定を更新する

リモート アクセスに関して次の設定が正しく構成されていることを確認します。

> [!NOTE]
> `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>` の実行時にエラー メッセージが表示された場合は、無視してかまいません。 これは、ドメインがグループ ポリシー オブジェクトを介してその構成を設定していないことを意味します。

1. リモート デスクトップ プロトコル (RDP) が有効になっていることを確認します。

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. 次のように、RDP ポートが既定のポート 3389 を使用して正しくセットアップされます。

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   VM をデプロイすると、ポート 3389 に対する既定の規則が作成されます。 ポート番号を変更するには、VM が Azure にデプロイされた後で行ってください。

1. 次のように、リスナーがすべてのネットワーク インターフェイスでリッスンしています。

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. 次のように、RDP 接続のネットワーク レベル認証 (NLA) モードを構成します。

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. キープアライブ値を設定します。

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. 次のように、再接続オプションを設定します。

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. コンカレント接続数を制限します。

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. RDP リスナーに関連付けられている自己署名証明書をすべて削除します。

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   このコードにより、VM をデプロイすると接続できるようになります。 これらの設定は、VM が Azure にデプロイされた後にも確認できます。

1. VM がドメインの一部である場合は、次のポリシーをチェックして、前の設定が元に戻されていないことを確認します。

    |                 目標                  |                                                                            ポリシー                                                                            |                           値                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | RDP が有効になっている                        | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[コンポーネント]\[リモート デスクトップ サービス]\[リモート デスクトップ セッション ホスト]\[接続]         | ユーザーがリモート デスクトップを使用してリモートで接続できるようにする    |
    | NLA グループ ポリシー                      | [設定]\[管理用テンプレート]\[コンポーネント]\[リモート デスクトップ サービス]\[リモート デスクトップ セッション ホスト]\[セキュリティ]                                                    | NLA を使ってリモート アクセスにユーザー認証を要求する |
    | キープ アライブ設定                   | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[Windows コンポーネント]\[リモート デスクトップ サービス]\[リモート デスクトップ セッション ホスト]\[接続] | キープアライブ接続間隔を構成する                   |
    | 再接続設定                    | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[Windows コンポーネント]\[リモート デスクトップ サービス]\[リモート デスクトップ セッション ホスト]\[接続] | 自動的に再接続する                                    |
    | 限られた数の接続の設定 | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[Windows コンポーネント]\[リモート デスクトップ サービス]\[リモート デスクトップ セッション ホスト]\[接続] | 接続数を制限する                                |

## <a name="configure-windows-firewall-rules"></a>Windows ファイアウォール規則の構成

1. 3 つのプロファイル (ドメイン、標準、パブリック) で Windows ファイアウォールを有効にします。

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. 次の例を実行して、3 つのファイアウォール プロファイル (ドメイン、プライベート、パブリック) を通じて WinRM を許可し、PowerShell リモート サービスを有効にします。

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -DisplayName 'Windows Remote Management (HTTP-In)' -Enabled True
   ```

1. RDP トラフィックを許可するために以下のファイアウォール規則を有効にします。

   ```powershell
   Set-NetFirewallRule -DisplayGroup 'Remote Desktop' -Enabled True
   ```

1. VM が仮想ネットワーク内部の ping 要求に応答できるように、"ファイルとプリンターの共有" 規則を有効にします。

   ```powershell
   Set-NetFirewallRule -DisplayName 'File and Printer Sharing (Echo Request - ICMPv4-In)' -Enabled True
   ```

1. Azure プラットフォーム ネットワークのルールを作成します。

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. VM がドメインの一部である場合は、次の Azure AD ポリシーをチェックして、前の設定が元に戻されていないことを確認します。

    |                 目標                 |                                                                         ポリシー                                                                          |                  値                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | Windows ファイアウォール プロファイルを有効にする | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[ネットワーク]\[ネットワーク接続]\[Windows ファイアウォール]\[ドメイン プロファイル]\[Windows ファイアウォール]   | すべてのネットワーク接続を保護する         |
    | RDP を有効にする                           | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[ネットワーク]\[ネットワーク接続]\[Windows ファイアウォール]\[ドメイン プロファイル]\[Windows ファイアウォール]   | 着信リモート デスクトップの例外を許可する |
    |                                      | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[ネットワーク]\[ネットワーク接続]\[Windows ファイアウォール]\[標準プロファイル]\[Windows ファイアウォール] | 着信リモート デスクトップの例外を許可する |
    | ICMP-V4 を有効にする                       | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[ネットワーク]\[ネットワーク接続]\[Windows ファイアウォール]\[ドメイン プロファイル]\[Windows ファイアウォール]   | ICMP の例外を許可する                   |
    |                                      | [コンピューターの構成]\[ポリシー]\[Windows の設定]\[管理用テンプレート]\[ネットワーク]\[ネットワーク接続]\[Windows ファイアウォール]\[標準プロファイル]\[Windows ファイアウォール] | ICMP の例外を許可する                   |

## <a name="verify-the-vm"></a>VM の確認

VM が正常であり、セキュリティで保護されており、RDP アクセス可能であることを確認します。

1. ディスクが正常で一貫性があることを確認するには、次回の VM 再起動時にディスクをチェックします。

   ```powershell
   chkdsk.exe /f
   ```

   クリーンで正常なディスクがレポートに示されていることを確認してください。

1. ブート構成データ (BCD) を設定します。

   ```powershell
   bcdedit.exe /set "{bootmgr}" integrityservices enable
   bcdedit.exe /set "{default}" device partition=C:
   bcdedit.exe /set "{default}" integrityservices enable
   bcdedit.exe /set "{default}" recoveryenabled Off
   bcdedit.exe /set "{default}" osdevice partition=C:
   bcdedit.exe /set "{default}" bootstatuspolicy IgnoreAllFailures

   #Enable Serial Console Feature
   bcdedit.exe /set "{bootmgr}" displaybootmenu yes
   bcdedit.exe /set "{bootmgr}" timeout 5
   bcdedit.exe /set "{bootmgr}" bootems yes
   bcdedit.exe /ems "{current}" ON
   bcdedit.exe /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Dump ログは、Windows のクラッシュの問題をトラブルシューティングするのに役立つ場合があります。 Dump ログ コレクションを有効にします。

   ```powershell
   # Set up the guest OS to collect a kernel dump on an OS crash event
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

   # Set up the guest OS to collect user mode dumps on a service crash event
   $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
   if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
   New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value 'C:\CrashDumps'
   New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
   New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
   Set-Service -Name WerSvc -StartupType Manual
   ```

1. Windows Management Instrumentation (WMI) リポジトリに一貫性があることを確認します。

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   リポジトリが破損している場合は、「[WMI:リポジトリが破損しているかどうか](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484)」を参照してください。

1. 他にポート 3389 を使用しているアプリケーションがないことを確認します。 このポートは、Azure の RDP サービスに使用します。 VM で使用されているポートを確認するには、`netstat.exe -anob` を実行します。

   ```powershell
   netstat.exe -anob
   ```

1. ドメイン コントローラーである Windows VHD をアップロードするには、次のようにします。

   - [この追加の手順](https://support.microsoft.com/kb/2904015)に従って、ディスクを準備します。

   - Directory Services Restore Mode (DSRM) で VM を起動しなければならない場合に備えて、DSRM パスワードを把握しておいてください。 詳細については、「[DSRM パスワードの設定](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11))」を参照してください。

1. あらかじめ登録された Administrator アカウントとパスワードを把握していることを確認します。 現在のローカル Administrator パスワードをリセットし、このアカウントを使用して RDP 接続を介して Windows にサインイン可能であることを確認することもできます。 このアクセス許可は、"リモート デスクトップ サービスを使ったログオンを許可" グループ ポリシー オブジェクトによって制御されています。 このオブジェクトは、次の場所からローカル グループ ポリシー エディターで表示します。

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. 次の Azure AD ポリシーをチェックして、RDP アクセスをブロックしていないことを確認します。

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. 次の Azure AD ポリシーをチェックして、必要なアクセス アカウントがいずれも削除されていないことを確認します。

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   ポリシーには次のグループが一覧表示されるはずです。

   - 管理者

   - Backup Operators

   - Everyone

   - ユーザー

1. VM を再起動して、Windows が引き続き正常であり、RDP 接続を介してアクセス可能であることを確認します。 この時点で、ローカル Hyper-V サーバーに VM を作成して、VM が完全に起動することを確認することを検討してください。 次に、RDP を介して VM に到達できることを確認するためのテストを実行します。

1. 余分な Transport Driver Interface (TDI) フィルターはすべて削除します。 たとえば、TCP パケットや追加のファイアウォールを分析するソフトウェアは削除します。

1. 物理コンポーネントまたはその他の仮想化テクノロジに関連する、その他のサードパーティ ソフトウェアまたはドライバーをアンインストールします。

### <a name="install-windows-updates"></a>Windows 更新プログラムのインストール

理想的には、継続して "*パッチ レベル*" でマシンを更新する必要があります。そうできない場合は、以下の更新プログラムがインストールされていることを確認してください。 最新の更新プログラムを入手するには、Windows Update の履歴ページを参照してください。[Windows 10 および Windows Server 2019](https://support.microsoft.com/help/4000825)、[Windows 8.1 および Windows Server 2012 R2](https://support.microsoft.com/help/4009470)、[Windows 7 SP1 および Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469)。

<br />

|        コンポーネント        |     Binary     | Windows 7 SP1、Windows Server 2008 R2 SP1 |       Windows 8、Windows Server 2012        | Windows 8.1、Windows Server 2012 R2 | Windows 10 v1607、Windows Server 2016 v1607 |      Windows 10 v1703      | Windows 10 v1709、Windows Server 2016 v1709 | Windows 10 v1803、Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| ストレージ                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061          | -                                           | -                          | -                                           | -                                           |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.332             | -                                           | -                                           |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614          | 10.0.14393.953 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384          | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | volmgr.sys     |                                           |                                             |                                     |                                             | 10.0.15063.0               | -                                           | -                                           |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726          | 10.0.14393.1066 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726          | -                                           | -                          | -                                           | -                                           |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726          | 10.0.14393.1198 - KB4022715                 | -                          | -                                           | -                                           |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650          | 10.0.14393.2007 - KB4345418                 | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                  | -                                           |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614          | 10.0.14393.576 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614          | 10.0.14393.206 - KB4022715                  | -                          | -                                           | -                                           |
| ネットワーク                 | netvsc.sys     | -                                         | -                                           | -                                   | 10.0.14393.1198 - KB4022715                 | 10.0.15063.250 - KB4020001 | -                                           | -                                           |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726          | 10.0.14393.479 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726          | 10.0.14393.251 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| コア                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| リモート デスクトップ サービス | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850          | 10.0.14393.0 - KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726          | 10.0.14393.594 - KB4022715                  | -                          | -                                           | -                                           |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
| セキュリティ                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                           | KB4012606                                   | KB4012606                  | -                                           | -                                           |
|                         |                |                                           | KB4012216                                   |                                     | KB4013198                                   | KB4013198                  | -                                           | -                                           |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                           | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         |                |                                           | KB4012217                                   |                                     | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         | CVE-2018-0886  | KB4103718                                 | KB4103730                                   | KB4103725                           | KB4103723                                   | KB4103731                  | KB4103727                                   | KB4103721                                   |
|                         |                | KB4103712                                 | KB4103726                                   | KB4103715                           |                                             |                            |                                             |                                             |

> [!NOTE]
> VM のプロビジョニング中に誤って再起動されないようにするために、すべての Windows Update インストールが完了し、保留中の更新プログラムがないことを確認することをお勧めします。 これを行う 1 つの方法は、考えられるすべての Windows 更新プログラムをインストールし、一度再起動してから、`sysprep.exe` コマンドを実行します。

## <a name="determine-when-to-use-sysprep"></a>Sysprep を使用する状況の判断

システム準備ツール (`sysprep.exe`) は、Windows インストールをリセットするために使用できるプロセスです。
Sysprep は、個人データをすべて削除し、コンポーネントをいくつかリセットすることによって、"すぐに使用できる" エクスペリエンスを提供します。

`sysprep.exe` は通常、特定の構成を持つ他の複数の VM のデプロイ元となるテンプレートを作成するために実行します。 このテンプレートは、*一般化されたイメージ* と呼ばれます。

1 つのディスクから 1 つの VM のみを作成する場合は、Sysprep を使用する必要はありません。 代わりに、*特殊化されたイメージ* から VM を作成することができます。 特殊化されたディスクから VM を作成する方法については、以下を参照してください。

- [特殊化されたディスクからの VM の作成](create-vm-specialized.md)
- [特殊化された VHD ディスクからの VM の作成](./create-vm-specialized-portal.md)

一般化されたイメージを作成する場合は、Sysprep を実行する必要があります。 詳しくは、「[How to use Sysprep: An Introduction](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10))」 (Sysprep の使用方法: 概要) をご覧ください。

Windows ベースのコンピューターにインストールされているロールまたはアプリケーションには、一般化されたイメージをサポートしていないものもあります。 この手順を使用する前に、Sysprep でお使いのコンピューターのロールがサポートされていることを確認してください。 詳しくは、「[Sysprep Support for Server Roles (サーバー ロールの sysprep サポート)](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)」を参照してください。

特に、Sysprep では、実行前にドライブを完全に復号化する必要があります。 VM で暗号化を有効にしている場合は、Sysprep を実行する前に無効にしてください。


### <a name="generalize-a-vhd"></a>VHD の一般化

>[!NOTE]
> 次の手順で `sysprep.exe` を実行した後、VM をオフにします。 Azure でそれからイメージを作成するまでは再びオンにしないでください。

1. Windows VM にサインインします。
1. PowerShell セッションを管理者として実行します。
1. panther ディレクトリ (C:\Windows\Panther) を削除します。
1. ディレクトリを `%windir%\system32\sysprep` に変更します 次に、`sysprep.exe` を実行します。
1. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、 **[一般化する]** チェック ボックスがオンになっていることを確認します。

    ![システム準備ツール](media/prepare-for-upload-vhd-image/syspre.png)
1. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
1. **[OK]** を選択します。
1. Sysprep が完了したら、VM をシャットダウンします。 VM をシャットダウンするために **[再起動]** を使用しないでください。

これで VHD をアップロードする準備ができました。 一般化されたディスクから VM を作成する方法の詳細については、[一般化された VHD のアップロードと Azure での新しい VM の作成](/previous-versions/azure/virtual-machines/windows/sa-upload-generalized)に関するページをご覧ください。

>[!NOTE]
> カスタム *unattend.xml* ファイルはサポートされていません。 **additionalUnattendContent** プロパティはサポートされていますが、Azure プロビジョニング エージェントが使用する *unattend.xml* ファイルに [microsoft-windows-shell-setup](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) オプションを追加するためのサポートは限られています。 たとえば、FirstLogonCommands と LogonCommands を追加するには、[additionalUnattendContent](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent) を使用できます。 詳細については、「[additionalUnattendContent FirstLogonCommands の例](https://github.com/Azure/azure-quickstart-templates/issues/1407)」をご覧ください。

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>仮想ディスクを容量固定の VHD に変換する

このセクションのいずれかの方法を使用して、仮想ディスクを Azure に必要な形式に変換し、サイズ変更します。

1. 仮想ディスクの変換またはサイズ変更処理を実行する前に、VM をバックアップします。

1. Windows VHD がローカル サーバーで正しく動作していることを確認します。 Azure に変換またはアップロードする前に、VM 自体に発生しているすべてのエラーを解決します。

1. 仮想ディスクを固定の種類に変換します。

1. Azure の要件を満たすように仮想ディスクのサイズを変更します。

   1. Azure のディスクの仮想サイズは、1 MiB にアラインする必要があります。 VHD に 1 MiB の端数がある場合は、1 MiB の倍数になるようにディスクのサイズを変更する必要があります。 MiB の端数があるディスクでは、アップロードした VHD からイメージを作成する際にエラーが発生します。 サイズを確認するには、PowerShell の [Get-VHD](/powershell/module/hyper-v/get-vhd) コマンドレットを使用し、"Size" (Azure で 1 MiB の倍数である必要があります) と "FileSize" ("Size" に VHD フッターの 512 バイトを足した値に等しくなります) を表示します。
   
   1. 第 1 世代 VM で OS VHD に許容される最大サイズは 2,048 GiB (2 TiB) です。 
   1. データ ディスクの最大サイズは 32,767 GiB (32 TiB) です。

> [!NOTE]
> - 固定ディスクに変換し、必要に応じてサイズを変更した後に Windows OS ディスクを準備する場合は、そのディスクを使用する VM を作成します。 VM を起動してサインインし、この記事のセクションを続行して、アップロードの準備を完了します。  
> - データ ディスクを準備している場合は、このセクションで中断し、ディスクのアップロードに進むことができます。

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Hyper-V マネージャーを使用してディスクを変換する

1. Hyper-V マネージャーを開いて、左側のローカル コンピューターを選択します。 コンピューター リストの上にあるメニューで、 **[アクション]**  >  **[ディスクの編集]** の順に選択します。
1. **[仮想ハード ディスクの場所]** ページで、お使いの仮想ディスクを選択します。
1. **[アクションの選択]** ページで、 **[変換]**  >  **[次へ]** の順に選択します。
1. VHDX から変換するには、 **[VHD]**  >  **[次へ]** の順に選択します。
1. 容量可変ディスクから変換するには、 **[容量固定]**  >  **[次へ]** の順に選択します。
1. 新しい VHD ファイルの保存先となるパスを見つけて選択します。
1. **[完了]** を選択します。

### <a name="use-powershell-to-convert-the-disk"></a>PowerShell を使用してディスクを変換する

PowerShell で [Convert-VHD](/powershell/module/hyper-v/convert-vhd) コマンドレットを使用して、仮想ディスクを変換できます。 このコマンドレットのインストールに関する情報が必要な場合は、[Hyper-V ロールのインストール](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)に関するページを参照してください。

次の例は、ディスクを VHDX から VHD に変換します。 また、ディスクを容量可変ディスクを容量固定ディスクに変換します。

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

この例で、**Path** の値を、変換する仮想ハード ディスクのパスに置き換えてください。 **DestinationPath** の値を、変換したディスクの新しいパスおよび名前に置き換えます。

### <a name="use-hyper-v-manager-to-resize-the-disk"></a>Hyper-V マネージャーを使用してディスクのサイズを変更する

1. Hyper-V マネージャーを開いて、左側のローカル コンピューターを選択します。 コンピューター リストの上にあるメニューで、 **[アクション]**  >  **[ディスクの編集]** の順に選択します。
1. **[仮想ハード ディスクの場所]** ページで、お使いの仮想ディスクを選択します。
1. **[アクションの選択]** ページで、 **[展開]**  >  **[次へ]** の順に選択します。
1. **[仮想ハード ディスクの場所]** ページで、新しいサイズを GiB 単位で入力し、 **[次へ]** を選択します。
1. **[完了]** を選択します。

### <a name="use-powershell-to-resize-the-disk"></a>PowerShell を使用してディスクのサイズを変更する

PowerShell で [Resize-VHD](/powershell/module/hyper-v/resize-vhd) コマンドレットを使用して、仮想ディスクのサイズを変更できます。 このコマンドレットのインストールに関する情報が必要な場合は、[Hyper-V ロールのインストール](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)に関するページを参照してください。

次の例では、Azure のアラインメント要件を満たすために、ディスクのサイズを 100.5 MiB から 101 MiB に変更します。

```powershell
Resize-VHD -Path C:\test\MyNewVM.vhd -SizeBytes 105906176
```

この例では、**Path** の値をサイズ変更する仮想ハード ディスクのパスに置き換えます。 **SizeBytes** の値を、ディスクの新しいサイズ (バイト単位) に置き換えます。

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK ディスク フォーマットからの変換

[VMDK ファイル形式](https://en.wikipedia.org/wiki/VMDK)の Windows VM イメージがある場合は、[Azure Migrate](../../migrate/server-migrate-overview.md) を使用して VMDK を変換し、Azure にアップロードできます。

## <a name="complete-the-recommended-configurations"></a>推奨される構成を完了する

次の設定は、VHD のアップロードに影響しません。 ただし、これらを構成しておくことを強くお勧めします。

- [Azure 仮想マシン エージェント](https://go.microsoft.com/fwlink/?LinkID=394789)をインストールします。 その後で、VM 拡張機能を有効にできます。 VM 拡張機能によって、VM で使用する重要な機能のほとんどが実装されます。 たとえば、パスワードをリセットしたり、RDP を構成したりするには、拡張機能が必要です。 詳細については、「[Azure 仮想マシン エージェントの概要](../extensions/agent-windows.md)」を参照してください。
- Azure で VM を作成した後は、パフォーマンスを向上させるために、ページ ファイルを "*テンポラル ドライブ ボリューム*" 上に置くことをお勧めします。 次のようにファイルの配置をセットアップできます。

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  データ ディスクが VM に接続されている場合、テンポラル ドライブ ボリュームの文字は通常 *D* になります。この指定は、設定や使用可能なドライブ数に応じて異なる場合があります。

  - ウイルス対策ソフトウェアによって提供されている可能性のあるスクリプト ブロッカーを無効にすることをお勧めします。 イメージから新しい VM をデプロイするときに実行される Windows プロビジョニング エージェント スクリプトが干渉されて、ブロックされる可能性があります。

## <a name="next-steps"></a>次のステップ

- [Resource Manager デプロイメント向けに Windows VM イメージを Azure にアップロードする](upload-generalized-managed.md)
- [Azure Windows VM のライセンス認証に関する問題のトラブルシューティング](../troubleshooting/troubleshoot-activation-problems.md)
