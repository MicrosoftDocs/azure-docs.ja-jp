---
title: Azure Windows VM の CMD と PowerShell | Microsoft Docs
description: Azure Windows VM の SAC 内で CMD と PowerShell コマンドを使用する方法
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: a106984bc60d0ccfe29a1956213aec6f87ad30dd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090181"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows コマンド - CMD と PowerShell

このセクションには、Windows VM にアクセスするために SAC の使用が必要になる可能性があるシナリオにおいて、一般的なタスクを実行するためのコマンド例が含まれています。たとえば、RDP 接続のエラーをトラブルシューティングする必要がある場合などです。

SAC は Windows Server 2003 以降のすべてのバージョンの Windows に含まれていますが、既定では無効になっています。 SAC は、`sacdrv.sys` カーネル ドライバー、`Special Administration Console Helper` サービス (`sacsvr`)、`sacsess.exe` プロセスに依存しています。 詳しくは、[緊急管理サービスのツールと設定](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10))に関する記事をご覧ください。

SAC を使用すると、シリアル ポート経由で実行中の OS に接続できます。 SAC から CMD を起動すると、実行中の OS 内で、`sacsess.exe` によって `cmd.exe` が起動されます。 シリアル コンソール機能を使用して SAC に接続するのと同時に、VM に RDP で接続すると、タスク マネージャーでこのことを確認できます。 SAC 経由でアクセスする CMD は、RDP 接続の際に使用する `cmd.exe` と同じものです。 まったく同じコマンドとツールが利用可能です。CMD インスタンスから PowerShell を起動することもできます。 SAC と Windows 回復環境 (WinRE) の主な違いは、SAC では実行中の 0S を管理できるのに対し、WinRE では別の最小限の OS が起動されるということです。 Azure VM では WinRE へのアクセスはサポートされていませんが、シリアル コンソール機能を使用すると、SAC 経由で Azure VM を管理できます。

SAC は 80 x 24 のスクリーン バッファーに制限されるうえ、スクロール バックもできないため、コマンドに `| more` を追加して、一度に 1 ページずつ出力を表示します。 `<spacebar>` で次のページを表示し、`<enter>` で次の行を表示します。  

`SHIFT+INSERT` は、シリアル コンソール ウィンドウ用の貼り付けのショートカットです。

SAC はスクリーン バッファーが制限されているため、長いコマンドを入力する場合は、ローカルのテキスト エディターに入力してから SAC に貼り付けた方が簡単な場合があります。

## <a name="view-and-edit-windows-registry-settings"></a>Windows レジストリ設定の表示と編集
### <a name="verify-rdp-is-enabled"></a>RDP が有効になっていることを確認する
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

2 番目のキー (\Policies 下) が存在するのは、関連するグループ ポリシー設定を構成する場合のみです。

### <a name="enable-rdp"></a>RDP を有効にする
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

2 番目のキー (\Policies 下) が必要になるのは、関連するグループ ポリシー設定が構成されている場合のみです。 これをグループ ポリシーで構成する場合、値が書き換えられるのは次にグループ ポリシーを更新するときです。

## <a name="manage-windows-services"></a>Windows サービスの管理

### <a name="view-service-state"></a>サービスの状態の表示
`sc query termservice`
###  <a name="view-service-logon-account"></a>サービスのログオン アカウントの表示
`sc qc termservice`
### <a name="set-service-logon-account"></a>サービスのログオン アカウントの設定 
`sc config termservice obj= "NT Authority\NetworkService"`

等号の後にはスペースが必要です。
### <a name="set-service-start-type"></a>サービス開始の種類の設定
`sc config termservice start= demand` 

等号の後にはスペースが必要です。 開始の値には、`boot`、`system`、`auto`、`demand`、`disabled`、`delayed-auto` などを使用できます。
### <a name="set-service-dependencies"></a>サービスの依存関係の設定
`sc config termservice depend= RPCSS`

等号の後にはスペースが必要です。
### <a name="start-service"></a>サービスの開始
`net start termservice`

or

`sc start termservice`
### <a name="stop-service"></a>サービスの停止
`net stop termservice`

or

