---
title: Azure Data Box Disk でのディスクのロック解除に関する問題のトラブルシューティング | Microsoft Docs
description: Azure Data Box Disk で見られる問題のトラブルシューティングを行う方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148115"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Azure Data Box Disk でのディスクのロック解除に関する問題のトラブルシューティング

この記事は Microsoft Azure Data Box Disk を対象にしており、ロック解除ツールを使用するときに発生する問題のトラブルシューティングに使用されるワークフローについて説明します。 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Data Box Disk ロック解除ツールのエラー


| エラー メッセージ/ツールの動作      | Recommendations                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| 現在の .NET Framework はサポートされていません。 サポートされているバージョンは 4.5 以降です。<br><br>メッセージと共にツールが終了します。  | .NET 4.5 がインストールされていません。 Data Box Disk ロック解除ツールが実行されるホスト コンピューターに .NET 4.5 以降をインストールします。                                                                            |
| ボリュームのロック解除または確認を行えませんでした。 Microsoft サポートにお問い合わせください。  <br><br>ロックされたドライブのロック解除または確認がツールで失敗します。 | ツールで、提供されたパスキーを使用して、ロックされたドライブを 1 つもロック解除することができませんでした。 Microsoft サポートに手順をお問い合わせください。                                                |
| 以下のボリュームのロック解除と確認が行われました。 <br>ボリュームのドライブ文字: E:<br>パスキー werwerqomnf、qwerwerqwdfda ではどのボリュームもロック解除できませんでした <br><br>ツールで一部のドライブがロック解除され、成功したドライブの文字と失敗したドライブの文字が一覧表示されます。| 部分的に成功しました。 提供されたパスキーで一部のドライブをロック解除できませんでした。 Microsoft サポートに手順をお問い合わせください。 |
| ロックされたボリュームが見つかりませんでした。 Microsoft から受け取ったディスクが正しく接続されており、ロックされた状態であることを確認してください。          | ロックされたドライブがツールで見つかりません。 ドライブは既にロック解除されているか、検出されませんでした。 ドライブが接続されており、ロックされていることを確認します。                                                           |
| 致命的なエラー: 無効なパラメーター<br>パラメーター名: invalid_arg<br>使用方法:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>例:DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>例:DataBoxDiskUnlock /SystemCheck<br>例:DataBoxDiskUnlock /Help<br><br>/PassKeys:      このパスキーは Azure DataBox Disk の注文から取得します。 パスキーによってディスクのロックが解除されます。<br>/Help:          このオプションでは、コマンドレットの使用方法と例のヘルプが表示されます。<br>/SystemCheck:   このオプションでは、ツールを実行するための要件をシステムが満たしているかどうかがチェックされます。<br><br>終了するには何かキーを押してください。 | 無効なパラメーターが入力されました。 許可されているパラメーターは、/SystemCheck、/PassKey、/Help のみです。|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Windows クライアントを使用している場合のディスクのロック解除に関する問題

このセクションでは、データ コピーに Windows クライアントを使用している場合に、Data Box Disk のデプロイ中に直面するいくつかの重要な問題について詳しく説明します。

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>問題: BitLocker のドライブのロックを解除できませんでした
 
**原因** 

BitLocker ダイアログでパスワードを使用し、BitLocker のドライブのロック解除ダイアログでディスクのロックを解除しようとしました。 これは、うまくいきません。

**解決策**

Data Box Disks のロックを解除するには、Data Box Disk ロック解除ツールを使用し、Azure portal でパスワードを指定する必要があります。 詳細については、「[チュートリアル: Azure Data Box Disk の開梱、接続、ロック解除](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey)」をご覧ください。
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>問題: いくつかのボリュームのロックの解除または確認を行えませんでした。 Microsoft サポートにお問い合わせください。
 
**原因**

エラー ログに次のエラーが表示され、一部のボリュームのロックの解除または確認ができないことがあります。

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
これは、Windows クライアントに適切なバージョンの Windows PowerShell が存在していない可能性があることを示しています。

**解決策**

[Windows PowerShell v 5.0](https://www.microsoft.com/download/details.aspx?id=54616) をインストールして、操作を再試行することができます。
 
この方法を試してもロックを解除できない場合は、Data Box Disk ロック解除ツールが入っているフォルダーからログをコピーし、[Microsoft サポートにお問い合わせください](data-box-disk-contact-microsoft-support.md)。

## <a name="next-steps"></a>次の手順

- [検証の問題をトラブルシューティングする](data-box-disk-troubleshoot.md)方法を学習します。
