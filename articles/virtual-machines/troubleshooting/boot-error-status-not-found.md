---
title: Windows ブート マネージャー エラーのトラブルシューティング - 0xC0000225 "状態が見つかりません"
description: Azure VM でエラー コード 0xC0000225 が発生する問題を解決する手順。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: c17899a4-b270-4725-9530-0dcd829b178c
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: a91add684d7b7aaee67e7dd4f4f2d0c6df078132
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632226"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>Windows ブート マネージャー エラーのトラブルシューティング - 0xC0000225 "状態が見つかりません"
 
この記事では、Azure VM でエラー コード 0xC0000225 が発生する問題を解決する手順を示します。 このエラーは、状態またはオブジェクトが見つからないことを示しています。

## <a name="symptoms"></a>現象

[ブート診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、スクリーンショットに Windows を開始できなかったことを示すエラーが状態コード *0xc0000225* と共に表示されます。

このエラー コードに関連するファイルによって、問題を解決するための手順が通知されます。 **[ファイル]** セクションのテキストを検索して、適切なアクションを判断してください。

### <a name="drivers-os-related-or-third-party"></a>ドライバー、OS 関連、またはサードパーティ

ファイルが存在するが、(図のように) ドライバーを指しているか、ファイルが OS 関連かサード パーティである場合は、「[システム ファイルを修復する](#repair-the-system-file)」セクションの手順に従ってください。
 
次の図では、Windows ブート マネージャーで "Windows を開始できませんでした。 最近のハードウェアまたはソフトウェアの変更が原因の可能性があります。" と表示されています。 またこの図では、[状態] に "0xc0000225"、 **[ファイル]** に `\windows\System32\drivers\atapi.sys`、 **[情報]** に "重要なシステム ドライバーが見つからないか、エラーがあるため、オペレーティング システムをロードできませんでした。" が表示されています。

![Windows ブート マネージャーで "Windows を開始できませんでした。 最近のハードウェアまたはソフトウェアの変更が原因の可能性があります。" と表示されています。 また、[状態] に "0xc0000225"、[ファイル] に "\windows\System32\drivers\atapi.sys"、[情報] セクションに“重要なシステム ドライバーが見つからないか、エラーがあるため、オペレーティング システムをロードできませんでした。” が表示されています。](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>ファイルがない

状態コードが表示されても、ファイルが表示されない場合は、「[OSDEVICE 変数を追加する](#add-the-osdevice-variable)」セクションの手順に従ってください。

次の図では、Windows ブート マネージャーで "Windows を開始できませんでした。 最近のハードウェアまたはソフトウェアの変更が原因の可能性があります。" と表示されています。 またこの図では、[状態] に "0xc0000225"、 **[情報]** に "要求されたデバイスにアクセスできないため、ブートの選択でエラーが発生しました。" が表示されています。

![Windows ブート マネージャーで "Windows を開始できませんでした。 最近のハードウェアまたはソフトウェアの変更が原因の可能性があります。" と表示されています。 また、[状態] に "0xc0000225"、[情報] セクションに"要求されたデバイスにアクセスできないため、ブートの選択でエラーが発生しました。" が表示されています。](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>レジストリ ファイル

それがいずれかのレジストリ ファイル (\windows\system32\config\system など) を指している場合は、「[サポート チケットを作成する](#contact-support)」セクションの手順に従ってください。
 
次の図では、Windows ブート マネージャーで "Windows を開始できませんでした。 最近のハードウェアまたはソフトウェアの変更が原因の可能性があります。" と表示されています。 またこの図では、[状態] に "0xc0000225"、[ファイル] に `\windows\System32\config\system`、 **[情報]** に "システム レジストリ ファイルが見つからないか、エラーがあるため、オペレーティング システムをロードできませんでした。" が表示されています。

![Windows ブート マネージャーで "Windows を開始できませんでした。 最近のハードウェアまたはソフトウェアの変更が原因の可能性があります。" と表示されています。 また、[状態] に "0xc0000225"、[ファイル] に "\windows\System32\config\system"、[情報] セクションに"システム レジストリ ファイルが見つからないか、エラーがあるため、オペレーティング システムをロードできませんでした。" が表示されています。](./media/troubleshoot-boot-error-status-not-found/3.png)

次の図では、回復画面に "お使いの PC またはデバイスは修復する必要があります。 システム レジストリ ファイルが見つからないか、エラーがあるため、オペレーティング システムをロードできませんでした。" と表示されています。 またこの図では、[エラー コード] に "0xc0000225"、[ファイル] に `\windows\System32\config\system` が表示されています。

![回復画面に "お使いの PC またはデバイスは修復する必要があります。 システム レジストリ ファイルが見つからないか、エラーがあるため、オペレーティング システムをロードできませんでした。" と表示されています。 また、[エラー コード] に "0xc0000225"、[ファイル] に "\windows\System32\config\system" が表示されています。](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>原因

### <a name="missing-binary"></a>バイナリの欠落

システム **(.sys)** ファイルで、バイナリが見つからないか破損している可能性があります。

### <a name="bcd-corruption-or-improper-vhd-migration"></a>BCD の破損または不適切な VHD の移行

この場合、**ブート構成データ (BCD)** が破損しているか、オンプレミスから移行された **仮想ハード ドライブ (VHD)** が適切に準備されていません。 その結果 **OSDEVICE** 変数がない状態になっているため、追加する必要があります。

### <a name="registry-hive-corruption"></a>レジストリ ハイブの破損

レジストリ ハイブの破損には次の原因が考えられます。

- ハイブで障害が発生した
- ハイブがマウントされたが、空であった
- ハイブが適切に閉じられなかった
## <a name="solution"></a>解決策

### <a name="process-overview"></a>プロセスの概要

> [!TIP]
> VM の最新のバックアップがある場合は、[そのバックアップから VM の復元](../../backup/backup-azure-arm-restore-vms.md)を試みて、起動の問題を修正できます。

1. 修復 VM を作成してアクセスします。
1. 解決策を選択します。
   - [システム ファイルを修復する](#repair-the-system-file)
   - [OSDEVICE 変数を追加する](#add-the-osdevice-variable)
   - [サポート チケットを作成する](#contact-support)
1. シリアル コンソールとメモリ ダンプの収集を有効にします。
1. VM を再構築します。

### <a name="create-and-access-a-repair-vm"></a>修復 VM を作成してアクセスする

1. [仮想マシンの修復コマンド](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)に関する説明の手順 1 から 3 に従い、修復 VM を準備します。
1. リモート デスクトップ接続を使用して修復 VM に接続します。

### <a name="select-a-solution"></a>解決策を選択する

1. 管理者特権のコマンド プロンプトを開きます。
1. 前に特定した現象に基づいて、対応する解決策の手順に従います。 他の解決策の手順は、発生した問題に適用されないため省略してかまいません。

- [システム ファイルを修復する](#repair-the-system-file)
- [OSDEVICE 変数を追加する](#add-the-osdevice-variable)
- [サポート チケットを作成する](#contact-support)

### <a name="repair-the-system-file"></a>システム ファイルを修復する

1. 接続された VHD を使用して、仮想マシン (VM) のスクリーンショットに示されているバイナリのファイルの場所に移動します。
1. ファイルを右クリックして **[プロパティ]** を選択し、 **[詳細]** タブを選択してファイルに関する情報を表示します。
   1. ファイルのバージョンを確認します (次の図を参照)。

      !["cng.sys" ファイルのプロパティ ウィンドウでファイルのバージョンが強調表示されています。](./media/troubleshoot-boot-error-status-not-found/5.png)

1. ファイルの名前を **< BINARY.SYS >.old** に変更します。 **< BINARY.SYS >** はファイルの名前で置き換えてください。

   上の手順の図の場合は、ファイル **cng.sys** が **cng.sys.old** に名前変更されます。

   > [!NOTE]
   > ファイル名を変更する際に "ファイルが壊れているため、読み取ることができません" というメッセージが表示された場合は、この解決策が使えないため、[サポートにお問い合わせください](https://azure.microsoft.com/support/create-ticket/)。

1. 破損したファイルの名前が変更されたので、このファイルを内部リポジトリから復元して修正します。
   1. **CMD** セッションを開始します。
   1. **\windows\winsxs** に移動します。

   1. 次のコマンドを使用して、このセクションの冒頭で特定したバイナリを検索します。

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      このコマンドを実行すると、マシンにあるこのファイルのバージョンがすべて一覧表示され、そのコンポーネントのパス履歴が示されます。

      たとえば、**dir cng.sys** の名前が **dir cng.sys /s** に変更されているなどです。

   1. この一覧でファイルの最新バージョン (または任意のもの) を選択し、前のパスと次のコマンドを使用して、**windows\system32** フォルダーにファイルをコピーします。

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > 最新のバイナリが機能しなかった場合は、その前のバージョンを試すか、安定したファイルがあることがわかっているもの (パッチより前のバージョンなど) を試してください。

      たとえば、探しているバイナリが **cmimcext.sys** で、障害のあるドライブが **F:** の場合、最新バージョンの検索を実行すると、次の画面が表示されます。ここでは、`dir cmim* /s` のコマンド プロンプトのクエリで cmimcext.sys ファイルの最新バージョンが検索されています。

      !["Dir cmim */s" のコマンド プロンプトのクエリで cmimcext.sys ファイルの最新バージョンを検索します。](./media/troubleshoot-boot-error-status-not-found/6.png)

      上の図の例では、**C:** でクエリが実行されていますが、ドライブ文字は障害のあるドライブのもの (**F:** ) である必要があります。これは、修復 VM でデータ ディスクとして接続されている OS ディスクです。

      この結果、ファイルをコピーするコマンドは次のようになります: `copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers`

このタスクが完了したら、「[シリアル コンソールとメモリ ダンプの収集を有効にする](#enable-the-serial-console-and-memory-dump-collection)」に進みます。

### <a name="add-the-osdevice-variable"></a>OSDEVICE 変数を追加する

現在のブート セットアップ情報を収集し、アクティブなパーティションの識別子をメモします。 次にこの情報を使用して、お使いの VM の世代に応じた指示に従って **OSDEVICE** 変数を追加します。

この情報収集で **\boot\bcd** ファイルがないためにエラーが発生した場合は、代わりに「[システム ファイルを修復する](#repair-the-system-file)」の手順に従ってください。 

1. 第 1 世代 VM の場合は、管理者として管理者特権でのコマンド プロンプトを開き、次のコマンドを入力します。

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   次の図では、第 1 世代 VM での Windows ブート ローダーが示されており、identifier 属性が強調表示されています。 強調表示された identifier 属性は、一意の英数字文字列を示します。

   ![第 1 世代 VM での Windows ブート ローダーが示されており、identifier 属性が強調表示されています。 強調表示された identifier 属性は、一意の英数字文字列を示します。](./media/troubleshoot-boot-error-status-not-found/7.png)

   パスが **\Windows\system32\winload.exe** である Windows ブート ローダーの識別子をメモします。

1. 第 2 世代 VM の場合は、OS ディスクがオンラインであることと、そのパーティションのドライブ文字が割り当てられていることを確認します。 これを確認したら、ブート セットアップ情報を収集します。
   1. **Windows Search** で「**Disk Management**」と入力し、ディスク管理コンソールを開きます。 このコンソールを使用して、修復 VM に接続されているディスク番号と、BCD ストアを保持する拡張ファームウェア インターフェイス (EFI) パーティションを特定します。

   次の図では、Disk 2 が、修復 VM に接続されているディスク番号です。 この図には Disk 2 の EFI システム パーティションも示されています。これはサイズが 100 MB で、文字が割り当てられていません。

   ![修復 VM に接続されているディスク番号として Disk 2 が示されています。 また、Disk 2 の EFI システム パーティションも示されています。これは 100 MB で、文字が割り当てられていません。](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. 管理者として管理者特権でのコマンド プロンプトを開き、次のコマンドを入力します。
      1. コマンド `diskpart` を使用して **DISKPART ツール** を開きます。
      1. すべてのディスクを一覧表示し、前の手順で特定した接続ディスクを選択します。
      
         ```
         list disk
         sel disk <DISK #>
         ```

         次の図は、ディスクの一覧表示と選択の結果を示しています。 Disk 0 (127 GB / Online)、Disk 1 (32 GB / Online)、Disk 2 (127 GB / Online) が表示され、コマンド `sel disk 2` で Disk 2 が選択されています。

         ![ディスクの一覧表示とその後の選択の結果。 Disk 0 (127 GB | Online)、Disk 1 (32 GB | Online)、Disk 2 (127 GB | Online) が表示され、Disk 2 が選択されています。](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. パーティションを一覧表示し、前の手順で特定した EFI システム パーティションを選択します。
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         次の図は、パーティションの一覧表示と選択の結果を示しています。 Partition 1 (Reserved / 16MB)、Partition 2 (System / 100MB)、Partition 3 (Primary / 126 GB) が表示され、コマンド `sel part 2` で Partition 2 が選択されています。

         ![パーティションの一覧表示とその後の選択の結果。 Partition 1 (Reserved | 16MB)、Partition 2 (System | 100MB)、Partition 3 (Primary | 126 GB) が表示され、Partition 2 が選択されています。](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. コマンド `assign` を使用して EFI パーティションに文字を割り当てます。

         次の図では、`assign` コマンドと新しいドライブ **SYSTEM (F:)** の両方がファイル エクスプローラーに表示されています。

         ![assign コマンドと新しいドライブ SYSTEM (F:) がファイル エクスプローラーに表示されています。](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. 次のコマンドを使用して BCD ストアのデータを一覧表示します。
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         次の図では、第 2 世代 VM での Windows ブート ローダーが示されており、identifier 属性が強調表示されています。 強調表示された identifier 属性は、値が **{default}** になっています。

         ![第 2 世代 VM での Windows ブート ローダーが示されており、identifier 属性が強調表示されています。 強調表示された identifier 属性は、値が default になっています。](./media/troubleshoot-boot-error-status-not-found/12.png)

         パスが **\windows\system32\winload.efi** である Windows ブート ローダーの識別子をメモします。

1. アクティブ パーティションの OSDEVICE 変数が欠落していることに注意してください。

   ![Windows ブート マネージャーと Windows ブートローダーの属性がコマンド プロンプトに一覧表示されていますが、OS デバイス属性はありません。](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   この図では、Windows ブート マネージャーと Windows ブート ローダーの属性がコマンド プロンプトに一覧表示されていますが、OSDEVICE 属性はありません。

1. 次の情報に基づいて OSDEVICE 変数を追加します。

   単一パーティションの OS ディスクの場合は、`BOOT` を追加します。

   > [!NOTE]
   > ブート可能なフォルダーは、Windows フォルダー **\windows** と同じパーティションに配置されます。
   > - 第 1 世代 VM のブート可能なフォルダーは **(\ boot\ bcd)** です。
   > - 第 2 世代 VM のブート可能なフォルダーは **EFI\Microsoft\boot\bcd** です。

   第 1 世代 VM の場合は、次のコマンドを入力します。

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   第 2 世代 VM の場合は、次のコマンドを入力します。

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   複数パーティションの OS ディスクの場合は、`PARTITION=<LETTER OF WINDOWS FOLDER>:` を追加します。

   > [!NOTE]
   > ブート可能なフォルダーは、Windows フォルダー **\windows** とは別のパーティションに配置される場合がほとんどです。
   > - 第 1 世代 VM のブート可能なフォルダーは **(\ boot\ bcd)** です。
   > - 第 2 世代 VM のブート可能なフォルダーは **EFI\Microsoft\boot\bcd** です。

   第 1 世代 VM の場合は、次のコマンドを入力します。

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   第 2 世代 VM の場合は、次のコマンドを入力します。

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

このタスクが完了したら、「[シリアル コンソールとメモリ ダンプの収集を有効にする](#enable-the-serial-console-and-memory-dump-collection)」に進みます。

### <a name="contact-support"></a>サポートに問い合わせる

**レジストリ ファイル** エラーには解決策がありますが、さらにサポートを受けるには [サポート チケットを作成](https://azure.microsoft.com/support/create-ticket/)する必要があります。

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

[VM 修復コマンドの手順 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) に従って VM を再構築します。
