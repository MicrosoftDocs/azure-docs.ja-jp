---
title: セキュリティ コントロール
description: Azure SQL Database を評価するためのセキュリティ コントロールのチェックリスト
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 97d2cd8e9ba51e4fc6ebab8459b04f4f37e876d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668425"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database と SQL Managed Instance のセキュリティ コントロール
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

この記事では、Azure SQL Database と Azure SQL Managed Instance に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | Notes |
|---|---|--|
| サービス エンドポイントのサポート| はい | 適用対象: [SQL Database](../index.yml) のみ。 |
| Azure Virtual Network の挿入のサポート| はい | [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) のみに適用。 |
| ネットワークの分離とファイアウォールのサポート| はい | データベースとサーバーの両方のレベルでのファイアウォール。 ネットワークの分離は、[SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) のみです。 |
| 強制トンネリングのサポート| はい | [ExpressRoute](../expressroute/../index.yml) VPN による [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)。 |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| Azure Monitoring のサポート (Log Analytics や Application Insights など)| はい | [SQL 監査](../../azure-sql/database/auditing-overview.md)を使用した [Azure Event Hubs](../event-hubs/../index.yml) の統合を介して、Imperva の SecureSphere による SIEM ソリューションもサポートされます。 |
| コントロールプレーンと管理プレーンのログ記録および監査| はい | 一部のイベントについてのみ、はい |
| データ プレーンのログ記録と監査 | はい | [SQL 監査](../../azure-sql/database/auditing-overview.md)による |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 認証| はい | Azure Active Directory (Azure AD) |
| 承認| はい | なし |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | Notes |
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | はい | [Always Encrypted](always-encrypted-certificate-store-configure.md) に関する記事で説明されているように、"使用時の暗号化" と呼ばれます。 サーバー側暗号化では、[Transparent Data Encryption](transparent-data-encryption-tde-overview.md) が使われます。|
| 転送中の暗号化:<ul><li>Azure ExpressRoute の暗号化</li><li>仮想ネットワークでの暗号化</li><li>仮想ネットワーク間の暗号化</ul>| はい | HTTPS を使用します。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | サービス マネージド キーとカスタマー マネージド キーの処理がどちらも提供されます。 後者は [Azure Key Vault](../key-vault/../index.yml) を通じて提供されます。 |
| Azure のデータ サービスによって提供される列レベルの暗号化| はい | [Always Encrypted](always-encrypted-certificate-store-configure.md) により。 |
| 暗号化された API 呼び出し| はい | HTTPS/TLS を使用します。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| いいえ  | なし |

## <a name="additional-security-controls-for-sql-database"></a>SQL Database の追加のセキュリティ コントロール

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 予防: 脆弱性評価 | はい | 「[SQL の脆弱性評価サービスは、データベースの脆弱性を特定するのに役に立ちます](sql-vulnerability-assessment.md)」をご覧ください。 |
| 予防: データの検出と分類  | はい | 「[Azure SQL Database および SQL Data Warehouse のデータの検出と分類](data-discovery-and-classification-overview.md)」をご覧ください。 |
| 検出: 脅威の検出 | はい | 「[Azure SQL Database の Advanced Threat Protection](threat-detection-overview.md)」をご覧ください。 |

## <a name="next-steps"></a>次のステップ

- [Azure サービス全体の組み込みセキュリティ コントロール](../../security/fundamentals/security-controls.md)について学習します。
