---
title: 監査ポリシー構成ポリシーの適用時に仮想マシンが応答しない
description: この記事では、監査ポリシー構成ポリシーの適用時に仮想マシン (VM) が応答しなくなり、それにより Azure VM の起動が妨げられるという問題を解決する手順について説明します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3f6383b5-81fa-49ea-9434-2fe475e4cbef
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: e55fa377f28572901202b4d722bea70786edae22
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88942017"
---
# <a name="virtual-machine-is-unresponsive-while-applying-audit-policy-configuration-policy"></a>監査ポリシー構成ポリシーの適用時に仮想マシンが応答しない

この記事では、監査ポリシー構成ポリシーの適用時に仮想マシン (VM) が応答しなくなり、それにより Azure VM の起動が妨げられるという問題を解決する手順について説明します。

## <a name="symptom"></a>症状

[ブート診断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)を使用して VM のスクリーンショットを表示すると、オペレーティング システム (OS) が起動中に "**Applying Audit Policy Configuration policy**" (監査ポリシー構成ポリシーを適用中) というメッセージと共に停止したことがスクリーンショットに示されていることがわかります。

  ![次のメッセージが示されている起動中の OS: "Applying Audit Policy Configuration policy" (監査ポリシー構成ポリシーを適用中)](./media/vm-unresponsive-applying-audit-configuration-policy/1.png)

  ![Windows Server 2012 で次のメッセージが示されている起動中の OS: "Applying Audit Policy Configuration policy" (監査ポリシー構成ポリシーを適用中)](./media/vm-unresponsive-applying-audit-configuration-policy/2.png)

## <a name="cause"></a>原因

ポリシーによって古いユーザー プロファイルのクリーンアップが行われるときに、ロックの競合が発生しています。

> [!NOTE]
> これは、Windows Server 2012 および Windows Server 2012 R2 にのみ当てはまります。

問題のあるポリシーを次に示します。 *[コンピューターの構成]\[ポリシー]\[管理用テンプレート]\[システム/ユーザー プロファイル]\[システム再起動時に指定した日数を経過しているユーザー プロファイルを削除する]* 。

## <a name="solution"></a>解決策

### <a name="process-overview"></a>プロセスの概要

1. 修復 VM を作成してアクセスします。
1. ポリシーを無効にする。
1. シリアル コンソールとメモリ ダンプの収集を有効にします。
1. VM を再構築します。
1. メモリ ダンプ ファイルを収集し、サポート チケットを送信します。

### <a name="create-and-access-a-repair-vm"></a>修復 VM を作成してアクセスする

1. [仮想マシンの修復コマンド](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)に関する説明の手順 1 から 3 に従い、修復 VM を準備します。
1. リモート デスクトップ接続を使用して修復 VM に接続します。

### <a name="disable-the-policy"></a>ポリシーを無効にする

1. 修復 VM で、**レジストリ エディター**を開きます。
1. **HKEY_LOCAL_MACHINE** キーを見つけて、メニューから **[ファイル] > [ハイブの読み込み]** の順に選択します。

   ![ハイブを読み込むためのレジストリ エディター内でのナビゲーション。](./media/vm-unresponsive-applying-audit-configuration-policy/3.png)

   - [ハイブの読み込み] を使用すると、オフライン システムからレジストリ キーを読み込むことができます。 この場合、システムは修復 VM に接続されている破損したディスクです。
   - システム全体の設定は **HKEY_LOCAL_MACHINE** に格納され、**HKLM** と略記できます。

1. 接続されているディスクで、`\windows\system32\config\SOFTWARE` ファイルを開きます。

   - 名前の入力を求められたら、「**BROKENSOFTWARE**」と入力します。
   - **BROKENSOFTWARE** が読み込まれたことを確認するには、**HKEY_LOCAL_MACHINE** を展開し、追加した **BROKENSOFTWARE** キーを探します。

