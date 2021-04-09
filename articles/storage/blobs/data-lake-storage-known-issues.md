---
title: Azure Data Lake Storage Gen2 に関する既知の問題 | Microsoft Docs
description: Azure Data Lake Storage Gen2 に関する制限と既知の問題について説明します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: db55a29e53c53e26fbb04d96ddf1864946bafbfd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581280"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 に関する既知の問題

この記事では、Azure Data Lake Storage Gen2 に関する制限と既知の問題について説明します。

## <a name="supported-blob-storage-features"></a>サポートされる BLOB ストレージの機能

階層型名前空間を持つアカウントで、より多くの BLOB ストレージ機能が動作するようになりました。 完全な一覧については、「[Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能](data-lake-storage-supported-blob-storage-features.md)」を参照してください。

## <a name="supported-azure-service-integrations"></a>サポートされる Azure サービスの統合

Azure Data Lake Storage Gen2 は、データの取り込み、分析の実行、およびビジュアル表現の作成に使用できるいくつかの Azure サービスをサポートしています。 サポートされる Azure サービスの一覧については、「[Azure Data Lake Storage Gen2 をサポートする Azure サービス](data-lake-storage-supported-azure-services.md)」を参照してください。

「[Azure Data Lake Storage Gen2 をサポートするAzure サービス](data-lake-storage-supported-azure-services.md)」を参照してください。

## <a name="supported-open-source-platforms"></a>サポートされるオープン ソース プラットフォーム

一部のオープン ソース プラットフォームは Data Lake Storage Gen2 をサポートしています。 完全な一覧については、「[Data Lake Storage Gen2 をサポートするオープン ソース プラットフォーム](data-lake-storage-supported-open-source-platforms.md)」を参照してください。

「[Azure Data Lake Storage Gen2 をサポートするオープン ソース プラットフォーム](data-lake-storage-supported-open-source-platforms.md)」を参照してください。

## <a name="blob-storage-apis"></a>BLOB ストレージ API

BLOB API と Data Lake Storage Gen2 API では、同じデータを処理できます。

このセクションでは、BLOB API と Data Lake Storage Gen2 API を使用して同じデータを操作する場合の問題と制限事項について説明します。

* BLOB API と Data Lake Storage API を使用して、ファイルの同じインスタンスに書き込むことはできません。 Data Lake Storage Gen2 API を使用してファイルに書き込むと、そのファイルのブロックは、[Get Block List](/rest/api/storageservices/get-block-list) BLOB API への呼び出しで認識されなくなります。 唯一の例外は、上書きする場合です。 いずれかの API を使用して、ファイルまたは BLOB を上書きできます。

* 区切り記号を指定せずに [List Blobs](/rest/api/storageservices/list-blobs) 操作を使用した場合、結果にはディレクトリと BLOB の両方が含まれます。 区切り記号を使用する場合は、スラッシュ (`/`) のみを使用してください。 サポートされている区切り記号はこれだけです。

* [Delete Blob](/rest/api/storageservices/delete-blob) API を使用してディレクトリを削除した場合、そのディレクトリは空の場合にのみ削除されます。 これは、Blob API を使用してディレクトリを再帰的に削除することはできないことを意味します。

次の BLOB REST API はサポートされていません。

* [Put Blob (Page)](/rest/api/storageservices/put-blob)
* [Put Page](/rest/api/storageservices/put-page)
* [Get Page Ranges](/rest/api/storageservices/get-page-ranges)
* [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob)
* [Put Page from URL](/rest/api/storageservices/put-page-from-url)

アンマネージド VM ディスクは、階層型名前空間があるアカウントではサポートされていません。 ストレージ アカウントで階層型名前空間を有効にする場合は、階層型名前空間機能が有効ではないストレージ アカウントにアンマネージド VM ディスクを配置してください。

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>アクセス制御リスト (ACL) を再帰的に設定するためのサポート

親ディレクトリから子項目に ACL 変更を再帰的に適用する機能が公開されています。 この機能の現在のリリースでは、PowerShell、Azure CLI、および .NET、Java、および Python SDK を使用して ACL の変更を適用できます。 Azure portal または Azure Storage Explorer のサポートはまだ提供されていません。

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

AzCopy の最新バージョン ([AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)) のみを使用してください。 AzCopy の以前のバージョン (AzCopy v8.1 など) はサポートされていません。

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure ストレージ エクスプローラー

バージョン `1.6.0` 以降のみを使用します。

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Azure portal での Storage Explorer

ACL はまだサポートされていません。

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>サード パーティ製アプリケーション

REST API を使用して動作するサード パーティ製アプリケーションは、Blob API を呼び出す Data Lake Storage Gen2 アプリケーションと一緒に使用すると、そのアプリケーションが動作するのと同じように引き続き動作します。

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>アクセス制御リスト (ACL) と匿名読み取りアクセス

コンテナーへの[匿名読み取りアクセス](./anonymous-read-access-configure.md)が許可されている場合、そのコンテナーやコンテナーに含まれているファイルには ACL は作用しません。

## <a name="diagnostic-logs"></a>診断ログ

リテンション期間の設定はまだサポートされていませんが、Azure Storage Explorer、REST、SDK などのサポートされているツールを使用して、ログを手動で削除することができます。

## <a name="lifecycle-management-policies-with-premium-tier-for-azure-data-lake-storage"></a>Azure Data Lake Storage の Premium レベルでのライフサイクル管理ポリシー

Premium レベルで格納されているデータは、ホット、クール、アーカイブ層間で移動することはできません。 ただし、Premium レベルのデータを別のアカウントのホット アクセス層にコピーすることはできます。

## <a name="dremio-support-with-premium-performance-blockblobstorage-storage-accounts"></a>Premium パフォーマンス BlockBlobStorage ストレージ アカウントでの Dremio のサポート

Dremio は、階層型名前空間機能が有効になっている BlockBlobStorage アカウントにまだ接続していません。 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure Storage Blob (WASB) ドライバー (Data Lake Storage Gen2 ではサポートされていません)

現時点では、BLOB API のみで動作するように設計された WASB ドライバーでは、いくつかの一般的なシナリオで問題が発生します。 特に、階層型名前空間が有効なストレージ アカウントのクライアントである場合です。 これらの問題は、Data Lake Storage のマルチプロトコル アクセスによっても軽減されません。 

しばらく (おそらく当面) の間は、階層型名前空間が有効なストレージ アカウントのクライアントとして、WASB ドライバーを使用しているお客様はサポートされません。 代わりに、Hadoop 環境で [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) ドライバーを使用することをお勧めします。 Hadoop branch 3 より前のバージョンを使用するオンプレミスの Hadoop 環境から移行しようとしている場合は、お客様と組織にとって適切な方法についてご連絡できるように、Azure サポート チケットを開いてください。