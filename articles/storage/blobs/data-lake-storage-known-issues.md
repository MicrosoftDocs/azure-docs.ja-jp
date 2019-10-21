---
title: Azure Data Lake Storage Gen2 に関する既知の問題 | Microsoft Docs
description: Azure Data Lake Storage Gen2 に関する制限と既知の問題について説明します
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: fb333db693c0f42b66e9fd45f5eb3c879787875d
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959108"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 に関する既知の問題

この記事では、階層型名前空間を持つストレージ アカウント (Azure Data Lake Storage Gen2) でまだサポートされていないか部分的にサポートされている機能とツールについて説明します。

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>BLOB ストレージ API

BLOB Storage API と Azure Data Lake Gen2 API 間の相互運用性がまだ不十分なために発生する可能性のある機能の運用性の問題を防ぐために、BLOB Storage API は無効になっています。

> [!NOTE]
> Data Lake Storage のマルチプロトコル アクセスのパブリック プレビューに登録した場合は、BLOB API と Data Lake Storage Gen2 API で同じデータを操作できます。 詳細については、[Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)に関する記事を参照してください。

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>既存のツール、アプリケーション、サービスがある場合の対処方法

これらのいずれかで BLOB API が使用されているときに、それらを使用してお使いのアカウントにアップロードされるすべてのコンテンツを操作する場合は、2 つのオプションがあります。

* **オプション 1**:BLOB API が Azure Data Lake Gen2 API と相互運用できるようになるまで、お使いの BLOB ストレージ アカウントで階層型名前空間を有効にしません。 階層型名前空間なしでストレージ アカウントを使用すると、ディレクトリやコンテナーのアクセス制御リストなど、Data Lake Storage Gen2 固有の機能にアクセスできなくなります。

* **オプション 2**:[Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)のパブリック プレビューに登録します。 診断ログなどの BLOB ストレージ 機能に加え、BLOB API を呼び出すツールとアプリケーションで、階層型名前空間があるアカウントを操作できます。

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>BLOB API が無効にされる前に BLOB API を使ってデータを読み込んでいた場合の対処方法

これらの API を使用して、データが無効にされる前に、データを読み込んでおり、そのデータにアクセスする運用上の要件がある場合は、次の情報を用意して Microsoft サポートにお問い合わせください。

> [!div class="checklist"]
> * サブスクリプション ID (名前ではなく GUID)。
> * ストレージ アカウント名。
> * 運用に大きな影響があるかどうか。その場合は、どのストレージ アカウントか。
> * 運用に大きな影響がない場合でも、何らかの理由でこのデータを別のストレージ アカウントにコピーする必要があるかどうか、ある場合はその理由をお伝えください。

このような状況では、階層型名前空間機能が有効にされていないストレージ アカウントにこのデータをコピーできるように、一定の期間 BLOB API へのアクセスを復元できます。

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>階層型名前空間があるアカウントでの BLOB API の使用に関する問題と制限事項

Data Lake Storage のマルチプロトコル アクセスのパブリック プレビューに登録した場合は、BLOB API と Data Lake Storage Gen2 API で同じデータを操作できます。

このセクションでは、BLOB API と Data Lake Storage Gen2 API を使用して同じデータを操作する場合の問題と制限事項について説明します。

* BLOB API と Data Lake Storage API の両方を使用して、ファイルの同じインスタンスに書き込むことはできません。

* Data Lake Storage Gen2 API を使用してファイルに書き込むと、そのファイルのブロックは、[Get Block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) BLOB API への呼び出しで認識されなくなります。

* Data Lake Storage Gen2 API または BLOB API のいずれかを使用して、ファイルを上書きできます。 これはファイルのプロパティには影響しません。

