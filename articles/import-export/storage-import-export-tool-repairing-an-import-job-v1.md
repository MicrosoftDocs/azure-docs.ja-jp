---
title: Azure Import/Export のインポート ジョブ - v1 の修復 | Microsoft Docs
description: Azure Import/Export サービスを使って、作成したインポート ジョブの修復と実行の方法について説明します。
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: af6234a81e8f79424cc3b1ec4178a175d9ff0146
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706058"
---
# <a name="repairing-an-import-job"></a>Import ジョブの修復
Microsoft Azure Import/Export サービスでは、ファイルの全部または一部の Windows Azure Blob service へのコピーに失敗する場合があります。 失敗の原因には次のものが挙げられます。  
  
-   ファイルが破損している  
  
-   破損したドライブ  
  
-   ファイル転送中にストレージ アカウント キーが変更された  
  
Microsoft Azure Import/Export ツールは、インポート ジョブのコピー ログ ファイルを使用して実行できます。 このツールにより、インポート ジョブを完了するために、不足しているファイルまたはファイルの一部が Windows Azure ストレージ アカウントにアップロードされます。  
  
## <a name="repairimport-parameters"></a>RepairImport パラメーター

次のパラメーターは **RepairImport** と共に指定できます。 
  
|||  
|-|-|  
|**/r:** <RepairFile\>|**必須。** 修復の進行状況を追跡し、中断された修復を再開できる修復ファイルへのパスです。 各ドライブには修復ファイルが 1 つのみ必要です。 特定のドライブの修復を開始する際、まだ存在していない修復ファイルへのパスに渡されます。 中断された修復を再開するには、既存の修復ファイルの名前を指定してください。 ターゲット ドライブに対応する修復ファイルを必ず指定してください。|  
|**/logdir:** <LogDirectory\>|**省略可。** ログ ディレクトリ。 詳細ログ ファイルは、このディレクトリに書き込まれます。 ログ ディレクトリが指定されていない場合、現在のディレクトリがログ ディレクトリとして使用されます。|  
|**/d:**<TargetDirectories\>|**必須。** インポートされた元のファイルを含む、セミコロンで区切られた 1 つまたは複数のディレクトリです。 インポート ドライブを使用することもできますが、元のファイルの別の場所が使用できる場合は必要ありません。|  
|**/bk:** <BitLockerKey\>|**省略可。** 元のファイルがある暗号化されたドライブのロックを解除する場合は BitLocker キーを指定します。|  
|**/sn:**<StorageAccountName\>|**必須。** インポート ジョブのストレージ アカウント名です。|  
|**/sk:**<StorageAccountKey\>|コンテナー SAS が指定されていない場合のみ **必須**。 インポート ジョブのストレージ アカウントのアカウント キーです。|  
|**/csas:**<ContainerSas\>|ストレージ アカウント キーが指定されていない場合のみ **必須**。 インポート ジョブに関連付けられている BLOB にアクセスするためのコンテナー SAS です。|  
|**/CopyLogFile:** <DriveCopyLogFile\>|**必須。** ドライブ コピー ログ ファイル (詳細ログまたはエラー ログ) へのパスです。 ファイルは、Windows Azure Import/Export サービスによって生成され、ジョブに関連付けられた BLOB ストレージからダウンロードできます。 コピー ログ ファイルには、障害が発生した BLOB または修復するファイルに関する情報が含まれています。|  
|**/PathMapFile:**<DrivePathMapFile\>|**省略可。** 同じジョブにインポートした複数のファイルに同じ名前が使用されている場合、あいまいさを解決するために使用されているテキスト ファイルへのパスです。 はじめてツールを実行すると、このファイルにすべてのあいまいな名前が入力されます。 その後のツールの実行では、あいまいさの解決のためにこのファイルが使用されます。|  
  
## <a name="using-the-repairimport-command"></a>RepairImport コマンドの使用  
ネットワーク経由でデータをストリーミングすることでインポート データを修復するには、インポートした元のファイルが格納されたディレクトリを、`/d` パラメーターを使用して指定する必要があります。 ストレージ アカウントからダウンロードしたコピー ログ ファイルも指定します。 部分的な不具合を持つインポート ジョブを修復するための一般的なコマンド ラインは、次のようになります。  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
次の、ログ ファイルをコピーする例では、インポート ジョブに転送されたファイルの 64 K 要素が 1 つドライブで破損しています。 これが検出された唯一の不具合であるため、ジョブ内の残りの BLOB は正常にインポートされています。  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
このコピー ログが Azure Import/Export ツールに渡されると、ツールは、欠損コンテンツをネットワーク経由でコピーすることでこのファイルのインポートを完了させようとします。 上の例の後、ツールは `C:\Users\bob\Pictures` と `X:\BobBackup\photos` の 2 つのディレクト内で元のファイル `\animals\koala.jpg` を検索します。 ファイル `C:\Users\bob\Pictures\animals\koala.jpg` が存在する場合、Azure Import/Export ツールはデータの欠損範囲を対応する BLOB にコピーします`http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`。  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>RepairImport を使用する際の競合の解決  
ツールで必要なファイルを検出できない、または開けない場合があります。これには、ファイルが検索できない、ファイルにアクセスできない、ファイル名があいまい、ファイルのコンテンツが正しくない、のいずれかが原因として考えられます。  
  
あいまいエラーは、ツールが `\animals\koala.jpg` を検索しようとし、`C:\Users\bob\pictures` と `X:\BobBackup\photos` の両方にこの名前が使用されているファイルが存在する場合に発生します。 つまり、`C:\Users\bob\pictures\animals\koala.jpg` と `X:\BobBackup\photos\animals\koala.jpg` の両方がインポート ジョブ ドライブに存在するということです。  
  
これらのエラーは、`/PathMapFile` オプションを使用すると解決できます。 ツールで正確に特定できなかったファイルのリストを含むファイル名を指定できます。 次のコマンドラインの例では、`9WM35C2V_pathmap.txt` に入力します。  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
これで、ツールは `9WM35C2V_pathmap.txt` への問題のあるファイル パスをそれぞれのラインに 1 つずつ書き込むようになります。 たとえば、コマンド実行後、ファイルには次のエントリが追加されます。  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 リスト内のファイルごとに、ファイルを見つけて開き、ファイルがツールで使用できることを確認します。 ファイルの検索場所を明示的にツールに指定する場合は、パス マップ ファイルを変更し、同じ行に各ファイルのパスをタブ文字で区切って追加します。  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
必要なファイルをツールで使用できるようにしたら、あるいはパス マップ ファイルを更新したら、ツールに戻りインポート処理を完了できます。  
  
## <a name="next-steps"></a>次のステップ
 
* [Azure Import/Export ツールの設定](storage-import-export-tool-setup-v1.md)   
* [インポート ジョブ用のハード ドライブを準備する](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
* [コピー ログ ファイルによるジョブの状態の確認](storage-import-export-tool-reviewing-job-status-v1.md)   
* [エクスポート ジョブの修復](./storage-import-export-tool-repairing-an-export-job-v1.md)