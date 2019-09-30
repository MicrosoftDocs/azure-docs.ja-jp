---
title: Azure Storage のセキュリティ コントロール
description: Azure Storage を評価するためのセキュリティ コントロールのチェックリスト
services: storage
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 8cb14c19e8816d53c7d9385563f916bee5d4a6af
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886205"
---
# <a name="security-controls-for-azure-storage"></a>Azure Storage のセキュリティ コントロール

この記事では、Azure Storage に組み込まれているセキュリティ コントロールについて説明します。 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| 保存時のサーバー側の暗号化:Microsoft のマネージド キー | はい |  |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | はい | 「[Azure Key Vault で顧客が管理するキーを Storage Service Encryption に使用する](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | 標準の HTTPS/TLS 機構をサポートします。  サービスに転送するデータは、ユーザーが事前に暗号化することもできます。 |
| API 呼び出しの暗号化| はい |  |

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| 該当なし |  |
| ネットワークの分離とファイアウォールのサポート| はい | |
| 強制トンネリングのサポート| 該当なし |  |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Azure Monitor のメトリックが利用できるようになり、ログのプレビューが開始されました |
| コントロールと管理プレーンのログ記録と監査 | はい | Azure Resource Manager アクティビティ ログ |
| データ プレーンのログ記録と監査| はい | サービスの診断ログおよび Azure Monitor のログのプレビューが開始されました  |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 認証| はい | Azure Active Directory、共有キー、共有アクセス トークン。 |
| Authorization| はい | RBAC、POSIX ACL、SAS トークンを使用した承認をサポートします |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | Azure Resource Manager API によりリソース プロバイダーのバージョン管理をサポートします |

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../../security/fundamentals/security-controls.md)について説明します。