---
title: セキュリティ ポリシーをシステムに適用しているときに Azure VM が応答しない
description: この記事では、Azure VM でシステムにセキュリティ ポリシーを適用しているときに VM が応答せず、読み込み画面が停止する問題を解決する手順について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 047c8afbfe7b489e5c3ac0ccb677f6fc021443a8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632641"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>セキュリティ ポリシーをシステムに適用しているときに Azure VM が応答しない

この記事では、Azure VM でセキュリティ ポリシーを適用している間に OS がハングし、応答しなくなる問題を解決する手順について説明します。

## <a name="symptoms"></a>現象

[ブート診断](boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、スクリーンショットに次のメッセージが表示され、OS が停止していることが示されることがわかります。

> ' システムにセキュリティ ポリシーを適用しています '。

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Windows Server 2012 R2 スタートアップ画面が停止しているスクリーンショット。":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="OS スタートアップ画面のスクリーンショットが停止しているスクリーンショット。":::

## <a name="cause"></a>原因

この問題の考えられる原因は多数あります。 メモリ ダンプ分析が実行されるまで、原因を知ることはできません。

## <a name="resolution"></a>解決方法

### <a name="process-overview"></a>プロセスの概要

> [!TIP]
> VM の最新のバックアップがある場合は、[そのバックアップから VM の復元](../../backup/backup-azure-arm-restore-vms.md)を試行して、起動の問題を修正することができます。

1. [修復 VM を作成してアクセスする](#create-and-access-a-repair-vm)
2. [シリアル コンソールとメモリ ダンプの収集を有効にする](#enable-serial-console-and-memory-dump-collection)
3. [VM を再構築する](#rebuild-the-vm)
4. [メモリ ダンプ ファイルを収集する](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>修復 VM を作成してアクセスする

1. [VM 修復コマンドの手順 1 から 3](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) を使用して、修復 VM を準備します。
2. リモート デスクトップ接続を使用して、修復 VM に接続します。

### <a name="enable-serial-console-and-memory-dump-collection"></a>シリアル コンソールとメモリ ダンプの収集を有効にする

メモリ ダンプの収集とシリアル コンソールを有効にするには、このスクリプトを実行します。

1. 管理者特権でコマンド プロンプト セッション (管理者として実行) を開きます。
2. BCD ストアのデータを表示し、ブート ローダーの識別子を確認します。これは次の手順で使用します。

     1. 第 1 世代 VM の場合、次のコマンドを入力し、表示される識別子を書き留めます。

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        コマンドで、\<BOOT PARTITON> をブート フォルダーが格納されている接続ディスクのパーティションの文字に置き換えます。

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="図は、第 1 世代の VM に BCD ストアを一覧表示した出力を示しています。Windows ブートローダーに識別子番号を一覧表示しています。":::

     2. 第 2 世代 VM の場合、次のコマンドを入力し、表示される識別子を書き留めます。

        ```console
        bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
        ```

        - コマンドの \<LETTER OF THE EFI SYSTEM PARTITION> を、EFI システム パーティションの文字に置き換えます。
        - ディスク管理コンソールを起動して、"EFI System Partition" というラベルが付いた適切なシステム パーティションを特定すると便利な場合があります。
        - 識別子は一意の GUID の場合もあれば、既定の "bootmgr" の場合もあります。
3. 次のコマンドを実行して、シリアル コンソールを有効にします。

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

    - コマンドの \<VOLUME LETTER WHERE THE BCD FOLDER IS> を、BCD フォルダーの文字に置き換えます。
    - コマンドの \<BOOT LOADER IDENTIFIER> を、前の手順で確認した識別子に置き換えます。
4. OS ディスクの空き領域が、VM のメモリ サイズ (RAM) よりも大きいことを確認します。

    1. OS ディスクに十分な領域がない場合は、メモリ ダンプ ファイルが作成される場所を変更する必要があります。 OS ディスク上にファイルを作成するのではなく、十分な空き領域がある VM に接続されている他のデータ ディスクを参照することができます。 場所を変更するには、"%SystemRoot%" を以下に記載のコマンドでデータ ディスクのドライブ文字 (たとえば、"F:") に置き換えます。
    2. 次のコマンドを入力します (推奨されるダンプ構成)。

        Load Broken OS Disk: (破損した OS ディスクを読み込む)

        ```console
        REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
        ```

        Enable on ControlSet001: (ControlSet001 で有効にする)

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Enable on ControlSet002: (ControlSet002 で有効にする)

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        破損した OS ディスクのアンロード:

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>VM を再構築する

[VM 修復コマンドの手順 5](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) を使用して、VM を再構成します。

### <a name="collect-the-memory-dump-file"></a>メモリ ダンプ ファイルを収集する

この問題を解決するには、まずクラッシュに関するメモリ ダンプ ファイルを収集し、サポートに連絡してメモリ ダンプ ファイルを渡す必要があります。 ダンプ ファイルを収集するには、次の手順を行います。

1. 新しい修復 VM に OS ディスクを接続する:

    - [VM 修復コマンドの手順 1 から 3](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) を使用して、新しい修復 VM を準備します。
    - リモート デスクトップ接続を使用して、修復 VM に接続します。

2. ダンプ ファイルを探してサポート チケットを送信します。

    - 復旧 VM で、接続されている OS ディスクの Windows フォルダーに移動します。 接続されている OS ディスクに割り当てられているドライブ文字が `F` の場合は、`F:\Windows` に移動する必要があります。
    - memory.dmp ファイルを検索し、メモリ ダンプ ファイルと共に[サポート チケットを送信](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。
    - memory.dmp ファイルが見つからない場合は、代わりに[シリアル コンソールでマスク不可能割り込み (NMI) 呼び出し](serial-console-windows.md#use-the-serial-console-for-nmi-calls)を使用することをお勧めします。 このガイドに従い、[NMI 呼び出しを使用してクラッシュ ダンプ ファイルを生成](/windows/client-management/generate-kernel-or-complete-crash-dump)できます。

## <a name="next-steps"></a>次のステップ

ローカル ユーザーとグループ ポリシーを適用するときに問題が発生した場合は、「[グループ ポリシーの [ローカル ユーザーとグループ] ポリシーの適用時に VM が応答しない](unresponsive-vm-apply-group-policy.md)」を参照してください。