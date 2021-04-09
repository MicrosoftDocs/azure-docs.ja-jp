---
title: Azure VM ファイルの回復に関するトラブルシューティングを行う
description: Azure VM バックアップからファイルとフォルダーを復旧する場合の問題のトラブルシューティングを行います。
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 0ee856b10c6a5fbea6f6f76b2082949ab9c1e0db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700305"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Azure VM バックアップのファイル回復に関する問題のトラブルシューティングを行う

この記事では、Azure 仮想マシン (VM) バックアップからファイルとフォルダーを復旧する際に発生する問題の解決に役立つトラブルシューティングの手順について説明します。

## <a name="common-error-messages"></a>一般的なエラー メッセージ

このセクションでは、表示される可能性のあるエラー メッセージをトラブルシューティングする手順について説明します。

### <a name="exception-caught-while-connecting-to-target"></a>"Exception caught while connecting to target (ターゲットへの接続中に例外がキャッチされました)"

**考えられる原因**: スクリプトで回復ポイントにアクセスできません。

**推奨される操作:** この問題を解決するには、[スクリプトは実行されたが、接続は失敗](#the-script-runs-but-the-connection-to-the-iscsi-target-failed)に関するセクションに記載されている手順に従ってください。

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"The target has already been logged in via an iSCSI session (ターゲットは iSCSI セッション経由で既にログインされています)"

**考えられる原因**: スクリプトは同じマシンで既に実行されたため、ドライブがアタッチされています。

**推奨される操作:** 回復ポイントのボリュームは既にアタッチされています。 それらは、元の VM と同じドライブ文字を使用してマウントできません。 エクスプローラーで使用可能なボリュームを参照してください。

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>"This script is invalid because the disks have been dismounted via portal/exceeded the 12-hr limit. (ディスクがポータル経由でマウント解除されているか、12 時間の制限を超過しているため、このスクリプトは無効です。) Download a new script from the portal (ポータルから新しいスクリプトをダウンロードしてください)"

**考えられる原因**: ディスクがポータルからマウント解除されているか、12 時間の時間制限を超過しました。

**推奨される操作:** スクリプトは、ダウンロード後 12 時間経過すると無効になり、実行できなくなります。 ファイルの回復を続行するには、ポータルに移動し、新しいスクリプトをダウンロードします。

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>iscsi_tcp module can’t be loaded (iscsi_tcp モジュールを読み込めません) (または) iscsi_tcp_module not found (iscsi_tcp_module が見つかりません)

**推奨される操作:** この問題を解決するには、「[スクリプトは正常にダウンロードされますが、実行に失敗します](#the-script-downloads-successfully-but-fails-to-run)」の手順に従います。

## <a name="common-problems"></a>一般的な問題

このセクションでは、ファイル回復用のスクリプトをダウンロードして実行するときに発生するおそれがある一般的な問題のトラブルシューティングを行う手順について説明します。

### <a name="you-cant-download-the-script"></a>スクリプトをダウンロードできない

1. [スクリプトをダウンロードするために必要なアクセス許可](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script)があることを確認します。
1. Azure ターゲット IP への接続を確認します。 管理者特権のコマンド プロンプトで、次のいずれかのコマンドを実行します。

   `nslookup download.microsoft.com`

    or

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>スクリプトは正常にダウンロードされますが、実行に失敗します

SUSE Linux Enterprise Server 12 SP4 で項目レベルの回復 (ILR) 用の Python スクリプトを実行すると、"iscsi_tcp module can’t be loaded (iscsi_tcp モジュールを読み込めません)" または "iscsi_tcp_module not found (iscsi_tcp_module が見つかりません)" というエラーで失敗します。

**考えられる原因**: ILR モジュールでは、**iscsi_tcp** を使用して、バックアップ サービスへの TCP 接続が確立されます。 SLES 12 SP4 リリースの一環として、open-iscsi パッケージから **iscsi_tcp** が削除されため、ILR 操作は失敗します。

**推奨される操作:** SUSE 12 SP4 VM では、ファイル回復スクリプトの実行がサポートされていません。 SUSE 12 SP4 の以前のバージョンで回復操作を試してみてください。

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>スクリプトは実行されたが、iSCSI ターゲットへの接続は失敗した

"Exception caught while connecting to target (ターゲットへの接続中に例外がキャッチされました)" というエラー メッセージが表示されることがあります。

1. スクリプトが実行されているマシンが[アクセス要件](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script)を満たしていることを確認します。
1. Azure ターゲット IP への接続を確認します。 管理者特権のコマンド プロンプトで、次のいずれかのコマンドを実行します。

   `nslookup download.microsoft.com`

   or

   `ping download.microsoft.com`
1. iSCSI 送信ポート 3260 にアクセスできることを確認します。
1. Azure ターゲット IP または回復サービスの URL へのトラフィックをブロックしているファイアウォールや NSG がないか確認します。
1. ウイルス対策ソフトウェアがスクリプトの実行をブロックしていないことを確認します。

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>回復ポイントに接続したが、ディスクがアタッチされなかった

この問題を解決するには、お使いのオペレーティング システム用の手順に従ってください。

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>記憶域プールがあるサーバーで Windows ファイルの回復が失敗する

(記憶域プールを含む) Windows 2012 R2 および Windows 2016 でスクリプトを初めて実行するときに、記憶域プールが読み取り専用で VM にアタッチされていることがあります。

>[!Tip]
> [スクリプトを実行するための適切なコンピューター](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)があることを確かめます。

この問題を解決するには、手動で記憶域プールへの読み取り/書き込みアクセス権を割り当て、仮想ディスクをアタッチする必要があります。

1. **[サーバー マネージャー]**  >  **[ファイル サービスおよびストレージ サービス]**  >  **[ボリューム]**  >  **[記憶域プール]** の順に移動します。

   ![記憶域プールのオプションを示すスクリーンショット。](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. **[記憶域プール]** ウィンドウで、使用可能な記憶域プールを右クリックし、 **[読み取り-書き込みアクセス権の設定]** を選択します。

   ![記憶域プールの右クリック オプションを示すスクリーンショット。](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. 記憶域プールに読み取り/書き込みアクセス権が割り当てられたら、 **[仮想ディスク]** セクション内を右クリックし、 **[仮想ディスクのアタッチ]** を選択します。

   ![仮想ディスクの右クリック オプションを示すスクリーンショット。](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>ディスクにボリュームが含まれていないため、Linux ファイルの回復で自動マウントに失敗する

ファイルの回復を実行しているときに、バックアップ サービスによってボリュームが検出され、自動マウントが行われます。 しかし、バックアップ ディスクに未加工のパーティションがある場合、それらのディスクは自動マウントされず、回復用のデータ ディスクは表示されません。

この問題を解決するには、[Azure 仮想マシンのバックアップからのファイルの復旧](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms)に関する記事を参照してください。

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>OS でファイル システムを特定できなかったため、Linux ファイルの回復が失敗する

ファイルの回復スクリプトを実行すると、データ ディスクのアタッチに失敗します。 "The following partitions failed to mount since the OS couldn't identify the filesystem (OS でファイル システムを特定できなかったため、次のパーティションをマウントできませんでした)" というエラーが表示されます。

この問題を解決するには、ボリュームがサードパーティ製アプリケーションで暗号化されているかどうかを確認します。 それが暗号化されている場合、ディスクまたは VM はポータルで暗号化されたものとして表示されません。

1. バックアップされた VM にサインインし、次のコマンドを実行します。

   `lsblk -f`

   ![ブロック デバイスを一覧表示するコマンドの結果を示すスクリーンショット。](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. ファイル システムと暗号化を確認します。 ボリュームが暗号化されている場合、ファイルの回復はサポートされません。 詳細については、「[Azure VM バックアップのサポート マトリックス](./backup-support-matrix-iaas.md#support-for-file-level-restore)」を確認してください。

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>ディスクはアタッチされているが、ボリュームがマウントされない

この問題を解決するには、お使いのオペレーティング システム用の手順に従ってください。

#### <a name="windows"></a>Windows

Windows 用のファイル回復スクリプトを実行すると、"0 recovery volumes attached (0 個の回復ボリュームがアタッチされています)" というメッセージが表示されます。 ただし、ディスク管理コンソールではディスクが検出されます。

**考えられる原因**: iSCSI を介してボリュームをアタッチしたときに、検出されたいくつかのボリュームがオフラインになりました。 iSCSI チャネルによって、VM とサービスの間で通信が行われる場合、これらのボリュームは検出されてオンラインになり、マウントされません。

   ![0 個の回復ボリュームがアタッチされていることを示すスクリーンショット。](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

この問題を特定して解決するには、次の手順を行います。

>[!Tip]
>[スクリプトを実行するための適切なコンピューター](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)があることを確かめます。

1. **cmd** ウィンドウで **diskmgmt** を実行して、 **[ディスクの管理]** を開きます。
1. 追加のディスクがないか探します。 以下の例では、**Disk 2** が追加のディスクです。

   ![追加ディスクを含む [ディスクの管理] ウィンドウのスクリーンショット。](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. **[新しいボリューム]** を右クリックし、 **[ドライブ文字とパスの変更]** を選択します。

   ![追加ディスクの右クリック オプションを示すスクリーンショット。](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. **[ドライブ文字またはパスの変更]** ウィンドウで、 **[次のドライブ文字を割り当てる]** を選択し、使用可能なドライブを割り当てて **[OK]** を選択します。

   ![[ドライブ文字またはパスの変更] ウィンドウのスクリーンショット。](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. エクスプローラーを開いて、選択したドライブを表示し、ファイルを参照します。

#### <a name="linux"></a>Linux

>[!Tip]
>[スクリプトを実行するための適切なコンピューター](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)があることを確かめます。

保護された Linux VM で LVM または RAID アレイを使用する場合は、「[Azure 仮想マシンのバックアップからのファイルを回復する](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms)」の手順に従ってください。

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>マウントされたボリュームからファイルをコピーできない

コピーが次のエラーで失敗する場合があります。"0x80070780:ファイルにアクセスできません。" 

ソース サーバーでディスクの重複除去が有効になっているか確認します。 そうである場合は、復元サーバーでもドライブに対して重複除去が有効になっていることを確認します。 復元サーバー上でドライブを重複除去しないように、重複除去を未構成のままにすることができます。

## <a name="next-steps"></a>次のステップ

- [Azure 仮想マシンのバックアップからファイルとフォルダーを復旧する](backup-azure-restore-files-from-vm.md)