`sc stop termservice`
## <a name="manage-networking-features"></a>ネットワーク機能の管理
### <a name="show-nic-properties"></a>NIC プロパティの表示
`netsh interface show interface` 
### <a name="show-ip-properties"></a>IP プロパティの表示
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>IPSec 構成の表示
`netsh nap client show configuration`  
### <a name="enable-nic"></a>NIC の有効化
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>DHCP を使用するように NIC を設定する
`netsh interface ip set address name="<interface name>" source=dhcp`

`netsh` の詳細については、[こちら](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts)をクリックしてください。

IP アドレスを取得するために DHCP を使用するように、ゲスト OS で Azure VM を常に構成する必要があります。 Azure の静的 IP 設定では、VM に静的 IP を指定するために引き続き DHCP が使用されます。
### <a name="ping"></a>ping
`ping 8.8.8.8` 
### <a name="port-ping"></a>ポートの ping  
Telnet クライアントのインストール

`dism /online /Enable-Feature /FeatureName:TelnetClient`

接続をテストする

`telnet bing.com 80`

Telnet クライアントの削除

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Windows で利用できる既定の方法に制限されている場合は、PowerShell を使用してポートの接続をテストする方がよい場合があります。 例については、以下の PowerShell に関するセクションをご覧ください。
### <a name="test-dns-name-resolution"></a>DNS 名前解決のテスト
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Windows ファイアウォール規則の表示
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Windows ファイアウォールの無効化
`netsh advfirewall set allprofiles state off`

トラブルシューティング時にこのコマンドを使用して、Windows ファイアウォールを一時的に無効にすることができます。 これは、次の再起動時か、以下のコマンドを使用した有効化時に有効になります。 Windows ファイアウォールを無効にするために Windows ファイアウォール サービス (MPSSVC) を停止したり、ベース フィルター エンジン (BFE) サービスを停止したりしないでください。 MPSSVC や BFE を停止すると、すべての接続がブロックされます。
### <a name="enable-windows-firewall"></a>Windows ファイアウォールの有効化
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>ユーザーとグループの管理
### <a name="create-local-user-account"></a>ローカル ユーザー アカウントの作成
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>ローカル グループにローカル ユーザーを追加する
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>ユーザー アカウントが有効になっていることを確認する
`net user <username> | find /i "active"`

一般化されたイメージから作成した Azure VM では、ローカル管理者アカウントの名前が、VM のプロビジョニング時に指定する名前に変更されます。 したがって、通常は `Administrator` ではありません。
### <a name="enable-user-account"></a>ユーザー アカウントの有効化
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>ユーザー アカウント プロパティの表示
`net user <username>`

ローカル管理者アカウントからの関連する行の例:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>ローカル グループの表示
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Windows イベント ログの管理
### <a name="query-event-log-errors"></a>イベント ログ エラーの照会
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

`/c:10` を変更して返すイベントの数を指定するか、これを移動してフィルターに一致するイベントをすべて返すようにします。
### <a name="query-event-log-by-event-id"></a>イベント ID でイベント ログを照会する
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>イベント ID とプロバイダーでイベント ログを照会する
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>過去 24 時間のイベント ID とプロバイダーでイベント ログを照会する
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

`604800000` を使用すると、24 時間の代わりに 7 日間を指定できます。
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>過去 7 日間のイベント ID、プロバイダー、EventData でイベント ログを照会する
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>インストールしたアプリケーションの表示または削除
### <a name="list-installed-applications"></a>インストールしたアプリケーションの一覧表示
`wmic product get Name,InstallDate | sort /r | more`

最近のインストールを確認しやすくするために、`sort /r` によってインストールの日付で降順に並べ替えます。 `<spacebar>` で出力の次のページに進み、`<enter>` で 1 行進みます。
### <a name="uninstall-an-application"></a>アプリケーションのアンインストール
`wmic path win32_product where name="<name>" call uninstall`

`<name>` を、上記のコマンドで返される、削除するアプリケーションの名前に置き換えます。

## <a name="file-system-management"></a>ファイル システムの管理
### <a name="get-file-version"></a>ファイル バージョンの取得
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

この例では、仮想 NIC ドライバーのファイル バージョンが返されます。それが netvsc.sys、netvsc63.sys、netvsc60.sys のいずれであるかは、Windows のバージョンによって異なります。
### <a name="scan-for-system-file-corruption"></a>システム ファイルの破損のスキャン
`sfc /scannow`

