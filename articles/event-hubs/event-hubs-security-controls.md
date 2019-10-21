---
title: Azure Event Hubs のセキュリティ コントロール
description: Azure Event Hubs を評価するためのセキュリティ コントロールのチェックリスト
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 277a745d26961ed509258d5423fc3c0da9b79a24
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219396"
---
# <a name="security-controls-for-azure-event-hubs"></a>Azure Event Hubs のセキュリティ コントロール

この記事では、Azure Event Hubs に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ | ドキュメント |
|---|---|--|--|
| サービス エンドポイントのサポート| はい |  |  |
| VNet インジェクションのサポート| いいえ | |  |
| ネットワークの分離とファイアウォールのサポート| はい |  |  |
| 強制トンネリングのサポート| いいえ |  |  |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | メモ| ドキュメント |
|---|---|--|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | |  |
| コントロールと管理プレーンのログ記録と監査| はい |  |  |
| データ プレーンのログ記録と監査| はい |   |  |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ| ドキュメント |
|---|---|--|--|
| 認証| はい | | [Azure Event Hubs へのアクセスを承認する](authorize-access-event-hubs.md)、[Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-azure-active-directory.md)、[Shared Access Signature を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-shared-access-signature.md) |
| Authorization|  はい | | [Azure Active Directory を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する](authenticate-managed-identity.md)、[Event Hubs リソースにアクセスするために Azure Active Directory でアプリケーションを認証する](authenticate-application.md)、[Shared Access Signature (SAS) を使用して Event Hubs リソースへのアクセスを認証する](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ | ドキュメント |
|---|---|--|--|
| 保存時のサーバー側の暗号化:Microsoft のマネージド キー |  はい | |  |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | いいえ |  |  |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |  |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | |  |
| API 呼び出しの暗号化| はい |  |  |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ| ドキュメント |
|---|---|--|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | |  |

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。
