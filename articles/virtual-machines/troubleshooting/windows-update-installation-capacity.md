---
title: OS の起動に関するトラブルシューティングを行う - Windows Update のインストール容量
description: Windows Update (KB) でエラーが発生し、応答しなくなった問題を Azure VM で解決するための手順。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 0c0ec45eee86031e1533b97ccf352de0ecf70e38
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633156"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>OS の起動に関するトラブルシューティングを行う - Windows Update のインストール容量

この記事では、Windows Update (KB) でエラーが発生し、応答しなくなった問題を Azure 仮想マシン (VM) で解決する手順について説明します。

## <a name="symptom"></a>症状

起動診断を使用して VM のスクリーンショットを表示すると、そのスクリーンショットに、進行中の Windows Update (KB) が表示されますが、エラー コード **C01A001D** で失敗します。 次の図には、Windows Update (KB) がスタックしており、"エラー C01A001D 更新操作 #####/##### の適用中 (######)" というメッセージが示されています。

![Windows Update (KB) がスタックしており、"エラー C01A001D 更新操作 X/Y の適用中 (Z)" というメッセージが示されている。](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>原因

このような状況では、ファイル システムでコア ファイルを作成できないため、オペレーティング システム (OS) では Windows Update (KB) のインストールを完了できません。 このエラー コードに基づいて、オペレーティング システムでディスクにファイルを書き込むことはできません。

## <a name="solution"></a>解決策

### <a name="process-overview"></a>プロセスの概要:

> [!TIP]
> VM の最新のバックアップがある場合は、[そのバックアップから VM の復元](../../backup/backup-azure-arm-restore-vms.md)を試行して、起動の問題を修正することができます。

1. 修復 VM を作成してアクセスします。
1. ディスク上の領域を解放します。
1. シリアル コンソールとメモリ ダンプの収集を有効にします。
1. VM を再構築します。

> [!NOTE]
> このエラーが発生する場合、ゲスト OS は動作しなくなります。 この問題を解決するには、オフライン モードでこの問題のトラブルシューティングを行います。

### <a name="create-and-access-a-repair-vm"></a>修復 VM を作成してアクセスする

1. [仮想マシンの修復コマンド](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)に関する説明の手順 1 から 3 に従い、修復 VM を準備します。
1. リモート デスクトップ接続を使用して、修復 VM に接続します。

### <a name="free-up-space-on-the-disk"></a>ディスク上の領域を解放する

この問題を解決するには、次のようにします。

- ディスクがまだ最大サイズの 1 TB になっていない場合は、1 TB になるまでサイズを変更します。
- ディスク クリーンアップを実行します。
- ドライブをデフラグします。

1. ディスクがいっぱいかどうかを確認します。 ディスクのサイズが 1 TB 未満の場合は、[PowerShell を使用](../windows/expand-os-disk.md)して最大 1 TB まで拡張します。
1. ディスクが既に 1 TB の場合は、ディスク クリーンアップを実行する必要があります。
   1. [ディスク クリーンアップ ツール](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) を使用して、空き領域を増やします。
1. サイズ変更とクリーンアップが完了したら、次のコマンドを使用してドライブをデフラグします。

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
フラグメント化のレベルによっては、デフラグに数時間かかることがあります。

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>シリアル コンソールとメモリ ダンプの収集を有効にする

**[推奨]** : VM を再構築する前に、次のスクリプトを実行してシリアル コンソールとメモリ ダンプの収集を有効にします。

1. 管理者として、管理者特権でのコマンド プロンプト セッションを開きます。
1. 次のコマンドを実行します。

   **シリアル コンソールの有効化**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. OS ディスクの空き領域が、VM のメモリ サイズ (RAM) よりも大きいことを確認します。

   OS ディスクに十分な領域がない場合は、メモリ ダンプ ファイルが作成される場所を変更し、その場所で十分な空き領域がある VM に接続された任意のデータ ディスクを参照します。 場所を変更するには、次のコマンドで、 **%SystemRoot%** をデータ ディスクのドライブ文字 (**F:** など) に置き換えます。

   OS ダンプを有効にするための推奨構成:

    **破損した OS ディスクを読み込む:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
   **ControlSet001 で有効にする**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **ControlSet002 での有効化**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **破損した OS ディスクのアンロード**:

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>VM を再構築する

[VM 修復コマンドの手順 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) に従って VM を再構築します。
