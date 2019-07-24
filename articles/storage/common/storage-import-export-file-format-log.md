---
title: Azure Import/Export のログ ファイルの形式 | Microsoft Docs
description: インポート/エクスポート サービス ジョブの手順を実行したときに作成されるログ ファイルの形式について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 00e226134039d29efd744290c4bc63abd50adc89
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697834"
---
# <a name="azure-importexport-service-log-file-format"></a>Azure Import/Export サービスのログ ファイルの形式
Microsoft Azure Import/Export サービスが、インポート ジョブまたはエクスポート ジョブの一部としてドライブでアクションを実行する場合、そのジョブに関連付けられているストレージ アカウントのブロック BLOB にログが書き込まれます。  
  
Import/Export サービスによって書き込まれるログは 2 種類あります。  
  
-   エラー ログは、エラーが発生したときに常に生成されます。  
  
-   詳細ログは既定では無効になっていますが、[Put Job](/rest/api/storageimportexport/jobs) または [Update Job Properties](/rest/api/storageimportexport/jobs) 操作で `EnableVerboseLog` プロパティを設定すると有効にできます。  
  
## <a name="log-file-location"></a>ログ ファイルの保存先  
ログは、`Put Job` 操作で設定できる `ImportExportStatesPath` 設定で指定されるコンテナーまたは仮想ディレクトリでブロック BLOB に書き込まれます。 ログの書き込み先となる場所は、ジョブに認証を指定する方法と `ImportExportStatesPath` で指定される値と共によって異なります。 ジョブの認証はストレージ アカウント キーまたはコンテナーの SAS (共有アクセス署名) を通じて指定できます。  
  
コンテナーまたは仮想ディレクトリの名前は `waimportexport` の既定の名前か、指定する別のコンテナー名または仮想ディレクトリ名のいずれかにできます。  
  
次の表は、利用可能なオプションを示しています。  
  
|認証方法|`ImportExportStatesPath` 要素の値|ログ BLOB の場所|  
|---------------------------|----------------------------------------------|---------------------------|  
|ストレージ アカウント キー|既定値|`waimportexport` という名前のコンテナー。規定のコンテナーです。 例: <br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|ストレージ アカウント キー|ユーザーが指定した値|ユーザーが指定したコンテナー。 例: <br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|コンテナー SAS|既定値|`waimportexport` という名前の仮想ディレクトリ。これは SAS で指定されたコンテナーの下にある既定の名前です。<br /><br /> たとえば、ジョブに指定された SAS が `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue` の場合、ログの場所は `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport` になります。|  
|コンテナー SAS|ユーザーが指定した値|SAS で指定されたコンテナーの下にあるユーザーが指定した仮想ディレクトリ。<br /><br /> たとえば、ジョブに指定された SAS が `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue` で、指定された仮想ディレクトリの名前が `mylogblobs` の場合、ログの場所は `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs` になります。|  
  
[Get Job](/rest/api/storageimportexport/jobs) 操作を呼び出すことで、エラー ログと詳細ログの URL が取得できます。 ログは、ドライブの処理が完了すると利用できます。  
  
## <a name="log-file-format"></a>ログ ファイルの形式  
両方のログの形式は同じで、ハード ドライブとお客様のアカウント間で BLOB をコピーする間に発生したイベントの XML の説明が格納される BLOB です。  
  
詳細ログには、すべての BLOB (インポート ジョブの場合) またはファイル (エクスポート ジョブの場合) のコピー操作のステータスに関する完全な情報が含まれており、エラー ログには、インポートまたはエクスポート ジョブ中にエラーが発生した BLOB やファイルの情報のみが含まれます。  
  
詳細ログの形式を次に示します。 エラー ログは同じ構造になっていますが、成功した操作を除外します。  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

ログ ファイルの要素を次の表に示します。  
  