1. **BROKENSOFTWARE** に移動し、読み込まれたハイブに **CleanupProfiles** キーが存在するかどうかを確認します。

   - キーが存在する場合は、**CleanupProfiles** ポリシーが設定されています。 その値は、保持ポリシーを日数で表します。
   - キーが存在しない場合、**CleanupProfile** ポリシーは設定されていません。 このような場合は、先に進み、[メモリ ダンプ ファイルと共にサポート チケットを送信](#collect-the-memory-dump-file-and-submit-a-support-ticket)してください。

1. 次のコマンドを使用して、**CleanupProfiles** キーを削除します。

   `reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f`

1. 次のコマンドを使用して、**BROKENSOFTWARE** ハイブをアンロードします。

   `reg unload HKLM\BROKENSOFTWARE`

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>シリアル コンソールとメモリ ダンプの収集を有効にする

**[推奨]** : VM を再構築する前に、次のスクリプトを実行してシリアル コンソールとメモリ ダンプの収集を有効にします。

1. 管理者として、管理者特権でのコマンド プロンプト セッションを開きます。
1. BCD ストアのデータを表示し、ブート ローダーの識別子を確認します。これは次の手順で使用します。

   1. 第 1 世代 VM の場合、次のコマンドを入力し、表示される識別子を書き留めます。

      `bcdedit /store <BOOT PARTITON>:\boot\bcd /enum`

      - コマンドの `<BOOT PARTITON>` を、アタッチされているディスクの、ブート フォルダーが格納されているパーティションの文字に置き換えます。

        ![図 4 は、第 1 世代の VM に BCD ストアを一覧表示した出力を示しています。Windows ブート ローダーに識別子番号を一覧表示しています。](./media/vm-unresponsive-applying-audit-configuration-policy/4.png)

   1. 第 2 世代 VM の場合、次のコマンドを入力し、表示された識別子を書き留めます。

      `bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

      - コマンドの `<LETTER OF THE EFI SYSTEM PARTITION>` を、EFI システム パーティションの文字に置き換えます。
      - ディスクの管理コンソールを起動して、**EFI システム パーティション**というラベルが付いた適切なシステム パーティションを特定すると役立つ場合があります。
      - 識別子は一意の GUID の場合もあれば、既定の **bootmgr** の場合もあります。

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
   
### <a name="rebuild-the-virtual-machine"></a>仮想マシンを再構築する

1. [VM 修復コマンドの手順 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) に従って VM を再構築します。

1. VM が正常に起動するかテストして、問題が解決したかどうかを確認します。

   - 問題が解決されていない場合は、続けて[ダンプ ファイルを収集し、サポート チケットを送信](#collect-the-memory-dump-file-and-submit-a-support-ticket)してください。
   - 問題が解決された場合は、それ以上の手順は必要ありません。

問題が解決された場合、ポリシーはローカルで無効になっています。 永続的なソリューションの場合は、VM に対して CleanupProfiles ポリシーを使用しないでください。理由は、それによってユーザー プロファイルが自動的に削除されるためです。 スケジュールされたタスクやスクリプトなど、別の方法を使用してプロファイルのクリーンアップを実行します。

**このポリシーは使用しないでください:** 
 *[マシン]\[管理用テンプレート]\[システム]\[ユーザー プロファイル]\[システム再起動時に指定した日数を経過しているユーザー プロファイルを削除する]* 。

### <a name="the-issue-should-now-be-fixed"></a>問題が修正されたことを確認する

VM をテストして、正常に動作していることを確認します。 まだ問題が発生する場合は、さらに参考情報を得るために次のセクションに進んでください。

### <a name="collect-the-memory-dump-file-and-submit-a-support-ticket"></a>メモリ ダンプ ファイルを収集してサポート チケットを送信する

この問題を解決するには、まずクラッシュに関するメモリ ダンプ ファイルを収集してから、サポートに連絡してメモリ ダンプ ファイルを渡す必要があります。 ダンプ ファイルを収集するには、次の手順を行います。

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>新しい修復 VM に OS ディスクを接続する

1. [VM 修復コマンド](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)の手順 1 から 3 を使用して、新しい修復 VM を準備します。
1. リモート デスクトップ接続を使用し、修復 VM に接続します。

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>ダンプ ファイルを探してサポート チケットを送信する

1. 復旧 VM で、接続されている OS ディスクの Windows フォルダーに移動します。 接続されている OS ディスクに割り当てられているドライブ文字が *F* の場合は、`F:\Windows` に移動する必要があります。
1. `memory.dmp` ファイルを探し、メモリ ダンプ ファイルと共に[サポート チケットを送信](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。
1. `memory.dmp` ファイルが見つからない場合は、代わりに[シリアル コンソールでマスク不可能割り込み (NMI) 呼び出し](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls)を使用します。 そのガイドに従い、[ここで NMI 呼び出しを使用してクラッシュ ダンプ ファイルを生成](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump)します。
