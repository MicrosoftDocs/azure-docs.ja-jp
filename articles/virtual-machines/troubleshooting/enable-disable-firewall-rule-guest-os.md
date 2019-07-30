---
title: Azure VM でゲスト OS 上のファイアウォール規則を有効または無効にする |Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 7a547efb7af69c58f8e04615d24dd7c230f0c8b0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444650"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Azure VM のゲスト OS 上のファイアウォール規則を有効または無効にする

この記事では、ゲスト オペレーティング システム ファイアウォールが仮想マシン (VM) で一部のトラフィックをフィルター処理する疑いがある場合のトラブルシューティング用の参照事項を提供します。 これが役立つと言える理由：

*   RDP 接続の失敗の原因となったファイアウォールに意図的に変更が行われた場合、カスタム スクリプト拡張機能を使用すると、問題を解決できます。

*   すべてのファイアウォール プロファイルを無効にするのは、RDP に固有のファイアウォール規則を設定するよりも間違いのないトラブルシューティングの方法です。

## <a name="solution"></a>解決策

ファイアウォール規則を構成する方法は、必要な VM へのアクセスのレベルによって異なります。 次の例では、RDPを使用しています。 ただし、適切なレジストリ キーをポイントして、他の種類のトラフィックに同じメソッドを適用できます。

### <a name="online-troubleshooting"></a>オンライン トラブルシューティング 

#### <a name="mitigation-1-custom-script-extension"></a>軽減策 1: カスタム スクリプト拡張機能

1.  次のテンプレートを使用して、スクリプトを作成します。

    *   ルールを有効にする：
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   ルールを無効にする：
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Azure portal で、[カスタム スクリプト拡張機能](../extensions/custom-script-windows.md)を使用してこのスクリプトをアップロードします。 

#### <a name="mitigation-2-remote-powershell"></a>軽減策 2: リモート PowerShell

VM がオンラインで、同じ仮想ネットワーク上の別の VM でアクセスできる場合、もう一方の VM を使用して次の軽減策を行うことができます。

1.  トラブルシューティング用 VM で、PowerShell コンソール ウィンドウを開きます。

2.  必要に応じて、次のコマンドを実行します。

    *   ルールを有効にする：
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   ルールを無効にする：
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>軽減策 3: PSTools コマンド

VM がオンラインで、同じ仮想ネットワーク上の別の VM でアクセスできる場合、もう一方の VM を使用して次の軽減策を行うことができます。

1.  トラブルシューティング用の VM で、[PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)をダウンロードします。

2.  CMD インスタンスを開き、内部 IP (DIP) を通してVM にアクセスします。 

    * ルールを有効にする：
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   ルールを無効にする：
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>軽減策 4: リモート レジストリ

VM がオンラインにあり、同じ仮想ネットワーク上の別の VM でアクセスできる場合、他の VM 上の[リモート レジストリ](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry)を使用できます。

1.  トラブルシューティング用 VM で、レジストリ エディター (regedit.exe) を起動し、**ファイル** > **ネットワーク レジストリへの接続**を選択します。

2.  *ターゲット マシン*\SYSTEM 分岐を開き、次の値を指定します:

    * ルールを有効にするには、次のレジストリ値を開きます：
    
        *ターゲット マシン*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        次に、文字列で**Active = FALSE**を**Active = TRUE**に変更します：

        **v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|**
    
    * ルールを無効にするには、次のレジストリ値を開きます：
    
        *ターゲット マシン*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        次に、**Active = TRUE**を**Active = FALSE**に変更:
        
        **v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|**

3.  変更を適用するには VM を再起動します。

### <a name="offline-troubleshooting"></a>オフライン トラブルシューティング 

どのメソッドでも VM に到達できない場合、カスタム スクリプト拡張機能は使えなくなり、オフライン モードでシステム ディスクから直接作業する必要があります。

これらの手順を行う前に、バックアップとして、影響を受ける VM のシステム ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。

1.  [復旧 VM にシステム ディスクを取り付ける](troubleshoot-recovery-disks-portal-windows.md)。

2.  復旧 VM へのリモート デスクトップ接続を開始します。

3.  ディスクが [ディスクの管理] コンソールで **[オンライン]** になっていることを確認します。 接続された システム ディスクに割り当てられたドライブ文字をメモします。

4.  変更を行う前に、変更のロールバックが必要な場合に備えて、\windows\system32\config フォルダーのコピーを作成します。

5.  トラブルシューティング用 VM で、レジストリ エディター (regedit.exe) を起動します。

6.  **HKEY_LOCAL_MACHINE** キーを選択し、メニューから **[ファイル]**  >  **[ハイブの読み込み]** を選択します。

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  \Windows\system32\config\SYSTEM ファイルを見つけて開きます。 

    > [!Note]
    > 名前を求められます。 「**BROKENSYSTEM**」と入力し、**HKEY_LOCAL_MACHINE** を展開します。 これにより、**BROKENSYSTEM** という名前の追加キーが表示されます。 このトラブルシューティングでは、**BROKENSYSTEM** として、これらの問題のあるハイブをマウントします。

8.  BROKENSYSTEM ブランチでは、次の変更を加えます：

    1.  どの**ControlSet**レジストリ キーから VM が開始するかを確認します。 HKLM\BROKENSYSTEM\Select\Current でキー番号が表示されます。

    2.  ルールを有効にするには、次のレジストリ値を開きます：
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        次に、**Active = FALSE**を**Active = True**に変更します。
        
        **v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|**

    3.  ルールを無効にするには、次のレジストリ キーを開きます：

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        次に、**Active = True**を**Active = FALSE**に変更します。
        
        **v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|**

9.  **BROKENSYSTEM** を選択し、メニューから **[ファイル]**  >  **[ハイブのアンロード]** を選択します。

10. [システム ディスクをデタッチし、VM を再作成します](troubleshoot-recovery-disks-portal-windows.md)。

11. 問題が解決しているかどうかを確認します。
