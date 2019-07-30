---
title: Advanced Data Security - Azure SQL Database | Microsoft Docs
description: 機密データの探索と分類、データベースの脆弱性の管理、Azure SQL データベースへの脅威を示す可能性がある異常なアクティビティの検出などの機能について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: v-mohabe
ms.reviewer: vanto
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: ce02fee31041222c48d62ef8410b97fedf74dfdb
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297673"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Azure SQL Database 向け Advanced Data Security

Advanced Data Security は、高度な SQL セキュリティ機能のための統合パッケージです。 機密データの検出と分類、データベースの潜在的な脆弱性の検出と軽減、データベースへの脅威を示す可能性がある異常なアクティビティの検出を行う機能が含まれています。 これらの機能を 1 つの場所で有効にして管理できます。

## <a name="overview"></a>概要

Advanced Data Security (ADS) により、データの探索と分類、脆弱性の評価、Advanced Threat Protection などの高度な SQL セキュリティ機能のセットが提供されます。

- [データの検出と分類](sql-database-data-discovery-and-classification.md) (現在プレビュー段階) では、Azure SQL Database に組み込まれる、データベースの機密データの検出、分類、ラベル付と保護を行う機能が用意されています。 データベースの分類の状態を把握し、データベース内やその境界を越えて機密データへのアクセスを追跡するために使用できます。
- [脆弱性評価](sql-vulnerability-assessment.md)では、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 セキュリティの状態を表示することができ、セキュリティの問題を解決して、データベースのセキュリティを強化するために実行可能な手順が含まれます。
- [Advanced Threat Protection](sql-database-threat-detection-overview.md) では、データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンが見つかるとすぐにセキュリティ通知を提供します。 Advanced Threat Protection アラートでは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法が提供されます。

SQL ADS を一度有効にすれば、含まれているすべての機能が有効になります。 1 回のクリックで、SQL Database サーバー上またはマネージド インスタンス上のすべてのデータベースに対して ADS を有効にできます。 ADS の設定を有効にしたり、管理したりするには、[SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) ロール、SQL データベース管理者ロール、または SQL サーバー管理者ロールに属している必要があります。 

ADS の価格は Azure Security Center Standard レベルと同じです。ここで保護対象の各 SQL Database サーバーまたはマネージド インスタンスは 1 つのノードとしてカウントされます。 新しく保護されたリソースは、Security Center Standard レベルの無料試用版として使用できます。 詳しくは、[Azure Security Center の価格ページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。

## <a name="getting-started-with-ads"></a>ADS の使用を開始する

ADS の使用を始める手順は以下のとおりです。

## <a name="1-enable-ads"></a>1.ADS を有効にする

ADS を有効にするには、ご使用の SQL Database サーバーまたはマネージド インスタンスの **[セキュリティ]** 見出しの下の **[Advanced Data Security]** に移動します。 データベース サーバーまたはマネージド インスタンス上のすべてのデータベースに対して ADS を有効にするには、 **[サーバーで Advanced Data Security を有効にします]** をクリックします。

> [!NOTE]
> ストレージ アカウントが自動的に作成され、**脆弱性評価**のスキャン結果を格納するように構成されます。 同じリソース グループおよびリージョン内の別のサーバー用に ADS を既に有効にしてある場合は、既存のストレージ アカウントが使用されます。

![ADS を有効にする](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> ADS のコストは、ノード全体が SQL Database サーバーまたはマネージド インスタンスである場合の、ノードあたりの Azure Security Center Standard レベルの料金に合わせられています。 したがって、1 回お支払いいただければ、データベース サーバーまたはマネージド インスタンス上のすべてのデータベースが ADS で保護されます。 最初は無料試用版で ADS をお試しいただけます。

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2.データの分類、脆弱性の追跡、脅威アラートの調査を開始する

**[データの検出と分類]** カードをクリックして、分類が推奨される機密性の高い列を表示し、永続的な機密ラベルを使ってデータを分類します。 **[脆弱性評価]** カードをクリックして、脆弱性のスキャンとレポートを表示および管理し、セキュリティ水準を追跡します。 セキュリティ通知を受け取った場合は、 **[Advanced Threat Protection]** カードをクリックして、通知の詳細を表示し、Azure Security Center のセキュリティ通知ページを使って Azure サブスクリプション内のすべての通知の統合レポートを表示します。

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>手順 3.SQL Database サーバーまたはマネージド インスタンス上で ADS の設定を管理する

ADS の設定を表示および管理するには、ご使用の SQL Database サーバーまたはマネージド インスタンスの **[セキュリティ]** 見出しの下の **[Advanced Data Security]** に移動します。 このページでは、ADS を有効または無効にしたり、SQL Database サーバーまたはマネージド インスタンス全体に対する脆弱性評価や Advanced Threat Protection の設定を変更したりできます。

![サーバーの設定](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4.SQL Database の ADS の設定を管理する

特定のデータベースに対する ADS の設定をオーバーライドするには、 **[データベース レベルで Advanced Data Security を有効にします]** チェック ボックスをオンにします。 このオプションは、データベース サーバーまたはマネージド インスタンス上の全データベースについて受け取る通知の代わりに、またはそれに加えて、個別のデータベースに対する Advanced Threat Protection アラートまたは脆弱性評価結果を別に受け取る特定の要件がある場合にのみ使用します。

チェック ボックスをオンにすると、このデータベースに対して関連する設定を構成することができます。
 
![データベースと Advanced Threat Protection の設定](./media/sql-advanced-protection/database_threat_detection_settings.png) 

データベース サーバーまたはマネージド インスタンス用の Advanced Data Security 設定には、ADS のデータベース ウィンドウからもアクセスできます。 メインの ADS ウィンドウで **[設定]** をクリックし、 **[Advanced Data Security サーバー設定を表示します]** をクリックします。 

![データベース設定](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>次の手順 

- [データの検出と分類](sql-database-data-discovery-and-classification.md)についてさらに詳しく学習する 
- [脆弱性評価](sql-vulnerability-assessment.md)についてさらに詳しく学習する 
- [Advanced Threat Protection](sql-database-threat-detection.md) についてさらに詳しく学習する
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について学習します。
