---
title: "Azure Advisor のパフォーマンスに関する推奨事項 | Microsoft Docs"
description: "Advisor を使用して、Azure のデプロイのパフォーマンスを最適化します。"
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: d04c209894ab8ee35b92381f9679f68addd64f00
ms.lasthandoff: 02/21/2017

---

# <a name="advisor-performance-recommendations"></a>Advisor のパフォーマンスに関する推奨事項

Advisor のパフォーマンスに関する推奨事項は、ビジネスに不可欠なアプリケーションのスピードと応答性を強化して向上させるために役立ちます。 Advisor のパフォーマンスに関する推奨事項は、Advisor ダッシュボードの **[パフォーマンス]** タブで取得できます。

![Advisor の [パフォーマンス] タブ](./media/advisor-performance-recommendations/advisor-performance-tab.png)

## <a name="sql-database-recommendations"></a>SQL Database に関する推奨事項

Advisor は、すべての Azure リソースに関する推奨事項を、一貫性がある統合された形で提示します。 SQL Database Advisor と統合して、SQL Azure Database のパフォーマンスを向上させるための推奨事項を生成します。 SQL Database Advisor は、SQL Azure Database の使用履歴を分析することで、パフォーマンスを評価します。 その後、データベースの一般的なワークロードを実行する上で最適な推奨事項を提示します。 

> [!NOTE]
> 推奨事項を取得するには、データベースを約&1; 週間使用し、その週の間に、何らかの一貫性のあるアクティビティが行われている必要があります。 SQL Database Advisor は、ランダムでむらのある瞬間的なアクティビティよりも、一貫性のあるアクティビティのクエリ パターンをより簡単に最適化できます。

SQL Database Advisor の詳細については「[SQL Database Advisor](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/)」を参照してください。

![SQL Database に関する推奨事項](./media/advisor-performance-recommendations/advisor-performance-sql.png)

## <a name="redis-cache-recommendations"></a>Redis Cache に関する推奨事項

Azure Advisor は、メモリの高使用率、サーバーの負荷、ネットワークの帯域幅、または大量のクライアント接続によってパフォーマンスに悪影響を与える可能性がある Redis Cache インスタンスを識別します。 潜在的な問題を回避するためのベスト プラクティスの推奨事項も提示します。 Redis Cache に関する推奨事項の詳細については、「[Redis Cache Advisor](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor)」を参照してください。


## <a name="app-services-recommendations"></a>App Services に関する推奨事項

Azure Advisor は、App Services のエクスペリエンスを向上させ、関連するプラットフォームの機能を検出するためのベスト プラクティスの推奨事項を統合します。 App Services に関する推奨事項の例を次に示します。
- アプリの実行時にメモリや CPU のリソースが使い尽くされるインスタンスと対応策の検出
- Web アプリやデータベースなどのリソースの配置によってパフォーマンスの向上とコストの削減を実現できるインスタンスの検出 

App Services に関する推奨事項の詳細については、「[Azure App Service のベスト プラクティス](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/)を参照してください。
![App Services に関する推奨事項](./media/advisor-performance-recommendations/advisor-performance-app-service.png)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Advisor のパフォーマンスに関する推奨事項にアクセスする方法

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーション ウィンドウで **[その他のサービス]** をクリックし、サービス メニュー ウィンドウで **[監視と管理]** までスクロールし、**[Azure Advisor]** をクリックします。 これにより、Advisor ダッシュボードが起動します。 
3. Advisor ダッシュボードで、**[パフォーマンス]** タブをクリックし、推奨事項を受け入れるサブスクリプションを選択します。

> [!NOTE]
> Advisor の推奨事項にアクセスするには、最初にサブスクリプションを Advisor に**登録**する必要があります。 **サブスクリプションの所有者**が Advisor ダッシュボードを起動して **[推奨の取得]** ボタンをクリックすると、サブスクリプションが登録されます。 これは **1 回限りの操作**です。 サブスクリプションが登録されると、サブスクリプション、リソース グループ、特定のリソースの**所有者**、**共同作成者**、または**閲覧者**が Advisor の推奨事項にアクセスできます。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、次のリソースをご覧ください。

-  [Advisor 入門](advisor-overview.md)
-  [Advisor の使用を開始する](advisor-get-started.md)
-  [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)
-  [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
-  [Advisor のコストに関する推奨事項](advisor-performance-recommendations.md)

