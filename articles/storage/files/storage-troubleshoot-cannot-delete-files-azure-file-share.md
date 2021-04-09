---
title: Azure ファイル共有 - Azure ファイル共有からファイルを削除できない
description: Azure ファイル共有からのファイル削除のエラーを特定し、トラブルシューティングを行います。
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 3d4f10745d90ccd83e7251af40d3e92a230f2fcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629684"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure ファイル共有 - Azure ファイル共有からファイルを削除できない

Azure ファイル共有からのファイルの削除に失敗すると、次のようないくつかの現象が発生する可能性があります。

**症状 1:**

次の 2 つの問題のいずれかにより、Azure ファイル共有内のファイルを削除できません。

* ファイルが削除対象にマークされている
* 指定されたリソースが、SMB クライアントによって使用されている可能性がある

**症状 2:**

このコマンドを実行するのに十分なクォータがありません

## <a name="cause"></a>原因

エラー 1816 は、ファイル共有がマウントされているコンピューター上のファイルに対して許可されている、同時に開くことのできるハンドルの上限に達したときに発生します。 詳しくは、[Azure Storage のパフォーマンスとスケーラビリティのチェック リスト](../blobs/storage-performance-checklist.md)に関するページをご覧ください。

## <a name="resolution"></a>解決方法

ハンドルをいくつか閉じて、同時に開いているハンドルの数を減らします。

## <a name="prerequisite"></a>前提条件

### <a name="install-the-latest-azure-powershell-module"></a>最新の Azure PowerShell モジュールをインストールします

* [Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Azure に接続します:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>ターゲット ストレージ アカウントのサブスクリプションを選択します:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>ターゲット ストレージ アカウントのコンテキストを作成します:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>ファイル共有の現在開いているハンドルを取得します:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>結果の例:

|HandleId|Path|ClientIp|ClientPort|OpenTime|LastReconnectTime|FileId|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|New folder/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>開いているハンドルを閉じます:

開いているハンドルを閉じるには、次のコマンドを使用します。

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>次のステップ

* [Windows での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure File Sync のトラブルシューティング](storage-sync-files-troubleshoot.md)