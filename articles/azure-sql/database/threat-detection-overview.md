---
title: 高度な脅威保護
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: Advanced Threat Protection では、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics での潜在的なセキュリティ脅威を示す異常なデータベース アクティビティが検出されます。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, sstein
ms.date: 12/01/2020
tags: azure-synapse
ms.openlocfilehash: 931e914cd3c184136395a9bb9a7e148a90e9fb91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461938"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Azure SQL Database、SQL Managed Instance、Azure Synapse Analytics のための Advanced Threat Protection
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md)、[Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)、[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) の Advanced Threat Protection では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティが検出されます。

Advanced Threat Protection は、高度な SQL セキュリティ機能の統合パッケージである [Azure Defender for SQL](azure-defender-for-sql.md) オファリングの一部です。 Advanced Threat Protection は、Azure Defender for SQL ポータルを使用して一元的にアクセスおよび管理できます。

## <a name="overview"></a>概要

Advanced Threat Protection で提供される新しいセキュリティ階層では、異常なアクティビティに対するセキュリティ アラートが提供されるので、お客様は潜在的な脅威が発生したときにそれを検出して対応できます。 不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセスやクエリのパターンが見つかった場合に、ユーザーはアラートを受信します。 Advanced Threat Protection では、アラートと [Azure Security Center](https://azure.microsoft.com/services/security-center/) が統合されます。これには、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法が含まれます。 Advanced Threat Protection を使用すると、データベースに対する潜在的な脅威に簡単に対処でき、セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要はありません。

完全な調査エクスペリエンスを実現するために、監査を有効にし、データベース イベントを Azure ストレージ アカウントの監査ログに書き込むことをお勧めします。  監査を有効にするには、[Azure SQL Database と Azure Synapse の監査](../../azure-sql/database/auditing-overview.md)、または [Azure SQL Managed Instance の監査](../managed-instance/auditing-configure.md)に関するページを参照してください。

## <a name="alerts"></a>警告

Advanced Threat Protection for Azure SQL Database では、データベースへのアクセスや悪用を試みる、通常とは異なる、害を及ぼす可能性がある異常なアクティビティが検出されます。 Azure SQL Database のアラートの一覧については、[Azure Security Center の SQL Database および Azure Synapse Analytics のアラート](../../security-center/alerts-reference.md#alerts-sql-db-and-warehouse)に関するセクションを参照してください。

## <a name="explore-detection-of-a-suspicious-event"></a>疑わしいイベントの検出を試す

異常なデータベース アクティビティが検出されると、電子メールで通知を受け取ります。 電子メールでは、異常なアクティビティの特徴、データベース名、サーバー名、アプリケーション名、イベントの時刻など、疑わしいセキュリティ イベントについての情報が提供されます。 さらに、データベースへの潜在的な脅威の考えられる原因と調査や緩和のための推奨されるアクションについての情報も提供されます。

![異常アクティビティ レポート](./media/threat-detection-overview/anomalous_activity_report.png)

1. 電子メールの **[View recent SQL alerts]\(最近の SQL アラートの表示\)** リンクをクリックして Azure Portal を起動し、Azure Security Center のアラート ページを表示します。このページには、データベースで検出されたアクティブな脅威の概要が示されます。

   ![アクティビティの脅威](./media/threat-detection-overview/active_threats.png)

1. 特定のアラートをクリックすると、さらに詳細な情報と、この脅威を調査し、今後の脅威に対処するためのアクションが表示されます。

   たとえば、SQL インジェクションはインターネットにおける Web アプリケーションの最も一般的なセキュリティ問題の 1 つであり、データ駆動型アプリケーションの攻撃に使用されます。 攻撃者は、アプリケーションの脆弱性を利用してアプリケーションの入力フィールドに悪意のある SQL ステートメントを挿入し、データベースのデータを侵害または変更します。 SQL インジェクションのアラートの場合、アラートの詳細に、悪用された脆弱な SQL ステートメントが含まれています。

   ![特定のアラート](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>Azure portal でアラートを調べる

Advanced Threat Protection では、アラートが [Azure Security Center](https://azure.microsoft.com/services/security-center/) と統合されています。 データベース内のライブ SQL Advanced Threat Protection タイルと Azure portal の SQL Azure Defender ブレードでは、アクティブな脅威の状態を追跡できます。

**[Advanced Threat Protection アラート]** をクリックすると、Azure Security Center のアラート ページが起動され、データベースに対して検出されたアクティブな SQL 脅威の概要が表示されます。

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection-alerts.png" alt-text="データベースでの Advanced Threat Protection のアラートに関する概要":::

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection.png" alt-text="Security Center での脅威の防止":::

## <a name="next-steps"></a>次のステップ

- [Azure SQL Database と Azure Synapse の Advanced Threat Protection](threat-detection-configure.md) について説明します。
- [Azure SQL Managed Instance の Advanced Threat Protection](../managed-instance/threat-detection-configure.md) について説明します。
- [Azure Defender for SQL](azure-defender-for-sql.md) についてさらに詳しく学習します。
- [Azure SQL Database 監査](../../azure-sql/database/auditing-overview.md)の詳細について参照してください
- [Azure Security Center](../../security-center/security-center-introduction.md) の詳細について学習します。
- 価格の詳細については、[Azure SQL Database の価格のページ](https://azure.microsoft.com/pricing/details/sql-database/)を参照してください。