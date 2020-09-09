---
title: Azure Data Lake Storage Gen2 に関する既知の問題 | Microsoft Docs
description: Azure Data Lake Storage Gen2 に関する制限と既知の問題について説明します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: fd500b80f0c564fc0f4c7e311483790a83a4101a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923740"
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

* BLOB API と Data Lake Storage API の両方を使用して、ファイルの同じインスタンスに書き込むことはできません。 Data Lake Storage Gen2 API を使用してファイルに書き込むと、そのファイルのブロックは、[Get Block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) BLOB API への呼び出しで認識されなくなります。 Data Lake Storage Gen2 API または BLOB API のいずれかを使用して、ファイルを上書きできます。 これはファイルのプロパティには影響しません。

* 区切り記号を指定せずに [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 操作を使用した場合、結果にはディレクトリと BLOB の両方が含まれます。 区切り記号を使用する場合は、スラッシュ (`/`) のみを使用してください。 サポートされている区切り記号はこれだけです。

* [Delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API を使用してディレクトリを削除した場合、そのディレクトリは空の場合にのみ削除されます。 これは、Blob API を使用してディレクトリを再帰的に削除することはできないことを意味します。

次の BLOB REST API はサポートされていません。

* [Put Blob (Page)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Get Page Ranges](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Incremental Copy Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Put Page from URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Append Block from URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

アンマネージド VM ディスクは、階層型名前空間があるアカウントではサポートされていません。 ストレージ アカウントで階層型名前空間を有効にする場合は、階層型名前空間機能が有効ではないストレージ アカウントにアンマネージド VM ディスクを配置してください。

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>アクセス制御リスト (ACL) を再帰的に設定するためのサポート

親ディレクトリから子項目に ACL 変更を再帰的に適用する機能は、[パブリック プレビュー](recursive-access-control-lists.md)にあります。 この機能の現在のリリースでは、PowerShell、.NET SDK、および Python SDK を使用して ACL の変更を適用できます。 Java SDK、Azure CLI、Azure portal、または Azure Storage Explorer のサポートはまだ提供されていません。

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

AzCopy の最新バージョン ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)) のみを使用してください。 AzCopy の以前のバージョン (AzCopy v8.1 など) はサポートされていません。

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

バージョン  `1.6.0` 以降のみを使用します。

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Azure portal での Storage Explorer

ACL はまだサポートされていません。

<a id="third-party-apps"></a>

## <a name="thirdpartyapplications"></a>サード パーティ製アプリケーション

REST API を使用して動作するサード パーティ製アプリケーションは、Blob API を呼び出す Data Lake Storage Gen2 アプリケーションと一緒に使用すると、そのアプリケーションが動作するのと同じように引き続き動作します。

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>アクセス制御リスト (ACL) と匿名読み取りアクセス

コンテナーへの[匿名読み取りアクセス](storage-manage-access-to-resources.md)が許可されている場合、そのコンテナーやコンテナーに含まれているファイルには ACL は作用しません。

## <a name="premium-performance-blockblobstorage-storage-accounts"></a>Premium パフォーマンス BlockBlobStorage ストレージ アカウント

### <a name="diagnostic-logs"></a>診断ログ

Azure portal を使用して診断ログを有効にすることはできません。 PowerShell を使用して有効にすることができます。 次に例を示します。

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

リテンション期間の設定はまだサポートされていませんが、Azure Storage Explorer、REST、SDK などのサポートされているツールを使用して、ログを手動で削除することができます。

### <a name="lifecycle-management-policies"></a>ライフサイクル管理ポリシー

- ライフサイクル管理ポリシーは、Premium BlockBlobStorage ストレージ アカウントではまだサポートされていません。 

- Premium レベルから下位レベルにデータを移動することはできません。 

- **[BLOB の削除]** アクションは現在サポートされていません。 

### <a name="hdinsight-support"></a>HDInsight のサポート

HDInsight クラスターを作成しても、階層型名前空間機能が有効になっている BlockBlobStorage アカウントはまだ選択できません。 ただし、アカウントを作成した後で、そのアカウントをクラスターにアタッチすることはできます。

### <a name="dremio-support"></a>Dremio のサポート

Dremio は、階層型名前空間機能が有効になっている BlockBlobStorage アカウントにまだ接続していません。 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure Storage Blob (WASB) ドライバー (Data Lake Storage Gen2 ではサポートされていません)

現時点では、BLOB API のみで動作するように設計された WASB ドライバーでは、いくつかの一般的なシナリオで問題が発生します。 特に、階層型名前空間が有効なストレージ アカウントのクライアントである場合です。 これらの問題は、Data Lake Storage のマルチプロトコル アクセスによっても軽減されません。 

しばらく (おそらく当面) の間は、階層型名前空間が有効なストレージ アカウントのクライアントとして、WASB ドライバーを使用しているお客様はサポートされません。 代わりに、Hadoop 環境で [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) ドライバーを使用することをお勧めします。 Hadoop branch 3 より前のバージョンを使用するオンプレミスの Hadoop 環境から移行しようとしている場合は、お客様と組織にとって適切な方法についてご連絡できるように、Azure サポート チケットを開いてください。
