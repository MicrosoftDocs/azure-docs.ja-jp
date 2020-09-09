---
title: セキュリティ コントロール
description: Azure Resource Manager サービスを評価するための組み込みセキュリティ コントロールのチェックリスト。
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bb8742c38fae88dc1fd1fd1ec175b248f30df3a0
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054459"
---
# <a name="security-controls-for-azure-resource-manager"></a>Azure Resource Manager のセキュリティ コントロール

この記事では、Azure Resource Manager に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | Notes |
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | はい |  |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | HTTPS/TLS。 |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | 該当なし | Azure Resource Manager では、制御データのみが格納され、お客様のコンテンツは格納されません。 |
| 列レベルの暗号化 (Azure Data Services)| はい | |
| API 呼び出しの暗号化| はい | |

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | Notes |
|---|---|--|
| サービス エンドポイントのサポート| いいえ | |
| VNet インジェクションのサポート| はい | |
| ネットワークの分離とファイアウォールのサポート| いいえ |  |
| 強制トンネリングのサポート| いいえ |  |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| いいえ | |
| コントロールと管理プレーンのログ記録と監査| はい | アクティビティ ログでは、リソースに対して実行された書き込み操作 (PUT、POST、DELETE) がすべて公開されます。「[リソースのアクションを監査するアクティビティ ログの表示](view-activity-logs.md)」を参照してください。 |
| データ プレーンのログ記録と監査| 該当なし | |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 認証| はい | [Azure Active Directory](../../active-directory/index.yml) ベース。|
| 承認| はい | |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい |  |

## <a name="next-steps"></a>次のステップ

- [Azure サービス全体の組み込みセキュリティ コントロール](../../security/fundamentals/security-controls.md)について学習します。