* 区切り記号を指定せずに [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 操作を使用した場合、結果にはディレクトリと BLOB の両方が含まれます。

  区切り記号を使用する場合は、スラッシュ (`/`) のみを使用してください。 サポートされている区切り記号はこれだけです。

* [Delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API を使用してディレクトリを削除した場合、そのディレクトリは空の場合にのみ削除されます。

  これは、Blob API を使用してディレクトリを再帰的に削除することはできないことを意味します。

次の BLOB REST API はサポートされていません。

* [Put Blob (Page)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Get Page Ranges](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Incremental Copy Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Put Page from URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put Blob (Append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Append Block from URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

## <a name="issues-with-unmanaged-virtual-machine-vm-disks"></a>アンマネージド仮想マシン (VM) ディスクに関する問題

アンマネージド VM ディスクは、階層型名前空間があるアカウントではサポートされていません。 ストレージ アカウントで階層型名前空間を有効にする場合は、階層型名前空間機能が有効ではないストレージ アカウントにアンマネージド VM ディスクを配置してください。


## <a name="support-for-other-blob-storage-features"></a>その他の BLOB ストレージ機能のサポート

階層型名前空間を持つストレージ アカウント (Azure Data Lake Storage Gen2) でまだサポートされていないか部分的にサポートされているその他すべての機能とツールを次の表に示します。

| 機能 / ツール    | 詳細情報    |
|--------|-----------|
| **Data Lake Storage Gen2 ストレージ アカウントの API** | 部分的にサポート <br><br>Data Lake Storage のマルチプロトコル アクセスは、現在、パブリック プレビュー段階です。 このプレビューでは、階層型名前空間があるアカウントで、.NET、Java、Python SDK の BLOB API を使用できます。  これらの SDK には、ディレクトリの操作やアクセス制御リスト (ACL) の設定を可能にする API はまだ含まれていません。 これらの機能を実行するには、Data Lake Storage Gen2 **REST** API を使用できます。 |
| **AzCopy** | バージョン固有のサポート <br><br>AzCopy の最新バージョン ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)) のみを使用します。 AzCopy の以前のバージョン (AzCopy v8.1 など) はサポートされていません。|
| **Azure Blob Storage ライフサイクル管理ポリシー** | [Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)のプレビューに登録している場合にのみサポートされます。 クール アクセス レベルとアーカイブ アクセス レベルは、プレビューでのみサポートされています。 BLOB スナップショットの削除は、まだサポートされていません。 |
| **Azure Content Delivery Network (CDN)** | まだサポートされていません|
| **Azure Search** |[Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)のプレビューに登録している場合にのみサポートされます。|
| **Azure Storage Explorer** | バージョン固有のサポート <br><br>バージョン `1.6.0` 以降のみを使用します。 <br>バージョン `1.6.0` は[無料のダウンロード](https://azure.microsoft.com/features/storage-explorer/)として提供されています。|
| **BLOB コンテナーの ACL** |まだサポートされていません|
| **Blobfuse** |まだサポートされていません|
| **カスタム ドメイン** |まだサポートされていません|
| **ファイル システム エクスプローラー** | 制限付きサポート |
| **診断ログ** |診断ログは、[Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)のプレビューに登録している場合にのみサポートされます。 <br><br>Azure portal でのログの有効化は現在サポートされていません。 PowerShell を使用してログを有効にする方法の例を次に示します。 <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>` <br><br>この例に示すように、`-ServiceType` パラメーターの値として `Blob` を指定してください。 <br><br>現時点では、診断ログの表示に Azure Storage Explorer を使用できません。 ログを表示するには、AzCopy または SDK を使用してください。
| **不変ストレージ** |まだサポートされていません <br><br>不変ストレージでは、[WORM (Write Once, Read Many)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) 状態でデータを格納できます。|
| **オブジェクト レベルの階層** |クール レベルとアーカイブ レベルは、[Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)のプレビューに登録している場合にのみサポートされます。 <br><br> その他のすべてのアクセス層は、まだサポートされていません。|
| **Powershell と CLI のサポート** | 機能の制限あり <br><br>アカウントの作成などの管理操作がサポートされています。 ファイルのアップロードやダウンロードなどのデータ プレーン操作は、[Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)の一部として、パブリック プレビュー段階にあります。 ディレクトリの操作とアクセス制御リスト (ACL) の設定は、まだサポートされていません。 |
| **静的な Web サイト** |まだサポートされていません <br><br>具体的には、[静的な Web サイト](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)にファイルを提供する機能です。|
| **サード パーティ製アプリケーション** | 制限付きサポート <br><br>REST API を使用して動作するサード パーティ製アプリケーションは、Data Lake Storage Gen2 と使用しても引き続き機能します。 <br>[Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)のパブリック プレビューに登録した場合、BLOB API を呼び出すアプリケーションが機能する可能性があります。 
| **バージョン管理機能** |まだサポートされていません <br><br>これには、[スナップショット](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)と[論理的な削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)が含まれます。|


