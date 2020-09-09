---
title: Windows STOP エラーのトラブルシューティング – ディレクトリ サービスの初期化エラー
description: Azure の Active Directory ドメイン コントローラー仮想マシン (VM) がループでスタックし、再起動が必要であることを示す問題を解決します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 909481964f8aa3272715e235fa011562225a9422
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028364"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>Windows STOP エラーのトラブルシューティング – ディレクトリ サービスの初期化エラー

この記事では、Azure の Active Directory ドメイン コントローラー仮想マシン (VM) がループでスタックし、再起動が必要であることを示す問題を解決するための手順について説明します。

## <a name="symptom"></a>症状

[ブート診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、エラーのため VM を再起動する必要があることを示すスクリーンショットが表示され、Windows Server 2008 R2 では停止コード **0xC00002E1**、Windows Server 2012 以降では **0xC00002E2** が表示されます。

![Windows Server 2012 の起動画面に、"問題が発生したため、PC を再起動する必要があります。 エラー情報を収集しています。自動的に再起動します。" と表示されます。](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>原因

エラー コード **0xC00002E2** は **STATUS_DS_INIT_FAILURE**、エラー コード **0xC00002E1** は **STATUS_DS_CANT_START** を表します。 どちらのエラーもディレクトリ サービスに問題がある場合に発生します。

OS が起動すると、ユーザー ログインの認証を行うローカル セキュリティ認証サーバー (**LSASS.exe**) によって自動的に再起動されます。 VM 上のオペレーティング システムが、ローカルの Active Directory データベースへの読み取り/書き込みアクセス許可を持たないドメイン コントローラーの場合、認証は行われません。 **Active Directory (AD)** にアクセスできないため、LSASS.exe は認証を行うことができず、OS の再起動が強制されます。

このエラーは、次のいずれかの状況が原因で発生する可能性があります。

- ローカル AD データベース (**NTDS.DIT**) を保持しているディスクにアクセスできない。
- ローカル AD データベース (NTDS.DIT) を保持しているディスクの空き領域が不足している。
- ローカル AD データベース (NTDS.DIT) のファイルが欠落している。
- VM に複数のディスクがあり、記憶域ネットワーク (SAN) のポリシーが正しく構成されていない。 SAN ポリシーが **[ONLINEALL]** に設定されておらず、ディスク マネージャーで OS 以外のディスクがオフライン モードで接続されています。
- ローカル AD データベース (NTDS.DIT) のファイルが破損している。

## <a name="solution"></a>解決策

### <a name="process-overview"></a>プロセスの概要:

1. 修復 VM を作成してアクセスします。
1. ディスク上の領域を解放します。
1. AD データベースを含むドライブが接続されていることを確認します。
1. ディレクトリ サービス復元モードを有効にします。
1. **[推奨]** : VM を再構築する前に、シリアル コンソールとメモリ ダンプの収集を有効にします。
1. VM を再構築します。
1. SAN のポリシーを再構成します。

> [!NOTE]
> このエラーが発生する場合、ゲスト OS は動作しなくなります。 この問題を解決するには、オフライン モードでトラブルシューティングを行います。

### <a name="create-and-access-a-repair-vm"></a>修復 VM を作成してアクセスする

1. [VM 修復コマンドの手順 1 から 3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) を使用して、修復 VM を準備します。
1. リモート デスクトップ接続を使用し、修復 VM に接続します。

### <a name="free-up-space-on-disk"></a>ディスク上の領域を解放する

ディスクが修復 VM に接続されたので、Active Directory 内部データベースを保持しているディスクに、正常に実行するのに十分な空き領域があることを確認します。

1. ドライブを右クリックして **[プロパティ]** を選択し、ディスクがいっぱいになっていないかどうかを確認します。
1. ディスクの空き領域が 300 Mb 未満の場合は [PowerShell](../windows/expand-os-disk.md) を使用して最大 1 Tb まで拡張します。
1. ディスクの使用済み領域が 1 Tb に達した場合は、ディスクのクリーンアップを実行します。

   1. PowerShell を使用して、破損した VM から[データ ディスクをデタッチします](../windows/detach-disk.md#detach-a-data-disk-using-powershell)。
   1. 破損した VM からデタッチしたら、機能している VM に[データ ディスクを接続します](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)。
   1. [ディスク クリーンアップ ツール](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)を使用して、追加の領域を解放します。

1. **省略可能** - 領域がさらに必要な場合は、CMD インスタンスを開き、`defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g` コマンドを入力して、ドライブのデフラグを実行します。

  * コマンド内の `<LETTER ASSIGNED TO THE OS DISK>` を OS ディスクの文字に置き換えます。 たとえば、ディスク文字が `F:` の場合、コマンドは `defrag F: /u /x /g` となります。

  * フラグメント化のレベルによっては、デフラグに数時間かかることがあります。

ディスクに十分な空き領域がある場合は、次の作業に進みます。

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>AD Directory データベースを含むドライブが接続されていることを確認します

1. 管理者特権の CMD インスタンスを開き、次のコマンドを実行します。

   1. レジストリ ファイルをロードします。

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      指定 `f:` は、ディスクがドライブ `F:` であることを前提としています。 OS ディスクが格納されているドライブに属するドライブ文字を使用します。

   1. **NTDS.DIT** のドライブ文字とフォルダーを特定します。

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. レジストリ ファイルをアンロードします。

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. Azure portal を使用して、NTDS.DIT が設定されているドライブが VM に追加されていることを確認します。
1. ゲスト OS からのディスク管理コンソールを使用して、NTDS.DIT が格納されているディスクがオンラインであることを確認します。
   1. ディスク管理ツールは、 **[管理ツール] > [コンピューターの管理] > [ストレージ]** にあります。または、CMD インスタンスの `diskmgmt.msc` コマンドを使用してアクセスすることもできます。
1. ディスクが VM に接続されていない場合は、データ ディスクを再接続して問題を修正します。

   ディスクが正常に接続されている場合は、次の作業に進みます。

### <a name="enable-directory-services-restore-mode"></a>ディレクトリ サービス復元モードを有効にする

起動中に NTDS.DIT ファイルの存在を確認しないように、**ディレクトリ サービス復元モード (DSRM)** モードで起動するように VM を設定します。

1. 続行する前に、ディスクを修復 VM に接続するための前の作業が完了していること、および NTDS.DIT ファイルが配置されているディスクが判別されていることを確認してください。
1. 管理者特権の CMD インスタンスを使用して、そのストアの起動中のパーティション情報を一覧表示し、アクティブなパーティションから識別子を検索します。

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   `< Drive Letter >` を前の手順で特定した文字に置き換えます。

   ![このスクリーンショットは、"bcdedit /store <ドライブ文字>:\boot\bcd /enum" コマンドを入力した後の管理者特権の CMD インスタンスを示し、Windows ブート マネージャーが識別子と共に表示されています。](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. 起動中のパーティションで `safeboot DsRepair` フラグを有効にします。

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   `< Drive Letter >` と `< Identifier >` を、前の手順で判別した値に置き換えます。

1. 起動オプションを再度照会し、変更が適切に設定されていることを確認します。

   ![このスクリーンショットは、safeboot DsRepair フラグを有効にした後の、管理者特権の CMD インスタンスを示しています。](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>推奨: VM を再構築する前に、シリアル コンソールとメモリ ダンプの収集を有効にする

メモリ ダンプの収集とシリアル コンソールを有効にするには、管理者特権のコマンド プロンプト セッション (管理者として実行) を開いて次のスクリプトを実行して、次のコマンドを実行します。

1. シリアル コンソールの有効化:

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. OS ディスクの空き領域が、VM のメモリ サイズ (RAM) 以上であることを確認します。

  1. OS ディスクに十分な領域がない場合、メモリ ダンプ ファイルが作成される場所を変更し、その場所で十分な空き領域がある VM に接続された任意のデータ ディスクを参照します。

     場所を変更するには、以下のコマンドで `%SystemRoot%` をデータ ディスクのドライブ文字 (`F:` など) に置き換えます。

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>OS ダンプを有効にするには、次の構成を使用することをお勧めします。

  **破損した OS ディスクのロード:**

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

  **ControlSet001 での有効化**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **ControlSet002 での有効化**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **破損した OS ディスクのアンロード**:

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>VM を再構築する

1. [VM 修復コマンドの手順 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) を使用して、VM を再構成します。

### <a name="reconfigure-the-storage-area-network-policy"></a>記憶域ネットワーク ポリシーを再構成する

1. DSRM モードで起動する場合、ログインに使用できる唯一のユーザーは、VM がドメイン コントローラーに昇格されたときに使用された復旧管理者だけです。 他のすべてのユーザーには認証エラーが表示されます。

   1. 他に使用可能な DC がない場合は、`.\administrator` または `machinename\administrator` と DSRM パスワードを使用してローカルにログインする必要があります。

1. すべてのディスクがオンラインになるように SAN ポリシーを設定します。

   1. 管理者特権の CMD インスタンスを開き、`DISKPART` と入力します。
   1. ディスクの一覧を照会します。

      `DISKPART> list disk`

   1. 次のコマンドを入力して、オンラインにする必要があるディスクを選択し、SAN ポリシーを変更します。

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. 問題が解決したら、フラグ `DsRepair safeboot` が削除されるようにします。

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. VM を再起動する。

   > [!NOTE]
   > VM がオンプレミスから移行されたばかりで、さらに多くのドメイン コントローラーをオンプレミスから Azure に移行する場合は、次の記事の手順に従って、今後の移行でこの問題が発生しないようにすることを検討してください。
   >
   > [Azure PowerShell を使用して既存のオンプレミス ハイパー-V のドメイン コント ローラーを Azure にアップロードする方法](https://support.microsoft.com/help/2904015)
