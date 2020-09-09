---
title: Windows STOP エラー - メモリなし状態
description: この記事では、Windows の起動に失敗し、"メモリなし状態" という状態が表示される問題を解決する手順について説明します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: d29c7e49-4578-43c8-b829-831da4e48932
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 33b4c59e14301e496d0eddafa7bdfdf201b7aa29
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005907"
---
# <a name="windows-stop-error---status-no-memory"></a>Windows STOP エラー - メモリなし状態

この記事では、Windows の起動に失敗し、状態またはエラー コード #0xC0000017 ("メモリなし状態" とも呼ばれます) が表示される問題を解決する手順について説明します。

## <a name="symptom"></a>症状

[ブート診断](./boot-diagnostics.md)を使用して仮想マシン (VM) のスクリーンショットを表示したときに、スクリーンショットにエラー コード `0xC0000017` が表示されていることがわかります。 実行している Windows のバージョンに応じて、**Windows ブート マネージャー**または**回復画面**にこのコードが表示されることがあります。

   **Windows ブート マネージャー**

   ![Windows ブート マネージャーに、"Windows を開始できませんでした。 最近のハードウェアまたはソフトウェアの変更が原因の可能性があります。" と表示されます。 下へスクロールすると、状態コード "0xC0000017" と、"必要なファイルが見つからないか、ファイルにエラーがあるため、アプリケーションまたはオペレーティング システムをロードできませんでした。" という情報が表示されます。](./media/troubleshoot-windows-stop-error/1.png)

   **回復画面**
 
   ![回復画面に、"お使いの PC またはデバイスは修復する必要があります。 ramdisk デバイスの作成に使用できるメモリが不足しています。" と表示されます。 また、エラー コード "0xC0000017" も表示されます。](./media/troubleshoot-windows-stop-error/2.png)

## <a name="cause"></a>原因

オペレーティング システムのディスクがいっぱいになっているか、過度に断片化されています。または、オペレーティング システム (OS) がメモリまたはページ ファイル、あるいはその両方にアクセスできません。

## <a name="solution"></a>解決策

### <a name="process-overview"></a>プロセスの概要:

1. 修復 VM を作成してアクセスする
1. ディスクの空き領域を増やす
1. BCD ストアから不良メモリを削除する
1. ページ ファイルを既定の場所に復元する
1. シリアル コンソールとメモリ ダンプの収集を有効にする
1. VM を再構築する

> [!NOTE]
> このエラーが発生する場合、ゲスト OS は動作しなくなります。 この問題を解決するには、オフライン モードでトラブルシューティングを行います。

### <a name="create-and-access-a-repair-vm"></a>修復 VM を作成してアクセスする

1. [VM 修復コマンドの手順 1 から 3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) を使用して、修復 VM を準備します。
1. リモート デスクトップ接続を使用し、修復 VM に接続します。

### <a name="for-generation-2-vms-assign-a-letter-to-the-extensible-firmware-interface-efi-partition"></a>第 2 世代 VM の場合、拡張ファームウェア インターフェイス (EFI) パーティションに文字を割り当てる

第 2 世代 VM を使用している場合、アタッチされているディスクの EFI パーティションに文字が割り当てられていない可能性があります。 このトラブルシューティング ガイドに進む前に、次の手順に従って、このパーティションに文字を割り当てる必要があります。

1. Windows Search で「`diskmgmt`」と入力し、**ディスクの管理コンソール**を開きます。
1. 修復 VM にアタッチされている破損したディスクを特定します。 通常、このディスクはコンソールの最後に表示され、最も大きな数値になっています。
1. そのディスクに、文字値 (*F:* ドライブなど) が割り当てられていない **EFI システム パーティション**を保持するパーティションがあるかどうかを確認します。 すべてのパーティションが割り当てられている場合は、ディスクの空き領域を増やす手順に進んでかまいません。 それ以外の場合は、引き続きこのディスクに文字を割り当てます。

   ![アタッチされているディスク "ディスク 2" と、"EFI システム パーティション" である 100 MB の未割り当てのパーティションが表示されたディスクの管理コンソール。](./media/troubleshoot-windows-stop-error/3.png)

