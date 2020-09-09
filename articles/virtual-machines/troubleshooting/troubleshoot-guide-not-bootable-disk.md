---
title: ブート エラー "This is not a bootable disk" (これは起動可能なディスクではありません)
description: この記事では、Azure Virtual Machine のディスクが起動できない問題を解決する手順を説明します。
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 16f6919577955bda5b04db26deb9fe78a467e364
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509037"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>ブート エラー This is not a bootable disk (これは起動可能なディスクではありません)

この記事では、Azure Virtual Machine (VM) のディスクが起動できない問題を解決する手順を説明します。

## <a name="symptoms"></a>現象

[起動診断](./boot-diagnostics.md)を利用して VM のスクリーンショットを表示すると、スクリーンショットのプロンプトに "This is not a bootable disk. Please insert a bootable floppy and press any key to try again..." (これは起動可能なディスクではありません。起動可能なフロッピーを挿入し、任意のキーを押して、もう一度やり直してください...) というメッセージが表示されます。

   図 1

   ![図 1 の *"これは起動可能なディスクではありません。 起動可能なフロッピーを挿入し、任意のキーを押して、もう一度やり直してください..."* というメッセージ](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>原因

このエラー メッセージは、OS のブート プロセスがアクティブなシステム パーティションを見つけることができなかったことを意味します。 または、ブート構成データ (BCD) ストアに参照が欠落していることにより、Windows パーティションが特定できなかったことを意味する場合もあります。

## <a name="solution"></a>解決策

### <a name="process-overview"></a>プロセスの概要

1. 修復 VM を作成してアクセスします。
2. パーティションの状態を Active に設定します。
3. ディスク パーティションを修正します。
4. **[推奨]** : VM を再構築する前に、シリアル コンソールとメモリ ダンプの収集を有効にします。
5. 元の VM を再構築します。

   > [!NOTE]
   > このブート エラーが発生する場合、ゲスト OS は動作しなくなります。 この問題を解決するには、オフライン モードでトラブルシューティングを行います。

### <a name="create-and-access-a-repair-vm"></a>修復 VM の作成とアクセス

1. [仮想マシンの修復コマンド](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)に関する説明の手順 1 から 3 に従い、修復 VM を準備します。
2. リモート デスクトップ接続を使用し、修復 VM に接続します。

### <a name="set-partition-status-to-active"></a>パーティションの状態を Active に設定する

第 1 世代の VM を使用している場合は、BCD ストアを保持している OS パーティションが "*アクティブ*" とマークされていることを確認する必要があります。 第 2 世代の VM を使用している場合は、「[ディスク パーティションの修正](#fix-the-disk-partition)」に進んでください。これは、後の世代では *Status* フラグが非推奨となっているためです。

1. 管理者特権でコマンド プロンプト *(cmd.exe)* を開きます。
2. *diskpart* と入力して、Diskpart ツールを起動します。
3. *list disk* と入力して、システム ディスクの一覧を表示し、接続されている OS VHD を特定します。
4. 接続された OS VHD が見つかったら、*sel disk #* と入力して、ディスクを選択します。  接続されている OS VHD が Disk 1 である図 2 を確認してください。

   図 2

   ![図 2 では、list disk のコマンド出力を示す *DISKPART* ウィンドウを示しています。テーブルでは、Disk 0、Disk 1 を示しています。  sel disk 1 のコマンド出力も示されています。Disk 1 が選択されたディスクです。](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. ディスクを選択したら、*list partition* と入力して、選択したディスクのパーティションを一覧表示します。
6. ブート パーティションを特定したら、*sel partition #* と入力してパーティションを選択します。  通常、ブート パーティションのサイズは約 350 MB です。  図 3 で Partition 1 がブート パーティションになっていることを確認してください。

   図 3

   ![図 3 の *list partition* のコマンド出力を示す *DISKPART* ウィンドウ。 テーブルには、Partition 1 と Partition 2 があります。 また、*sel partition 1* のコマンド出力で、ディスクとして Partition 1 が選択されています。](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. 'detail partition' と入力し、パーティションの状態を確認します。 図 4 の *Active:No* になっているパーティション、または、図 5 の 'Active:Yes' になっているパーティションを確認してください。

   図 4

   ![図 4 は、Partition 1 が *Active: No* に設定されているときの *detail partition* のコマンド出力を示す *DISKPART* ウィンドウを示します。](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   図 5

   ![図 5 の *detail partition* のコマンド出力を示す *DISKPART* ウィンドウ。Partition 1 は *Active:Yes* に設定されています。](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. パーティションが**アクティブでない**場合、*active* と入力して *Active* フラグを変更します。
9. *detail partition* と入力して、状態が正しく変更されたことを確認します。

   図 6

   ![図 6 の *detail partition* のコマンド出力を示す diskpart ウィンドウ。Partition 1 は *Active:はい*](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. *exit* と入力して DISKPART ツールを終了し、ご自分の構成の変更を保存します。

### <a name="fix-the-disk-partition"></a>ディスク パーティションの修正

1. 管理者特権でコマンド プロンプト (cmd.exe) を開きます。
2. 次のコマンドを使用して、ディスク上で *CHKDSK* を実行し、エラーを修正します。

   `chkdsk <DRIVE LETTER>: /f`

   '/f' コマンド オプションを追加すると、ディスク上のすべてのエラーが修正されます。 <DRIVE LETTER> は、接続されている OS VHD の文字に置き換えてください。

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>推奨:VM を再構築する前に、シリアル コンソールとメモリ ダンプの収集を有効にします。

メモリ ダンプ コレクションとシリアル コンソールを有効にするには、次のスクリプトを実行します。

1. 管理者特権でコマンド プロンプト セッション (管理者として実行) を開きます。
2. 次のコマンドを実行します。

   シリアル コンソールの有効化

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. OS ディスクの空き領域が、VM 上のメモリ サイズ (RAM) の容量と同じであることを確認します。

   OS ディスク上に十分な領域がない場合、メモリ ダンプ ファイルが作成される場所を変更し、それを十分な空き領域がある VM に接続された任意のデータ ディスクに参照させます。 場所を変更するには、"%SystemRoot%" を次のコマンドでデータ ディスクのドライブ文字 (たとえば、"F:") に置き換えます。

#### <a name="suggested-configuration-to-enable-os-dump"></a>OS ダンプを有効にするための推奨構成

**破損した OS ディスクのロード**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**ControlSet001 での有効化:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**ControlSet002 での有効化:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**破損した OS ディスクのアンロード**:

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>元の VM の再構築

[仮想マシンの修復コマンドの手順 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) の説明に従って、VM を再構成します。
