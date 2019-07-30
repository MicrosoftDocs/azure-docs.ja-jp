---
title: Azure Data Box Disk でのデータのコピーに関する問題のトラブルシューティング | Microsoft Docs
description: ログを使用して Azure Data Box Disk でのデータのコピー中に見られる問題のトラブルシューティングを行う方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148135"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Azure Data Box Disk でのデータのコピーに関する問題のトラブルシューティング

この記事は、Microsoft Azure Data Box Disk を対象にしており、データをディスクにコピーするときに目にする問題のトラブルシューティング方法について説明します。 この記事には、分割コピー ツールを使用するときの問題も含まれています。


## <a name="data-copy-issues-when-using-a-linux-system"></a>Linux システムを使用するときのデータのコピーに関する問題

このセクションでは、データをディスクにコピーするために Linux クライアントを使用するときに直面する上位の問題の一部について詳述します。

### <a name="issue-drive-getting-mounted-as-read-only"></a>問題: ドライブが読み取り専用としてマウントされる
 
**原因** 

クリーンでないファイル システムが原因である可能性があります。

Data Box Disk でドライブを読み取り/書き込み用として再マウントできません。 このシナリオは、dislocker によって暗号化解除されたドライブではサポートされていません。 次のコマンドを使用して、デバイスを正常に再マウントできた可能性があります。

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

ただし、再マウントが成功しても、データは保持されません。

**解決策**

Linux システムで次の手順を実行します。

1. ntfsfix ユーティリティの `ntfsprogs` パッケージをインストールします。
2. ロック解除ツールによって、ドライブに提供されているマウント ポイントをマウント解除します。 マウント ポイント数は、ドライブによって異なります。

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. 対応するパスで `ntfsfix` を実行します。 強調表示された数字は、ステップ 2 と同じでなければなりません。

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. 次のコマンドを実行して、マウントの問題を生じさせる可能性のある休止状態のメタデータを削除します。

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. クリーンなマウント解除を実行します。

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. クリーンなロック解除を実行して、マウントします。
7. ファイルに書き込むことによって、マウント ポイントをテストします。
8. マウント解除後に再マウントして、ファイルの永続性を検証します。
9. データのコピーを続行します。
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>問題: データがコピー後に保持されないエラー
 
**原因** 

ドライブのマウント解除後、ドライブにデータがないことがわかった場合 (データがコピーされたにもかかわらず) は、ドライブを読み取り専用としてマウントした後で、読み取り/書き込みとして再マウントした可能性があります。

**解決策**
 
その場合は、「[ドライブが読み取り専用としてマウントされる](#issue-drive-getting-mounted-as-read-only)」の解決方法を参照してください。

上記の方法が該当しない場合は、Data Box Disk ロック解除ツールが入っているフォルダーからログをコピーし、[Microsoft サポートにお問い合わせください](data-box-disk-contact-microsoft-support.md)。


## <a name="data-box-disk-split-copy-tool-errors"></a>Data Box Disk 分割コピー ツールのエラー

複数のディスクにわたってデータを分割するために、分割コピー ツールを使用するときに目にする問題は、次の表で要約されています。

|エラー メッセージ/警告 |Recommendations |
|---------|---------|
|[情報] Retrieving BitLocker password for volume: m (ボリューム m の BitLocker パスワードを取得しています) <br>[エラー] Exception caught while retrieving BitLocker key for volume m: (ボリューム m の BitLocker キーの取得中に例外がキャッチされました)<br> Sequence contains no elements. (シーケンスに要素が含まれていません。)|対象となる Data Box Disk がオフラインである場合は、このエラーがスローされます。 <br> ディスクをオンラインにするには、`diskmgmt.msc` ツールを使用します。|
|[エラー] 例外がスローされました: WMI 操作に失敗しました:<br> Method=UnlockWithNumericalPassword、ReturnValue=2150694965、 <br>Win32Message=指定した回復パスワードの形式が無効です。 <br>BitLocker 回復パスワードは 48 桁である必要があります。 <br>回復パスワードが正しい形式であることを確認し、再試行してください。|Data Box Disk ロック解除ツールを使用して、ディスクのロックを解除してから、コマンドを再試行してください。 詳細については、次を参照してください <li> [Windows クライアントの Data Box Disk のロック解除](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client)に関するページ。 </li><li> [Linux クライアントの Data Box Disk のロック解除](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)に関するページ。 </li>|
|[エラー] 例外がスローされました: A DriveManifest.xml file exists on the target drive. (DriveManifest.xml ファイルがターゲット ドライブに存在しています。) <br> This indicates the target drive may have been prepared with a different journal file. (これは、ターゲット ドライブが別のジャーナル ファイルを使用して準備されたことを示しています。) <br>To add more data to the same drive, use the previous journal file. (同じドライブにデータをさらに追加するには、前のジャーナル ファイルを使用します。) To delete existing data and reuse target drive for a new import job, delete the *DriveManifest.xml* on the drive. (既存のデータを削除して、新しいインポート ジョブのためにターゲット ドライブを再利用するには、ドライブの DriveManifest.xml を削除します。) Rerun this command with a new journal file. (新しいジャーナル ファイルを使用して、このコマンドを再実行します。)| このエラーは、複数のインポート セッションで同じドライブのセットを使用しようとしたときに発生します。 <br> 1 つの分割とコピーのセッションには、1 つのドライブのセットのみを使用します。|
|[エラー] 例外がスローされました: CopySessionId importdata-sept-test-1 refers to a previous copy session and cannot be reused for a new copy session. (CopySessionId importdata-sept-test-1 が前のコピー セッションを参照しており、新しいコピー セッションのために再利用できません。)|このエラーは、前に正常に完了したジョブと同じジョブ名を新しいジョブに使用しようとした場合にレポートされます。<br> 新しいジョブに一意の名前を割り当ててください。|
|[情報] Destination file or directory name exceeds the NTFS length limit. (ターゲットのファイルまたはディレクトリの名前が、NTFS の長さの制限を超えています。) |このメッセージは、ファイル パスが長いためにターゲット ファイルの名前が変更された場合にレポートされます。<br> この動作を制御するには、`config.json` ファイル内の disposition オプションを変更します。|
|[エラー] 例外がスローされました: Bad JSON escape sequence. (JSON エスケープ シーケンスが正しくありません。) |このメッセージは、config.json の形式が無効である場合にレポートされます。 <br> ファイルを保存する前に、[JSONlint](https://jsonlint.com/) を使用して `config.json` を検証してください。|


## <a name="next-steps"></a>次の手順

- [検証ツールの問題をトラブルシューティングする](data-box-disk-troubleshoot.md)方法を学習します。
