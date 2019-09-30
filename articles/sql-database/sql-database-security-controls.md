---
title: Azure SQL Database のセキュリティ コントロール
description: Azure SQL Database を評価するためのセキュリティ コントロールのチェックリスト
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 77ff55389bac53d8719d86b4ac77f281415af49f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886229"
---
# <a name="security-controls-for-azure-sql-database"></a>Azure SQL Database のセキュリティ コントロール

この記事では、Azure SQL Database に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL Database には、[単一データベース](sql-database-single-index.yml)と[マネージド インスタンス](sql-database-managed-instance.md)の両方が含まれます。 次のエントリは、明記されている場合を除き、両方のオファリングに適用されます。

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | [単一データベース](sql-database-single-index.yml)のみに適用。 |
| Azure Virtual Network の挿入のサポート| はい | [マネージド インスタンス](sql-database-managed-instance.md)のみに適用。 |
| ネットワークの分離とファイアウォールのサポート| はい | データベースとサーバーの両方のレベルでのファイアウォール。 ネットワークの分離は、[マネージド インスタンス](sql-database-managed-instance.md)のみです。 |
| 強制トンネリングのサポート| はい | [ExpressRoute](../expressroute/index.yml) VPN による[マネージド インスタンス](sql-database-managed-instance.md)。 |

## <a name="monitoring--logging"></a>監視とログ記録

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| Azure Monitoring のサポート (Log Analytics や Application Insights など)| はい | [SQL 監査](sql-database-auditing.md)を使用した [Azure Event Hubs](../event-hubs/index.yml) の統合を介して、Imperva の SecureSphere による SIEM ソリューションもサポートされます。 |
| コントロールプレーンと管理プレーンのログ記録および監査| はい | 一部のイベントについてのみ、はい |
| データ プレーンのログ記録と監査 | はい | [SQL 監査](sql-database-auditing.md)による |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 認証| はい | Azure Active Directory (Azure AD) |
| Authorization| はい | なし |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | はい | [Always Encrypted](sql-database-always-encrypted.md) に関する記事で説明されているように、"使用時の暗号化" と呼ばれます。 サーバー側暗号化では、[Transparent Data Encryption](transparent-data-encryption-azure-sql.md) が使われます。|
| 転送中の暗号化:<ul><li>Azure ExpressRoute の暗号化</li><li>仮想ネットワークでの暗号化</li><li>仮想ネットワーク間の暗号化</ul>| はい | HTTPS を使用します。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | サービス マネージド キーとカスタマー マネージド キーの処理がどちらも提供されます。 後者は [Azure Key Vault](../key-vault/index.yml) を通じて提供されます。 |
| Azure のデータ サービスによって提供される列レベルの暗号化| はい | [Always Encrypted](sql-database-always-encrypted.md) により。 |
| 暗号化された API 呼び出し| はい | HTTPS/SSL を使用。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| いいえ  | なし |

## <a name="additional-security-controls-for-sql-database"></a>SQL Database の追加のセキュリティ コントロール

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 予防: 脆弱性評価 | はい | 「[SQL の脆弱性評価サービスは、データベースの脆弱性を特定するのに役に立ちます](sql-vulnerability-assessment.md)」をご覧ください。 |
| 予防: データの検出と分類  | はい | 「[Azure SQL Database および SQL Data Warehouse のデータの検出と分類](sql-database-data-discovery-and-classification.md)」をご覧ください。 |
| 検出: 脅威の検出 | はい | 「[Azure SQL Database の Advanced Threat Protection](sql-database-threat-detection-overview.md)」をご覧ください。 |

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。
