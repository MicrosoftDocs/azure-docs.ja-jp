---
title: Windows ブート マネージャーが原因で Windows 仮想マシンを起動できない
description: この記事では、Windows ブート マネージャーが原因で Azure 仮想マシンを起動できない問題を解決するための手順を示します。
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 2457952051f575306de46e3e8145cc26678a1ef8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526541"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Windows ブート マネージャーが原因で Windows VM を起動できない

この記事では、Windows ブート マネージャーが原因で Azure 仮想マシン (VM) を起動できない問題を解決するための手順を示します。

## <a name="symptom"></a>症状

VM がユーザー プロンプトで待機していて、手動で指示しない限り起動しない。

次のスクリーンショットのように、[ブート診断](./boot-diagnostics.md)を使用して VM を確認すると、Windows ブート マネージャーにより "*開始するオペレーティング システムを選択するか、TAB キーを押してツールを選択してください:* " というメッセージが表示されます。

図 1
 
![Windows ブート マネージャーにより "開始するオペレーティング システムを選択するか、TAB キーを押してツールを選択してください:" というメッセージが表示される](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>原因

このエラーの原因は、Windows ブート マネージャーの BCD フラグ *displaybootmenu* です。 このフラグが有効になっている場合、Windows ブート マネージャーは、起動プロセス中に、実行するローダーを選択するように求めるプロンプトをユーザーに対して表示します。これにより起動が遅延します。 Azure では、この機能によって VM の起動にかかる時間が長くなる場合があります。

## <a name="solution"></a>解決策

プロセスの概要:

1. シリアル コンソールを使用して、より短い起動時間を構成します。
2. 修復 VM を作成してアクセスします。
3. 修復 VM でより短い起動時間を構成します。
4. **[推奨]** : VM を再構築する前に、シリアル コンソールとメモリ ダンプの収集を有効にします。
5. VM を再構築します。

### <a name="configure-for-faster-boot-time-using-serial-console"></a>シリアル コンソールを使用して、より短い起動時間を構成する

シリアル コンソールへのアクセス権がある場合、起動時間を短縮する方法は 2 つあります。 *displaybootmenu* の待機時間を短縮するか、このフラグを完全に削除します。

1. 指示に従って [Windows 用 Azure シリアル コンソール](./serial-console-windows.md)にアクセスし、テキストベースのコンソールにアクセスできるようにします。

   > [!NOTE]
   > シリアル コンソールにアクセスできない場合は、「[修復 VM を作成してアクセスする](#create-and-access-a-repair-vm)」に進んでください。

2. **オプション A**: 待機時間を短縮する

   a. 待機時間は既定では 30 秒に設定されていますが、より短い時間 (たとえば 5 秒) に変更できます。

   b. シリアル コンソールで次のコマンドを使用してタイムアウト値を調整します。

      `bcdedit /set {bootmgr} timeout 5`

3. **オプション B**: BCD フラグを削除する

   a. [Display Boot Menu]\(ブート メニューの表示\) プロンプトが表示されないようにするには、次のコマンドを入力します。

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > 前の手順でシリアル コンソールを使用してより短いブート時間を構成できなかった場合は、代わりに次の手順に進むことができます。 オフライン モードでトラブルシューティングを行い、この問題を解決します。

### <a name="create-and-access-a-repair-vm"></a>修復 VM を作成してアクセスする

1. [VM 修復コマンドの手順 1 から 3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) を使用して、修復 VM を準備します。
2. リモート デスクトップ接続を使用して、修復 VM に接続します。

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>修復 VM でより短い起動時間を構成する

1. 管理者特権のコマンド プロンプトを開きます。
2. 次のように入力して DisplayBootMenu を有効にします。

   **第 1 世代 VM** の場合、次のコマンドを使用します。

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   **第 2 世代 VM** の場合、次のコマンドを使用します。

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   不等号とそこに含まれるテキスト (例: "< text here >") を置き換えます。

3. タイムアウト値を 5 秒に変更します。

   **第 1 世代 VM** の場合、次のコマンドを使用します。

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   **第 2 世代 VM** の場合、次のコマンドを使用します。

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   不等号とそこに含まれるテキスト (例: "< text here >") を置き換えます。

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>推奨:VM を再構築する前に、シリアル コンソールとメモリ ダンプの収集を有効にする

メモリ ダンプの収集とシリアル コンソールを有効にするには、次のスクリプトを実行します。

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
