---
title: Advanced Threat Protection - Azure SQL Database | Microsoft Docs
description: Advanced Threat Protection では、Azure SQL Database での潜在的なセキュリティ脅威を示す異常なデータベース アクティビティが検出されます。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: 710a94c919f4262c3f572f28d03c79b77e658287
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797066"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Azure SQL Database の Advanced Threat Protection

[Azure SQL Database](sql-database-technical-overview.md) および [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) の Advanced Threat Protectionでは、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティが検出されます。

Advanced Threat Protection は、高度な SQL セキュリティ機能の統合パッケージである [Advanced Data Security](sql-database-advanced-data-security.md) (ADS) オファリングの一部です。 Advanced Threat Protection は、SQL ADS ポータルを使用して一元的にアクセスおよび管理できます。

> [!NOTE]
> このトピックは Azure SQL サーバーのほか、その Azure SQL サーバーに作成される SQL Database と SQL Data Warehouse の両方に当てはまります。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。

## <a name="what-is-advanced-threat-protection"></a>Advanced Threat Protection とは

 Advanced Threat Protection で提供される新しいセキュリティ階層では、異常なアクティビティに対するセキュリティ アラートが提供されるので、お客様は潜在的な脅威が発生したときにそれを検出して対応できます。 不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセスやクエリのパターンが見つかった場合に、ユーザーはアラートを受信します。 Advanced Threat Protection では、アラートと [Azure Security Center](https://azure.microsoft.com/services/security-center/) が統合されます。これには、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法が含まれます。 Advanced Threat Protection を使用すると、データベースに対する潜在的な脅威に簡単に対処でき、セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要はありません。

完全な調査エクスペリエンスを実現するために、データベース イベントを Azure ストレージ アカウントの監査ログに書き込む、[SQL Database Auditing](sql-database-auditing.md) を有効にすることをお勧めします。  

## <a name="advanced-threat-protection-alerts"></a>Advanced Threat Protection のアラート

Azure SQL Database の Advanced Threat Protection では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティが検出され、次のアラートがトリガーされます。

- **Vulnerability to SQL Injection (SQL インジェクションにつながる脆弱性)**:このアラートは、アプリケーションがデータベースにエラーのある SQL ステートメントを生成したときにトリガーされます。 このアラートは、SQL インジェクション攻撃に対する脆弱性が存在する可能性を示すものです。 エラーのあるステートメントが生成される理由として、次の 2 つが考えられます。

  - アプリケーション コードの欠陥により、エラーのある SQL 文が作成される
  - アプリケーション コードまたはストアド プロシージャが、SQL インジェクションに悪用される可能性があるエラーのある SQL ステートメントを作成するときにユーザー入力をサニタイズしない
- **Potential SQL injection (SQL インジェクションの可能性)**:このアラートは、SQL インジェクションに対する特定されたアプリケーションの脆弱性に対してアクティブな悪用が発生したときにトリガーされます。 これは、攻撃者が脆弱なアプリケーション コードまたはストアド プロシージャを使用して悪意のある SQL 文を挿入しようとしていることを意味します。
- **Access from unusual location (通常とは異なる場所からのアクセス)**:このアラートは、だれかが通常とは異なる地理的な場所から SQL サーバーにログオンしたことで SQL Server へのアクセス パターンに変化が生じたときにトリガーされます。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。
- **Access from unusual Azure data center (通常とは異なる Azure データ センターからのアクセス)**:このアラートは、SQL Server へのアクセス パターンに変化が生じたときにトリガーされます。たとえば、だれかが通常とは異なる Azure データ センターから SQL Server にログオンしたことが、サーバーで最近確認された場合です。 このアラートで正当なアクション (Azure、Power BI、Azure SQL クエリ エディターの新しいアプリケーション) が検出されることがあります。 別のケースでは、このアラートによって Azure リソース/サービス (元従業員、外部の攻撃者) からの悪意のあるアクションが検出されます。
- **Access from unfamiliar principal (通常とは異なるプリンシパルからのアクセス)**:このアラートは、だれかが通常とは異なるプリンシパル (SQL ユーザー) を使用して SQL サーバーにログオンしたことで SQL サーバーへのアクセス パターンに変化が生じたときにトリガーされます。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。
- **Access from a potentially harmful application (潜在的に有害なアプリケーションからのアクセス)**:このアラートは、データベースにアクセスするために潜在的に有害なアプリケーションが使用されたときにトリガーされます。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートで一般的な攻撃ツールを使用した攻撃が検出されます。
- **Brute force SQL credentials (SQL 資格情報に対するブルート フォース攻撃)**:このアラートは、異なる資格情報でログインに失敗した回数が異常に多いときにトリガーされます。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートでブルート フォース攻撃が検出されます。

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>疑わしいイベントが検出されたときの異常なデータベース アクティビティの調査

異常なデータベース アクティビティが検出されると、電子メールで通知を受け取ります。 電子メールでは、異常なアクティビティの特徴、データベース名、サーバー名、アプリケーション名、イベントの時刻など、疑わしいセキュリティ イベントについての情報が提供されます。 さらに、データベースへの潜在的な脅威の考えられる原因と調査や緩和のための推奨されるアクションについての情報も提供されます。

![異常アクティビティ レポート](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. 電子メールの **[View recent SQL alerts]\(最近の SQL アラートの表示\)** リンクをクリックして Azure Portal を起動し、Azure Security Center のアラート ページを表示します。このページには、SQL データベースで検出されたアクティブな脅威の概要が示されます。

   ![アクティビティの脅威](./media/sql-database-threat-detection/active_threats.png)

2. 特定のアラートをクリックすると、さらに詳細な情報と、この脅威を調査し、今後の脅威に対処するためのアクションが表示されます。

   たとえば、SQL インジェクションはインターネットにおける Web アプリケーションの最も一般的なセキュリティ問題の 1 つであり、データ駆動型アプリケーションの攻撃に使用されます。 攻撃者は、アプリケーションの脆弱性を利用してアプリケーションの入力フィールドに悪意のある SQL ステートメントを挿入し、データベースのデータを侵害または変更します。 SQL インジェクションのアラートの場合、アラートの詳細に、悪用された脆弱な SQL ステートメントが含まれています。

   ![特定のアラート](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Azure portal でデータベースの Advanced Threat Protection アラートを調査する

Advanced Threat Protection では、アラートが [Azure Security Center](https://azure.microsoft.com/services/security-center/) と統合されています。 データベース内のライブ SQL Advanced Threat Protection タイルと Azure portal の SQL ADS ブレードでは、アクティブな脅威の状態が追跡されます。

**[Advanced Threat Protection アラート]** をクリックすると、Azure Security Center のアラート ページが起動され、データベースまたはデータ ウェアハウスに対して検出されたアクティブな SQL 脅威の概要が表示されます。

   ![Advanced Threat Protection アラート](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Advanced Threat Protection アラート 2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>次の手順

- [単一のデータベースおよびプールされたデータベースでの Advanced Threat Protection](sql-database-threat-detection.md) の詳細について学習します。
- [マネージド インスタンスでの Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) の詳細について学習します。
- [Advanced Data Security](sql-database-advanced-data-security.md) の詳細について学習します。
- [Azure SQL Database 監査](sql-database-auditing.md)の詳細について参照してください
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について学習します。
- 価格の詳細については、[SQL Database の価格のページ](https://azure.microsoft.com/pricing/details/sql-database/)を参照してください。  
