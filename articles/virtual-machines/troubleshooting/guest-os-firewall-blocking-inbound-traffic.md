---
title: Azure VM のゲスト OS のファイアウォールがインバウンド トラフィックをブロックしている | Microsoft Docs
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
ms.openlocfilehash: 0a0da446385c592bfeda2e01e209ef1fb75b7de3
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213113"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Azure VM のゲスト OS のファイアウォールがインバウンド トラフィックをブロックしている

この記事では、ゲスト オペレーティング システムのファイアウォールによりインバウンド トラフィックがブロックされた場合に発生するリモート デスクトップ ポータル (RDP) の問題を修正する方法を説明します。

## <a name="symptoms"></a>現象

Azure 仮想マシン (VM) に接続するための RDP 接続を利用できません。 [ブート診断] から [スクリーンショット] にアクセスすると、ようこそ画面 (Ctrl + Alt + Del) でオペレーティング システムが完全に読み込まれています。

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

RDP 規則が RDP トラフィックを許可する設定になっていません。

### <a name="cause-2"></a>原因 2

ゲスト システムのファイアウォール プロファイルが、RDP トラフィックを含めたすべてのインバウンド接続をブロックする設定になっています。

![ファイアウォール設定](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>解決策

これらの手順を行う前に、バックアップとして、影響を受ける VM のシステム ディスクのスナップショットを取得します。 詳細については、 [ディスクのスナップショット作成](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。

この問題を解決するには、[リモート ツールを使用して Azure VM に関する問題のトラブルシューティングを行う方法](remote-tools-troubleshoot-azure-vm-issues.md)に関する記事で紹介されている方法のいずれかを使用して VM にリモート接続したうえで、ゲスト オペレーティング システムのファイアウォール規則を、RDP トラフィックを**許可**するように編集します。

### <a name="online-troubleshooting"></a>オンライン トラブルシューティング

[シリアル コンソールに接続し、PowerShell インスタンスを開きます](serial-console-windows.md#use-cmd-or-powershell-in-serial-console)。 VM でシリアル コンソールが有効になっていない場合は、「[VM をオフライン修復する](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)」をご覧ください。

#### <a name="mitigation-1"></a>対応策 1

1.  Azure エージェントが VM にインストールされ、正しく動作している場合には、VM メニューの **[サポート + トラブルシューティング]** > **[パスワードのリセット]** で [構成のみのリセット] オプションを利用できます。

2.  この回復オプションで行われることは、次のとおりです。

    *   RDP コンポーネントを有効にします (無効になっている場合)。

    *   Windows ファイアウォール プロファイルを有効にします。

    *   Windows ファイアウォールで RDP 規則がオンになっていることを確認します。

    *   ここまでの手順で問題が解決しない場合には、ファイアウォール規則を手動でリセットします。 これには次のコマンドを実行し、"Remote Desktop" という名前が含まれる規則をすべて表示するクエリを実行します。

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        RDP ポートが 3389 以外のポートに設定されていた場合には、作成されてこのポートに設定されている可能性があるカスタム規則を探す必要があります。 カスタム ポートが設定されているインバウンド規則をすべて表示するクエリを実行するには、次のコマンドを実行します。

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  規則が無効になっている場合には、有効にします。 組み込みのリモート デスクトップ グループなど、グループ全体を開くには、次のコマンドを実行します。

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    これに対して、特定のリモート デスクトップ (TCP 受信) 規則を開くには、次のコマンドを実行します。

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  トラブルシューティングのために、ファイアウォール プロファイルをオフにすることができます。

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    トラブルシューティングが完了し、ファイアウォールを正しく設定した後は、ファイアウォールをもう一度有効にします。

    > [!Note]
    > 変更を適用するために VM を再起動する必要はありません。

5.  RDP 接続を使って VM にアクセスしてみます。

#### <a name="mitigation-2"></a>対応策 2

1.  ファイアウォール プロファイルを表示するクエリを実行し、インバウンド ファイアウォール ポリシーが [ *BlockInboundAlways*] に設定されているかどうかを確認します。

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > 次のガイドラインは、設定に応じてファイアウォール ポリシーに適用されるものです。
    >    * *BlockInbound*:インバウンド トラフィックを許可する規則が有効になっていないと、インバウンド トラフィックがすべてブロックされます。
    >    * *BlockInboundAlways*:ファイアウォール規則がすべて無視され、トラフィックがすべてブロックされます。

2.   *DefaultInboundAction*  を編集し、トラフィックを **許可** するようにこれらのプロファイルを設定します。 そのためには、次のコマンドを実行します。

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  プロファイルを表示するクエリをもう一度実行し、変更が正常にできていることを確認します。 そのためには、次のコマンドを実行します。

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > 変更を適用するために VM を再起動する必要はありません。

4.  RDP を使用して VM にもう一度アクセスしてみます。

### <a name="offline-mitigations"></a>オフラインの対応策

1.  [復旧 VM にシステム ディスクを取り付ける](troubleshoot-recovery-disks-portal-windows.md)。

2.  復旧 VM へのリモート デスクトップ接続を開始します。

3.  ディスクが ディスクの管理コンソールで  **オンライン**  の印がついていることを確認します。 アタッチしたシステム ディスクに割り当てられているドライブ文字をメモします。

#### <a name="mitigation-1"></a>対応策 1

 [ゲスト OS 上のファイアウォール規則を有効または無効にする方法](enable-disable-firewall-rule-guest-os.md)に関するページを参照してください。

#### <a name="mitigation-2"></a>対応策 2

1.  [復旧 VM にシステム ディスクを取り付ける](troubleshoot-recovery-disks-portal-windows.md)。

2.  復旧 VM へのリモート デスクトップ接続を開始します。

3.  システム ディスクを回復 VM にアタッチしたら、[ディスクの管理] コンソールでそのディスクに  **[オンライン]**  のフラグが設定されていることを確認します。 接続された OS ディスクに割り当てられたドライブ文字をメモします。

4.  CMD インスタンスを管理者特権で開き、次のスクリプトを実行します。

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [システム ディスクをデタッチして、VM を再作成します](troubleshoot-recovery-disks-portal-windows.md)。

6.  問題が解決しているかどうかを確認します。
