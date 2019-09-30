---
title: Azure VM 起動時のブルー スクリーン エラー | Microsoft Docs
description: 起動時にブルー スクリーン エラーが表示される問題のトラブルシューティング方法 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: 921e97fa393a3005e3ba392502d291301df3d65c
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058078"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Azure VM の起動時に Windows でブルー スクリーン エラーが表示される
この記事では、Microsoft Azure で Windows 仮想マシン (VM) を起動するときに発生する可能性のあるブルー スクリーン エラーについて説明します。 また、サポート チケットのためのデータを収集する手順について説明します。 

> [!NOTE] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、Resource Manager デプロイ モデルの使用方法について説明しています。最新のデプロイでは、クラシック デプロイ モデルではなくこのモデルを使用することをお勧めします。

## <a name="symptom"></a>症状 

Windows VM が起動しません。 [[ブート診断]](./boot-diagnostics.md) のブート スクリーンショットを確認すると、ブルー スクリーンに次のいずれかのエラー メッセージが表示されます。

- 問題が発生したため、PC を再起動する必要があります。 エラー情報を収集しています。再起動できます。
- 問題が発生したため、PC を再起動する必要があります。

このセクションでは、VM の管理時に発生する可能性がある一般的なエラー メッセージの一覧を示します。

## <a name="cause"></a>原因

STOP エラーが発生する理由は複数存在する可能性があります。 最も一般的な原因を次に示します。

- ドライバーに関する問題
- システム ファイルまたはメモリの破損
- アプリケーションがメモリの禁止されたセクターにアクセスしている

## <a name="collect-memory-dump-file"></a>メモリ ダンプ ファイルを収集する

この問題を解決するには、まずクラッシュに関するダンプ ファイルを収集し、サポートに連絡してダンプ ファイルを渡す必要があります。 ダンプ ファイルを収集するには、次の手順を実行します。

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>復旧 VM に OS ディスクを接続する

1. バックアップとして、影響を受ける VM の OS ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。
2. [復旧 VM に OS ディスクを接続します](../windows/troubleshoot-recovery-disks-portal.md)。 
3. 復旧 VM にリモート デスクトップ接続します。

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

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [OS ディスクを切断し、影響を受ける VM に OS ディスクを接続し直します](../windows/troubleshoot-recovery-disks-portal.md)。
4. 問題を再現するために VM を起動すると、ダンプ ファイルが生成されます。
5. OS ディスクを復旧 VM に接続し、ダンプ ファイルを収集し、ダンプ ファイルと共に[サポート チケットを送信](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。