1. 管理者として管理者特権でのコマンド プロンプトを開き、「`diskpart`」と入力して、**DiskPart** ツールを起動します。
1. 次のコマンドを入力します。

   ```
   list disk
   sel disk <NUMBER OF THE ATTACHED DISK>
   list partition
   sel partition <NUMBER OF THE SYSTEM (EFI) PARTION>
   assign
   ```
   
   - コマンドの `<NUMBER OF THE ATTACHED DISK>` を、手順 2. で特定したディスク番号に置き換えます。
   - コマンドの `<NUMBER OF THE SYSTEM PARTION>` を、EFI システム パーティションのパーティション番号に置き換えます。 このパーティションには文字がまだ割り当てられていませんが、種類が **System** で、サイズが約 100 MB である必要があります。

   > [!NOTE]
   > ディスクの管理コンソールのパーティションと、DiskPart ツールに表示されるものを比較すると、アタッチされているディスクの EFI システム パーティションに対応するパーティション番号を確認するのに役立ちます。

1. コマンド プロンプト ウィンドウを閉じます。

### <a name="free-up-space-on-the-disk"></a>ディスクの空き領域を増やす

破損したディスクが修復 VM にアタッチされたら、そのディスクの OS に、正しく機能するための十分な空き領域があることを確認する必要があります。 

1. アタッチされているディスクのドライブを右クリックし、 **[プロパティ]** を選択して、ディスクがいっぱいになっていないかどうかを確認します。
1. ディスクの**空き領域が 300 MB 未満**の場合は、[PowerShell を使用して最大 1 TB まで拡張](../windows/expand-os-disk.md)します。
1. ディスク サイズが **1 TB** になったら、ディスク クリーンアップを実行する必要があります。 [ディスク クリーンアップ ツール](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)を使用して、空き領域を増やすことができます。
1. 管理者特権でのコマンド プロンプト (管理者として実行) インスタンスを開き、ドライブのデフラグを実行します。

   ``
   defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g
   ``
   
   - 断片化のレベルによっては、デフラグに数時間かかることがあります。
   - コマンドの `<LETTER ASSIGNED TO THE OS DISK>` を、OS ディスクの文字 (*F:* ドライブなど) に置き換えます。

### <a name="clean-out-bad-memory-from-the-boot-configuration-data-bcd-store"></a>ブート構成データ (BCD) ストアから不良メモリを削除する

1. 管理者特権でのコマンド プロンプト (管理者として実行) を開きます。
1. 次のコマンドを使用して、ブート構成ファイルに対して無効なメモリ フラグのクエリを実行します。

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /enum {badmemory}
   ``
   
   - コマンドの `<LETTER ASSIGNED TO THE OS DISK>` を、OS ディスクの文字 (*F:* ドライブなど) に置き換えます。

1. クエリで不良メモリ ブロックが示されない場合は、次のタスクに進んでください。 それ以外の場合は、手順 4. に進みます。
1. 不良メモリ ブロックが特定された場合は、これらのブロックが ramdisk の作成の妨げとなっているので、次のコマンドを使用して削除する必要があります。

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /deletevalue {badmemory} badmemorylist
   ``
   
   - コマンドの `<LETTER ASSIGNED TO THE OS DISK>` を、OS ディスクの文字 (*F:* ドライブなど) に置き換えます。

### <a name="restore-the-page-file-to-its-default-location"></a>ページ ファイルを既定の場所に復元する

ページ ファイルには、コンピューターのランダム アクセス メモリ (RAM) で保持できないデータがオーバーフローまたはバックアップとして格納されます。 Azure の既定の場所である一時ドライブではなく、VHD でこのファイルがホストされている可能性があります。 その場合、ファイルにアクセスできない可能性があるため、既定の場所に復元する必要があります。

手順を実行する前に、正常なディスクに **\windows\system32\config** フォルダーのコピーを作成してください。 この手順により、不要な変更を元に戻すことができるようになります。 重要なシステム ファイルを操作するため、この予防措置を強くお勧めします。

