---
title: "Azure Import/Export ツールのインポート ジョブのコマンドのクイック リファレンス - v1 | Microsoft Docs"
description: "Azure Import/Export ツールで頻繁に使用されるインポート ジョブ コマンドのコマンド リファレンスです。 このリファレンスでは、Import/Export ツール v1 について説明します。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 632100f324c47d69f64cff020aafbaa300ae8c2b
ms.lasthandoff: 03/30/2017


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>インポート ジョブで頻繁に使用するコマンドのクイック リファレンス
ここでは、頻繁に使用するいくつかのコマンドのクイック リファレンスを提供します。 詳細な使用方法については、「[インポート ジョブ用のハード ドライブを準備する](storage-import-export-tool-preparing-hard-drives-import-v1.md)」を参照してください。  

## <a name="prepare-the-disks-when-data-already-copied-to-the-disks"></a>データがディスクにコピー済みの場合にディスクを準備する
 BitLocker でまだ暗号化されていないハード ドライブにデータをコピー済みの場合にディスクを準備するためのサンプル コマンドを次に示します。  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>1 つのディレクトリをハード ドライブにコピーする  
 BitLocker でまだ暗号化されていないハード ドライブに 1 つのソース ディレクトリをコピーするためのサンプル コマンドを次に示します。  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-wwo-directories-to-a-hard-drive"></a>2 つのディレクトリをハード ドライブにコピーする  
 2 つのソース ディレクトリをドライブにコピーするには、2 つのコマンドが必要です。  
  
 最初のコマンドでは、共通のパラメーターのほか、ログ ディレクトリ、ストレージ アカウント キー、コピー先のドライブ文字と `format/encrypt` の要件を指定します。  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 2 番目のコマンドでは、ジャーナル ファイル、新しいセッション ID、およびコピー元とコピー先の場所を指定します。  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>2 番目のコピー セッションでサイズの大きなファイルをハード ドライブにコピーする  
 前のコピー セッションで準備されたドライブにサイズの大きな 1 つのファイルをコピーするサンプル コマンドを次に示します。  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>次のステップ

* [インポート ジョブ用のハード ドライブを準備するためのサンプル ワークフロー](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)

