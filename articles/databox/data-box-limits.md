---
title: Azure Data Box の制限 | Microsoft Docs
description: Microsoft Azure Data Box のコンポーネントと接続に関するシステム制限および推奨サイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/22/2019
ms.author: alkohli
ms.openlocfilehash: 147cf61dcd36edc75a936cf9b467fd89c8d8a965
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58082825"
---
# <a name="azure-data-box-limits"></a>Azure Data Box の制限

Microsoft Azure Data Box をデプロイおよび運用する際には、以下の制限事項を考慮してください。 次の表で、Data Box の制限について説明します。


## <a name="data-box-service-limits"></a>Data Box サービスの制限

 - Data Box サービスで複数のストレージ アカウントを使用している場合、すべてのストレージ アカウントが同じ Azure リージョンに属している必要があります。
 - 3 つの以内のストレージ アカウントを使用することをお勧めします。 それ以上のストレージ アカウントを使用するとパフォーマンスに影響する可能性があります。

## <a name="data-box-limits"></a>Data Box の制限

- xv@ Data Box は、最大で 5 億個のファイルを格納できます。

## <a name="azure-storage-limits"></a>Azure Storage の制限

ここでは、Data Box サービスに適用される Azure Storage サービスの制限および Azure Files、Azure ブロック BLOB、Azure ページ BLOB に必要な名前付け規則について説明します。 ストレージの制限を十分に確認し、すべての推奨事項に従ってください。

Azure Storage サービスの制限と共有、コンテナー、およびファイルの名前付けのベスト プラクティスに関する最新情報については、以下のページをご覧ください。