「[Repair a Windows Image](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image)」(Windows イメージの修復) もご覧ください。
### <a name="scan-for-system-file-corruption"></a>システム ファイルの破損のスキャン
`dism /online /cleanup-image /scanhealth`

「[Repair a Windows Image](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image)」(Windows イメージの修復) もご覧ください。
### <a name="export-file-permissions-to-text-file"></a>ファイルのアクセス許可をテキスト ファイルにエクスポートする
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>ファイルのアクセス許可を ACL ファイルに保存する
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>ACL ファイルからファイルのアクセス許可を復元する
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

`/restore` を使用する際のパスには、`/save` を使用する際に指定したフォルダーの親フォルダーを指定する必要があります。 この例では、上記の `/save` の例で指定した `\MachineKeys` フォルダーの親は、`\RSA` です。
### <a name="take-ntfs-ownership-of-a-folder"></a>フォルダーの NTFS 所有権を取得する
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>再帰的にフォルダーに NTFS アクセス許可を付与する
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>デバイスの管理
### <a name="remove-non-present-pnp-devices"></a>存在しない PNP デバイスの削除
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>グループ ポリシーの管理
### <a name="force-group-policy-update"></a>グループ ポリシーを強制的に更新する
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>その他のタスク
### <a name="show-os-version"></a>OS バージョンの表示
`ver`

or 

`wmic os get caption,version,buildnumber /format:list`

or 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>OS のインストール日の表示
`systeminfo | find /i "original"`

or 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>最終ブート時刻の表示
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>タイム ゾーンの表示
`systeminfo | find /i "time zone"`

or

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Windows の再起動
`shutdown /r /t 0`

`/f` を追加すると、ユーザーへの警告なしで実行中のアプリケーションを強制的に閉じます。
### <a name="detect-safe-mode-boot"></a>セーフ モードでのブートの検出
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Windows コマンド - PowerShell

SAC で PowerShell を実行するには、コマンド プロンプトで次のように入力します。

`powershell <enter>`

> [!CAUTION]
> 他の PowerShell コマンドを実行する前に、PowerShell セッションから PSReadLine モジュールを削除してください。 SAC の PowerShell セッションで PSReadLine が実行されていると、クリップボードから貼り付けたテキストに余分な文字が追加されるという既知の問題があります。

最初に、PSReadLine が読み込まれているかどうかを確認します。 Windows Server 2016、Windows 10、およびそれ以降のバージョンの Windows では、既定で読み込まれています。 以前のバージョンの Windows では、手動でインストールしない限り PSReadLine は存在しません。 

このコマンドが出力なしでプロンプトに戻る場合は、モジュールが読み込まれておらず、通常どおり引き続き SAC で PowerShell セッションを使用できます。

`get-module psreadline`

上記のコマンドで PSReadLine モジュールのバージョンが返される場合は、次のコマンドを実行してモジュールをアンロードします。 このコマンドは、モジュールを削除したりアンインストールしたりするのではなく、現在の PowerShell セッションからアンロードするのみです。

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Windows レジストリ設定の表示と編集
### <a name="verify-rdp-is-enabled"></a>RDP が有効になっていることを確認する
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

2 番目のキー (\Policies 下) が存在するのは、関連するグループ ポリシー設定を構成する場合のみです。
### <a name="enable-rdp"></a>RDP を有効にする
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

