---
title: Azure Relay のセキュリティ コントロール
description: この記事は、Azure Relay を評価するための組み込みのセキュリティ コントロールのチェックリストを提供します。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 64abee031bb20e2bdb10bf1cc3cd77e135713550
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85316619"
---
# <a name="security-controls-for-azure-relay"></a>Azure Relay のセキュリティ コントロール

この記事では、Azure Relay に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント |
|---|---|--|--|
| サービス エンドポイントのサポート| いいえ |  |   |
| ネットワークの分離とファイアウォールのサポート| いいえ |  |   |
| 強制トンネリングのサポート| 該当なし | Relay は TLS トンネルです  |   |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | Notes| ドキュメント |
|---|---|--|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | |   |
| コントロールと管理プレーンのログ記録と監査| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) を使用。 |   |
| データ プレーンのログ記録と監査| はい | 接続の成功/失敗およびエラーはログに記録されます。  |   |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | Notes| ドキュメント |
|---|---|--|--|
| 認証| はい | SAS を使用。 | [Azure Relay の認証と承認](relay-authentication-and-authorization.md) |
| 承認|  はい | SAS を使用。 | [Azure Relay の認証と承認](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント |
|---|---|--|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー |  該当なし | Relay は Web ソケットであり、データは保持されません。 |   |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | いいえ | Microsoft の TLS 証明書のみを使用します。  |   |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |   |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | Service には TLS が必要です。 |   |
| API 呼び出しの暗号化| はい | HTTPS。 |


## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | Notes| ドキュメント |
|---|---|--|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) を使用。|   |

## <a name="next-steps"></a>次のステップ

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。