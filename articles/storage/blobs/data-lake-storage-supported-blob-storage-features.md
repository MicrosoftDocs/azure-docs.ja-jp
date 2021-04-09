---
title: Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能 | Microsoft Docs
description: Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能について説明します
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.openlocfilehash: 769163dd09ec29c2c206d0273e71de27732fb9db
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516280"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能

階層型名前空間があるアカウントで、[診断ログ](../common/storage-analytics-logging.md)、[アクセス レベル](storage-blob-storage-tiers.md)、[BLOB ストレージ ライフサイクル管理ポリシー](storage-lifecycle-management-concepts.md)などの BLOB ストレージの機能が動作するようになりました。 そのため、これらの機能へのアクセスを失うことなく、Blob Storage アカウントで階層型名前空間を有効にできます。

Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能を次の表に示します。 サポートは継続的に拡張されるため、この表に示されている項目は時間の経過と共に変化します。 機能のプレビュー状態に関連する特定の問題の詳細については、[既知の問題](data-lake-storage-known-issues.md)に関する記事を参照してください。

## <a name="supported-blob-storage-features"></a>サポートされている BLOB ストレージ機能

次の表は、各 BLOB ストレージ機能が Data Lake Storage Gen2 でどのようにサポートされているかを示しています。 Standard と [Premium のパフォーマンス](premium-tier-for-data-lake-storage.md) レベルの列があります。 

|BLOB ストレージ機能 |Standard |Premium |関連記事 |
|---------------|-------------------|---|
|ホット アクセス層|一般公開|サポートされていません|[Azure BLOB Storage: ホット、クール、アーカイブ アクセス層](storage-blob-storage-tiers.md)|
|クール アクセス層|一般公開|サポートされていません|[Azure BLOB Storage: ホット、クール、アーカイブ アクセス層](storage-blob-storage-tiers.md)|
|events|一般公開|一般公開|[Blob Storage のイベント処理](storage-blob-event-overview.md)|
|メトリック (クラシック)|一般公開|一般公開|[Azure Storage Analytics のメトリック (クラシック)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Monitor のメトリック|一般公開|プレビュー|[Azure Monitor の Azure Storage メトリック](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB ストレージの PowerShell コマンド|一般公開|一般公開|[クイック スタート: PowerShell を使用して BLOB をアップロード、ダウンロード、および一覧表示する](storage-quickstart-blobs-powershell.md)|
|BLOB ストレージの Azure CLI コマンド|一般公開|一般公開|[クイック スタート: Azure CLI を使用して BLOB を作成、ダウンロード、一覧表示する](storage-quickstart-blobs-cli.md)|
|BLOB ストレージ API|一般公開|一般公開|[クイック スタート: .NET 用 Azure Blob Storage クライアント ライブラリ v12](storage-quickstart-blobs-dotnet.md)<br>[クイック スタート: Java v12 SDK で BLOB を管理する](storage-quickstart-blobs-java.md)<br>[クイック スタート: Python v12 SDK で BLOB を管理する](storage-quickstart-blobs-python.md)<br>[クイック スタート: Node.js の JavaScript v12 SDK を使用して BLOB を管理する](storage-quickstart-blobs-nodejs.md)|
|診断ログ|一般公開|プレビュー |[Azure Storage Analytics のログ](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|アーカイブ アクセス層|一般公開|サポートされていません|[Azure BLOB Storage: ホット、クール、アーカイブ アクセス層](storage-blob-storage-tiers.md)|
|ライフサイクル管理ポリシー (階層化)|一般公開|まだサポートされていません|[Azure Blob Storage のライフサイクルの管理](storage-lifecycle-management-concepts.md)|
|ライフサイクル管理ポリシー (BLOB の削除)|一般公開|一般公開|[Azure Blob Storage のライフサイクルの管理](storage-lifecycle-management-concepts.md)|
|Azure Monitor へのログイン|プレビュー |プレビュー|[Azure Storage の監視](./monitor-blob-storage.md)|
|スナップショット|プレビュー<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|プレビュー<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[BLOB のスナップショット](snapshots-overview.md)|
|静的な Web サイト|プレビュー<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|プレビュー<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Azure Storage での静的な Web サイトのホスティング](storage-blob-static-website.md)|
|不変ストレージ|プレビュー<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|プレビュー<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](storage-blob-immutable-storage.md)|
|コンテナーの論理的な削除|プレビュー|プレビュー|[コンテナーの論理的な削除 (プレビュー)](soft-delete-container-overview.md)|
|Azure Storage インベントリ|プレビュー|プレビュー|[Azure Storage インベントリを使用して BLOB データを管理する (プレビュー)](blob-inventory.md)|
|カスタム ドメイン|プレビュー<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|プレビュー<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|[カスタム ドメインを Azure Blob ストレージ エンドポイントにマップする](storage-custom-domain-name.md)|
|BLOB の論理的な削除|まだサポートされていません|まだサポートされていません|[BLOB の論理的な削除](./soft-delete-blob-overview.md)|
|blobfuse|一般公開|一般公開|[blobfuse を使用して Blob Storage をファイル システムとしてマウントする方法](storage-how-to-mount-container-linux.md)|
|匿名パブリック アクセス |一般公開|一般公開| 「[コンテナーと BLOB の匿名パブリック読み取りアクセスを構成する](anonymous-read-access-configure.md)」を参照してください。|
|カスタマー マネージド アカウントのフェールオーバー|まだサポートされていません|まだサポートされていません|[ディザスター リカバリーとアカウントのフェールオーバー](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|カスタマー指定のキー|まだサポートされていません|まだサポートされていません|[BLOB ストレージに対する要求で暗号化キーを指定する](encryption-customer-provided-keys.md)|
|暗号化スコープ|まだサポートされていません|まだサポートされていません|[暗号化スコープの作成と管理 (プレビュー)](encryption-scope-manage.md)|
|Change Feed|まだサポートされていません|まだサポートされていません|[Azure Blob Storage の変更フィードのサポート](storage-blob-change-feed.md)|
|オブジェクト レプリケーション|まだサポートされていません|まだサポートされていません|[ブロック BLOB のオブジェクト レプリケーションを構成する](object-replication-configure.md)|
|BLOB バージョン管理|まだサポートされていません|まだサポートされていません|[BLOB のバージョン管理を有効にして管理する](versioning-enable.md)|

<div id="preview-form"><sup>1</sup>Data Lake Storage Gen2 でスナップショット、不変ストレージ、または静的 Web サイトを使用するには、この<a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>フォーム</a>に記入して、プレビューに登録する必要があります。  </div>
<div id="preview-form-2"><sup>2</sup>カスタム ドメイン名は、BLOB サービスまたは静的な Web サイトのエンドポイントにのみマップできます。 Data Lake ストレージ エンドポイントはサポートされていません。</a>。  </div>

## <a name="see-also"></a>関連項目

- [Azure Data Lake Storage Gen2 に関する既知の問題](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage Gen2 がサポートされている Azure のサービス](data-lake-storage-supported-azure-services.md)
- [Azure Data Lake Storage Gen2 がサポートされているオープン ソース プラットフォーム](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)