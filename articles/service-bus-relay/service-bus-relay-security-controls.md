---
title: Azure Service Bus Relay のセキュリティ コントロール
description: Azure Service Bus Relay を評価するためのセキュリティ コントロールのチェックリスト
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 7f4e2a31673905a7e28d1dbb5520650aefc6f368
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219990"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Azure Service Bus Relay のセキュリティ コントロール

この記事では、Azure Service Bus Relay に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ | ドキュメント |
|---|---|--|--|
| サービス エンドポイントのサポート| いいえ |  |   |
| ネットワークの分離とファイアウォールのサポート| いいえ |  |   |
| 強制トンネリングのサポート| 該当なし | Relay は TLS トンネルです  |   |

## <a name="monitoring--logging"></a>監視とログ記録

| セキュリティ コントロール | はい/いいえ | メモ| ドキュメント |
|---|---|--|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | |   |
| コントロールと管理プレーンのログ記録と監査| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) を使用。 |   |
| データ プレーンのログ記録と監査| はい | 接続の成功/失敗およびエラーはログに記録されます。  |   |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ| ドキュメント |
|---|---|--|--|
| 認証| はい | SAS を使用。 | [Azure Relay の認証と承認](relay-authentication-and-authorization.md) |
| Authorization|  はい | SAS を使用。 | [Azure Relay の認証と承認](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ | ドキュメント |
|---|---|--|--|
| 保存時のサーバー側の暗号化:Microsoft のマネージド キー |  該当なし | Relay は Web ソケットであり、データは保持されません。 |   |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | いいえ | Microsoft の TLS 証明書のみを使用します。  |   |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |   |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | Service には TLS が必要です。 |   |
| API 呼び出しの暗号化| はい | HTTPS。 |


## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ| ドキュメント |
|---|---|--|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) を使用。|   |

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。