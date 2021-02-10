---
title: Windows Update の適用時に、Azure VM が C01A001D エラー で応答しません。
description: この記事では、Windows Update がエラーを起こし、Azure VM で応答しなくなった問題を解決するステップについて説明します。
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 145d58013b259bf14c26d0840b8cd4299cfe85f0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632624"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>Windows Update の適用時に、Azure VM が C01A001D エラー でて応答しません。

この記事では、Windows Update (KB) がエラーを起こし、Azure VM で応答しなくなった問題を解決するステップについて説明します。

## <a name="symptoms"></a>現象

VM のスクリーンショットを見るために [ブート診断](./boot-diagnostics.md) を使用すると、 Windows Update (KB) の進行状況が表示されますが、次のエラーコードによって失敗します。'C01A001D'

![Windows Update が応答しません](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>原因

ファイル システムでコアファイルの作成ができません。 オペレーティング システムがディスクにファイルを書き込めません。

## <a name="resolution"></a>解像度

### <a name="process-overview"></a>プロセスの概要

> [!TIP]
> VM の最新のバックアップがある場合は、[そのバックアップから VM の復元](../../backup/backup-azure-arm-restore-vms.md)を試行して、起動の問題を修正することができます。

1. [修復 VM](#create-and-access-a-repair-vm)を作成してアクセスします。
2. [ハードディスクの空き領域を増やします](#free-up-space-on-the-hard-disk)。
3. [インデックス付きVM を再構築する前に、シリアル コンソールとメモリ ダンプの収集を有効にします](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection)。
4. [VM を再構築します](#rebuild-the-vm)。

> [!NOTE]
> このエラーの発生時には、ゲスト OS は操作できない状態です。 この問題を解決するには、オフライン モードでトラブルシューティングを行う必要があります。

### <a name="create-and-access-a-repair-vm"></a>修復 VM を作成してアクセスする

1. [VM 修復コマンドのステップ 1 から 3 ](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)に従い、修復 VM を準備します。
2. リモート デスクトップ接続を使用し、修復 VM に接続します。

### <a name="free-up-space-on-the-hard-disk"></a>ハードディスクの空き領域を増やす

ディスクが 1 TB になっていない場合には、サイズを変更する必要があります。 ディスクが 1 TB になったら、ディスクのクリーンアップとドライブの最適化を実行します。

1. ディスクがいっぱいかどうかを確認します。 ディスクが 1 TB 未満の場合は、[PowerShell を使用して最大 1 TB にまで拡張します](../windows/expand-os-disk.md)。
2. ディスクが 1 TB になったら、ディスクのクリーンアップを実行します。
    - [破損した VM からデータ ディスクをデタッチします](../windows/detach-disk.md)。
    - [データ ディスクを、機能している VM に接続します](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)。
    - [ディスク クリーンアップ ツール](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) を使用して、空き領域を増やします。
3. サイズ変更とクリーンアップの完了後に、ドライブの最適化を実行します。

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    断片化のレベルによっては、処理に時間がかかることがあります。

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>推奨:VM を再構築する前に、シリアル コンソールとメモリ ダンプの収集を有効にします。

1. 管理者特権でコマンド プロンプト セッション (管理者として実行) を開きます。
2. 次のコマンドを実行します。

    Enable Serial Console: (シリアル コンソールを有効にする)

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. OS ディスクの空き領域が、VM メモリ (RAM) のサイズ以上であることを確認してください。

    OS ディスクに十分な空き領域がない場合には、メモリ ダンプ ファイルが作成される場所を、VM に接続され、なおかつ十分な空き容量があるデータ ディスクに変更してください。 場所を変更するには、次のコマンドの中で、`%SystemRoot%` を変更先のデータ ディスクのドライブ文字 (たとえば "F:") に置換します。

    **Enable OS dump suggested configuration:** (OS ダンプの推奨構成を有効にする)

    Load Broken OS Disk: (破損した OS ディスクを読み込む)

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

### <a name="rebuild-the-vm"></a>VM を再構築する

[VM 修復コマンドのステップ 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) を使って、VM を再構成します。
