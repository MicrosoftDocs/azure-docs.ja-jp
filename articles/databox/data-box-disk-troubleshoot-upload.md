---
title: ログを使用したデータのアップロードに関するトラブルシューティング
titleSuffix: Azure Data Box Disk
description: ログの使用方法、および Azure Data Box Disk にデータをアップロードするときに見られる問題のトラブルシューティングを行う方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 4b53cf607bdf60c785c7324d9ede526a0983b7e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97605277"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Azure Data Box Disk でデータのアップロードに関する問題をトラブルシューティングするためのログを理解する

この記事は Microsoft Azure Data Box Disk を対象にしており、Azure にデータをアップロードするときに見られる問題について説明しています。

## <a name="about-upload-logs"></a>アップロード ログについて

データセンターでデータが Azure にアップロードされると、ストレージ アカウントごとに `_error.xml` ファイルと `_verbose.xml` ファイルが生成されます。 これらのログは、データのアップロードに使用されたものと同じストレージ アカウントにアップロードされます。 

これらのログは両方とも同じ形式です。そして、これらにはディスクから Azure Storage アカウントへのデータのコピー中に発生したイベントの XML 記述が含まれています。

詳細ログには、すべての BLOB またはファイルのコピー操作の状態に関する完全な情報が含まれています。一方、エラー ログには、アップロード中にエラーが発生した BLOB またはファイルに関する情報のみが含まれます。

エラー ログの構造は詳細ログと同じですが、エラー ログでは成功した操作が除外されます。

## <a name="download-logs"></a>ログをダウンロードする

アップロード ログを検索するには、次の手順を行います。

