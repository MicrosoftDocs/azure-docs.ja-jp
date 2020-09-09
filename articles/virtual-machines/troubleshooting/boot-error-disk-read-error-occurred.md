---
title: ブート エラーのトラブルシューティング - ディスク読み取りエラーが発生しました
description: この記事では、Azure VM でディスクを読み取ることができない問題を解決する手順を説明します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: f59903ed111be1fe414f4b3ded250d754c91d323
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069154"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>ブート エラーのトラブルシューティング - ディスク読み取りエラーが発生しました

この記事では、Azure 仮想マシン (VM) でディスクを読み取ることができない問題を解決する手順を説明します。

## <a name="symptoms"></a>現象

[起動診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、スクリーンショットには次のようなメッセージが表示されます: "ディスク読み取りエラーが発生しました。 Press Ctrl+Alt+Del to restart' has been found for your virtual machine. (オペレーティング システムが見つかりませんでした。オペレーティング システムが含まれていないすべてのドライバーを切断してください。再起動するには Ctrl + Alt + Del キーを押してください)" が表示されます。

   ![エラー メッセージ:ディスク読み取りエラーが発生しました。 Ctrl+Alt+Del キーを押して再起動してください。](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>原因

このエラー メッセージは、ディスクの構造が破損していて読み取ることができないことを示します。 第 1 世代の VM を使用している場合は、ブート構成データが含まれるディスク パーティションが**アクティブ**に設定されていない可能性もあります。

## <a name="solution"></a>解決策

### <a name="process-overview"></a>プロセスの概要

1. 修復 VM を作成してアクセスします。
1. 解決策を選択します。
   - [パーティションの状態をアクティブに設定する](#set-partition-status-to-active)
   - [ディスク パーティションを修正する](#fix-the-disk-partition)
1. シリアル コンソールとメモリ ダンプの収集を有効にします。
1. VM を再構築します。

> [!NOTE]
> このブート エラーが発生する場合、ゲスト オペレーティング システム (OS) は動作しなくなります。 この問題を解決するには、オフライン モードでトラブルシューティングを行います。

### <a name="create-and-access-a-repair-vm"></a>修復 VM を作成してアクセスする

1. [仮想マシンの修復コマンド](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)に関する説明の手順 1 から 3 に従い、修復 VM を準備します。
1. リモート デスクトップ接続を使用して、修復 VM に接続します。

### <a name="set-partition-status-to-active"></a>パーティションの状態をアクティブに設定する

第 1 世代の VM を使用している場合は、BCD ストアを保持している OS パーティションが**アクティブ**としてマークされていることを確認する必要があります。 第 2 世代の VM を使用している場合は、「[ディスク パーティションを修正する](#fix-the-disk-partition)」に進んでください。状態フラグは後の世代で非推奨になっています。

1. 管理者特権でコマンド プロンプト (cmd.exe) を開きます。
1. 「**diskpart**」と入力して、**DISKPART** ツールを起動します。
1. 「**list disk**」と入力して、システム上のディスクの一覧を表示し、接続されている OS 仮想ハード ディスク (VHD) を確認します。
1. 接続された OS VHD が見つかったら、**sel disk #** と入力して、ディスクを選択します。 Disk 1 が接続された OS VHD である例については、次の図を参照してください。

   ![Disk 0 と Disk 1 がテーブルに表示されている、diskpart の **list disk** コマンドの出力ウィンドウ。 ウィンドウには、**sel disk 1** コマンドの出力も示されており、Disk 1 が選択されたディスクになっています](./media/disk-read-error-occurred/2.png)

1. ディスクを選択したら、**list partition** と入力して、選択したディスクのパーティションを一覧表示します。
1. ブート パーティションを特定したら、**sel partition #** と入力してパーティションを選択します。 ブート パーティションのサイズは、多くの場合、約 350 MB です。  たとえば、Partition 1 がブート パーティションである次の図を参照してください。

   ![Partition 1 と Partition 2 がテーブルに表示されている、diskpart の **list partition** コマンドの出力ウィンドウ。 ウィンドウには、**sel partition 1** コマンドの出力も示されており、Partition 1 が選択されたディスクになっています。](./media/disk-read-error-occurred/3.png)

1. 「**detail partition**」と入力して、パーティションの状態を確認します。 パーティショの設定の例については、次のスクリーンショットを参照してください: **Active: No** または **Active: Yes**。

   **Active: No**

   ![diskpart の **detail partition** コマンドの出力ウィンドウ。Partition 1 が **Active: No** に設定されています。](./media/disk-read-error-occurred/4.png)

   **Active: Yes**

   ![diskpart の **detail partition** コマンドの出力ウィンドウ。Partition 1 が **Active: Yes** に設定されています。](./media/disk-read-error-occurred/5.png)

1. パーティションが**アクティブ**に設定されていない場合は、「**active**」と入力してアクティブ フラグを変更します。
1. 「**detail partition**」と入力して、状態の変更が正常に完了したことを確認し、出力に以下が含まれることを確認します: **Active: Yes**。 
1. **exit** と入力して DISKPART ツールを終了し、ご自分の構成の変更を保存します。

### <a name="fix-the-disk-partition"></a>ディスク パーティションを修正する

1. 管理者特権でコマンド プロンプト (cmd.exe) を開きます。
1. 次のコマンドを使用して、ディスクに対して **CHKDSK** を実行し、エラーの修正を行います。

   `chkdsk <DRIVE LETTER>: /f`

   **/f** コマンド オプションを追加すると、ディスク上のすべてのエラーが修正されます。 **< DRIVE LETTER >** は、接続されている OS VHD の文字に置き換えます。

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>シリアル コンソールとメモリ ダンプの収集を有効にする

**[推奨]** : VM を再構築する前に、次のスクリプトを実行してシリアル コンソールとメモリ ダンプの収集を有効にします。

1. 管理者として、管理者特権でのコマンド プロンプト セッションを開きます。
1. 次のコマンドを実行します。

   **シリアル コンソールを有効にする**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. OS ディスクの空き領域が、VM のメモリ サイズ (RAM) よりも大きいことを確認します。

   OS ディスクに十分な領域がない場合は、メモリ ダンプ ファイルが作成される場所を変更し、その場所で十分な空き領域がある VM に接続された任意のデータ ディスクを参照します。 場所を変更するには、次のコマンドで、 **%SystemRoot%** をデータ ディスクのドライブ文字 (**F:** など) に置き換えます。

   OS ダンプを有効にするための推奨構成:

   **破損した OS ディスクからのレジストリ ハイブのロード**:

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **ControlSet001 での有効化:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **ControlSet002 での有効化:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **破損した OS ディスクのアンロード:**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>VM を再構築する

[VM 修復コマンドの手順 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) を使用して、VM を再構築します。
