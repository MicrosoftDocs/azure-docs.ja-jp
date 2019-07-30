---
title: Azure VPN Gateway のセキュリティ属性
description: Azure VPN Gateway を評価するためのセキュリティ属性のチェックリスト
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e58e7b3f77d9bb673e300aa60ad07ca9dba5153
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082168"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Azure VPN Gateway のセキュリティ属性

この記事では、Azure VPN Gateway に組み込まれている一般的なセキュリティ属性について説明します。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | 該当なし | VPN ゲートウェイは顧客データを格納するのではなく、顧客データを転送します。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | VPN ゲートウェイは、Azure VPN ゲートウェイと顧客のオンプレミスの VPN デバイス (S2S) または VPN クライアント (P2S) の間の顧客パケットを暗号化します。 VPN ゲートウェイはまた、VNet 間の暗号化もサポートします。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ | 顧客が指定した仮共有キーは保存時に暗号化されますが、まだ CMK には統合されていません。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) と HTTPS を使用します。  |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| 該当なし | |
| VNet インジェクションのサポート| 該当なし | 。 |
| ネットワークの分離とファイアウォールのサポート| はい | VPN ゲートウェイは、顧客の仮想ネットワークごとの専用の VM インスタンスです。  |
| 強制トンネリングのサポート| はい |  |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | [Azure Monitor の診断ログ/アラート](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor のメトリック/アラート](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)に関するページを参照してください。  |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | サービスを管理し、Azure VPN ゲートウェイを構成するための [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)。 |
| Authorization| はい | [RBAC](../role-based-access-control/overview.md) 経由で承認をサポートします。 |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | Azure Resource Manager アクティビティ ログ。 |
| データ プレーンのログ記録と監査 | はい | VPN 接続のログ記録と監査のための [Azure Monitor の診断ログ](../azure-resource-manager/resource-group-audit.md)。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | 管理操作では、Azure VPN ゲートウェイ構成の状態を Azure Resource Manager テンプレートとしてエクスポートしたり、一定期間にわたってバージョン管理したりできます。 | 