1. Windows Search で「**regedit**」と入力し、レジストリ エディター アプリケーションを開きます。
1. レジストリ エディターで、**HKEY_LOCAL_MACHINE** キーを強調表示し、メニューから **[ファイル] > [ハイブの読み込み]** を選択します。

   ![レジストリ エディターのハイブの読み込みメニュー。](./media/troubleshoot-windows-stop-error/4.png)

1. [ハイブの読み込み] ダイアログで、 **\windows\system32\config\SYSTEM** を選択し、[開く] をクリックします。
   1. 名前の入力を求められたら、「**BROKENSYSTEM**」と入力します。 この名前は、トラブルシューティング中に影響を受けるハイブを区別するのに役立ちます。
   1. **[HKEY_LOCAL_MACHINE]** を展開して、追加した新しい BROKENSYSTEM キーを表示します。
1. レジストリ エディターを使用して、マシンの起動元の ControlSet を特定します。
   1. **[HKEY_LOCAL_MACHINE] > [BROKENSYSTEM] > [選択]** の順に移動します。
   1. 表示されたキーで、Current のデータ値を確認します。 たとえば、この値が **1** または **0x00000001 (1)** の場合、コントロール セットは ControlSet001 になります。
1. ページ ファイルの作成が構成されている場所を確認します。
   1. HKEY_LOCAL_MACHINE\BROKENSYSTEM で、手順 4. で確認した ControlSet 番号 (**ControlSet001** など) に一致するディレクトリを展開します。
   1. **[コントロール] > [セッション マネージャー] > [メモリ管理]** の順に移動し、**ExistingPageFiles** キーの場所を確認します。
   1. このキーは、Azure の既定の場所である一時ドライブにある必要があります。 そこにはなく、別の場所の VHD (データ ディスク ドライブや OS ドライブなど) にある場合は、それを削除する必要があります。
   1. エクスプローラーでその場所を参照し、**pagefile.sys** ファイルを削除します。

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>シリアル コンソールとメモリ ダンプの収集を有効にする

**[推奨]** : VM を再構築する前に、次のスクリプトを実行してシリアル コンソールとメモリ ダンプの収集を有効にします。

メモリ ダンプ コレクションとシリアル コンソールを有効にするには、次のスクリプトを実行します。

1. 管理者として、管理者特権でのコマンド プロンプト セッションを開きます。
1. BCD ストアのデータを表示し、ブート ローダーの識別子を確認します。これは次の手順で使用します。

   1. 第 1 世代 VM の場合、次のコマンドを入力し、表示された識別子を書き留めます。
   
      ``
      bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
      ``
   
   - コマンドの `<BOOT PARTITON>` を、アタッチされているディスクの、ブート フォルダーが格納されているパーティションの文字に置き換えます。

      ![第 1 世代 VM での BCD ストアを示す出力。Windows ブート ローダーに識別子番号が表示されています。](./media/troubleshoot-windows-stop-error/5.png)

   1. 第 2 世代 VM の場合、次のコマンドを入力し、表示された識別子を書き留めます。
   
      ``
      bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
      ``
   
   - コマンドの `<LETTER OF THE EFI SYSTEM PARTITION>` を、EFI システム パーティションの文字に置き換えます。
   - ディスクの管理コンソールを起動して、**EFI システム パーティション**というラベルが付いた適切なシステム パーティションを特定すると役立つ場合があります。
   - 識別子は一意の GUID の場合もあれば、既定の **bootmgr** の場合もあります。

1. 次のコマンドを実行して、シリアル コンソールを有効にします。

   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
   
   - コマンドの `<VOLUME LETTER WHERE THE BCD FOLDER IS>` を、BCD フォルダーの文字に置き換えます。
   - コマンドの `<BOOT LOADER IDENTIFIER>` を、前の手順で確認した識別子に置き換えます。

1. OS ディスクの空き領域が、VM のメモリ サイズ (RAM) よりも大きいことを確認します。

   OS ディスクに十分な領域がない場合は、メモリ ダンプ ファイルが作成される場所を変更し、その場所で十分な空き領域がある VM に接続された任意のデータ ディスクを参照します。 場所を変更するには、次のコマンドで、 **%SystemRoot%** をデータ ディスクのドライブ文字 (**F:** ドライブなど) に置き換えます。

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

[VM 修復コマンドの手順 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) を使用して、VM を再構築します。