|XML 要素|type|説明|  
|-----------------|----------|-----------------|  
|`DriveLog`|XML 要素|ドライブ ログを表します。|  
|`Version`|属性、String|ログのフォーマットのバージョン。|  
|`DriveId`|String|ドライブのハードウェア シリアル番号。|  
|`Status`|String|ドライブの処理状態です。 詳細については、下の `Drive Status Codes` 表をご覧ください。|  
|`Blob`|入れ子になった XML 要素|BLOB を表します。|  
|`Blob/BlobPath`|String|BLOB の URI。|  
|`Blob/FilePath`|String|ドライブ上のファイルの相対パス。|  
|`Blob/Snapshot`|DateTime|BLOB のスナップショット バージョン (エクスポート ジョブの場合のみ)。|  
|`Blob/Length`|整数|BLOB の長さの合計 (単位: バイト)。|  
|`Blob/LastModified`|DateTime|BLOB の最終変更日時 (エクスポート ジョブの場合のみ)。|  
|`Blob/ImportDisposition`|String|BLOB のインポート処理 (インポート ジョブの場合のみ)。|  
|`Blob/ImportDisposition/@Status`|属性、String|インポート処理の状態です。|  
|`PageRangeList`|入れ子になった XML 要素|ページ BLOB のページ範囲の一覧を表します。|  
|`PageRange`|XML 要素|ページ範囲を表します。|  
|`PageRange/@Offset`|属性、整数|BLOB のページ範囲の開始オフセット。|  
|`PageRange/@Length`|属性、整数|ページ範囲のバイト長。|  
|`PageRange/@Hash`|属性、String|ページ範囲の Base16 エンコード MD5 ハッシュ。|  
|`PageRange/@Status`|属性、String|ページ範囲の処理状態です。|  
|`BlockList`|入れ子になった XML 要素|ブロック BLOB のブロック一覧を表します。|  
|`Block`|XML 要素|ブロックを表します。|  
|`Block/@Offset`|属性、整数|BLOB のブロックの開始オフセット。|  
|`Block/@Length`|属性、整数|ブロックのバイト長。|  
|`Block/@Id`|属性、String|ブロック ID。|  
|`Block/@Hash`|属性、String|ブロックの Base16 エンコード MD5 ハッシュ。|  
|`Block/@Status`|属性、String|ブロックの処理状態です。|  
|`Metadata`|入れ子になった XML 要素|BLOB メタデータを表します。|  
|`Metadata/@Status`|属性、String|BLOB メタデータの処理の状態です。|  
|`Metadata/GlobalPath`|String|グローバル メタデータ ファイルへの相対パス。|  
|`Metadata/GlobalPath/@Hash`|属性、String|グローバル メタデータ ファイルの Base16 エンコード MD5 ハッシュ。|  
|`Metadata/Path`|String|メタデータ ファイルへの相対パス。|  
|`Metadata/Path/@Hash`|属性、String|メタデータ ファイルの Base16 エンコード MD5 ハッシュ。|  
|`Properties`|入れ子になった XML 要素|BLOB のプロパティを表します。|  
|`Properties/@Status`|属性、String|ファイルが見つからない、完了などの BLOB のプロパティの処理状態です。|  
|`Properties/GlobalPath`|String|グローバル プロパティ ファイルへの相対パス。|  
|`Properties/GlobalPath/@Hash`|属性、String|グローバル プロパティ ファイルの Base16 エンコード MD5 ハッシュ。|  
|`Properties/Path`|String|プロパティ ファイルへの相対パス。|  
|`Properties/Path/@Hash`|属性、String|プロパティ ファイルの Base16 エンコード MD5 ハッシュ。|  
|`Blob/Status`|String|BLOB の処理状態です。|  
  
## <a name="drive-status-codes"></a>ドライブの状態コード  
次の表は、ドライブの処理に関する状態コードを示します。  
  
