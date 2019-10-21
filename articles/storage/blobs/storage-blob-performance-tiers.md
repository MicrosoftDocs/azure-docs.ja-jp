---
title: Azure ブロック BLOB ストレージのパフォーマンス レベル - Azure Storage
description: Azure Blob ストレージのパフォーマンス レベル。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/02/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ac483a338b7d71142b89b13e41fc048346ac037f
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803920"
---
# <a name="azure-block-blob-storage-performance-tiers"></a>Azure ブロック BLOB ストレージのパフォーマンス レベル

企業がパフォーマンスを重視するクラウド ネイティブ アプリケーションを展開する場合、さまざまなパフォーマンス レベルでコスト効率の高いデータ ストレージのオプションを用意することが重要です。

Azure ブロック BLOB ストレージには、次の 2 つの異なるパフォーマンス レベルがあります。

- Premium: 高いトランザクション率と一貫した 1 桁のストレージ待機時間のために最適化されています
- Standard: 高容量および高スループットのために最適化されています

この異なるパフォーマンス レベルでは、次の条件が該当する必要があります。

- Standard パフォーマンスは、すべての [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=storage)で利用できます。 Premium パフォーマンスは、[特定のリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=storage)で利用できます。
- Premium パフォーマンスでは、トランザクション率が高いアプリケーションのワークロードの[総ストレージ コストを低減](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/)するよう価格が最適化されています。
- Premium パフォーマンスでは、ブロック BLOB と追加 BLOB をサポートするブロック BLOB ストレージ アカウントを使用する必要があります。
- Standard パフォーマンスは、General Purpose v1、General Purpose v2、BLOB ストレージ アカウントで使用できます。
- Premium パフォーマンスおよび Standard パフォーマンスはいずれも、[高スループット ブロック BLOB](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) をサポートします。 高スループット ブロック BLOB は、256 KiB を超える Premium パフォーマンスで利用できます。 高スループット ブロック BLOB は、4 MiB を超える Put Block または Put Blob サイズの Standard パフォーマンスで利用できます。
- 現在、Premium パフォーマンスはローカル冗長ストレージ (LRS) のみで利用できます。

## <a name="premium-performance"></a>Premium パフォーマンス

Premium パフォーマンスのブロック BLOB ストレージでは、高性能なハードウェア経由でデータを利用できます。 データは、低待機時間のために最適化されたソリッドステート ドライブ (SSD) に格納されています。 SSD は従来のハード ドライブと比べ、スループットが高くなります。

Premium パフォーマンス ブロック BLOB ストレージは、高速で一貫性のある応答時間を必要とするワークロードに最適です。 これは、小規模なトランザクションを多数実行するワークロードに最適です。

## <a name="standard-performance"></a>Standard パフォーマンス

Standard パフォーマンスでは、最もコスト効率の高い方法でデータを格納できるよう、さまざまな[アクセス層](storage-blob-storage-tiers.md)をサポートしています。 大規模なデータ セットで、高容量および高スループットを実現するために最適化されています。

## <a name="blob-lifecycle-management"></a>BLOB のライフサイクル管理

Blob Storage のライフサイクル管理には、ルールベースのポリシーが豊富に用意されています。

- Premium - データは、そのライフサイクルの終了時に期限切れになります
- Standard - データを最適なアクセス層に移行し、そのライフサイクルの終了時にデータを期限切れにします

詳細については、「[Azure Blob Storage のライフサイクルを管理する](storage-lifecycle-management-concepts.md)」を参照してください。

Premium のブロック BLOB ストレージ アカウントに格納されているデータは、ホット、クール、およびアーカイブ層間で移動することはできません。 ただし、ブロック BLOB ストレージ アカウントから*別の*アカウントのホット アクセス層に BLOB をコピーすることは可能です。 別にアカウントにデータをコピーするには、[Put Block From URL](/rest/api/storageservices/put-block-from-url) API または [AzCopy v10](../common/storage-use-azcopy-v10.md) を使用します。 **Put Block From URL** API では、サーバー上のデータを同期的にコピーします。 この呼び出しは、すべてのデータが、元のサーバー上の場所からコピー先の場所に移動された後でのみ完了します。

## <a name="next-steps"></a>次の手順

GPv2 および BLOB ストレージ アカウントにおけるホット、クール、アーカイブの評価

- [ホット、クール、アーカイブのリージョンごとの提供状況を確認する](https://azure.microsoft.com/regions/#services)
- [Azure Blob Storage のライフサイクルの管理](storage-lifecycle-management-concepts.md)
- [アーカイブ層から BLOB データをリハイドレートする方法を確認する](storage-blob-rehydration.md)
- [Azure Storage のメトリックを有効にして現在のストレージ アカウントの使用状況を評価する](../common/storage-enable-and-view-metrics.md)
- [BLOB ストレージ アカウントと GPv2 アカウントのホット、クール、アーカイブのリージョンごとの料金を確認する](https://azure.microsoft.com/pricing/details/storage/)
- [データ転送の価格を確認する](https://azure.microsoft.com/pricing/details/data-transfers/)
