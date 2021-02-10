---
title: VM の起動が停止し、"Windows の準備をしています。 コンピューターの電源を切らないでください。" が表示される (Azure 内) | Microsoft Docs
description: 次のメッセージで停止する問題をトラブルシューティングするためのステップを紹介します。VM の起動が停止し、"Windows の準備をしています。 コンピューターの電源を切らないでください。" が表示される
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: f3e4d51b4d41fa0dc23e9b12ac0251c14215de5c
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633012"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM の起動が停止し、"Windows の準備をしています。 コンピューターの電源を切らないでください。" が表示される (Azure 内)

この記事では、Microsoft Azure で Windows 仮想マシン (VM) を起動するときに発生する可能性のある "準備しています" 画面および "Windows の準備をしています" 画面について説明します。 また、サポート チケットのためのデータを収集する手順について説明します。

 

## <a name="symptoms"></a>現象

Windows VM が起動しません。 **起動診断** を利用して VM のスクリーンショットを取得すると、VM に "準備しています" または "Windows の準備をしています" というメッセージが表示されることがあります。

![Windows Server 2012 R2 でのメッセージの例](./media/troubleshoot-vm-configure-update-boot/message1.png)

![メッセージの例](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>原因

この問題は通常、構成の変更後にサーバーが最終リブートを行っている場合に発生します。 構成の変更は、Windows Update またはサーバーのロール/機能に関する変更によって初期化される可能性があります。 Windows Update では、更新のサイズが大きかった場合、オペレーティング システムが変更を再構成するまでに必要な時間が長くなります。

## <a name="collect-an-os-memory-dump"></a>OS のメモリ ダンプの収集

> [!TIP]
> VM の最新のバックアップがある場合は、[そのバックアップから VM の復元](../../backup/backup-azure-arm-restore-vms.md)を試みて、起動の問題を修正できます。

変更が処理されるまで待っても問題が解決されないときは、場合によっては、メモリ ダンプ ファイルを収集し、サポートに問い合わせる必要があります。 ダンプ ファイルを収集するには、次の手順を実行します。

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>復旧 VM に OS ディスクを接続する

1. バックアップとして、影響を受ける VM の OS ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。
2. [復旧 VM に OS ディスクを接続します](./troubleshoot-recovery-disks-portal-windows.md)。
3. 復旧 VM にリモート デスクトップ接続します。 
4. OS ディスクが暗号化されている場合、次の手順に進む前に暗号化をオフにする必要があります。 詳細については、[起動できない VM で暗号化されている OS ディスクを復号する](troubleshoot-bitlocker-boot-error.md#solution)方法に関するページを参照してください。

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>ダンプ ファイルを探してサポート チケットを送信する

1. 復旧 VM で、接続されている OS ディスクの Windows フォルダーに移動します。 接続されている OS ディスクに割り当てられているドライブ文字が F の場合は、F:\Windows に移動する必要があります。
2. memory.dmp ファイルを探し、ダンプ ファイルと共に[サポート チケットを送信](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。 

ダンプ ファイルが見つからない場合は、ダンプ ログとシリアル コンソールを有効にする次の手順に進みます。

### <a name="enable-dump-log-and-serial-console"></a>ダンプ ログとシリアル コンソールを有効にする

ダンプ ログとシリアル コンソールを有効にするには、次のスクリプトを実行します。

1. 管理者特権のコマンド プロンプト セッション ([管理者として実行]) を開きます。
2. 次のスクリプトを実行します。

    このスクリプトでは、接続されている OS ディスクに割り当てられているドライブ文字が F であると想定しています。実際の VM の適切な値に置き換えてください。

    ```powershell
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

    1. この VM に選択しているサイズに応じて、RAM と同じサイズのメモリを割り当てることができる十分な領域がディスク上にあることを確認します。
    2. 十分な領域がない場合、または大きなサイズの VM (G、GS、または E シリーズ) の場合、このファイルが作成される場所を変更し、VM に接続されている他のデータ ディスクに任せることができます。 このために、以下のキーを変更する必要があります。
    
        ```console
        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

        reg unload HKLM\BROKENSYSTEM
        ```

3. [OS ディスクを切断し、影響を受ける VM に OS ディスクを接続し直します](./troubleshoot-recovery-disks-portal-windows.md)。
4. VM を起動し、シリアル コンソールにアクセスします。
5. **[マスク不可能割り込み (NMI) を送信する]** を選択し、メモリ ダンプをトリガーします。
    ![マスク不可能割り込みを送信する場所に関するイメージ](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. OS ディスクを復旧 VM にもう一度アタッチし、ダンプ ファイルを収集します。

## <a name="contact-microsoft-support"></a>Microsoft サポートに問い合わせる

ダンプ ファイルを収集した後、根本原因を分析するために [Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)に問い合わせてください。
