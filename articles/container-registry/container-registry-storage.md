---
title: コンテナー イメージの保存
description: Azure Container Registry にコンテナー イメージやその他の成果物が保存される方法について、セキュリティ、冗長性、および容量も含めて詳細に説明します。
ms.topic: article
ms.date: 03/03/2021
ms.custom: references_regions
ms.openlocfilehash: ec4328b44d5493b8d765fa30c548adc3d747d446
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183269"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure Container Registry へのコンテナー イメージの保存

すべての Azure Container Registry ([Basic、Standard、および Premium](container-registry-skus.md)) は、保存時の暗号化などの Azure の高度なストレージ機能の恩恵を受けています。 次のセクションで、Azure Container Registry (ACR) におけるイメージの保存に関する機能と制限について説明します。

## <a name="encryption-at-rest"></a>保存時の暗号化

レジストリ内のすべてのコンテナー イメージとその他の成果物は、保存時に暗号化されます。 Azure では、イメージは保存前に自動的に暗号化され、アプリケーションやサービスがイメージをプルするときにその場で暗号解除されます。 必要に応じて、[カスタマー マネージド キー](container-registry-customer-managed-keys.md)を使用して追加の暗号化レイヤーを適用します。

## <a name="regional-storage"></a>リージョンでの保存

Azure Container Registry は、データの保存場所とコンプライアンス要件を満たすために、レジストリが作成されるリージョンにデータを格納します。

データセンターの障害から保護するため、一部のリージョンは、特定のリージョンにある複数のデータセンターにデータがレプリケートされる、[ゾーン冗長](zone-redundancy.md)を備えています。

パフォーマンス向上のために別々の地域の複数のリージョンにデータを保存したい場合、または特定の地域で障害が発生した場合に備えて回復性を確保したい場合は、[geo レプリケーション](container-registry-geo-replication.md)を有効にする必要があります。

## <a name="geo-replication"></a>geo レプリケーション

高可用性の保証を必要とするシナリオでは、Premium レジストリの機能である [geo レプリケーション](container-registry-geo-replication.md)の使用を検討してください。 geo レプリケーションは、特定の地域で障害が発生した場合に、レジストリにアクセスできなくなるのを防ぐために役立ちます。 geo レプリケーションには、他にも、分散型の開発またはデプロイ シナリオで、プッシュとプルを高速で実行するためのネットワークに近いイメージの保存などのメリットがあります。

## <a name="zone-redundancy"></a>ゾーン冗長性

回復性と高可用性を備えた Azure Container Registry を作成するには、必要に応じて、選択した Azure リージョンで[ゾーン冗長](zone-redundancy.md)を有効にします。 Premium サービス レベルの機能であるゾーン冗長は、Azure [Availability Zones](../availability-zones/az-overview.md) を使用して、有効になっているリージョンごとに 3 つ以上の異なるゾーンにレジストリをレプリケートします。 geo レプリケーションとゾーン冗長を組み合わせることにより、レジストリの信頼性とパフォーマンスを向上できます。 

## <a name="scalable-storage"></a>スケーラブルなストレージ

Azure Container Registry では、[レジストリのストレージの制限](container-registry-skus.md#service-tier-features-and-limits)まで、必要な数のリポジトリ、イメージ、レイヤー、またはタグを作成できます。 

膨大な数のリポジトリとタグは、レジストリのパフォーマンスに影響する可能性があります。 レジストリのメンテナンス ルーチンの一部として、使用されていないリポジトリ、タグ、およびイメージを定期的に削除し、必要に応じて、タグが付けられていないマニフェストの[保持ポリシー](container-registry-retention-policy.md)を設定します。 リポジトリ、イメージ、タグなど、削除されたレジストリ リソースを、削除後に復旧することは *できません*。 レジストリのリソースを削除する方法については、「[Azure Container Registry 内のコンテナー イメージを削除する](container-registry-delete.md)」を参照してください。

## <a name="storage-cost"></a>Storage コスト

価格の詳細については、「[Azure Container Registry の価格][pricing]」を参照してください。

## <a name="next-steps"></a>次のステップ

Basic、Standard、Premium コンテナー レジストリの詳細については、[Azure Container Registry のサービス レベル](container-registry-skus.md)に関するページを参照してください。

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
