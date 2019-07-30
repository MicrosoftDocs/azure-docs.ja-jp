---
title: Azure Storage のスケーラビリティとパフォーマンスのターゲット - ストレージ アカウント
description: Azure ストレージ アカウントの容量、要求レート、受信と送信の帯域幅など、スケーラビリティとパフォーマンスのターゲットについて説明します。
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 046c2308d5cef2df7e12b6185fc24b8df4f821dc
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326977"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>ストレージ アカウントでの Azure Storage のスケーラビリティとパフォーマンスのターゲット

この記事では、Azure ストレージ アカウントでのスケーラビリティとパフォーマンスのターゲットについて詳しく説明します。 ここで示すスケーラビリティおよびパフォーマンスのターゲットは、ハイエンドのターゲットですが、達成可能です。 いかなる場合でも、ストレージ アカウントで達成される要求レートおよび帯域幅は、格納されたオブジェクトのサイズ、使用されているアクセス パターン、およびアプリケーションで実行されているワークロードの種類によって異なります。

必ずサービスをテストして、パフォーマンスがユーザー要件を満たしているかどうかを確認してください。 可能であれば、トラフィック量の急増を回避し、トラフィックがパーティション間でうまく分散されるようにしてください。

ワークロードがアプリケーションのパーティションで処理できる上限に達すると、Azure Storage はエラー コード 503 (サーバーがビジー状態) またはエラー コード 500 (操作タイムアウト) の応答を返し始めます。 503 エラーが発生する場合は、再試行に対してエクスポネンシャル バックオフ ポリシーを使用するようアプリケーションを変更することを検討します。 指数関数的バックオフによって、そのパーティションへの負荷が減少し、そのパーティションへのトラフィック量の増加が緩和されます。

## <a name="storage-account-scale-limits"></a>ストレージ アカウントのスケールの上限

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Premium パフォーマンス ストレージ アカウントのスケールの上限

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>ストレージ リソース プロバイダーのスケールの上限

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob Storage のスケール ターゲット

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure Files のスケール ターゲット

Azure Files と Azure File Sync のスケールおよびパフォーマンス ターゲットの詳細については、「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](../files/storage-files-scale-targets.md)」を参照してください。

> [!IMPORTANT]
> ストレージ アカウントの制限はすべての共有に適用されます。 ストレージ アカウントを最大までスケールアップすることは、ストレージ アカウントごとの共有が 1 つだけの場合にのみ達成できます。
>
> 5 TiB を超える Standard ファイル共有はプレビュー段階であり、いくつかの制限事項があります。
> 制限事項の一覧と、これらのより大きなファイル共有サイズのプレビューへのオンボードについては、Azure Files 計画ガイドの「[Standard ファイル共有](../files/storage-files-planning.md#standard-file-shares)」セクションを参照してください。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Premium ファイルのスケール ターゲット

Premium ファイルについては、ストレージ アカウント、共有、ファイルという 3 つの制限カテゴリについて考慮する必要があります。

例: 1 つの共有で 100,000 IOPS を達成できます。1 つのファイルで最大 5,000 IOPS までスケールアップできます。 そのため、たとえば、1 つの共有に 3 つのファイルを置くと、その共有から得られる最大 IOPS は 15,000 となります。

#### <a name="premium-file-share-limits"></a>Premium ファイル共有の制限

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync のスケール ターゲット

Azure File Sync は使用量無制限を目標に設計されていますが、使用量無制限が常に可能なわけではありません。 次の表では、Microsoft のテストの境界と、どのターゲットがハード制限かを示します。

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure Queue Storage のスケール ターゲット

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure Table Storage のスケール ターゲット

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>関連項目

- [Storage の料金詳細](https://azure.microsoft.com/pricing/details/storage/)
- [Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-subscription-service-limits.md)
- [Azure Storage のレプリケーション](../storage-redundancy.md)
- [Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../storage-performance-checklist.md)
