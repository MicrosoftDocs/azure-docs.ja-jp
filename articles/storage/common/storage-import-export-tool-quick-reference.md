---
title: Azure Import/Export ツールのインポート ジョブのコマンドのクイック リファレンス | Microsoft Docs
description: Azure Import/Export ツールで頻繁に使用されるインポート ジョブ コマンドのコマンド リファレンスです。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 7d21ab42188d5b8d6cb8c179a28d1b5bee822f05
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453588"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>インポート ジョブで頻繁に使用するコマンドのクイック リファレンス

この記事では、頻繁に使用するいくつかのコマンドのクイック リファレンスを提供します。 詳細な使用方法については、「[インポート ジョブ用のハード ドライブを準備する](../storage-import-export-tool-preparing-hard-drives-import.md)」を参照してください。

## <a name="first-session"></a>最初のセッション

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>2 番目のセッション

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>最新のセッションの中止

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>中断された最新のセッションの再開

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>最新のセッションへのドライブの追加

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>次の手順

* [インポート ジョブ用のハード ドライブを準備するためのサンプル ワークフロー](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
