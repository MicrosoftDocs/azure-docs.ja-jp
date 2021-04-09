---
title: Azure Import/Export のエクスポート ジョブの修復 - v1 | Microsoft Docs
description: Azure Import/Export サービスを使用して作成し実行したエクスポート ジョブを修復する方法について説明します。
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d84f26b2764a103a9b504c1480e88b58fed3c201
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706063"
---
# <a name="repairing-an-export-job"></a>Export ジョブの修復
エクスポート ジョブが完了した後、オンプレミスで Microsoft Azure Import/Export ツールを実行して、次を行うことができます。  
  
1.  Azure Import/Export サービスでエクスポートできなかったファイルをダウンロードする。  
  
2.  ドライブ上のファイルが正しくエクスポートされたことを検証する。  
  
この機能を使用するには Azure ストレージへの接続が必要になります。  
  
インポート ジョブを修復するためのコマンドは **RepairExport** です。

## <a name="repairexport-parameters"></a>RepairExport パラメーター

次のパラメーターは **RepairExport** とともに指定できます。  
  
|パラメーター|説明|  
|---------------|-----------------|  
|**/r:<RepairFile\>**|必須。 修復の進行状況を追跡し、中断された修復を再開できる修復ファイルへのパスです。 各ドライブには修復ファイルが 1 つのみ必要です。 特定のドライブの修復を開始する場合は、まだ存在していない修復ファイルにパスを渡します。 中断された修復を再開するには、既存の修復ファイルの名前を指定してください。 ターゲット ドライブに対応する修復ファイルを必ず指定してください。|  
|**/logdir:<LogDirectory\>**|省略可能。 ログ ディレクトリ。 詳細ログ ファイルは、このディレクトリに書き込まれます。 ログ ディレクトリが指定されていない場合、現在のディレクトリがログ ディレクトリとして使用されます。|  
|**/d:<TargetDirectory\>**|必須。 検証、修復するディレクトリです。 このディレクトリは、通常エクスポート ドライブのルート ディレクトリになりますが、エクスポートされたファイルのコピーが含まれるネットワーク ファイル共有にもできます。|  
|**/bk:<BitLockerKey\>**|省略可能。 エクスポート ファイルが格納されている暗号のロックを解除する場合は、BitLocker キーを指定します。|  
|**/sn:<StorageAccountName\>**|必須。 エクスポート ジョブのストレージ アカウント名です。|  
|**/sk:<StorageAccountKey\>**|コンテナー SAS が指定されていない場合のみ **必須**。 エクスポート ジョブのストレージ アカウントのアカウント キーです。|  
|**/csas:<ContainerSas\>**|ストレージ アカウント キーが指定されていない場合のみ **必須**。 エクスポート ジョブに関連付けられている BLOB にアクセスするためのコンテナー SAS です。|  
|**/CopyLogFile:<DriveCopyLogFile\>**|必須。 ドライブ コピー ログ ファイルへのパスです。 ファイルは、Windows Azure Import/Export サービスによって生成され、ジョブに関連付けられた BLOB ストレージからダウンロードできます。 コピー ログ ファイルには、障害が発生した BLOB または修復するファイルに関する情報が含まれています。|  
|**/ManifestFile:<DriveManifestFile\>**|省略可能。 エクスポート ドライブのマニフェスト ファイルへのパスです。 このファイルは、Windows Azure Import/Export サービスによって生成され、エクスポート ドライブ上に格納されます。 必要に応じて、ジョブに関連付けられているストレージ アカウントの BLOB に格納されます。<br /><br /> エクスポート ドライブ上のファイルの内容は、このファイルに格納されている MD5 ハッシュに基づいて検証されます。 破損したファイルはすべてダウンロードされ、ターゲット ディレクトリに書き換えられます。|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>RepairExport モードを使用して失敗したエクスポートを修正する  
Azure Import/Export ツールを使用して、エクスポートできなかったファイルをダウンロードできます。 コピー ログ ファイルには、エクスポートできなかったファイルの一覧が格納されます。  
  
エクスポート エラーの原因の可能性には次が含まれます。  
  
-   破損したドライブ  
  
-   転送処理中に変更されたストレージ アカウント キー  
  
WAImportExport ツールを **RepairExport** モードで実行するには、まず、エクスポートしたファイルが保存されているドライブをコンピューターに接続します。 次に、Azure Import/Export ツールを実行して、`/d` パラメーターでそのドライブへのパスを指定します。 また、ダウンロードしたドライブのコピー ログ ファイルへのパスも指定する必要があります。 次のコマンド ラインの例では、エクスポートに失敗したファイルを修復するツールが実行されます。  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
次の例は、BLOB 内のブロックの 1 つがエクスポートに失敗したことを示すコピー ログ ファイルの例です。  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
コピー ログ ファイルでは、Windows Azure Import/Export サービスが BLOB ブロックの 1 つをエクスポート ドライブのファイルにダウンロードしている間にエラーが発生したことを示しています。 ファイルの他のコンポーネントは正常にダウンロードされ、ファイルの長さは正しく設定されていました。 この場合、ツールはドライブ上のファイルを開き、ストレージ アカウントからブロックをダウンロードして、長さ 65536 でオフセット 65536 から始まる範囲のファイルに書き込みます。  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>RepairExport を使用してドライブの内容を検証する  
**RepairExport** オプションを指定して Azure インポート/エクスポート ツールを実行すれば、ドライブ上のデータが正しいかどうかを検証することもできます。 各エクスポート ドライブのマニフェスト ファイルには、ドライブの内容に関する MD5 が含まれています。  
  
また Azure Import/Export サービスは、エクスポート プロセス中にマニフェスト ファイルをストレージ アカウントに保存することもできます。 マニフェスト ファイルの場所は、ジョブが完了した際に [Get Job](/rest/api/storageimportexport/jobs) 操作を通じて利用可能になります。 ドライブ マニフェスト ファイルの形式の詳細については、[Import/Export サービスのマニフェスト ファイルの形式](/previous-versions/azure/storage/common/storage-import-export-file-format-metadata-and-properties)に関する記事を参照してください。  
  
次の例では、**/ManifestFile** パラメーターと **/CopyLogFile** パラメーターを使用する Azure Import/Export ツールを実行する方法を示しています。  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
次の例では、マニフェスト ファイルを示します。  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
修復処理が終わると、ツールではマニフェスト ファイルで参照されている各ファイルを読み取り、MD5 ハッシュに基づいて、ファイルの整合性を検証します。 上記のマニフェストでは、次のコンポーネントを確認します。  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

検証に失敗したコンポーネントはすべて、ツールによってダウンロードされ、ドライブ上の同じファイルに書き換えられます。  
  
## <a name="next-steps"></a>次のステップ
 
* [Azure Import/Export ツールの設定](storage-import-export-tool-setup-v1.md)   
* [インポート ジョブ用のハード ドライブを準備する](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
* [コピー ログ ファイルによるジョブの状態の確認](storage-import-export-tool-reviewing-job-status-v1.md)   
* [インポート ジョブの修復](storage-import-export-tool-repairing-an-import-job-v1.md)