|状態コード|説明|  
|-----------------|-----------------|  
|`Completed`|ドライブは、処理が正常に完了しました。|  
|`CompletedWithWarnings`|ドライブは、BLOB で指定されたインポート処理あたり 1 つ以上の BLOB で警告付きで処理が完了しました。|  
|`CompletedWithErrors`|ドライブは、1 つ以上の BLOB またはチャンクのエラーを完了しました。|  
|`DiskNotFound`|ドライブでディスクが見つかりませんでした。|  
|`VolumeNotNtfs`|ディスク上の最初のデータのボリュームは、NTFS 形式ではありません。|  
|`DiskOperationFailed`|ドライブで操作を実行する際に、不明なエラーが発生しました。|  
|`BitLockerVolumeNotFound`|BitLocker の暗号化可能なボリュームは見つかりませんでした。|  
|`BitLockerNotActivated`|ボリュームで BitLocker が有効になっていません。|  
|`BitLockerProtectorNotFound`|数字のパスワード キー保護機能は、ボリューム上に存在しません。|  
|`BitLockerKeyInvalid`|提供された数値パスワードでは、ボリュームはロック解除できません。|  
|`BitLockerUnlockVolumeFailed`|ボリュームのロックを解除する際に、不明なエラーが発生しました。|  
|`BitLockerFailed`|BitLocker 操作を実行する際に、不明なエラーが発生しました。|  
|`ManifestNameInvalid`|マニフェスト ファイル名が無効です。|  
|`ManifestNameTooLong`|マニフェスト ファイル名が長すぎます。|  
|`ManifestNotFound`|マニフェスト ファイル名は見つかりませんでした。|  
|`ManifestAccessDenied`|マニフェスト ファイルへのアクセスが拒否されました。|  
|`ManifestCorrupted`|マニフェスト ファイルは破損しています (コンテンツがハッシュと一致していません)。|  
|`ManifestFormatInvalid`|マニフェストのコンテンツは、必要な形式に準拠していません。|  
|`ManifestDriveIdMismatch`|マニフェスト ファイルのドライブ ID はドライブからの 1 回の読み取りと一致しません。|  
|`ReadManifestFailed`|マニフェストからの読み取り中にディスク I/O エラーが発生しました。|  
|`BlobListFormatInvalid`|エクスポート BLOB では、必須の形式に準拠していない BLOB が一覧表示されます。|  
|`BlobRequestForbidden`|ストレージ アカウントでの BLOB へのアクセスが許可されていません。 無効なストレージ アカウント キーまたはコンテナー SAS が原因の可能性があります。|  
|`InternalError`|ドライブを処理中に内部エラーが発生しました。|  
  
## <a name="blob-status-codes"></a>BLOB の状態コード  
次の表は、BLOB の処理に関する状態コードを示します。  
  
|状態コード|説明|  
|-----------------|-----------------|  
|`Completed`|BLOB は、処理が正常に完了しました。|  
|`CompletedWithErrors`|BLOB の処理が完了しましたが、1 つ以上のページ範囲やブロック、メタデータ、プロパティのエラーが発生しました。|  
|`FileNameInvalid`|ファイル名が無効です。|  
|`FileNameTooLong`|ファイル名が長すぎます。|  
|`FileNotFound`|ファイルが見つかりませんでした。|  
|`FileAccessDenied`|ファイルへのアクセスが拒否されました。|  
|`BlobRequestFailed`|BLOB にアクセスする Blob service の要求に失敗しました。|  
|`BlobRequestForbidden`|BLOB にアクセスする Blob service の要求は許可されていません。 無効なストレージ アカウント キーまたはコンテナー SAS が原因の可能性があります。|  
|`RenameFailed`|BLOB (インポート ジョブの場合) またはファイル (エクスポート ジョブの場合) の名前を変更できませんでした。|  
|`BlobUnexpectedChange`|BLOB (エクスポート ジョブの場合) で予期しない変更が発生しました。|  
|`LeasePresent`|BLOB にはリースがあります。|  
|`IOFailed`|BLOB の処理中に、ディスクまたはネットワークの I/O エラーが発生しました。|  
|`Failed`|BLOB の処理中に、不明なエラーが発生しました。|  
  
## <a name="import-disposition-status-codes"></a>インポート処理の状態コード  
次の表は、インポート処理を解決するための状態コードを示します。  
  
|状態コード|説明|  
|-----------------|-----------------|  
|`Created`|BLOB が作成されました。|  
|`Renamed`|BLOB の名前が、名前の変更のインポート処理によって名前が変更されました。 `Blob/BlobPath` 要素には、名前が変更された BLOB の URI が含まれています。|  
|`Skipped`|BLOB は、`no-overwrite` インポート処理によってスキップされました。|  
|`Overwritten`|BLOB は、`overwrite` インポート処理によって既存の BLOB を上書きしました。|  
|`Cancelled`|以前のエラーにより、インポート処理によりさらなる処理が停止しました。|  
  
## <a name="page-rangeblock-status-codes"></a>ページ範囲またはブロックに関する状態コード  
次の表は、ページ範囲またはブロックの処理に関する状態コードを示します。  
  
