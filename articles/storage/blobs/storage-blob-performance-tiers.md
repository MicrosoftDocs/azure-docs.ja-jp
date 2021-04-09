---
title: ブロック BLOB ストレージのパフォーマンス レベル - Azure Storage
description: Azure ブロック BLOB ストレージの Premium と Standard のパフォーマンス レベルの違いについて説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: cf0b5d29e0dc375a07fe024ef0763c8200275055
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880710"
---
# <a name="performance-tiers-for-block-blob-storage"></a>ブロック BLOB ストレージのパフォーマンス レベル

企業がパフォーマンスを重視するクラウド ネイティブ アプリケーションを展開する場合、さまざまなパフォーマンス レベルでコスト効率の高いデータ ストレージのオプションを用意することが重要です。

Azure ブロック BLOB ストレージには、次の 2 つの異なるパフォーマンス レベルがあります。

- **Premium**: 高いトランザクション率と一貫した 1 桁のストレージ待機時間のために最適化されています
- **Standard**: 高容量および高スループットのために最適化されています

この異なるパフォーマンス レベルでは、次の条件が該当する必要があります。

| 領域 |Standard パフォーマンス  |Premium パフォーマンス  |
|---------|---------|---------|
|利用可能なリージョン     |   すべてのリージョン      | [一部のリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|サポートされる[ストレージ アカウントの種類](../common/storage-account-overview.md#types-of-storage-accounts)     |     汎用 v2、BlobStorage、汎用 v1    |    BlockBlobStorage     |
|[高スループット ブロック BLOB](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) をサポート     |    はい (4 MiB を超える MiB PutBlock または PutBlock のサイズ)     |    はい (256 KiB を超える MiB PutBlock または PutBlock のサイズ)    |
|冗長性     |     「[ストレージ アカウントの種類](../common/storage-account-overview.md#types-of-storage-accounts)」を参照してください。   |  現在、ローカル冗長ストレージ (LRS) とゾーン冗長ストレージ (ZRS) のみをサポートしています。<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> ゾーン冗長ストレージ (ZRS) は、一部のリージョンで Premium パフォーマンス ブロック BLOB ストレージ アカウントに使用できます。</div>

コストに関して、Premium パフォーマンスは、トランザクション率が高いアプリケーションのワークロードの[総ストレージ コストを低減](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/)するよう価格が最適化されています。

## <a name="premium-performance"></a>Premium パフォーマンス

Premium パフォーマンスのブロック BLOB ストレージでは、高性能なハードウェア経由でデータを利用できます。 データは、低待機時間のために最適化されたソリッドステート ドライブ (SSD) に格納されています。 SSD は従来のハード ドライブと比べ、スループットが高くなります。

Premium パフォーマンス ストレージは、高速で一貫性のある応答時間を必要とするワークロードに最適です。 これは、小規模なトランザクションを多数実行するワークロードに最適です。 ワークロードの例を次に示します。

- **対話型ワークロード**。 これらのワークロードには、即時の更新とユーザーからのフィードバック が必要です (eコマースやマッピング アプリケーションなど)。 たとえば、eコマース アプリケーションでは、表示頻度の低い項目はキャッシュされない可能性があります。 ただし、要求時には顧客に瞬時に表示される必要があります。

- **分析**。 IoT シナリオでは、毎秒、小さな書き込み操作がクラウドにプッシュされる可能性があります。 大量のデータが収集され、分析のために集計され、ほぼ瞬時に削除される場合があります。 Premium ブロック BLOB ストレージのインジェスト機能を利用すると、この種類のワークロードに効率的に対応できます。

- **人工知能/機械学習 (AI/ML)** 。 AI/ML では、ビジュアル、音声、テキストなどのさまざまなデータの種類の使用と処理を扱います。 このハイ パフォーマンス コンピューティング型のワークロードでは大量のデータが処理されますが、データ分析のために迅速な応答と効率的なインジェスト時間が必要です。

- **データの変換**。 データの継続的な編集、変更、および変換を必要とするプロセスでは、即時の更新が必要です。 正確なデータ表現のために、このデータのユーザーには、こうした即時に反映された変更が表示される必要があります。

## <a name="standard-performance"></a>Standard パフォーマンス

Standard パフォーマンスでは、最もコスト効率の高い方法でデータを格納できるよう、さまざまな[アクセス層](storage-blob-storage-tiers.md)をサポートしています。 大規模なデータ セットで、高容量および高スループットを実現するために最適化されています。

- **バックアップおよびディザスター リカバリーのデータセット**。 Standard パフォーマンス ストレージでは、コスト効率に優れた層が提供されるため、短期および長期のディザスター リカバリー データセット、セカンダリ バックアップ、コンプライアンス データ アーカイブにおける最適なユース ケースとなります。

- **メディア コンテンツ**。 多くの場合、画像とビデオは最初に作成されて保存されるときに頻繁にアクセスされますが、このコンテンツ タイプは古くなるにつれて使用頻度が落ちます。 Standard パフォーマンス ストレージでは、メディア コンテンツのニーズに適した層が提供されます。 

- **バルク データ処理**。 これらの種類のワークロードは、安定した待機時間の短さより、コスト効果の高い高スループットのストレージを必要とするため、Standard Storage に適しています。 大量の生データセットは処理用にステージングされ、最終的にはクールな層に移行します。

## <a name="migrate-from-standard-to-premium"></a>Standard から Premium に移行する

既存の Standard パフォーマンス ストレージ アカウントを Premium パフォーマンスのブロック BLOB ストレージ アカウントに変換することはできません。 Premium パフォーマンス ストレージ アカウントに移行するには、BlockBlobStorage アカウントを作成し、データを新しいアカウントに移行する必要があります。 詳細については、「[ブロック BLOB ストレージ アカウントの作成](storage-blob-create-account-block-blob.md)」を参照してください。

ストレージ アカウント間で BLOB をコピーするには、最新バージョンの [AzCopy](../common/storage-use-azcopy-v10.md#transfer-data) コマンドライン ツールを使用できます。 データの移動と変換には、Azure Data Factory などの他のツールを使用することもできます。

## <a name="blob-lifecycle-management"></a>BLOB のライフサイクル管理

Blob ストレージのライフサイクル管理には、ルールベースのポリシーが豊富に用意されています。

- **Premium**: データは、そのライフサイクルの終了時に有効期限が切れます。
- **Standard**: データは、最適なアクセス層に移行され、そのライフサイクルの終了時に有効期限が切れます。

詳細については、「[Azure Blob Storage のライフサイクルを管理する](storage-lifecycle-management-concepts.md)」を参照してください。

Premium のブロック BLOB ストレージ アカウントに格納されているデータは、ホット、クール、およびアーカイブ層間で移動することはできません。 ただし、ブロック BLOB ストレージ アカウントから *別の* アカウントのホット アクセス層に BLOB をコピーすることは可能です。 別にアカウントにデータをコピーするには、[Put Block From URL](/rest/api/storageservices/put-block-from-url) API または [AzCopy v10](../common/storage-use-azcopy-v10.md) を使用します。 **Put Block From URL** API では、サーバー上のデータを同期的にコピーします。 この呼び出しは、すべてのデータが、元のサーバー上の場所からコピー先の場所に移動された後でのみ完了します。

## <a name="next-steps"></a>次のステップ

GPv2 および BLOB ストレージ アカウントでホット、クール、アーカイブを評価します。

- [アーカイブ層から BLOB データをリハイドレートする方法を確認する](storage-blob-rehydration.md)
- [Azure Storage のメトリックを有効にして現在のストレージ アカウントの使用状況を評価する](./monitor-blob-storage.md)
- [BLOB ストレージ アカウントと GPv2 アカウントのホット、クール、アーカイブのリージョンごとの料金を確認する](https://azure.microsoft.com/pricing/details/storage/)
- [データ転送の価格を確認する](https://azure.microsoft.com/pricing/details/data-transfers/)
