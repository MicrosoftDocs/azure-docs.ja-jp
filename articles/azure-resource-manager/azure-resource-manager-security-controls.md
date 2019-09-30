---
title: Azure Resource Manager のセキュリティ コントロール
description: Azure Resource Manager を評価するためのセキュリティ コントロールのチェックリスト
services: azure-resource-manager
author: msmbaldwin
manager: rkarlin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 97fd6611d7e2a2787b865365c4c7579f89f17d01
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886303"
---
# <a name="security-controls-for-azure-resource-manager"></a>Azure Resource Manager のセキュリティ コントロール

この記事では、Azure Resource Manager に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| 保存時のサーバー側の暗号化:Microsoft のマネージド キー | はい |  |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | HTTPS/TLS。 |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | 該当なし | Azure Resource Manager では、制御データのみが格納され、お客様のコンテンツは格納されません。 |
| 列レベルの暗号化 (Azure Data Services)| はい | |
| API 呼び出しの暗号化| はい | |

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ | |
| VNet インジェクションのサポート| はい | |
| ネットワークの分離とファイアウォールのサポート| いいえ |  |
| 強制トンネリングのサポート| いいえ |  |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| いいえ | |
| コントロールと管理プレーンのログ記録と監査| はい | アクティビティ ログでは、リソースに対して実行された書き込み操作 (PUT、POST、DELETE) がすべて公開されます。「[リソースのアクションを監査するアクティビティ ログの表示](resource-group-audit.md)」を参照してください。 |
| データ プレーンのログ記録と監査| 該当なし | |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 認証| はい | [Azure Active Directory](/azure/active-directory) ベース。|
| Authorization| はい | |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい |  |

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について説明します。