1. Azure へのデータのアップロード時にエラーが発生した場合、診断ログがあるフォルダーへのパスがポータルに表示されます。

    ![ポータル内のログへのリンク](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. **waies** に移動します。

    ![エラー ログと詳細ログ](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

各ケースで、エラー ログと詳細ログを参照してください。 各ログを選択し、ローカル コピーをダウンロードします。

## <a name="sample-upload-logs"></a>アップロード ログのサンプル

`_verbose.xml` のサンプルを次に示します。 この場合、注文は正常に完了し、エラーは発生していません。

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

同じ注文について、`_error.xml` のサンプルを次に示します。

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

`_error.xml` のサンプルを次に示します。ここで、注文はエラーで完了しています。 

この場合のエラー ファイルには、`Summary` セクションと、ファイル レベルのエラーをすべて含む別のセクションがあります。 

`Summary` には、`ValidationErrors` と `CopyErrors` が含まれています。 この場合、8 個のファイルまたはフォルダーが Azure にアップロードされ、検証エラーは発生していません。 Azure Storage アカウントにデータがコピーされたときには、5 個のファイルまたはフォルダーが正常にアップロードされています。 残りの 3 個のファイルまたはフォルダーは、Azure コンテナーの名前付け規則に従って名前が変更されてから、Azure に正常にアップロードされました。

ファイル レベルの状態は `BlobStatus` にあり、BLOB をアップロードするために実行されたアクションが説明されます。 この場合、データのコピー先のフォルダーがコンテナーの Azure 命名規則に準拠していないため、3 つのコンテナーの名前が変更されます。 それらのコンテナーにアップロードされた BLOB については、新しいコンテナー名、Azure 内の BLOB のパス、元の無効なファイル パス、および BLOB サイズが含まれます。
  
```xml
 <?xml version="1.0" encoding="utf-8"?>
  <DriveLog Version="2018-10-01">
    <DriveId>18041C582D7E</DriveId>
    <Summary>
     <!--Summary for validation and data copy to Azure -->
      <ValidationErrors>
        <None Count="8" />
      </ValidationErrors>
      <CopyErrors>
        <Completed Count="5" Description="No errors encountered" />
        <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
      </CopyErrors>
    </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
      <SizeInBytes>71528448</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Status>CompletedWithErrors</Status>
  </DriveLog>
```

## <a name="data-upload-errors"></a>データのアップロード エラー

次の表に、データを Azure にアップロードするときに生成されるエラーをまとめます。

| エラー コード | 説明                   |
|-------------|------------------------------|
|`None` |  正常に完了しました。           |
|`Renamed` | Blob の名前が正常に変更されました。   |
|`CompletedWithErrors` | アップロードは完了しましたが、エラーが発生しました。 エラーが発生したファイルの詳細がログ ファイルに記載されています。  |
|`Corrupted`|データ インジェスト中に計算された CRC が、アップロード中に計算された CRC と一致しません。  |  
|`StorageRequestFailed` | Azure ストレージ要求が失敗しました。   |     
|`LeasePresent` | この項目はリースされており、別のユーザーによって使用されています。 |
|`StorageRequestForbidden` |認証の問題のため、アップロードできませんでした。 |
|`ManagedDiskCreationTerminalFailure` | マネージド ディスクとしてアップロードできませんでした。 ファイルは、ステージング ストレージ アカウントでページ BLOB として使用できます。 ページ BLOB はマネージド ディスクに手動で変換できます。  |
|`DiskConversionNotStartedTierInfoMissing` | 事前作成された階層フォルダーの外部で VHD ファイルがコピーされたため、マネージド ディスクは作成されませんでした。 ファイルは、注文作成時に指定されたとおりに、ページ BLOB としてステージング ストレージ アカウントにアップロードされます。 これをマネージド ディスクに手動で変換できます。|
|`InvalidWorkitem` | Azure の名前付け規則と制限規則に準拠していないため、データをアップロードできませんでした。|
|`InvalidPageBlobUploadAsBlockBlob` | プレフィックス `databoxdisk-invalid-pb-` が付いたコンテナー内にブロック BLOB としてアップロードされました。|
|`InvalidAzureFileUploadAsBlockBlob` | プレフィックス `databoxdisk-invalid-af`- が付いたコンテナー内にブロック BLOB としてアップロードされました。|
|`InvalidManagedDiskUploadAsBlockBlob` | プレフィックス `databoxdisk-invalid-md`- が付いたコンテナー内にブロック BLOB としてアップロードされました。|
|`InvalidManagedDiskUploadAsPageBlob` |プレフィックス `databoxdisk-invalid-md-` が付いたコンテナー内にページ BLOB としてアップロードされました。 |
|`MovedToOverflowShare` |元の共有サイズが Azure の最大サイズ制限を超えたため、ファイルを新しい共有にアップロードしました。 新しいファイル共有名には、元の名前にサフィックス `-2` が付いています。   |
|`MovedToDefaultAzureShare` |いずれのフォルダーにも含まれていないファイルを既定の共有にアップロードしました。 共有名は `databox-` で始まります。 |
|`ContainerRenamed` |これらのファイルのコンテナーは Azure の名前付け規則に準拠していなかったため、名前が変更されています。 新しい名前は `databox-` で始まり、元の名前の SHA1 ハッシュが末尾に付加されています。 |
|`ShareRenamed` |これらのファイルの共有は Azure の名前付け規則に準拠していなかったため、名前が変更されています。 新しい名前は `databox-` で始まり、元の名前の SHA1 ハッシュが末尾に付加されています。 |
|`BlobRenamed` |これらのファイルは Azure の名前付け規則に準拠していなかったため、名前が変更されました。 `BlobPath` フィールドで新しい名前を確認してください。 |
|`FileRenamed` |これらのファイルは Azure の名前付け規則に準拠していなかったため、名前が変更されました。 `FileStoragePath` フィールドで新しい名前を確認してください。 |
|`DiskRenamed` |これらのファイルは Azure の名前付け規則に準拠していなかったため、名前が変更されました。 `BlobPath` フィールドで新しい名前を確認してください。 |


## <a name="next-steps"></a>次の手順

- [Data Box Disk の問題に関するサポート チケットを開きます](data-box-disk-contact-microsoft-support.md)。
