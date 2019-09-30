---
title: Azure Service Bus Messaging のセキュリティ コントロール
description: Azure Service Bus Messaging を評価するためのセキュリティ コントロールのチェックリスト
services: service-bus-messaging
ms.service: service-bus-messaging
author: axisc
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: ac931ac7cf6c8609019b120388e1b6ca836f72b8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886220"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Azure Service Bus Messaging のセキュリティ コントロール

この記事では、Azure Service Bus Messaging に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい (Premium レベルのみ) | VNet サービス エンドポイントは、[Service Bus Premium レベル](service-bus-premium-messaging.md)でのみサポートされます。 |
| VNet インジェクションのサポート| いいえ | |
| ネットワークの分離とファイアウォールのサポート| はい (Premium レベルのみ) |  |
| 強制トンネリングのサポート| いいえ |  |

## <a name="monitoring--logging"></a>監視とログ記録

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | [Azure Monitor とアラート](service-bus-metrics-azure-monitor.md)によってサポートされます。 |
| コントロールと管理プレーンのログ記録と監査| はい | 操作ログを使用できます (「[Service Bus 診断ログ](service-bus-diagnostic-logs.md)」を参照)。  |
| データ プレーンのログ記録と監査| いいえ |  |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 認証| はい | [Azure Active Directory マネージド サービス ID](service-bus-managed-service-identity.md)を使用して管理されます (「[Service Bus の認証と承認](service-bus-authentication-and-authorization.md)」を参照)。|
| Authorization| はい | [RBAC](authenticate-application.md) と SAS トークンによる承認がサポートされます (「[Service Bus の認証と承認](service-bus-authentication-and-authorization.md)」を参照)。 |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー |  はい (サーバー側の保存時の暗号化では既定で)。 | 顧客管理キーと BYOK はまだサポートされていません。 クライアント側の暗号化はクライアントの責任です |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | いいえ |   |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | 標準の HTTPS/TLS メカニズムがサポートされます。 |
| API 呼び出しの暗号化| はい | API 呼び出しは、[Azure Resource Manager](../azure-resource-manager/index.yml) と HTTPS を使用して行われます。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure Resource Manager API](/rest/api/resources/) によるリソース プロバイダーのバージョン管理がサポートされます。|

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。