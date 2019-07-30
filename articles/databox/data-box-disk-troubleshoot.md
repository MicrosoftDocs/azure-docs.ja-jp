---
title: Azure Data Box Disk のトラブルシューティング | Microsoft Docs
description: Azure Data Box Disk で見られる問題のトラブルシューティングを行う方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f725f38a335972ae8e0a8b8402a99202caa54a70
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147083"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>ログを使用して Azure Data Box Disk の検証の問題をトラブルシューティングする

この記事は、Microsoft Azure Data Box Disk に適用されます。 この記事では、このソリューションのデプロイ時に発生する可能性がある検証の問題を、ログを使用してトラブルシューティングする方法について説明します。

## <a name="validation-tool-log-files"></a>検証ツールのログ ファイル

[検証ツール](data-box-disk-deploy-copy-data.md#validate-data)を使用してディスク上のデータを検証すると、エラーのログを記録するために *error.xml* が生成されます。 ログ ファイルの場所はドライブの `Drive:\DataBoxDiskImport\logs` フォルダーです。 検証を実行すると、エラー ログへのリンクが提供されます。

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

検証のために複数のセッションを実行する場合、セッションごとに 1 つのエラー ログが生成されます。

- 次に示すのは、`PageBlob` フォルダーに読み込まれるデータが 512 バイトでアラインメントされていない場合のエラー ログの例です。 VHD や VHDX など、PageBlob にアップロードされるデータはすべて、512 バイトでアラインメントされている必要があります。 このファイル内のエラーは `<Errors>` に、警告は `<Warnings>` にあります。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- 次に示すのは、コンテナー名が無効な場合のエラー ログの例です。 ディスク上の `BlockBlob`、`PageBlob`、または `AzureFile` フォルダーの下に作成したフォルダーは、Azure Storage アカウント内のコンテナーになります。 コンテナーの名前は [Azure の名前付け規則](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)に従う必要があります。

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>検証ツールのエラー

*error.xml* に含まれるエラーと、対応する推奨アクションを次の表にまとめています。

| エラー コード| 説明                       | 推奨アクション               |
|------------|--------------------------|-----------------------------------|
| `None` | データが正常に検証されました。 | 操作は必要ありません。 |
| `InvalidXmlCharsInPath` |ファイル パスに無効な文字が含まれているため、マニフェスト ファイルを作成できませんでした。 | 続行するには、これらの文字を削除します。  |
| `OpenFileForReadFailed`| ファイルを処理できませんでした。 アクセスの問題またはファイル システムの破損が考えられます。|エラーのためのファイルを読み取れませんでした。 エラーの詳細は例外にあります。 |
| `Not512Aligned` | このファイルは PageBlob フォルダーに対して有効な形式ではありません。| `PageBlob` フォルダーには、512 バイトでアラインメントされたデータのみをアップロードしてください。 PageBlob フォルダーからファイルを削除するか、BlockBlob フォルダーに移動します。 検証を再試行します。|
| `InvalidBlobPath` | Azure BLOB の名前付け規則のとおりに、ファイル パスがクラウド内の有効な BLOB パスにマップしません。|Azure の名前付けガイドラインに従ってファイル パスの名前を変更してください。 |
| `EnumerationError` | 検証用のファイルを列挙できませんでした。 |このエラーには複数の理由が考えられます。 最も可能性が高い理由は、ファイルへのアクセスです。 |
| `ShareSizeExceeded` | このファイルが原因で、Azure ファイル共有のサイズが Azure の制限である 5 TB を超えました。|[Azure オブジェクトのサイズ制限](data-box-disk-limits.md#azure-object-size-limits)に準拠するよう、共有内のデータのサイズを減らしてください。 検証を再試行します。 |
| `AzureFileSizeExceeded` | ファイル サイズが Azure ファイルのサイズ制限を超えています。| [Azure オブジェクトのサイズ制限](data-box-disk-limits.md#azure-object-size-limits)に準拠するよう、ファイルまたはデータのサイズを減らしてください。 検証を再試行します。|
| `BlockBlobSizeExceeded` | ファイル サイズが Azure ブロック BLOB のサイズ制限を超えています。 | [Azure オブジェクトのサイズ制限](data-box-disk-limits.md#azure-object-size-limits)に準拠するよう、ファイルまたはデータのサイズを減らしてください。 検証を再試行します。 |
| `ManagedDiskSizeExceeded` | ファイル サイズが Azure マネージド ディスクのサイズ制限を超えています。 | [Azure オブジェクトのサイズ制限](data-box-disk-limits.md#azure-object-size-limits)に準拠するよう、ファイルまたはデータのサイズを減らしてください。 検証を再試行します。 |
| `PageBlobSizeExceeded` | ファイル サイズが Azure マネージド ディスクのサイズ制限を超えています。 | [Azure オブジェクトのサイズ制限](data-box-disk-limits.md#azure-object-size-limits)に準拠するよう、ファイルまたはデータのサイズを減らしてください。 検証を再試行します。 |
| `InvalidShareContainerFormat`  |ディレクトリ名が、コンテナーまたは共有の Azure 名前付け規則に準拠していません。         |ディスク上の既存のフォルダー下に作成された最初のフォルダーが、ストレージ アカウント内のコンテナーになります。 この共有またはコンテナーの名前が Azure の名前付け規則に準拠していません。 [Azure の名前付け規則](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)に準拠するようにファイルの名前を変更してください。 検証を再試行します。   |
| `InvalidBlobNameFormat` | Azure BLOB の名前付け規則のとおりに、ファイル パスがクラウド内の有効な BLOB パスにマップしません。|[Azure の名前付け規則](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)に準拠するようにファイルの名前を変更してください。 検証を再試行します。 |
| `InvalidFileNameFormat` | Azure ファイルの名前付け規則のとおりに、ファイル パスがクラウド内の有効なファイル パスにマップしません。 |[Azure の名前付け規則](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)に準拠するようにファイルの名前を変更してください。 検証を再試行します。 |
| `InvalidDiskNameFormat` | Azure マネージド ディスクの名前付け規則のとおりに、ファイル パスがクラウド内の有効なディスク名にマップしません。 |[Azure の名前付け規則](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)に準拠するようにファイルの名前を変更してください。 検証を再試行します。       |
| `NotPartOfFileShare` | アップロード パスが無効なため、ファイルをアップロードできませんでした。 Azure Files 内のフォルダーにファイルをアップロードしてください。   | エラーのあるファイルを削除し、事前に作成されたフォルダーにそれらのファイルをアップロードしてください。 検証を再試行します。 |
| `NonVhdFileNotSupportedForManagedDisk` | VHD 以外のファイルはマネージド ディスクとしてアップロードできません。 |VHD 以外のファイルはサポートされていませんので、削除してください。 検証を再試行します。 |


## <a name="next-steps"></a>次の手順

- [データ アップロード エラー](data-box-disk-troubleshoot-upload.md)のトラブルシューティングを行います。
