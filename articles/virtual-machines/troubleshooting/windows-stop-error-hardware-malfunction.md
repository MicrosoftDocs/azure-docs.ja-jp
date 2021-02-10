---
title: Windows STOP エラー - ハードウェアの誤動作
description: この記事では、Windows Server 2008 仮想マシンが、ハードウェアの誤動作が発生したことを示すエラー メッセージを表示してクラッシュしする場合の問題を解決する手順について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: 89faa5b29e0a972f31ad51a7354635a53176541a
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661359"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Windows STOP エラー - ハードウェアの誤動作

この記事では、Windows Server 2008 仮想マシンが、ハードウェアの誤動作が発生したことを示すエラー メッセージを表示してクラッシュしする場合の問題を解決する手順について説明します。

## <a name="symptoms"></a>現象

[ブート診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、スクリーンショットには、次のメッセージのブルー スクリーンが表示されていることがわかります。

**\*\*\*ハードウェアの誤動作**

**サポートについてはベンダーに問い合わせてください**

**\*\*\* システムは停止しました \*\*\***

#### <a name="blue-screen"></a>ブルー スクリーン

![スクリーンショットは、ハードウェアの誤動作によるブルー スクリーンのクラッシュを示しています。](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>シリアル コンソール

![シリアル コンソールが有効になっていた場合、スクリーンショットには、シリアル コンソール機能での "ハードウェアの誤動作" というメッセージが表示されます。](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>原因

この画面は、ゲスト OS が正しくセットアップされず、マスク不可能割り込み (NMI) が送信されたときに表示されます。 エラー メッセージは、ハンドラーでキャッチされなかった例外が、カーネル モードのプログラムによって生成されたことを示しています。 メモリ ダンプを収集すると、どの例外が生成されたかを特定できます。

## <a name="solution"></a>解決策

### <a name="process-overview"></a>プロセスの概要 

> [!TIP]
> VM の最新のバックアップがある場合は、[そのバックアップから VM の復元](../../backup/backup-azure-arm-restore-vms.md)を試行して、起動の問題を修正することができます。

1. マスク不可能割り込み (NMI) レジストリ キーを設定する 
2. 修復 VM を作成してアクセスする 
3. シリアル コンソールとメモリ ダンプの収集を有効にする 
4. VM を再構築する 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>マスク不可能割り込み (NMI) レジストリ キーを設定する

1. Azure portal を使用して、ゲスト OS が正常に起動するように VM を再起動します。 
2. VM への一部のアクセスが復元されたら、管理者特権のコマンド プロンプトを開きます (管理者として実行する)。 
3. 次のコマンドを使用して NMI レジストリ キーを設定します。

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [REG ADD コマンドに関する詳細情報を表示します](/windows-server/administration/windows-commands/reg-add)
4. *(省略可能)* メモリ ダンプ コレクションを設定します。

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(省略可能)* シリアル コンソールへのアクセスを設定します。

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [BCDEDIT コマンドに関する詳細情報を表示します](/windows-server/administration/windows-commands/bcdedit)
6. 次のコマンドを使用して VM を再起動します。

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [SHUTDOWN コマンドに関する詳細情報を表示します](/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> これで問題が修正されるはずです。

> [!NOTE]
> 再起動後、VM をテストして、正常に動作していることを確認します。 まだ問題が発生する場合は、さらに指示がある次のセクションに進むことができます。

> [!TIP]
> 上記のセクションでは、マスク不可能割り込み (NMI) レジストリ キーを設定することをお勧めします。ただし、後で VM が正常に起動しなかった場合は、ゲスト OS レジストリに対する意図した変更が行われていない可能性があります。 それに該当する場合は、下記の手順に従って、代わりに手動でレジストリ設定を追加することができます。

### <a name="create-and-access-a-repair-vm"></a>修復 VM を作成してアクセスする

1. [仮想マシンの修復コマンド](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)に関する説明の手順 1 から 3 に従い、修復 VM を準備します。
2. リモート デスクトップ接続を使用し、修復 VM に接続します。

### <a name="enable-serial-console-and-memory-dump-collection"></a>シリアル コンソールとメモリ ダンプの収集を有効にする

VM を再構築する前に、メモリ ダンプの収集とシリアル コンソールを有効にすることをお勧めします。 そのためには、下記のスクリプトを実行します。 

1. 管理者特権のコマンド プロンプト セッション ([管理者として実行]) を開きます。 
2. BCD ストアのデータを表示し、ブート ローダーの識別子を確認します。これは次の手順で使用します。 
    1. 第 1 世代 VM の場合、次のコマンドを入力し、表示された識別子を書き留めます。 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        コマンドの `<BOOT PARTITON>` を、アタッチされているディスクの、ブート フォルダーが格納されているパーティションの文字に置き換えます。 

        ![スクリーンショットは、第 1 世代 VM 内の BCD ストアを一覧表示した出力を示しています。そこには、Windows ブート ローダーの下に識別子番号が示されています。](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. 第 2 世代 VM の場合、次のコマンドを入力し、表示された識別子を書き留めます。
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * コマンドの `<LETTER OF THE EFI SYSTEM PARTITION>` を、EFI システム パーティションの文字に置き換えます。
        * ディスクの管理コンソールを起動して、*EFI システム パーティション* というラベルが付いた適切なシステム パーティションを特定すると役立つ場合があります。
        * 識別子は一意の GUID の場合もあれば、既定の *bootmgr* の場合もあります。
3. 次のコマンドを実行して、シリアル コンソールを有効にします。

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * コマンドの `<VOLUME LETTER WHERE THE BCD FOLDER IS>` を、BCD フォルダーの文字に置き換えます。
    * コマンドの `<BOOT LOADER IDENTIFIER>` を、前の手順で確認した識別子に置き換えます。
4. OS ディスクの空き領域が、VM のメモリ サイズ (RAM) よりも大きいことを確認します。 
    1. OS ディスクに十分な領域がない場合は、メモリ ダンプ ファイルが作成される場所を変更する必要があります。 OS ディスク上にファイルを作成するのではなく、十分な空き領域がある VM に接続されている他のデータ ディスクを参照することができます。 場所を変更するには、以下に示したコマンドで、 **%SystemRoot%** を、データ ディスクのドライブ文字 (たとえば **F:** ) に置き換えます。 
    2. 次のコマンドを入力します (推奨されるダンプ構成)。

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

* [VM 修復コマンドの手順 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) に従って VM を再構築します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure 仮想マシンのブート エラーのトラブルシューティング](./boot-error-troubleshoot.md)