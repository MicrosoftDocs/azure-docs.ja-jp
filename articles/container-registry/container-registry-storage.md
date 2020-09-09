---
title: コンテナー イメージの保存
description: Azure Container Registry に Docker コンテナー イメージが保存される方法について、セキュリティ、冗長性、および容量も含めて説明します。
ms.topic: article
ms.date: 06/18/2020
ms.openlocfilehash: d51014e9e0769091aba42682cce3a6a01cfa19de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214062"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure Container Registry へのコンテナー イメージの保存

すべての Azure Container Registry ([Basic、Standard、および Premium](container-registry-skus.md)) は、イメージ データのセキュリティ保護のための保存時の暗号化やイメージ データの保護のための geo 冗長などの Azure の高度なストレージ機能の恩恵を受けています。 次のセクションで、Azure Container Registry (ACR) におけるイメージの保存に関する機能と制限の両方について説明します。

## <a name="encryption-at-rest"></a>保存時の暗号化

レジストリ内のすべてのコンテナー イメージは、保存時に暗号化されます。 Azure では、イメージは保存前に自動的に暗号化され、アプリケーションやサービスがイメージをプルするときにその場で暗号解除されます。 必要に応じて、[カスタマー マネージド キー](container-registry-customer-managed-keys.md)を使用して追加の暗号化レイヤーを適用します。

## <a name="geo-redundant-storage"></a>geo 冗長ストレージ

Azure では、geo 冗長ストレージ スキームを使用して、コンテナー イメージの損失を防ぎます。 Azure Container Registry は、コンテナー イメージを複数の地理的に離れたデータ センターに自動的にレプリケートして、地域的なストレージ障害が発生した場合の損失を防ぎます。

## <a name="geo-replication"></a>geo レプリケーション

より以上の高可用性の保証を必要とするシナリオでは、Premium レジストリの機能である [geo レプリケーション](container-registry-geo-replication.md)の使用を検討します。 geo レプリケーションは、ストレージ障害だけでなく、*地域全体*で障害が発生した場合に、レジストリにアクセスできなくなるのを防ぐために役立ちます。 geo レプリケーションには、他にも、分散型の開発またはデプロイ シナリオで、プッシュとプルを高速で実行するためのネットワークに近いイメージの保存などのメリットがあります。

## <a name="scalable-storage"></a>スケーラブルなストレージ

Azure Container Registry では、[レジストリのストレージの制限](container-registry-skus.md#service-tier-features-and-limits)まで、必要な数のリポジトリ、イメージ、レイヤー、またはタグを作成できます。 

膨大な数のリポジトリとタグは、レジストリのパフォーマンスに影響する可能性があります。 レジストリのメンテナンス ルーチンの一部として、使用されていないリポジトリ、タグ、およびイメージを定期的に削除し、必要に応じて、タグが付けられていないマニフェストの[保持ポリシー](container-registry-retention-policy.md)を設定します。 リポジトリ、イメージ、タグなど、削除されたレジストリ リソースを、削除後に復旧することは*できません*。 レジストリのリソースを削除する方法については、「[Azure Container Registry 内のコンテナー イメージを削除する](container-registry-delete.md)」を参照してください。

## <a name="storage-cost"></a>Storage コスト

価格の詳細については、「[Azure Container Registry の価格][pricing]」を参照してください。

## <a name="next-steps"></a>次のステップ

Basic、Standard、Premium コンテナー レジストリの詳細については、[Azure Container Registry のサービス レベル](container-registry-skus.md)に関するページを参照してください。

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
