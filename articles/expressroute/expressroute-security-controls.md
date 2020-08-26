---
title: 'Azure ExpressRoute: セキュリティ コントロール'
description: セキュリティの脆弱性を防止、検出、対応するのに役立つ性質または機能である、Azure ExpressRoute のセキュリティ コントロールについて説明します。
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6a4589d1aa768548f2ae9ffa01f289c823c0b2e5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192420"
---
# <a name="security-controls-for-azure-expressroute"></a>Azure ExpressRoute のセキュリティ コントロール

この記事では、Azure ExpressRoute に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | Notes |
|---|---|--|
| サービス エンドポイントのサポート| 該当なし |  |
| VNet インジェクションのサポート| 該当なし | |
| ネットワークの分離とファイアウォールのサポート| はい | 各顧客は独自のルーティング ドメインに含まれ、独自の VNet にトンネリングされます |
| 強制トンネリングのサポート| 該当なし | Border Gateway Protocol (BGP) を使用。 |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | 「[ExpressRoute の監視、メトリック、およびアラート](expressroute-monitoring-metrics-alerts.md)」を参照してください。|
| コントロールと管理プレーンのログ記録と監査| はい |  |
| データ プレーンのログ記録と監査| いいえ |   |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 認証| はい | Gateway for Microsoft (GWM) (コントローラー) 用のサービス アカウント。開発と運用のための Just-In-Time (JIT) アクセス。 |
| 承認|  はい |Gateway for Microsoft (GWM) (コントローラー) 用のサービス アカウント。開発と運用のための Just-In-Time (JIT) アクセス。 |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | Notes |
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー |  該当なし | ExpressRoute は顧客データを格納しません。 |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | 該当なし |  |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| いいえ | |
| API 呼び出しの暗号化| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) と HTTPS を使用します。 |


## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | ネットワーク リソース プロバイダー (NRP) を使用。 |

## <a name="next-steps"></a>次のステップ

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。