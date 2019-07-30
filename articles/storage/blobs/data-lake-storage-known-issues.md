---
title: Azure Data Lake Storage Gen2 に関する既知の問題 | Microsoft Docs
description: Azure Data Lake Storage Gen2 に関する制限と既知の問題について説明します
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: normesta
ms.openlocfilehash: daf9199104047f714d568bd2796490b836243952
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443229"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 に関する既知の問題

この記事では、階層型名前空間を持つストレージ アカウント (Azure Data Lake Storage Gen2) でまだサポートされていないか部分的にサポートされている機能とツールについて説明します。

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>BLOB ストレージ API

BLOB Storage API と Azure Data Lake Gen2 API 間の相互運用性がまだ不十分なために発生する可能性のある機能の運用性の問題を防ぐために、BLOB Storage API は無効になっています。

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>既存のツール、アプリケーション、サービスがある場合の対処方法

これらのいずれかで BLOB API を使用しており、それらを使用して、アカウントにアップロードするすべてのコンテンツを処理する場合、BLOB API が Azure Data Lake Gen2 API と相互運用可能になるまで、お使いの BLOB ストレージ アカウントで階層型名前空間は有効にしないでください。

階層型名前空間なしでストレージ アカウントを使用すると、ディレクトリやファイル システムのアクセス制御リストなど、Data Lake Storage Gen2 固有の機能にアクセスできなくなります。

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>アンマネージド仮想マシン (VM) ディスクがある場合の対処方法

これらは無効にされた BLOB Storage API に依存しているため、ストレージ アカウントで階層型名前空間を有効にする場合は、これらを階層型名前空間機能が有効ではないストレージ アカウントに配置することを検討してください。

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>BLOB API が無効にされる前に BLOB API を使ってデータを読み込んでいた場合の対処方法

これらの API を使用して、データが無効にされる前に、データを読み込んでおり、そのデータにアクセスする運用上の要件がある場合は、次の情報を用意して Microsoft サポートにお問い合わせください。

> [!div class="checklist"]
> * サブスクリプション ID (名前ではなく GUID)。
> * ストレージ アカウント名。
> * 運用に大きな影響があるかどうか。その場合は、どのストレージ アカウントか。
> * 運用に大きな影響がない場合でも、何らかの理由でこのデータを別のストレージ アカウントにコピーする必要があるかどうか、ある場合はその理由をお伝えください。

このような状況では、階層型名前空間機能が有効にされていないストレージ アカウントにこのデータをコピーできるように、一定の期間 BLOB API へのアクセスを復元できます。

## <a name="all-other-features-and-tools"></a>その他すべての機能とツール

階層型名前空間を持つストレージ アカウント (Azure Data Lake Storage Gen2) でまだサポートされていないか部分的にサポートされているその他すべての機能とツールを次の表に示します。

| 機能 / ツール    | 詳細情報    |
|--------|-----------|
| **Data Lake Storage Gen2 ストレージ アカウントの API** | 部分的にサポート <br><br>Data Lake Storage Gen2 の **REST** API は利用できますが、他の BLOB SDK (.NET、Java、Python の SDK など) の API はまだ利用できません。|
| **AzCopy** | バージョン固有のサポート <br><br>AzCopy の最新バージョン ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)) のみを使用します。 AzCopy の以前のバージョン (AzCopy v8.1 など) はサポートされていません。|
| **Azure Blob Storage ライフサイクル管理ポリシー** | まだサポートされていません |
| **Azure Content Delivery Network (CDN)** | まだサポートされていません|
| **Azure Search** |まだサポートされていません|
| **Azure Storage Explorer** | バージョン固有のサポート <br><br>バージョン `1.6.0` 以降のみを使用します。 <br>バージョン `1.6.0` は[無料のダウンロード](https://azure.microsoft.com/features/storage-explorer/)として提供されています。|
| **BLOB コンテナーの ACL** |まだサポートされていません|
| **Blobfuse** |まだサポートされていません|
| **カスタム ドメイン** |まだサポートされていません|
| **診断ログ** |まだサポートされていません|
| **ファイル システム エクスプローラー** | 制限付きサポート |
| **不変ストレージ** |まだサポートされていません <br><br>不変ストレージでは、[WORM (Write Once, Read Many)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) 状態でデータを格納できます。|
| **オブジェクト レベルの階層** |まだサポートされていません <br><br>例: Premium、ホット、クール、アーカイブの各階層。|
| **Powershell と CLI のサポート** | 機能の制限あり <br><br>Powershell または CLI を使用してアカウントを作成できます。 ファイル システム、ディレクトリ、ファイルに対して操作を実行したり、アクセス制御リストを設定したりすることはできません。|
| **静的な Web サイト** |まだサポートされていません <br><br>具体的には、[静的な Web サイト](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)にファイルを提供する機能です。|
| **サード パーティ製アプリケーション** | 制限付きサポート <br><br>REST API を使用して動作するサード パーティ製アプリケーションは、Data Lake Storage Gen2 と使用しても引き続き機能します。 <br>BLOB API を使用するアプリケーションがある場合、そのアプリケーションを Data Lake Storage Gen2 と使用すると、高い確率でアプリケーションに問題が発生します。 詳細については、この記事の [Data Lake Storage Gen2 ストレージ アカウントでの BLOB Storage API の無効化](#blob-apis-disabled)に関するセクションを参照してください。|
| **バージョン管理機能** |まだサポートされていません <br><br>これには、[スナップショット](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)と[論理的な削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)が含まれます。|
|

