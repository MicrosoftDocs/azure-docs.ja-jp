---
title: システム状態のバックアップをトラブルシューティングする
description: この記事では、オンプレミスの Windows Server のシステム状態のバックアップに関する問題をトラブルシューティングする方法について説明します。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 7c8e68da1c5da7b25d1385a82bf7dcc2f876306d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89376283"
---
# <a name="troubleshoot-system-state-backup"></a>システム状態のバックアップをトラブルシューティングする

この記事では、システム状態のバックアップを使用しているときに発生する可能性がある問題の解決方法について説明します。

## <a name="basic-troubleshooting"></a>基本的なトラブルシューティング

システム状態のバックアップのトラブルシューティングを開始する前に、以下の検証ステップを実行することをお勧めします。

- [Microsoft Azure Recovery Services (MARS) エージェントが最新であることを確認する](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [MARS エージェントと Azure の間にネットワーク接続が存在することを確認する](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Microsoft Azure Recovery Services が (サービス コンソールで) 実行されていることを確認します。 必要に応じて、再起動して操作をやり直します
- [スクラッチ フォルダーの場所に 5 から 10% の空きボリューム領域があることを確認する](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [別のプロセスまたはウイルス対策ソフトウェアによって Azure Backup が妨げられているかどうかを確認する](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [スケジュールされたバックアップが失敗したが、手動バックアップは機能する](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- OS に最新の更新プログラムが適用されていることを確認する
- [サポートされていない属性を持つサポートされていないドライブとファイルはバックアップから除外されることを確認する](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 保護されているシステム上の **システム クロック** が適切なタイム ゾーンに構成されていることを確認します <br>
- [サーバーに .Net Framework バージョン 4.5.2 以降がインストールされていることを確認します](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- コンテナーに **サーバーを再登録する** 場合は、次のことを行います。 <br>
  - エージェントがサーバーからアンインストールされていることと、ポータルから削除されていることを確認します。 <br>
  - 最初にサーバーの登録に使用したのと同じパスフレーズを使用します <br>
- これがオフライン バックアップの場合は、ソース コンピューターとコピー用コンピューターの両方に Azure PowerShell バージョン 3.7.0 がインストールされていることを確認します
- [Azure 仮想マシン (VM) 上で Backup エージェントが実行されている場合の考慮事項](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>制限事項

- Microsoft では、システム状態の回復を使用して別のハードウェアに回復することはお勧めしません。
- 現在、システム状態のバックアップでは、"オンプレミス" の Windows サーバーがサポートされています。 この機能は、Azure VM では使用できません。

## <a name="prerequisites"></a>前提条件

Azure Backup でシステム状態のバックアップをトラブルシューティングする前に、以下の前提条件の確認を実行してください。  

### <a name="verify-windows-server-backup-is-installed"></a>Windows Server バックアップがインストールされていることを確認する

Windows Server バックアップがインストールされ、サーバーで有効になっていることを確認します。 インストールの状態を確認するには、次の PowerShell コマンドを実行します。

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

出力で、**Install State** が **available** と表示される場合は、Windows Server バックアップ機能をインストールできるが、サーバーにはインストールされていないことを意味します。 ただし、Windows Server バックアップがインストールされていない場合は、以下のいずれかの方法を使用してインストールします。

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>方法 1:PowerShell を使用して Windows Server バックアップをインストールする

PowerShell を使用して Windows Server バックアップをインストールするには、次のコマンドを実行します。

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>方法 2:サーバー マネージャーを使用して Windows Server バックアップをインストールする

サーバー マネージャーを使用して Windows Server バックアップをインストールするには、次の手順を実行します。

1. **サーバー マネージャー** で **[役割と機能の追加]** を選択します。 **役割と機能の追加ウィザード** が表示されます。

    ![ダッシュボード](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. **[インストールの種類]** を選択し、 **[次へ]** を選択します。

    ![インストールの種類](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. サーバー プールからサーバーを選択し、 **[次へ]** を選択します。 サーバーの役割で、既定の選択のままにし、 **[次へ]** を選択します。
4. **[機能]** タブで **[Windows Server バックアップ]** を選択し、 **[次へ]** を選択します。

    ![[機能の選択] ウィンドウ](./media/backup-azure-system-state-troubleshoot/features.png)

5. **[確認]** タブで、 **[インストール]** を選択して、インストール プロセスを開始します。
6. **[結果]** タブに、Windows Server バックアップ機能が Windows Server に正常にインストールされていることが示されます。

    ![インストールの結果](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>システム ボリューム情報のアクセス許可

ローカル システムに、Windows がインストールされているボリュームに配置されている **システム ボリューム情報** フォルダーへのフル コントロールが与えられていることを確認します。 通常、これは **C:\System Volume Information** です。 上記のアクセス許可が正しく設定されていない場合、Windows Server バックアップは失敗する可能性があります。

### <a name="dependent-services"></a>依存サービス

次のサービスが実行中の状態であることを確認します。

**サービス名** | **スタートアップの種類**
--- | ---
リモート プロシージャ呼び出し (RPC) | 自動
COM+ イベント システム (EventSystem) | 自動
システム イベント通知サービス (SENS) | 自動
ボリューム シャドウ コピー (VSS) | マニュアル
Microsoft ソフトウェア シャドウ コピー プロバイダー (SWPRV) | マニュアル

### <a name="validate-windows-server-backup-status"></a>Windows Server バックアップの状態を検証する

Windows Server バックアップの状態を検証するには、以下の手順を実行します。

- WSB PowerShell が実行されていることを確認する

  - 管理者特権の PowerShell から `Get-WBJob` を実行し、次のエラーが返らないことを確認します。

    > [!WARNING]
    > Get-WBJob:用語 'Get-WBJob' は、コマンドレット、関数、スクリプト ファイル、または操作可能なプログラムの名前として認識されません。 名前が正しく記述されていることを確認し、パスが含まれている場合はそのパスが正しいことを確認してから、再試行してください。

    - このエラーで失敗した場合は、前提条件の手順 1 で説明したように、サーバー コンピューター上に Windows Server バックアップ機能を再インストールます。

  - 管理者特権でのコマンド プロンプトから次のコマンドを実行して、WSB バックアップが正しく動作していることを確認します。

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >X を、システム状態のバックアップ イメージを格納するボリュームのドライブ文字に置き換えてください。

    - 管理者特権の PowerShell から `Get-WBJob` コマンドを実行して、ジョブの状態を定期的に確認します。
    - バックアップ ジョブが完了したら、`Get-WBJob -Previous 1` コマンドを実行して、ジョブの最終状態を確認します。

ジョブが失敗した場合は、WSB に問題があるために MARS エージェントによるシステム状態のバックアップ エラーが発生したことを示しています。

## <a name="common-errors"></a>一般的なエラー

### <a name="vss-writer-timeout-error"></a>VSS ライターのタイムアウト エラー

| 症状 | 原因 | 解像度
| -- | -- | --
| - MARS エージェントが次のエラー メッセージで失敗する:"VSS エラーで WSB ジョブが失敗しました。 エラーを解決するには、VSS イベント ログを確認してください"<br/><br/> - VSS アプリケーション イベント ログに次のエラー ログが存在する:"A VSS writer has rejected an event with error 0x800423f2, the writer's timeout expired between the Freeze and Thaw events" (VSS ライターでイベントがエラー 0x800423f2 で拒否されています。ライターのタイムアウトは Freeze イベントと Thaw イベントの間に発生しました)| コンピューターで CPU とメモリ リソースが不足しているため、VSS ライターを時間内に完了できません <br/><br/> 別のバックアップ ソフトウェアが既に VSS ライターを使用しています。その結果、このバックアップに対するスナップショット操作を完了できませんでした | システムの CPU/メモリが解放されるまで待つか、メモリ/CPU の使用量が多すぎるプロセスを中止して操作をやり直します。 <br/><br/>  進行中のバックアップが完了するまで待つか、コンピューター上で後でバックアップが実行されていないときに操作をやり直します。

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>シャドウ コピーを拡大するためのディスク領域が不足している

| 症状 | 解像度
| -- | --
| - MARS エージェントが次のエラー メッセージで失敗する:システム ファイルが格納されているボリュームのディスク領域の不足により、シャドウ コピーのボリュームを拡大できなかったため、バックアップに失敗しました <br/><br/> - volsnap システム イベント ログに次のエラー/警告ログが存在する:"There was insufficient disk space on volume C: to grow the shadow copy storage for shadow copies of C: due to this failure all shadow copies of volume C: are at risk of being deleted" (C: のシャドウ コピー用のシャドウ コピー ストレージを拡大するための十分なディスク領域がボリューム C: 上に存在しません。このエラーのせいで、ボリューム C: のすべてのシャドウコピーが削除される恐れがあります) | - 強調表示されているボリュームの領域を解放して、バックアップの進行中にシャドウ コピー コピーを拡大するための十分な領域が存在するようにします <br/><br/> - シャドウ コピーの領域の構成中に、シャドウ コピー用に使用される領域の量を制限できます。 詳細については、こちらの[記事](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)を参照してください

### <a name="efi-partition-locked"></a>EFI パーティションがロックされている

| 症状 | 解像度
| -- | --
| MARS エージェントが次のエラー メッセージで失敗する:"System state backup failed as the EFI system partition is locked. This can be due to system partition access by a third-party security or back up software" (EFI システム パーティションがロックされているため、システム状態のバックアップが失敗しました。これは、サードパーティのセキュリティまたはバックアップ ソフトウェアによるシステム パーティションへのアクセスが原因である可能性があります) | - 問題の原因がサードパーティのセキュリティ ソフトウェアの場合は、MARS エージェントを許可できるようにウイルス対策ベンダーに連絡する必要があります <br/><br/> - サードパーティのバックアップ ソフトウェアが実行されている場合は、それが終了するまで待機した後、バックアップを再試行します

## <a name="next-steps"></a>次のステップ

- Resource Manager デプロイでの Windows システムの状態の詳細については、[Windows Server のシステム状態のバックアップ](backup-azure-system-state.md)に関する記事を参照してください。
