---
title: Azure ExpressRoute のセキュリティ コントロール
description: Azure ExpressRoute を評価するためのセキュリティ コントロールのチェックリスト
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ab73ba6dd4b78d3cd0be5f4c7f7a502e5c58e08
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886318"
---
# <a name="security-controls-for-azure-expressroute"></a>Azure ExpressRoute のセキュリティ コントロール

この記事では、Azure ExpressRoute に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| 該当なし |  |
| VNet インジェクションのサポート| 該当なし | |
| ネットワークの分離とファイアウォールのサポート| はい | 各顧客は独自のルーティング ドメインに含まれ、独自の VNet にトンネリングされます |
| 強制トンネリングのサポート| 該当なし | Border Gateway Protocol (BGP) を使用。 |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | 「[ExpressRoute の監視、メトリック、およびアラート](expressroute-monitoring-metrics-alerts.md)」を参照してください。|
| コントロールと管理プレーンのログ記録と監査| はい |  |
| データ プレーンのログ記録と監査| いいえ |   |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 認証| はい | Gateway for Microsoft (GWM) (コントローラー) 用のサービス アカウント。開発と運用のための Just-In-Time (JIT) アクセス。 |
| Authorization|  はい |Gateway for Microsoft (GWM) (コントローラー) 用のサービス アカウント。開発と運用のための Just-In-Time (JIT) アクセス。 |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| 保存時のサーバー側の暗号化:Microsoft のマネージド キー |  該当なし | ExpressRoute は顧客データを格納しません。 |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | 該当なし |  |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| いいえ | |
| API 呼び出しの暗号化| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) と HTTPS を使用します。 |


## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | ネットワーク リソース プロバイダー (NRP) を使用。 |

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について説明します。