---
title: Windows STOP エラー -
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 8279aee4a0df8155b23e6f984d259bf92555dc95
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632835"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows STOP エラー - #0x000000EF "Critical Process Died"

この記事では、Azure VM での起動中に重要なプロセスが停止した問題を解決する手順について説明します。

## <a name="symptom"></a>症状

[起動診断](./boot-diagnostics.md)を利用して VM のスクリーンショットを表示すると、スクリーンショットにエラー *#0x000000EF* と共に *「Critical Process Died」* (重要なプロセスが停止しました) というメッセージが表示されます。

!["問題が発生したため、PC を再起動する必要があります。 エラー情報を収集しています。再起動できます。 (##% 完了) 詳細については、次のエラーを後からオンラインで検索してください:0x000000EF"](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>原因

通常、これは、起動時に障害が発生した重要なシステム プロセスが原因です。 重要なプロセスの問題の詳細については、[バグチェック 0xEF:CRITICAL_PROCESS_DIED](/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died) に関するページをご覧ください。

## <a name="solution"></a>解決策

### <a name="process-overview"></a>プロセスの概要:

> [!TIP]
> VM の最新のバックアップがある場合は、[そのバックアップから VM の復元](../../backup/backup-azure-arm-restore-vms.md)を試みて、起動の問題を修正できます。

1. 修復 VM を作成してアクセスします。
2. OS の破損がある場合は修正します。
3. **[推奨]** : VM を再構築する前に、シリアル コンソールとメモリ ダンプの収集を有効にします。
4. VM を再構築します。

> [!NOTE]
> このブート エラーが発生する場合、ゲスト OS は動作しなくなります。 この問題を解決するには、オフライン モードでトラブルシューティングを行います。

### <a name="create-and-access-a-repair-vm"></a>修復 VM を作成してアクセスする

1. [VM 修復コマンドの手順 1 から 3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) を使用して、修復 VM を準備します。
2. リモート デスクトップ接続を使用し、修復 VM に接続します。

### <a name="fix-any-os-corruption"></a>OS の破損がある場合は修正する

1. 管理者特権のコマンド プロンプトを開きます。
2. 次のシステム ファイル チェッカー (SFC) コマンドを実行します。

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * < BOOT DISK DRIVE > は修復 VM のブート ボリューム (通常は "C:") であり、< BROKEN DISK DRIVE > は破損した VM から接続されているディスクのドライブ文字になります。 不等号記号と、その中に含まれるテキスト (例: "< text >") を適切な文字に置き換えます。

3. 次に、[VM 修復コマンドの手順 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) を使用して、VM を再構成し、起動するかどうか確認します。
4. それでも VM が起動しない場合は、引き続きメモリ ダンプ ファイルの収集を行います。

### <a name="collect-the-memory-dump-file"></a>メモリ ダンプ ファイルを収集する

SFC の実行後も問題が解決しない場合は、問題の原因を特定するためにメモリ ダンプ ファイルを分析する必要があります。 メモリ ダンプ ファイルを収集するには、次の手順を実行します。

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>新しい修復 VM に OS ディスクを接続する

1. [VM 修復コマンドの手順 1 から 3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) を使用して、新しい修復 VM を準備します。
2. リモート デスクトップ接続を使用し、修復 VM に接続します。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>ダンプ ファイルを探してサポート チケットを送信する

3. 復旧 VM で、接続されている OS ディスクの Windows フォルダーに移動します。 接続されている OS ディスクに割り当てられているドライブ文字が *F* の場合は、*F:\Windows* に移動する必要があります。
4. *memory.dmp* ファイルを探し、メモリ ダンプ ファイルと共に [サポート チケットを送信](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。

   > [!NOTE]
   > ダンプ ファイルが見つからない場合は、以下の手順を実行してメモリ ダンプ収集とシリアル コンソールを有効にします。その後、このセクションに戻り、上記のタスクの手順を繰り返してメモリ ダンプ ファイルを収集します。

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>推奨:VM を再構築する前に、シリアル コンソールとメモリ ダンプの収集を有効にする

メモリ ダンプ コレクションとシリアル コンソールを有効にするには、次のスクリプトを実行します。

1. 管理者特権でコマンド プロンプト セッション (管理者として実行) を開きます。
2. 次のコマンドを実行します。

   シリアル コンソールを有効にする

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   不等号とそこに含まれるテキスト (例: "< text here >") を置き換えます。

3. OS ディスクの空き領域が、VM のメモリ サイズ (RAM) の容量と同じであることを確認します。

OS ディスクに十分な領域がない場合、メモリ ダンプ ファイルが作成される場所を変更し、その場所で十分な空き領域がある VM に接続された任意のデータ ディスクを参照する必要があります。 場所を変更するには、"%SystemRoot%" を次のコマンドでデータ ディスクのドライブ文字 (たとえば、"F:") に置き換えます。

#### <a name="suggested-configuration-to-enable-os-dump"></a>OS ダンプを有効にするための推奨構成

**破損した OS ディスクのロード:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**ControlSet001 での有効化:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**ControlSet002 での有効化:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**破損した OS ディスクのアンロード:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>元の VM を再構築する

[VM 修復コマンドの手順 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) を使用して、VM を再構成します。
