---
title: Azure Virtual Machines に RDP 接続を行うと内部エラーが発生する | Microsoft Docs
description: Microsoft Azure で RDP 内部エラーのトラブルシューティングを行う方法を学習する | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 4476e4732dfcf8d79c9678a7ff4719eba10e48f3
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445783"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>リモート デスクトップを介して Azure VM に接続しようとするときに、内部エラーが発生する

この記事では、Microsoft Azure で仮想マシン (VM) を接続しようとしたときに発生する可能性があるエラーについて説明します。
> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、Resource Manager デプロイ モデルの使用方法について説明しています。最新のデプロイでは、クラシック デプロイ モデルではなくこのモデルを使用することをお勧めします。

## <a name="symptoms"></a>現象

リモート デスクトップ プロトコル (RDP) を使用して Azure VM に接続できません。 "リモートの構成" セクションで接続がスタックするか、次のエラー メッセージが表示されます。

- RDP の内部エラー
- 内部エラーが発生しました
- このコンピューターはリモート コンピューターに接続できません。 接続を再試行してください。 問題が解決しない場合は、リモート コンピューターの所有者またはネットワーク管理者に問い合わせてください


## <a name="cause"></a>原因

この問題は、次の理由で発生する可能性があります。

- ローカルの RSA 暗号化キーにアクセスできない。
- TLS プロトコルが無効になっている。
- 証明書が破損しているか、有効期限が切れている。

## <a name="solution"></a>解決策

これらの手順を実行する前に、バックアップとして、影響を受ける VM の OS ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。

この問題のトラブルシューティングを行うには、シリアル コンソールを使用するか、VM の OS ディスクを復旧 VM にアタッチして [VM をオフライン修復](#repair-the-vm-offline)します。


### <a name="use-serial-control"></a>シリアル コントロールを使用する

[シリアル コンソールに接続し、PowerShell インスタンスを開きます](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
)。 VM でシリアル コンソールが有効になっていない場合は、「[VM をオフライン修復する](#repair-the-vm-offline)」セクションに移動します。

#### <a name="step-1-check-the-rdp-port"></a>手順 1: RDP ポートを確認する

1. PowerShell インスタンスで、[NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) を使用してポート 8080 が他のアプリケーションによって使用されているかどうかを確認します。

        Netstat -anob |more
2. Termservice.exe がポート 8080 を使用している場合は、手順 2 に進みます。 別のサービスまたは Termservice.exe 以外のアプリケーションがポート 8080 を使用している場合は、次の手順に従います。

    1. 3389 サービスを使用しているアプリケーションのサービスを停止します。

            Stop-Service -Name <ServiceName> -Force

    2. ターミナル サービスを開始します。

            Start-Service -Name Termservice

2. アプリケーションを停止できない場合、またはこの手法が適用できない場合は、RDP のポートを変更します。

    1. 次のようにポートを変更します。

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. 新しいポートのファイアウォールを次のように設定します。

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. Azure portal の RDP ポートで、[新しいポートのネットワーク セキュリティ グループを更新](../../virtual-network/security-overview.md)します。

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>手順 2:RDP の自己署名証明書に適切なアクセス許可を設定する

1.  PowerShell インスタンスで、次のコマンドを 1 つずつ実行して、RDP の自己署名証明書を更新します。

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. この手法を使用して証明書を更新できない場合は、リモートで RDP の自己署名証明書の更新を試してください。

    1. 問題が発生している VM に接続している作業用 VM から、**[実行]** ボックスに「**mmc**」を入力して、Microsoft 管理コンソールを開きます。
    2. **[ファイル]** メニューで **[スナップインの追加と削除]** を選択し、**[証明書]**、**[追加]** の順に選択します。
    3. **[コンピューター アカウント]**、**[別のコンピューター]** の順に選択してから、問題の VM の IP アドレスを追加します。
    4. **Remote Desktop\Certificates** フォルダーに移動し、証明書を右クリックして **[削除]** を選択します。
    5. シリアル コンソールから PowerShell インスタンスで、リモート デスクトップ構成サービスを再開します。

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. MachineKeys フォルダーに対するアクセス許可をリセットします。

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. VM を再起動し、VM へのリモート デスクトップ接続の開始を試行します。 エラーが引き続き発生する場合は、次の手順に移動します。

手順 3:サポートされているすべての TLS バージョンを有効にする

RDP クライアントでは、既定のプロトコルとして TLS 1.0 が使用されます。 ただし、これは新しい標準となっている TLS 1.1 に変更できます。 VM で TLS 1.1 が無効になっている場合、接続は失敗します。
1.  CMD インスタンスで、TLS プロトコルを有効にします。

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  AD ポリシーが変更を上書きすることを防ぐため、グループ ポリシーの更新を一時的に停止します。

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  変更が反映されるように、VM を再起動します。 問題が解決された場合は、次のコマンドを実行してグループ ポリシーを再度有効にします。

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    変更が元に戻された場合、それは会社のドメイン内に Active Directory ポリシーが存在していることを意味します。 この問題の発生を回避するには、そのポリシーを変更する必要があります。

### <a name="repair-the-vm-offline"></a>VM をオフライン修復する

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>復旧 VM に OS ディスクを接続する

1. [復旧 VM に OS ディスクを接続します](../windows/troubleshoot-recovery-disks-portal.md)。
2. OS ディスクを復旧 VM に接続したら、[ディスクの管理] コンソールでそのディスクが **[オンライン]** になっていることを確認します。 接続された OS ディスクに割り当てられたドライブ文字をメモします。
3. 復旧 VM へのリモート デスクトップ接続を開始します。

#### <a name="enable-dump-log-and-serial-console"></a>ダンプ ログとシリアル コンソールを有効にする

ダンプ ログとシリアル コンソールを有効にするには、次のスクリプトを実行します。

1. 管理者特権のコマンド プロンプト セッション (**[管理者として実行]**) を開きます。
2. 次のスクリプトを実行します。

    このスクリプトでは、接続されている OS ディスクに割り当てられているドライブ文字が F であると想定しています。このドライブ文字を実際の VM の適切な値に置き換えてください。

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>MachineKeys フォルダーに対するアクセス許可をリセットする

1. 管理者特権のコマンド プロンプト セッション (**[管理者として実行]**) を開きます。
2. 次のスクリプトを実行します。 このスクリプトでは、接続されている OS ディスクに割り当てられているドライブ文字が F であると想定しています。このドライブ文字を実際の VM の適切な値に置き換えてください。

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>サポートされているすべての TLS バージョンを有効にする

1.  管理者特権のコマンド プロンプト セッション (**[管理者として実行]**) を開き、次のコマンドを使用します。 次のスクリプトでは、接続されている OS ディスクに割り当てられているドライブ文字が F であると想定しています。このドライブ文字を実際の VM の適切な値に置き換えてください。
2.  どの TLS が有効になっているかを確認します。

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  キーが存在しないか、その値が **0** の場合、次のスクリプトを実行して、プロトコルを有効にします。

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  NLA を有効にします。

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [OS ディスクをデタッチして、VM を再作成](../windows/troubleshoot-recovery-disks-portal.md)してから、問題が解決されたかどうかを確認します。





