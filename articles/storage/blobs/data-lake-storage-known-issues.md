---
title: Azure Data Lake Storage Gen2 に関する既知の問題 | Microsoft Docs
description: Azure Data Lake Storage Gen2 に関する制限と既知の問題について説明します
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: normesta
ms.openlocfilehash: 89cfdbdaa034bae5ca736ccb9164255b833ed75d
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194710"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 に関する既知の問題

この記事では、Data Lake Storage Gen2 の既知の問題と一時的な制限事項について説明します。

## <a name="sdk-support-for-data-lake-storage-gen2-accounts"></a>Data Lake Storage Gen2 アカウントに対する SDK のサポート

Data Lake Storage Gen2 アカウントで利用できる SDK はありません。

## <a name="blob-storage-apis"></a>BLOB Storage API

Blob Storage API は、Data Lake Storage Gen2 アカウントではまだ使用できません。

BLOB Storage API と Azure Data Lake Gen2 API 間の相互運用性がまだ不十分なために発生する可能性のある不適切なデータ アクセスの問題を防ぐために、これらの API は無効になっています。

これらの API を使用して、データが無効にされる前に、データを読み込んでおり、そのデータにアクセスする運用上の要件がある場合は、次の情報を用意して Microsoft サポートにお問い合わせください。

* サブスクリプション ID (GUID、名前でなく)

* ストレージ アカウント名

* 運用に大きな影響があるかどうか。その場合は、どのストレージ アカウントか。

* 運用に大きな影響がない場合でも、何らかの理由でこのデータを別のストレージ アカウントにコピーする必要があるかどうか、ある場合はその理由をお伝えください。

このような状況では、階層型名前空間機能が有効にされていないストレージ アカウントにこのデータをコピーできるように、一定の期間 BLOB API へのアクセスを復元できます。

アンマネージド仮想マシン (VM) ディスクは、無効にされた Blob Storage API に依存しているため、ストレージ アカウントで階層型名前空間を有効にする場合は、アンマネージド VM ディスクを階層型名前空間機能が有効ではないストレージ アカウントに配置することを検討してください。

## <a name="api-interoperability"></a>API の相互運用性

Blob Storage API と Azure Data Lake Gen2 API 間には相互運用性がありません。

BLOB API を使用するツール、アプリケーション、サービス、またはスクリプトがあり、それらを使用して、アカウントにアップロードするすべてのコンテンツを処理する場合、BLOB API が Azure Data Lake Gen2 API と相互運用可能になるまで、BLOB ストレージ アカウントで階層型名前空間は有効にしないでください。 階層型名前空間なしでストレージ アカウントを使用すると、ディレクトリやファイルシステムのアクセス制御リストなど、Data Lake Storage Gen2 固有の機能にアクセスできなくなります。

## <a name="azure-storage-explorer"></a>Azure ストレージ エクスプローラー

Azure Storage Explorer を使用して Data Lake Storage Gen2 アカウントを表示または管理するには、[無料ダウンロード](https://azure.microsoft.com/features/storage-explorer/)として入手可能なバージョン `1.6.0` 以上のツールが必要です。

Azure Portal に埋め込まれている Storage Explorer のバージョンでは、階層型名前空間機能が有効な Data Lake Storage Gen2 アカウントの表示や管理を現在サポートしていません。

## <a name="blob-viewing-tool"></a>BLOB 表示ツール

Data Lake Storage Gen2 では、Azure portal の BLOB 表示ツールの一部の機能のみがサポートされています。

## <a name="third-party-applications"></a>サードパーティ アプリケーション

サードパーティ アプリケーションでは、Data Lake Storage Gen2 はサポートされていない場合があります。

サポートは、各サードパーティ アプリケーション プロバイダーの判断で行われます。 現時点では、Blob Storage API と Azure Data Lake Storage Gen2 API を使用して同じコンテンツを管理することはできません。 Microsoft では、この相互運用性を実現するための作業を進めているため、多くのサードパーティ ツールで Azure Data Lake Storage Gen2 が自動的にサポートされるようになる可能性があります。

## <a name="azcopy-support"></a>AzCopy のサポート

AzCopy バージョン 8 では、Data Lake Storage Gen2 はサポートされていません。

代わりに、最新のプレビュー バージョンの AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)) を使用してください (Data Lake Storage Gen2 エンドポイントをサポートしています)。

## <a name="azure-event-grid"></a>Azure Event Grid

Azure Data Lake Gen2 アカウントからイベントがまだ生成されていないため、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) はそのイベントを受信していません。  

## <a name="soft-delete-and-snapshots"></a>ソフト削除とスナップショット

Data Lake Storage Gen2 アカウントでは、ソフト削除とスナップショットを使用できません。

階層型名前空間機能が有効なストレージ アカウントでは、[スナップショット](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)と[ソフト削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)を含むすべてのバージョン管理機能をまだ使用できません。

## <a name="object-level-storage-tiers"></a>オブジェクト レベルのストレージ層

Azure Data Lake Storage Gen 2 アカウントでは、オブジェクト レベルのストレージ層 (ホット、コールド、アーカイブ) をまだ使用できませんが、階層型名前空間機能が有効ではないストレージ アカウントでは使用できます。

## <a name="azure-blob-storage-lifecycle-management-policies"></a>Azure Blob Storage ライフサイクル管理ポリシー

Data Lake Storage Gen2 アカウントでは、Azure Blob Storage ライフサイクル管理ポリシーをまだ使用できません。

このようなポリシーは、階層型名前空間機能が有効ではないストレージ アカウントに使用できます。

## <a name="diagnostic-logs"></a>診断ログ

Azure Data Lake Storage Gen2 アカウントでは、診断ログは使用できません。
