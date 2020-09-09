---
title: Windows STOP エラー - 0x00000074 Bad System Config Info
description: この記事では Azure Virtual Machine (VM) の Bad System Config Info により、Windows のブートができず、再起動する必要がある問題を解決するためのステップを紹介します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: 071b5786127af31a2ad3266c128dbfb7cacad656
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88942016"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Windows STOP エラー - 0x00000074 Bad System Config Info

この記事では Azure Virtual Machine (VM) の Bad System Config Info により、Windows のブートができず、再起動する必要がある問題を解決するためのステップを紹介します。

## <a name="symptom"></a>症状

[ブート診断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)を使用して VM のスクリーンショットを表示すると、Windows の STOP コード **#0x00000074** または **BAD_SYSTEM_CONFIG_INFO** が表示される。

"*問題が発生したため、PC を再起動する必要があります。再起動してください。* 
*この問題と、考えられる修正プログラムの詳細については、http://windows.com/stopcode をご覧ください。* 
*サポート担当者に連絡する場合は、次の情報をお伝えください。* 
*STOP コード: BAD_SYSTEM_CONFIG_INFO*"

  ![Windows STOP コード 0x00000074。"BAD_SYSTEM_CONFIG_INFO" とも表示されます。 Windows は、PC で問題が発生し、再起動する必要があることをユーザーに通知します。](./media/windows-stop-error-bad-system-config-info/1.png)

## <a name="cause"></a>原因

**SYSTEM** レジストリ ハイブが破損していると思われる場合、**BAD_SYSTEM_CONFIG_INFO** の STOP コードが発生します。 このエラーは、次のいずれかが原因となる可能性があります。

- レジストリ ハイブが正常に閉じられなかった。
- レジストリ ハイブが壊れている。
- レジストリのキーまたは値が不足している。

## <a name="solution"></a>解決策

### <a name="process-overview"></a>プロセスの概要:

1. 修復 VM を作成してアクセスします。
1. ハイブが壊れていないかを確認します。
1. シリアル コンソールとメモリ ダンプの収集を有効にします。
1. VM を再構築します。

> [!NOTE]
> このエラーが発生する場合、ゲスト オペレーティング システム (OS) は動作しなくなります。 この問題を解決するには、オフライン モードでトラブルシューティングを行います。

### <a name="create-and-access-a-repair-vm"></a>修復 VM の作成とアクセス

1. [仮想マシンの修復コマンド](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)に関する説明の手順 1 から 3 に従い、修復 VM を準備します。
1. ハイブが壊れていないかを確認します。
1. リモート デスクトップ接続を使用して、修復 VM に接続します。
1. `\windows\system32\config` フォルダーをコピーし、正常なディスク パーティションまたは別の安全な場所に保存します。 重要なレジストリ ファイルを編集するため、このフォルダーは予防措置としてバックアップします。

> [!NOTE]
> レジストリに加えた変更をロールバックする必要がある場合に備えて、`\windows\system32\config` フォルダーのコピーをバックアップとして作成します。

### <a name="check-for-hive-corruption"></a>ハイブが壊れていないかを確認する

次の手順は、原因がハイブの破損なのか、ハイブが正しく閉じられなかったことなのかを判断するのに役立ちます。 ハイブが正常に終了しなかった場合は、ファイルのロックを解除して VM を修正することができます。

1. 修復 VM で、**レジストリ エディター** アプリケーションを開きます。 Windows の検索バーに「REGEDIT」と入力して検索します。
1. レジストリ エディターで、 **[HKEY_LOCAL_MACHINE]** を選択して強調表示し、メニューから **[ファイル]、[ハイブの読み込み...]** を選択します。
1. `\windows\system32\config\SYSTEM` を参照し、 **[開く]** を選択します。
1. 名前の入力を求められたら、「**BROKENSYSTEM**」と入力します。

   1. ハイブが開けなかった場合、または空の場合は、ハイブが破損しています。 ハイブが破損している場合は、[サポート チケットを開きます](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

     ![レジストリ エディターがハイブを読み込めないことを示すエラーが発生しています。](./media/windows-stop-error-bad-system-config-info/2.png)

   1. ハイブが正常に開く場合は、ハイブが正常に閉じられませんでした。 手順 5. に進みます。

1. 正常に閉じられなかったハイブを修正するには、 **[BROKENSYSTEM]** を強調表示し、 **[ファイル]、[ハイブのアンロード...]** を選択して ファイルのロックを解除します。

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

[VM 修復コマンドの手順 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) に従って VM を再構築します。