- [Naming and referencing containers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (コンテナーの名前付けと参照)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (共有の名前付けと参照)
- [Block blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (ブロック BLOB とページ BLOB に関する規則)

> [!IMPORTANT]
> Azure Storage サービスの制限を超えているファイル/ディレクトリ、または Azure Files/BLOB の名前付け規則に準拠していないファイル/ディレクトリは、Data Box サービスを通じて Azure Storage に取り込まれません。

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

- 事前に作成された共有にファイルを直接コピーしないでください。 共有の下にフォルダーを作成し、そのフォルダーにファイルをコピーする必要があります。
- *StorageAccount_BlockBlob*  と *StorageAccount_PageBlob* の下のフォルダーはコンテナーです。 コンテナーは、たとえば、*BlockBlob/container* や *PageBlob/container* として作成されます。
- *StorageAccount_AzureFiles* の直下に作成された各フォルダーは、Azure ファイル共有に変換されます。
- コピー対象のオブジェクトと同じ名前の既存の Azure オブジェクト (BLOB やファイルなど) がクラウド内にある場合、Data Box はクラウド内のファイルを上書きします。
- *StorageAccount_BlockBlob* 共有と *StorageAccount_PageBlob* 共有に書き込まれる各ファイルは、それぞれブロック BLOB およびページ BLOB としてアップロードされます。
- Azure blob ストレージは、ディレクトリをサポートしていません。 *StorageAccount_BlockBlob* フォルダーの下にフォルダーを作成する場合、 blob 名に仮想フォルダーが作成されます。 Azure Files では、実際のディレクトリ構造が維持されます。
- *StorageAccount_BlockBlob* フォルダーと *StorageAccount_PageBlob* フォルダーの下に作成される空のディレクトリ階層 (ファイルを含まない) はアップロードされません。
- Azure へのデータのアップロード時にエラーが発生した場合は、ターゲット ストレージ アカウントにエラー ログが作成されます。 このエラー ログへのパスは、アップロードが完了した時に使用可能になります。ログを確認して、是正措置を行うことができます。 アップロードしたデータを確認せずにソースからデータを削除しないでください。

## <a name="azure-storage-account-size-limits"></a>Azure ストレージ アカウントのサイズ制限

ここでは、ストレージ アカウントにコピーするデータのサイズ制限を示します。 アップロードするデータがこれらの制限に準拠していることを確認してください。 これらの制限の最新情報については、「[Azure Blob Storage のスケール ターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets)」および「[Azure Files のスケール ターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)」をご覧ください。

| Azure ストレージ アカウントにコピーするデータのサイズ                      | 既定の制限          |
|---------------------------------------------------------------------|------------------------|
| ブロック BLOB とページ BLOB                                            | ストレージ アカウントあたり 500 TiB。 <br> これには、Data Box を含むすべてのソースのデータが含まれます。|
| Azure ファイル                                                          | 共有あたり 5 TiB です。<br> *StorageAccount_AzureFiles* の下のすべてのフォルダーはこの制限に従う必要があります。       |

## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

ここでは、書き込むことのできる Azure オブジェクトのサイズを示します。 アップロードするすべてのファイルがこれらの制限に準拠していることを確認してください。

| Azure オブジェクトの種類 | 既定の制限                                             |
|-------------------|-----------------------------------------------------------|
| ブロック BLOB        | ~ 4.75 TiB                                                 |
| ページ BLOB         | 8 TiB <br> ページ BLOB 形式でアップロードするファイルには 512 バイトをアライン (整数倍) する必要があります。そうでないと、アップロードが失敗します。 <br> VHD と VHDX には 512 バイトでアラインされます。 |
| Azure Files        | 1 TiB                                                      |
| マネージド ディスク     | 4 TiB <br> サイズと制限の詳細については、以下をご覧ください。 <li>[Standard SSD のスケーラビリティの目標](../virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Premium SSD のスケーラビリティの目標](../virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Standard HDD のスケーラビリティの目標](../virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[マネージド ディスクの価格と課金](../virtual-machines/windows/disks-types.md#billing)</li>                                                     |

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure ブロック BLOB、ページ BLOB およびファイルの名前付け規則

| エンティティ                                       | 規則                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ブロック BLOB とページ BLOB のコンテナー名 | 3 ～ 63 文字の有効な DNS 名にする必要があります。 <br>  先頭は文字か数字にします。 <br> 小文字、数字、ハイフン (-) のみを含めることができます。 <br> すべてのハイフン (-) は、その直前または直後に文字または数字が使用されている必要があります。 <br> 連続するハイフンを名前に使用することはできません。 |
| Azure ファイルの共有名                  | 同上                                                                                                                                                                                                                                                                                                             |
| Azure ファイルのディレクトリ名とファイル名     |<li> 小文字、大文字を区別し、長さが 255 文字以内である必要があります。 </li><li> 末尾にフォワード スラッシュ (/) を使用することはできません。 </li><li>使用した場合、自動的に削除されます。 </li><li> 次の文字は使用できません: <code>" \\ / : \| < > * ?</code></li><li> URL の予約文字は適切にエスケープしてください。 </li><li> 無効な URL パス文字は使用できません。 \\uE000 のようなコード ポイントは、有効な Unicode 文字ではありません。 制御文字 (0x00 to 0x1F, \\u0081 など) など一部の ASCII または Unicode 文字も使用できません。 Http/1.1 での Unicode 文字列に適用される規則については、RFC 2616 の Section 2.2:「Basic Rules」および RFC 3987 を参照してください。 </li><li> 以下のファイル名は使用できません。LPT1、LPT2、LPT3、LPT4、LPT5、LPT6、LPT7、LPT8、LPT9、COM1、COM2、COM3、COM4、COM5、COM6、COM7、COM8、COM9、PRN、AUX、NUL、CON、CLOCK$、ドット (.)、2 ドット(..)</li>|
| ブロック BLOB とページ BLOB の BLOB 名      | </li><li>BLOB 名は、大文字と小文字が区別されます。また、名前には任意の組み合わせの文字を使用できます。 </li><li>BLOB 名は 1 文字から 1,024 文字にする必要があります。 </li><li>URL の予約文字は適切にエスケープしてください。 </li><li>BLOB 名を構成するパスのセグメントの数が 254 個を超えないようにしてください。 パスのセグメントは、仮想ディレクトリの名前に対応した連続する区切り記号文字 (スラッシュ "/" など) の間の文字列です。</li> |