2 番目のキー (\Policies 下) が必要になるのは、関連するグループ ポリシー設定が構成されている場合のみです。 これをグループ ポリシーで構成する場合、値が書き換えられるのは次にグループ ポリシーを更新するときです。
## <a name="manage-windows-services"></a>Windows サービスの管理
### <a name="view-service-details"></a>サービスの詳細の表示
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` を使用できますが、サービスのログオン アカウントは含まれません。 それには `Get-WmiObject win32-service` を使用します。
### <a name="set-service-logon-account"></a>サービスのログオン アカウントの設定
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

`NT AUTHORITY\LocalService`、`NT AUTHORITY\NetworkService`、`LocalSystem` 以外のサービス アカウントを使用する場合は、アカウント名の後の最後の引数 (8 番目) に、アカウントのパスワードを指定します。
### <a name="set-service-startup-type"></a>サービスのスタートアップの種類を設定する
`set-service termservice -startuptype Manual`

`Set-service` には、スタートアップの種類として `Automatic`、`Manual`、または `Disabled` を指定できます。
### <a name="set-service-dependencies"></a>サービスの依存関係の設定
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>サービスの開始
`start-service termservice`
### <a name="stop-service"></a>サービスの停止
`stop-service termservice`
## <a name="manage-networking-features"></a>ネットワーク機能の管理
### <a name="show-nic-properties"></a>NIC プロパティの表示
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

or 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

2012 以降では `Get-NetAdapter` を使用できます。2008R2 では `Get-WmiObject` を使用します。
### <a name="show-ip-properties"></a>IP プロパティの表示
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>NIC の有効化
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

or

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

2012 以降では `Get-NetAdapter` を使用できます。2008R2 では `Get-WmiObject` を使用します。
### <a name="set-nic-to-use-dhcp"></a>DHCP を使用するように NIC を設定する
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

2012 以降では `Get-NetAdapter` を使用できます。 2008R2 では `Get-WmiObject` を使用します。 IP アドレスを取得するために DHCP を使用するように、ゲスト OS で Azure VM を常に構成する必要があります。 Azure の静的 IP 設定では、VM に IP を指定するために引き続き DHCP が使用されます。
### <a name="ping"></a>ping
`test-netconnection`

or

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

パラメーターなしで `Test-Netconnection` を実行すると、`internetbeacon.msedge.net` への ping を試みます。 これは 2012 以降で使用可能です。 2008R2 では、2 番目の例のように `Get-WmiObject` を使用します。
### <a name="port-ping"></a>ポートの ping
`test-netconnection -ComputerName bing.com -Port 80`

or

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

2012 以降では `Test-NetConnection` を使用できます。 2008R2 では `Net.Sockets.TcpClient` を使用します。
### <a name="test-dns-name-resolution"></a>DNS 名前解決のテスト
`resolve-dnsname bing.com` 

or 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

2012 以降では `Resolve-DnsName` を使用できます。 2008R2 では `System.Net.DNS` を使用します。
### <a name="show-windows-firewall-rule-by-name"></a>Windows ファイアウォール規則を名前で表示する
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Windows ファイアウォール規則をポートで表示する
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

or

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

2012 以降では `Get-NetFirewallPortFilter` を使用できます。 2008R2 では `hnetcfg.fwpolicy2` COM オブジェクトを使用します。 
### <a name="disable-windows-firewall"></a>Windows ファイアウォールの無効化
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

2012 以降では `Set-NetFirewallProfile` を使用できます。 2008R2 では、上記の CMD に関するセクションに示されているように、`netsh advfirewall` を使用します。
## <a name="manage-users-and-groups"></a>ユーザーとグループの管理
### <a name="create-local-user-account"></a>ローカル ユーザー アカウントの作成
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>ユーザー アカウントが有効になっていることを確認する
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

or 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

2012 以降では `Get-LocalUser` を使用できます。 2008R2 では `Get-WmiObject` を使用します。 この例では、組み込みのローカル管理者アカウントが表示されます。それは常に `S-1-5-21-*-500` という SID を持ちます。 一般化されたイメージから作成した Azure VM では、ローカル管理者アカウントの名前が、VM のプロビジョニング時に指定する名前に変更されます。 したがって、通常は `Administrator` ではありません。
### <a name="add-local-user-to-local-group"></a>ローカル グループにローカル ユーザーを追加する
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>ローカル ユーザー アカウントの有効化
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

この例では、組み込みのローカル管理者アカウントが有効化されます。それは常に `S-1-5-21-*-500` という SID を持ちます。 一般化されたイメージから作成した Azure VM では、ローカル管理者アカウントの名前が、VM のプロビジョニング時に指定する名前に変更されます。 したがって、通常は `Administrator` ではありません。
### <a name="view-user-account-properties"></a>ユーザー アカウント プロパティの表示
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

or 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

2012 以降では `Get-LocalUser` を使用できます。 2008R2 では `Get-WmiObject` を使用します。 この例では、組み込みのローカル管理者アカウントが表示されます。それは常に `S-1-5-21-*-500` という SID を持ちます。
### <a name="view-local-groups"></a>ローカル グループの表示
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

2012 以降では `Get-LocalUser` を使用できます。 2008R2 では `Get-WmiObject` を使用します。
## <a name="manage-the-windows-event-log"></a>Windows イベント ログの管理
### <a name="query-event-log-errors"></a>イベント ログ エラーの照会
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

`/c:10` を変更して返すイベントの数を指定するか、これを移動してフィルターに一致するイベントをすべて返すようにします。
### <a name="query-event-log-by-event-id"></a>イベント ID でイベント ログを照会する
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>イベント ID とプロバイダーでイベント ログを照会する
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>過去 24 時間のイベント ID とプロバイダーでイベント ログを照会する
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

`604800000` を使用すると、24 時間の代わりに 7 日間を指定できます。 |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>過去 7 日間のイベント ID、プロバイダー、EventData でイベント ログを照会する
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>インストールしたアプリケーションの表示または削除
### <a name="list-installed-software"></a>インストールされているソフトウェアを一覧表示する
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>ソフトウェアのアンインストール
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>ファイル システムの管理
### <a name="get-file-version"></a>ファイル バージョンの取得
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

この例では、仮想 NIC ドライバーのファイル バージョンが返されます。その名前が netvsc.sys、netvsc63.sys、netvsc60.sys のいずれであるかは、Windows のバージョンによって異なります。
### <a name="download-and-extract-file"></a>ファイルのダウンロードと抽出
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

この例では、`c:\bin` フォルダーを作成し、一連の Sysinternals ツールを `c:\bin` にダウンロードし、抽出します。
## <a name="miscellaneous-tasks"></a>その他のタスク
### <a name="show-os-version"></a>OS バージョンの表示
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>OS のインストール日の表示
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>最終ブート時刻の表示
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Windows のアップタイムの表示
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

`<days>:<hours>:<minutes>:<seconds>:<milliseconds>` の形式でアップタイムを返します。たとえば `49:16:48:00.00` です。 
### <a name="restart-windows"></a>Windows の再起動
`restart-computer`

`-force` を追加すると、ユーザーへの警告なしで実行中のアプリケーションを強制的に閉じます。
## <a name="instance-metadata"></a>インスタンス メタデータ

Azure VM 内から Azure インスタンス メタデータを照会して、osType、Location、vmSize、vmId、name、resourceGroupName、subscriptionId、privateIpAddress、publicIpAddress などの詳細を表示できます。

インスタンス メタデータを照会するには、ゲストのネットワーク接続が正常である必要があります。Azure ホストを通じてインスタンス メタデータ サービスに REST 呼び出しを実行する必要があるためです。 したがって、インスタンス メタデータの照会が可能であることは、ゲストがネットワーク経由で Azure でホストされるサービスに通信できることを意味します。

詳細については、「[Azure Instance Metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)」をご覧ください。

### <a name="instance-metadata"></a>インスタンス メタデータ
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>OS の種類 (インスタンス メタデータ)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>位置情報 (インスタンス メタデータ)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>サイズ (インスタンス メタデータ)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>VM ID (インスタンス メタデータ)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>VM 名 (インスタンス メタデータ)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>リソース グループ名 (インスタンス メタデータ)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>サブスクリプション ID (インスタンス メタデータ)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>タグ (インスタンス メタデータ)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>配置グループ ID (インスタンス メタデータ)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>プラットフォームの障害ドメイン (インスタンス メタデータ)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>プラットフォームの更新ドメイン (インスタンス メタデータ)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 プライベート IP アドレス (インスタンス メタデータ)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 パブリック IP アドレス (インスタンス メタデータ)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4 サブネット アドレス/プレフィックス (インスタンス メタデータ)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IPv6 IP アドレス (インスタンス メタデータ)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>MAC アドレス (インスタンス メタデータ)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>次の手順
* Windows のメインのシリアル コンソールのドキュメント ページは、[こちら](serial-console-windows.md)にあります。
* シリアル コンソールは、[Linux](serial-console-linux.md) VM でも使用できます。
* ブート診断の詳細については、[こちら](boot-diagnostics.md)を参照してください。