|状態コード|説明|  
|-----------------|-----------------|  
|`Completed`|ページ範囲またはブロックは、処理が正常に完了しました。|  
|`Committed`|ブロックはコミットされましたが、他のブロックが失敗したか、put full block list 自体が失敗したため、full block list ではコミットされていません。|  
|`Uncommitted`|ブロックはアップロードされましたが、コミットされていません。|  
|`Corrupted`|ページ範囲またはブロックは破損しています (コンテンツがハッシュと一致していません)。|  
|`FileUnexpectedEnd`|予期しないファイルの終わりが発生しました。|  
|`BlobUnexpectedEnd`|予期しない BLOB の終わりが発生しました。|  
|`BlobRequestFailed`|ページ範囲またはブロックにアクセスする Blob service の要求に失敗しました。|  
|`IOFailed`|ページ範囲またはブロックの処理中に、ディスクまたはネットワークの I/O エラーが発生しました。|  
|`Failed`|ページ範囲またはブロックの処理中に、不明なエラーが発生しました。|  
|`Cancelled`|以前のエラーにより、ページ範囲またはブロックのさらなる処理が停止しました。|  
  
## <a name="metadata-status-codes"></a>メタデータの状態コード  
次の表は、BLOB メタデータの処理に関する状態コードを示します。  
  
|状態コード|説明|  
|-----------------|-----------------|  
|`Completed`|メタデータは、処理が正常に完了しました。|  
|`FileNameInvalid`|メタデータ ファイル名が無効です。|  
|`FileNameTooLong`|メタデータ ファイル名が長すぎます。|  
|`FileNotFound`|メタデータ ファイル名が見つかりませんでした。|  
|`FileAccessDenied`|メタデータ ファイルへのアクセスが拒否されました。|  
|`Corrupted`|メタデータ ファイルは破損しています (コンテンツがハッシュと一致していません)。|  
|`XmlReadFailed`|メタデータのコンテンツは、必要な形式に準拠していません。|  
|`XmlWriteFailed`|メタデータ XML の書き込みに失敗しました。|  
|`BlobRequestFailed`|メタデータにアクセスする Blob service の要求に失敗しました。|  
|`IOFailed`|メタデータの処理中に、ディスクまたはネットワークの I/O エラーが発生しました。|  
|`Failed`|メタデータの処理中に、不明なエラーが発生しました。|  
|`Cancelled`|以前のエラーにより、メタデータのさらなる処理が停止しました。|  
  
## <a name="properties-status-codes"></a>プロパティの状態コード  
次の表は、BLOB プロパティの処理に関する状態コードを示します。  
  
|状態コード|説明|  
|-----------------|-----------------|  
|`Completed`|プロパティは、処理が正常に完了しました。|  
|`FileNameInvalid`|プロパティ ファイル名が無効です。|  
|`FileNameTooLong`|プロパティ ファイル名が長すぎます。|  
|`FileNotFound`|プロパティ ファイル名が見つかりませんでした。|  
|`FileAccessDenied`|プロパティ ファイルへのアクセスが拒否されました。|  
|`Corrupted`|プロパティ ファイルは破損しています (コンテンツがハッシュと一致していません)。|  
|`XmlReadFailed`|プロパティのコンテンツは、必要な形式に準拠していません。|  
|`XmlWriteFailed`|プロパティ XML の書き込みに失敗しました。|  
|`BlobRequestFailed`|プロパティにアクセスする Blob service の要求に失敗しました。|  
|`IOFailed`|プロパティの処理中に、ディスクまたはネットワークの I/O エラーが発生しました。|  
|`Failed`|プロパティの処理中に、不明なエラーが発生しました。|  
|`Cancelled`|以前のエラーにより、プロパティのさらなる処理が停止しました。|  
  
## <a name="sample-logs"></a>サンプル ログ  
詳細ログの例を次に示します。  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
対応するエラー ログを次に示します。  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 次のインポート ジョブのエラー ログには、インポート ドライブ上で見つからなかったファイルに関するエラーが含まれています。 後続のコンポーネントのステータスは `Cancelled` になります。  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

次のエクスポート ジョブのエラー ログでは、ドライブに BLOB コンテンツが正常に書き込まれましたが、BLOB のプロパティをエクスポートする際にエラーが発生したことを示します。  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="next-steps"></a>次の手順
 
* [ストレージの Import/Export REST API](/rest/api/storageimportexport/)
