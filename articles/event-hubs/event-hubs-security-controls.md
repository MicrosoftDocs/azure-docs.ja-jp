---
title: Azure Event Hubs のセキュリティ コントロール
description: Azure Event Hubs を評価するためのセキュリティ コントロールのチェックリスト
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 3f02315012840b20dc4aa8639ade954f23a5526e
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886329"
---
# <a name="security-controls-for-azure-event-hubs"></a>Azure Event Hubs のセキュリティ コントロール

この記事では、Azure Event Hubs に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| いいえ | |
| ネットワークの分離とファイアウォールのサポート| はい |  |
| 強制トンネリングのサポート| いいえ |  |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | |
| コントロールと管理プレーンのログ記録と監査| はい |  |
| データ プレーンのログ記録と監査| はい |   |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 認証| はい | |
| Authorization|  はい | |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| 保存時のサーバー側の暗号化:Microsoft のマネージド キー |  はい | |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | いいえ |  |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | |
| API 呼び出しの暗号化| はい |  |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | |

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について説明します。
