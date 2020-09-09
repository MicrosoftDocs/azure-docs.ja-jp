---
title: ポリシーの適用時に Azure 仮想マシンが応答しない
description: この記事では、Azure VM での起動中にポリシーを適用しているときに、読み込み画面が応答しない問題を解決する手順について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: cbf2fe491e1fe0b553eab04ca7190da0413a3ba6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526012"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>グループ ポリシーの [ローカル ユーザーとグループ] ポリシーの適用時に VM が応答しない

この記事では、Azure 仮想マシン (VM) が起動中にポリシーを適用しているときに、読み込み画面が応答しない問題を解決する手順について説明します。

## <a name="symptoms"></a>現象

[ブート診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、読み込み中に画面が停止して次のメッセージが表示されます:"グループ ポリシーの [ローカル ユーザーとグループ] ポリシーの適用中"。

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="グループ ポリシーのローカル ユーザーとグループ ポリシーの適用中 の読み込みの画面 (Windows Server 2012 R2)。":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="グループ ポリシーのローカル ユーザーとグループ ポリシーの適用中 の読み込みの画面 (Windows Server 2012)。":::

## <a name="cause"></a>原因

ポリシーによって古いユーザー プロファイルのクリーンアップが行われるときに、ロックの競合が発生しています。

> [!NOTE]
> これは、Windows Server 2012 および Windows Server 2012 R2 にのみ当てはまります。

問題のあるポリシーを次に示します。

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>解決方法

### <a name="process-overview"></a>プロセスの概要

1. [修復 VM を作成してアクセスする](#step-1-create-and-access-a-repair-vm)
1. [ポリシーを無効にする](#step-2-disable-the-policy)
1. [シリアル コンソールとメモリ ダンプの収集を有効にする](#step-3-enable-serial-console-and-memory-dump-collection)
1. [VM を再構築する](#step-4-rebuild-the-vm)

> [!NOTE]
> このブート エラーが発生すると、ゲスト OS は動作しなくなります。 オフライン モードでトラブルシューティングを行う必要があります。

### <a name="step-1-create-and-access-a-repair-vm"></a>手順 1:修復 VM を作成してアクセスする

1. [仮想マシンの修復コマンドの手順 1 から 3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) を使用して、修復 VM を準備します。
2. リモート デスクトップ接続を使用して、修復 VM に接続します。

### <a name="step-2-disable-the-policy"></a>手順 2:ポリシーを無効にする

1. 修復 VM で、レジストリ エディターを開きます。
1. **HKEY_LOCAL_MACHINE** キーを見つけて、メニューから **[ファイル]**  >  **[ハイブの読み込み]** の順に選択します。

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="強調表示された HKEY_LOCAL_MACHINE と [ハイブの読み込み] が表示されているメニューのスクリーンショット。":::

    - [ハイブの読み込み] を使用すると、オフライン システムからレジストリ キーを読み込むことができます。 この場合、システムは修復 VM に接続されている破損したディスクです。
    - システム全体の設定は `HKEY_LOCAL_MACHINE` に格納され、"HKLM" と略記できます。
1. 接続されたディスクで、`\windows\system32\config\SOFTWARE` ファイルにアクセスして開きます。

    1. 名前の入力を求められたら、「BROKENSOFTWARE」と入力します。
    1. BROKENSOFTWARE が読み込まれたことを確認するには、**HKEY_LOCAL_MACHINE** を展開し、追加した BROKENSOFTWARE キーを探します。
1. BROKENSOFTWARE に移動し、読み込まれたハイブに CleanupProfile キーが存在するかどうかを確認します。

    1. キーが存在する場合は、CleanupProfile ポリシーが設定されています。 その値は、保持ポリシーを日数で表します。 キーの削除を続行します。
    1. キーが存在しない場合、CleanupProfile ポリシーは設定されていません。 接続されている OS ディスクの Windows ディレクトリにある memory.dmp ファイルを含めて、[サポート チケットを送信します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

1. 次のコマンドを使用して、CleanupProfiles キーを削除します。

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
1.  次のコマンドを使用して、BROKENSOFTWARE ハイブをアンロードします。

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>手順 3:シリアル コンソールとメモリ ダンプの収集を有効にする

メモリ ダンプの収集とシリアル コンソールを有効にするには、次のスクリプトを実行します。

1. 管理者特権でコマンド プロンプト セッションを開きます。 (管理者として実行します。)
1. 次の各コマンドを実行して、シリアル コンソールを有効にします。
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. OS ディスクの空き領域が、VM のメモリ サイズ (RAM) 以上であるかどうかを確認します。

    OS ディスクに十分な空き領域がない場合には、メモリ ダンプの場所を変更し、接続されている十分な空き容量があるデータ ディスクを参照するようにしてください。 場所を変更するには、次のコマンドで "%SystemRoot%" をデータ ディスクのドライブ文字 (たとえば、"F:") に置き換えます。

    **OS ダンプを有効にするための推奨構成**

    破損した OS ディスクを読み込む:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Enable on ControlSet001: (ControlSet001 で有効にする)
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    Enable on ControlSet002: (ControlSet002 で有効にする)
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```
    
    Unload broken OS disk: (破損した OS ディスクをアンロードする)

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="step-4-rebuild-the-vm"></a>手順 4:VM を再構築する

[VM 修復コマンドのステップ 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) を使って、VM を再構成します。

問題が解決された場合、ポリシーはローカルで無効になっています。 永続的な解決策としては、VM で CleanupProfiles ポリシーを使用しないでください。 別の方法を使用してプロファイルのクリーンアップを実行してください。

このポリシーは使用しないでください:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>次のステップ

Windows Update の適用時に問題が発生した場合は、「[Windows Update の適用時に、Azure VM が C01A001D エラーで応答しません](./unresponsive-vm-apply-windows-update.md)」を参照してください。
