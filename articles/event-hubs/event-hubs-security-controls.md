---
title: Azure Event Hubs のセキュリティ コントロール
description: この記事では、Azure Event Hubs を評価するためのセキュリティ コントロールのチェックリスト (ネットワーク、ID、データ保護など) を提供します。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: da20778f1e24372e445d635e675df6484905f195
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85315399"
---
# <a name="security-controls-for-azure-event-hubs"></a>Azure Event Hubs のセキュリティ コントロール

この記事では、Azure Event Hubs に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント |
|---|---|--|--|
| サービス エンドポイントのサポート| はい |  |  |
| VNet インジェクションのサポート| いいえ | |  |
| ネットワークの分離とファイアウォールのサポート| はい |  |  |
| 強制トンネリングのサポート| いいえ |  |  |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | Notes| ドキュメント |
|---|---|--|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | |  |
| コントロールと管理プレーンのログ記録と監査| はい |  |  |
| データ プレーンのログ記録と監査| はい |   |  |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | Notes| ドキュメント |
|---|---|--|--|
| 認証| はい | | [Azure Event Hubs へのアクセスを承認する](authorize-access-event-hubs.md)、[Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-azure-active-directory.md)、[Shared Access Signature を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-shared-access-signature.md) |
| 承認|  はい | | [Azure Active Directory を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する](authenticate-managed-identity.md)、[Event Hubs リソースにアクセスするために Azure Active Directory でアプリケーションを認証する](authenticate-application.md)、[Shared Access Signature (SAS) を使用して Event Hubs リソースへのアクセスを認証する](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント |
|---|---|--|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー |  はい | |  |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | はい。 専用クラスターとして使用可能。 | Azure KeyVault 内のカスタマー マネージド キーを使用して、保存されているイベント ハブのデータを暗号化できます。 | [Azure portal を使用して Azure Event Hubs 保存データの暗号化用にカスタマー マネージド キーを構成する](configure-customer-managed-key.md) |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |  |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | |  |
| API 呼び出しの暗号化| はい |  |  |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | Notes| ドキュメント |
|---|---|--|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | |  |

## <a name="next-steps"></a>次のステップ

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。
