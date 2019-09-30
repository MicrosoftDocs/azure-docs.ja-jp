---
title: Azure VM でゲスト OS ファイアウォールを無効にする |Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 9ae8620b803fa9a911f44840a5fff5d190a316a1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086528"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Azure VM でゲスト OS ファイアウォールを無効にします

この記事では、ゲスト オペレーティング システムのファイアウォールが仮想マシン (VM) に部分的または完全なトラフィックをフィルター処理していると疑われる場合に参照事項を提供します。 これは、RDP 接続の失敗の原因となった、ファイアウォールに意図的に変更が加えられた場合に発生することがあります。

## <a name="solution"></a>解決策

この記事で説明されているプロセスは、ファイアウォール規則を正しく設定する方法という、実際の問題の修正に集中できるように、回避策として使用することを意図しています。 Windows ファイアウォール コンポーネントを有効にする、It\rquote s マイクロソフトのベスト プラクティスです。 ファイアウォール規則の \cf3 を構成する方法は、必要な VM that\rquote s へのアクセスのレベルによって異なります。

### <a name="online-solutions"></a>オンライン ソリューション 

VM がオンラインにあり、同じ仮想ネットワーク上の別の VM でアクセスできる場合、他の VM を使用してこれらを軽減策にできます。

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>軽減策 1: カスタム スクリプト拡張機能または実行コマンド機能

作動中の Azure エージェントがあれば、次のスクリプトをリモートで実行するのに[カスタム スクリプト拡張機能](../extensions/custom-script-windows.md)または[コマンドの実行](../windows/run-command.md)機能(Resource Manager Vm のみ)使用することができます。

> [!Note]
> * ファイアウォールがローカルに設定されている場合、次のスクリプトを実行します:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Active Directory ポリシーを介してファイアウォールを設定すると、一時的なアクセスに対して次のスクリプトの実行を使用できます。 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   ただし、ポリシーが再び適用されるとすぐに、リモート セッションからサインアウトされます。 この問題を根本的に修正するには、このコンピューターに適用されるポリシーを変更します。

#### <a name="mitigation-2-remote-powershell"></a>軽減策 2: リモート PowerShell

1.  RDP 接続を使用して到達できない VM と同じ仮想ネットワークに存在する VM に接続します。

2.  PowerShell コンソール ウィンドウを開きます。

3.  次のコマンドを実行します。

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> グループ ポリシー オブジェクトを介してファイアウォールを設定すると、このコマンドによってローカル レジストリ エントリのみが変更されるため、このメソッドは機能しません。 ポリシーがある場合、この変更が上書きされます。 

#### <a name="mitigation-3-pstools-commands"></a>軽減策 3: PSTools コマンド

1.  トラブルシューティング用の VM で、[PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)をダウンロードします。

2.  CMD インスタンスを開き、その DIP を通して VM にアクセスします。

3.  次のコマンドを実行します。

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>軽減策 4: リモート レジストリ 

次の手順に従って[リモート レジストリ](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry)を使用します。

1.  トラブルシューティング用の VM では、レジストリ エディターを起動し、**ファイル** > **ネットワーク レジストリへの接続**に移動します。

2.   *ターゲット マシン*\SYSTEM 分岐を開き、次の値を指定します：

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  サービスを再起動します。 リモート レジストリを使用して行うことはできないため、サービス コンソールの削除を行う必要があります。

4.  [ **Services.msc**]のインスタンスを開きます。

5.  **[サービス(ローカル)]** をクリックします。

6.  **[Connect to another computer]\(別のコンピューターに接続\)** を選択します。

7.  問題VMの **プライベート IP アドレス (DIP)**  を入力します。

8.  ローカルのファイアウォール ポリシーを再起動します。

9.  ローカル コンピューターからもう一度 RDP を介して VM に接続しようとしてください。

### <a name="offline-solutions"></a>オフラインのソリューション 

どのメソッドでも VM に到達できない場合、カスタム スクリプト拡張機能は使えなくなり、オフライン モードでシステム ディスクから直接作業する必要があります。 そのためには、次の手順に従います。

1.  [復旧 VM にシステム ディスクを取り付ける](troubleshoot-recovery-disks-portal-windows.md)。

2.  復旧 VM へのリモート デスクトップ接続を開始します。

3.  ディスクにディスク管理コンソールでオンラインとして印がついていることを確認します。 接続されたシステム ディスクに割り当てられたドライブ文字をメモします。

4.  変更を行う前に、変更のロールバックが必要な場合に備えて、\windows\system32\config フォルダーのコピーを作成します。

5.  トラブルシューティング用の VM で、レジストリ エディター (regedit.exe) を起動します。 

6.  このトラブルシューティングの手順で BROKENSYSTEM と BROKENSOFTWARE ハイブをマウントします。

7.  HKEY_LOCAL_MACHINE キーを選択したら、メニューから[ファイル] > [Hive の読み込み]を選択します。

8.  接続されたシステム ディスクで \windows\system32\config\SYSTEM ファイルを見つけます。

9.  管理者特権の PowerShell インスタンスを開き、次のコマンドを実行します:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [システム ディスクをデタッチし、VM を再作成します](troubleshoot-recovery-disks-portal-windows.md)。

11. 問題が解決しているかどうかを確認します。
