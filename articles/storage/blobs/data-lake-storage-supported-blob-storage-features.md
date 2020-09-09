---
title: Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能 | Microsoft Docs
description: Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能について説明します
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 87e0fe7baaf3d83c18b12227e9ecb5bf1d2fb361
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869458"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能

階層型名前空間があるアカウントで、[診断ログ](../common/storage-analytics-logging.md)、[アクセス レベル](storage-blob-storage-tiers.md)、 [BLOB ストレージ ライフサイクル管理ポリシー](storage-lifecycle-management-concepts.md)などの BLOB ストレージの機能が動作するようになりました。 そのため、これらの機能へのアクセスを失うことなく、Blob Storage アカウントで階層型名前空間を有効にできます。

Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能を次の表に示します。 サポートは継続的に拡張されるため、この表に示されている項目は時間の経過と共に変化します。 機能のプレビュー状態に関連する特定の問題の詳細については、[既知の問題](data-lake-storage-known-issues.md)に関する記事を参照してください。

## <a name="supported-blob-storage-features"></a>サポートされている BLOB ストレージ機能

> [!NOTE]
> サポート レベルは、機能が Data Lake Storage Gen2 でどのようにサポートされているかのみを示します。 
>
> Data Lake Storage Gen2 向けの [Premium パフォーマンスの BlockBlobStorage アカウント](storage-blob-create-account-block-blob.md)は、現在パブリック プレビューの段階にあります。 これらの種類のアカウントのサポート レベルは、**BlockBlobStorage (Premium)** 列に表示されます。

|BLOB ストレージ機能 |汎用 v2 |BlockBlobStorage (Premium) |関連記事 |
|---------------|-------------------|---|
|ホット アクセス層|一般公開|サポートされていません|[Azure BLOB Storage: ホット、クール、アーカイブ アクセス層](storage-blob-storage-tiers.md)|
|クール アクセス層|一般公開|サポートされていません|[Azure BLOB Storage: ホット、クール、アーカイブ アクセス層](storage-blob-storage-tiers.md)|
|events|一般公開|プレビュー|[Blob Storage のイベント処理](storage-blob-event-overview.md)|
|メトリック (クラシック)|一般公開|サポートされていません|[Azure Storage Analytics のメトリック (クラシック)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Monitor のメトリック|一般公開|プレビュー|[Azure Monitor の Azure Storage メトリック](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB ストレージの PowerShell コマンド|一般公開|プレビュー|[クイック スタート: PowerShell を使用して BLOB をアップロード、ダウンロード、および一覧表示する](storage-quickstart-blobs-powershell.md)|
|BLOB ストレージの Azure CLI コマンド|一般公開|プレビュー|[クイック スタート: Azure CLI を使用して BLOB を作成、ダウンロード、一覧表示する](storage-quickstart-blobs-cli.md)|
|BLOB ストレージ API|一般公開|プレビュー|[クイック スタート: .NET 用 Azure Blob Storage クライアント ライブラリ v12](storage-quickstart-blobs-dotnet.md)<br>[クイック スタート: Java v12 SDK で BLOB を管理する](storage-quickstart-blobs-java.md)<br>[クイック スタート: Python v12 SDK で BLOB を管理する](storage-quickstart-blobs-python.md)<br>[クイック スタート: Node.js の JavaScript v12 SDK を使用して BLOB を管理する](storage-quickstart-blobs-nodejs.md)|
|診断ログ|一般公開|プレビュー <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Azure Storage Analytics のログ](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|アーカイブ アクセス層|一般公開|サポートされていません|[Azure BLOB Storage: ホット、クール、アーカイブ アクセス層](storage-blob-storage-tiers.md)|
|ライフサイクル管理ポリシー|一般公開|まだサポートされていません|[Azure Blob Storage のライフサイクルの管理](storage-lifecycle-management-concepts.md)|
|Azure Monitor へのログイン|プレビュー |まだサポートされていません|[Azure Storage の監視](../common/monitor-storage.md)|
|スナップショット|プレビュー|まだサポートされていません|[BLOB のスナップショット](snapshots-overview.md)|
|静的な Web サイト|プレビュー|まだサポートされていません|[Azure Storage での静的な Web サイトのホスティング](storage-blob-static-website.md)|
|不変ストレージ|プレビュー|まだサポートされていません|[不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](storage-blob-immutable-storage.md)|
|ライフサイクル管理ポリシー|プレビュー|まだサポートされていません|[Azure Blob Storage のライフサイクルの管理](storage-lifecycle-management-concepts.md)|
|コンテナーの論理的な削除|プレビュー|プレビュー|[コンテナーの論理的な削除 (プレビュー)](soft-delete-container-overview.md)|
|BLOB の論理的な削除|まだサポートされていません|まだサポートされていません|[BLOB の論理的な削除](storage-blob-soft-delete.md)|
|blobfuse|プレビュー|まだサポートされていません|[blobfuse を使用して Blob Storage をファイル システムとしてマウントする方法](storage-how-to-mount-container-linux.md)|
|Change Feed|まだサポートされていません|まだサポートされていません|[Azure Blob Storage の変更フィードのサポート](storage-blob-change-feed.md)|
|アカウントのフェールオーバー|まだサポートされていません|まだサポートされていません|[ディザスター リカバリーとアカウントのフェールオーバー](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB コンテナーの ACL|まだサポートされていません<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|まだサポートされていません<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|[コンテナー ACL の設定](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|カスタム ドメイン|まだサポートされていません|まだサポートされていません|[カスタム ドメインを Azure Blob ストレージ エンドポイントにマップする](storage-custom-domain-name.md)|
|暗号化スコープ|まだサポートされていません|まだサポートされていません|[暗号化スコープの作成と管理 (プレビュー)](encryption-scope-manage.md)|

<div id="diagnostic-logging"><sup>1</sup> Premium ブロック BLOB ストレージ アカウントでは、Azure portal を使用して診断ログ (クラシック) を有効にすることはできません。 PowerShell を使用して有効にしてください。</div><br>

<div id="blob-container-ACL"><sup>2</sup> コンテナーのルート フォルダーに ACL を設定することはできますが、コンテナー自体は設定できません。</div><br>

<div id="preview-form"><sup>3</sup>Data Lake Storage Gen2 でスナップショット、不変ストレージ、または静的 Web サイトを使用するには、この<a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>フォーム</a>に記入して、プレビューに登録する必要があります。  </div>

## <a name="see-also"></a>関連項目

- [Azure Data Lake Storage Gen2 に関する既知の問題](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage Gen2 がサポートされている Azure のサービス](data-lake-storage-supported-azure-services.md)
- [Azure Data Lake Storage Gen2 がサポートされているオープン ソース プラットフォーム](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)
