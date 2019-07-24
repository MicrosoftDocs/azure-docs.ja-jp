---
title: Azure Data Box Disk の制限 | Microsoft Docs
description: Microsoft Azure Data Box Disk のシステム制限と推奨サイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 32445e3f6859a6161eb2fae20233c598234f18a0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58791645"
---
# <a name="azure-data-box-disk-limits"></a>Azure Data Box Disk の制限


Microsoft Azure Data Box Disk ソリューションをデプロイおよび運用する際には、以下の制限事項を考慮してください。

## <a name="data-box-service-limits"></a>Data Box サービスの制限

 - Data Box サービスは、[利用可能なリージョン](data-box-disk-overview.md#region-availability) に記載されている Azure リージョンで利用可能です。
 - Data Box Disk では 1 つのストレージ アカウントがサポートされます。

## <a name="data-box-disk-performance"></a>Data Box Disk のパフォーマンス

USB 3.0 で接続したディスクでテストしたところ、ディスクのパフォーマンスは最大 430 MB/s でした。 実際の数値は、使用するファイルのサイズによって異なります。 ファイルのサイズが小さい場合は、パフォーマンスが低下する可能性があります。

## <a name="azure-storage-limits"></a>Azure Storage の制限

ここでは、Data Box サービスに適用される Azure Storage サービスの制限および Azure Files、Azure ブロック BLOB、Azure ページ BLOB に必要な名前付け規則について説明します。 ストレージの制限を十分に確認し、すべての推奨事項に従ってください。

Azure Storage サービスの制限と共有、コンテナー、およびファイルの名前付けのベスト プラクティスに関する最新情報については、以下のページをご覧ください。

- [Naming and referencing containers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (コンテナーの名前付けと参照)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (共有の名前付けと参照)
- [Block blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (ブロック BLOB とページ BLOB に関する規則)

> [!IMPORTANT]
> Azure Storage サービスの制限を超えているファイル/ディレクトリ、または Azure Files/BLOB の名前付け規則に準拠していないファイル/ディレクトリは、Data Box サービスを通じて Azure Storage に取り込まれません。

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

- ディスクに直接データをコピーしないでください。 事前に作成した *BlockBlob* フォルダー、*PageBlob* フォルダー、および *AzureFile* フォルダーにデータをコピーします。
- *BlockBlob* および *PageBlob* の下位のフォルダーはコンテナーです。 たとえば、*BlockBlob/container* および *PageBlob/container* としてコンテナーが作成されます。
- コピー対象のオブジェクトと同じ名前の既存の Azure オブジェクト (BLOB など) がクラウド内にある場合、Data Box Disk はクラウド内で file を file(1) に名前変更します。
- *BlockBlob* 共有と *PageBlob* 共有に書き込まれる各ファイルは、それぞれブロック BLOB およびページ BLOB としてアップロードされます。
- *BlockBlob* フォルダーと *PageBlob* フォルダーの下に作成される空のディレクトリ階層 (ファイルを含まない) はアップロードされません。
- Azure へのデータのアップロード時にエラーが発生した場合は、ターゲット ストレージ アカウントにエラー ログが作成されます。 このエラー ログへのパスは、アップロードが完了するとポータルに表示されます。ログを確認して、是正措置を行うことができます。 アップロードしたデータを確認せずにソースからデータを削除しないでください。
- マネージド ディスクを順に指定した場合は、以下の追加の考慮事項を確認してください。

    - 事前に作成されたすべてのフォルダーにわたり、また、すべての Data Box Disk にわたって 1 つのリソース グループ内で特定の名前を持つマネージド ディスクを 1 つだけ保持できます。 これは、事前に作成されたフォルダーにアップロードされた VHD は一意の名前でなければならないことを意味します。 指定した名前が、リソース グループ内の既存のマネージド ディスクと一致しないようにしてください。 VHD の名前が同じである場合、1 つの VHD のみがその名前を持つマネージド ディスクに変換されます。 その他の VHD は、ステージング ストレージ アカウントにページ BLOB としてアップロードされます。
    - VHD は必ず、事前に作成されたフォルダーの 1 つにコピーします。 これらのフォルダー以外または自分で作成したフォルダーに VHD をコピーすると、VHD はマネージド ディスクではなく、ページ BLOB として Azure Storage アカウントにアップロードされます。
    - マネージド ディスクを作成するためにアップロードできるのは、容量固定の VHD のみです。 動的 VHD、差分 VHD、VHDX ファイルはサポートされていません。

## <a name="azure-storage-account-size-limits"></a>Azure ストレージ アカウントのサイズ制限

ここでは、ストレージ アカウントにコピーするデータのサイズ制限を示します。 アップロードするデータがこれらの制限に準拠していることを確認してください。 これらの制限の最新情報については、「[Azure Blob Storage のスケール ターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets)」および「[Azure Files のスケール ターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)」をご覧ください。

| Azure ストレージ アカウントにコピーするデータのサイズ                      | 既定の制限          |
|---------------------------------------------------------------------|------------------------|
| ブロック BLOB とページ BLOB                                            | ストレージ アカウントあたり 500 TB。 <br> これには、Data Box Disk を含むすべてのソースのデータが含まれます。|


## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

ここでは、書き込むことのできる Azure オブジェクトのサイズを示します。 アップロードするすべてのファイルがこれらの制限に準拠していることを確認してください。

| Azure オブジェクトの種類 | 既定の制限                                             |
|-------------------|-----------------------------------------------------------|
| ブロック BLOB        | ~ 4.75 TiB                                                 |
| ページ BLOB         | 8 TiB <br> (ページ BLOB 形式でアップロードするファイルには 512 バイトをアラインする必要があります。さもないと、アップロードは失敗します。 <br> VHD と VHDX の両方には 512 バイトがアラインされます。) |
|Azure Files        | 1 TiB <br> 最大 共有のサイズは 5 TiB     |
| マネージド ディスク     |4 TiB <br> サイズと制限の詳細については、以下をご覧ください。 <li>[マネージド ディスクのスケーラビリティ ターゲット](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure ブロック BLOB、ページ BLOB およびファイルの名前付け規則

| エンティティ                                       | 規則                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ブロック BLOB とページ BLOB のコンテナー名 <br> Azure Files の FileShare 名 | 3 ～ 63 文字の有効な DNS 名にする必要があります。 <br>  先頭は文字か数字にします。 <br> 小文字、数字、ハイフン (-) のみを含めることができます。 <br> すべてのハイフン (-) は、その直前または直後に文字または数字が使用されている必要があります。 <br> 連続するハイフンを名前に使用することはできません。 |
| Azure ファイルのディレクトリ名とファイル名     |<li> 小文字、大文字を区別し、長さが 255 文字以内である必要があります。 </li><li> 末尾にフォワード スラッシュ (/) を使用することはできません。 </li><li>使用した場合、自動的に削除されます。 </li><li> 次の文字は使用できません: <code>" \\ / : \| < > * ?</code></li><li> URL の予約文字は適切にエスケープしてください。 </li><li> 無効な URL パス文字は使用できません。 \\uE000 のようなコード ポイントは、有効な Unicode 文字ではありません。 制御文字 (0x00 to 0x1F, \\u0081 など) などの一部の ASCII または Unicode 文字も使用できません。 Http/1.1 での Unicode 文字列に適用される規則については、RFC 2616 の Section 2.2:「Basic Rules」および RFC 3987 を参照してください。 </li><li> 以下のファイル名は使用できません。LPT1、LPT2、LPT3、LPT4、LPT5、LPT6、LPT7、LPT8、LPT9、COM1、COM2、COM3、COM4、COM5、COM6、COM7、COM8、COM9、PRN、AUX、NUL、CON、CLOCK$、ドット (.)、2 ドット(..)</li>|
| ブロック BLOB とページ BLOB の BLOB 名      | BLOB 名は、大文字と小文字が区別されます。また、名前には任意の組み合わせの文字を使用できます。 <br> BLOB 名は 1 文字から 1,024 文字にする必要があります。 <br> URL の予約文字は適切にエスケープしてください。 <br>BLOB 名を構成するパスのセグメントの数が 254 個を超えないようにしてください。 パスのセグメントは、仮想ディレクトリの名前に対応した連続する区切り記号文字 (スラッシュ "/" など) の間の文字列です。 |

## <a name="managed-disk-naming-conventions"></a>マネージド ディスクの名前付け規則

| エンティティ | 規則                                             |
|-------------------|-----------------------------------------------------------|
| マネージド ディスク名       | <li> 名前は 1 文字から 80 文字で指定する必要があります。 </li><li> 名前の先頭は文字または数字であり、末尾は文字、数字、またはアンダースコアでなければなりません。 </li><li> 名前に使用できるのは、文字、数字、アンダースコア、ピリオド、およびハイフンのみです。 </li><li>   名前にはスペースまたは `/` は使用できません。                                              |

## <a name="next-steps"></a>次の手順

- [Data Box Disk のシステム要件](data-box-disk-system-requirements.md)を確認する
