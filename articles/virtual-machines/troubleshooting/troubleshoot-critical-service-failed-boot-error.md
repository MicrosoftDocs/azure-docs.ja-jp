---
title: Azure VM 起動時の "CRITICAL SERVICE FAILED" | Microsoft Docs
description: 起動時に発生する "0x0000005A-CRITICAL SERVICE FAILED" のトラブルシューティング方法 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: ca3fe83d7072ba774c8124c5108652ab7223041d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449810"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Azure VM 起動時に Windows が "CRITICAL SERVICE FAILED" をブルー スクリーンに表示する
この記事では、Microsoft Azure で Windows 仮想マシン (VM) を起動するときに発生する可能性のある "CRITICAL SERVICE FAILED" エラーについて説明します。 また、この問題の解決するためのトラブルシューティング手順について説明します。 

> [!NOTE] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、Resource Manager デプロイ モデルの使用方法について説明しています。最新のデプロイでは、クラシック デプロイ モデルではなくこのモデルを使用することをお勧めします。

## <a name="symptom"></a>症状 

Windows VM が起動しません。 [[ブート診断]](./boot-diagnostics.md) のブート スクリーンショットを確認すると、ブルー スクリーンに次のいずれかのエラー メッセージが表示されます。

- "問題が発生したため、PC を再起動する必要があります。 再起動できます。 この問題と可能な解決方法の詳細については、 https://windows.com/stopcode を参照してください。 サポート担当者に電話をかける場合、次の情報を伝えます。停止コード: CRITICAL SERVICE FAILED" 
- "問題が発生したため、PC を再起動する必要があります。 エラー情報を収集しています。自動的に再起動します。 詳細については、次のエラーを後からオンラインで検索してください: CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>原因

停止エラーの原因はいくつかあります。 最も一般的な原因を次に示します。
- ドライバーに関する問題
- システム ファイルまたはメモリの破損
- アプリケーションがメモリの禁止されたセクターにアクセスしている

## <a name="solution"></a>解決策 

この問題を解決するには、[サポートに連絡し、ダンプ ファイルを送信してください](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) (このダンプ ファイルは迅速に問題を診断するのに役立ちます)。または、次のセルフヘルプ ソリューションをお試しください。

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>復旧 VM に OS ディスクを接続する

1. バックアップとして、影響を受ける VM の OS ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。
2. [復旧 VM に OS ディスクを接続します](./troubleshoot-recovery-disks-portal-windows.md)。 
3. 復旧 VM へのリモート デスクトップ接続を確立します。

### <a name="enable-dump-logs-and-serial-console"></a>ダンプ ログとシリアル コンソールを有効にする

ダンプ ログと[シリアル コンソール](./serial-console-windows.md)は、サポート担当者がトラブルシューティングを実行するのに役立ちます。

ダンプ ログとシリアル コンソールを有効にするには、次のスクリプトを実行します。

1. 管理者特権のコマンド プロンプト セッション ([管理者として実行]) を開きます。
2. 次のスクリプトを実行します。

    このスクリプトでは、接続されている OS ディスクに割り当てられているドライブ文字が「F」であると想定しています。実際の VM の適切な値に置き換える必要があります。

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>署名されていないドライバーを置き換える

1. 復旧 VM で、管理者特権でのコマンド プロンプトから次のコマンドを実行します。 このコマンドは、次回の起動時にセーフ モードで起動するように、影響を受ける OS ディスクを設定します。

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    たとえば、接続した OS ディスクがドライブ F の場合は、次のコマンドを実行します。

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [OS ディスクを切断し、影響を受ける VM に OS ディスクを接続し直します](troubleshoot-recovery-disks-portal-windows.md)。 VM がセーフ モードで起動します。 依然としてエラーが発生する場合は、省略可能な手順を実行します。
3. **[実行]** ボックスを開き、**verifier** を実行して、ドライバーの検証マネージャー ツールを起動します。
4. **[署名されていないドライバーを自動的に選択する]** を選択し、 **[次へ]** をクリックします。
5. 署名されていないドライバー ファイルの一覧が表示されます。 ファイル名を覚えます。
6. これらのファイルの同じバージョンを作業用 VM からコピーし、これらの署名されていないファイルを置き換えます。 

7. セーフ モードでの起動設定を削除します。

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  VM を再起動します。 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>省略可能: ダンプ クラッシュ モードでのダンプ ログの分析

ダンプ ログを自分で分析するには、次の手順に従います。

1. 復旧 VM に OS ディスクを接続します。
2. 接続した OS ディスクで、 **\windows\system32\config** を参照します。ロールバックが必要な場合に備えて、すべてのファイルをバックアップとしてコピーします。
3. **レジストリ エディター** (regedit.exe) を起動します。
4. **HKEY_LOCAL_MACHINE** キーを選択します。 メニューで、 **[ファイル]**  >  **[ハイブの読み込み]** を選択します。
5. 接続した OS ディスクで、 **\windows\system32\config\SYSTEM** フォルダーを参照します。 ハイブの名前として、「**BROKENSYSTEM**」と入力します。 **HKEY_LOCAL_MACHINE** キーの下に、新しいレジストリ ハイブが表示されます。
6. **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** を参照し、次の変更を行います。

    Autoreboot = 0

    CrashDumpEnabled = 2
7.  **[BROKENSYSTEM]** を選択します。 メニューから、 **[ファイル]**  >  **[ハイブのアンロード]** を選択します。
8.  デバッグ モードで起動するように BCD 設定を変更します。 管理者特権のコマンド プロンプトで、次のコマンドを実行します。

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [OS ディスクを切断し、影響を受ける VM に OS ディスクを接続し直します](troubleshoot-recovery-disks-portal-windows.md)。
10. VM を起動し、ダンプの分析が表示されないことを確認します。 読み込みに失敗したファイルを見つけます。 作業用 VM のファイルでこのファイルを置き換える必要があります。 

    ダンプ分析の例を次に示します。 filecrypt.sys に **FAILURE** が付加されていることを確認できます。"FAILURE_BUCKET_ID:0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. VM が正常に起動したら、クラッシュ ダンプの設定を削除します。

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
