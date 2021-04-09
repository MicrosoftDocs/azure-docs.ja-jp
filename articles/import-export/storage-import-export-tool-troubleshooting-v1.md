---
title: Azure Import/Export のインポートとエクスポートに関する問題のトラブルシューティング | Microsoft Docs
description: Azure Import/Export を使用するときの一般的な問題に対処する方法について説明します。
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706046"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Azure Import/Export の問題のトラブルシューティング
この記事では、Azure Import/Export でデータをインポートおよびエクスポートするときの一般的な問題をトラブルシューティングする方法について説明します。

## <a name="a-copy-session-failed-what-i-should-do"></a>コピー セッションが失敗しました。 どうすればよいでしょうか。  

コピー セッションが失敗した場合、2 つの対処方法があります。  
* エラーが再試行可能な場合 (ネットワーク シェアが一時的にオフラインだったが、今はオンラインに戻っているなど) は、コピー セッションを再開できます。
* エラーが再試行できない場合 (コマンド ライン パラメーターに誤ったソース ファイル ディレクトリを指定したなど) は、コピー セッションを中断する必要があります。
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>コピー セッションを中断または再開できません。

ドライブのコピー セッションがはじめての場合、「The first copy session cannot be resumed or aborted (最初のコピー セッションを再開または中断できません) 」というエラー メッセージが表示されます。 この場合、古いジャーナル ファイルを削除し、コマンドを再実行できます。  

ドライブのコピー セッションが 2 回目以降の場合、セッションは常に再開または中断できます。  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>ジャーナル ファイルを紛失しました。 それでもジョブを作成できますか。

ドライブのジャーナル ファイルには、データ コピーからの完全なセッション情報が含まれています。 ドライブにファイルを追加すると、インポート ジョブの作成にジャーナル ファイルが使用されます。 ジャーナル ファイルを紛失した場合は、ドライブのすべてのコピー セッションを再実行する必要があります。

## <a name="next-steps"></a>次のステップ

* [Azure Import/Export ツールを設定する](storage-import-export-tool-setup-v1.md)
* [インポート ジョブ用のハード ドライブを準備する](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [コピー ログ ファイルを使用してジョブの状態を確認する](storage-import-export-tool-reviewing-job-status-v1.md)
* [インポート ジョブを修復する](storage-import-export-tool-repairing-an-import-job-v1.md)
* [エクスポート ジョブを修復する](storage-import-export-tool-repairing-an-export-job-v1.md)