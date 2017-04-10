---
title: "Azure Advisor の使用を開始する| Microsoft Docs"
description: "Azure Advisor の使用を開始します。"
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: 8ed18f6f62b6d2895e2470df1c3bdd448ea98210
ms.lasthandoff: 02/21/2017

---

# <a name="get-started-with-azure-advisor"></a>Azure Advisor の使用を開始する

Azure Portal を使用して Advisor にアクセスする方法、推奨事項を取得する方法、推奨事項を実装する方法、推奨事項を検索する方法、および推奨事項を更新する方法を説明します。

## <a name="get-advisor-recommendations"></a>Advisor の推奨事項を取得する

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーション ウィンドウで **[その他のサービス]** をクリックし、サービス メニュー ウィンドウで **[監視と管理]** までスクロールし、**[Azure Advisor]** をクリックします。 これにより、Advisor ダッシュボードが起動します。

  ![Azure Portal を使用した Azure Advisor へのアクセス](./media/advisor-overview/advisor-azure-portal-menu.png) 

3. Advisor ダッシュボードで、推奨事項を受け入れるサブスクリプションを選択します。 Advisor ダッシュボードに、選択したサブスクリプションの個人向けの推奨事項が表示されます。 
4. 特定のカテゴリの推奨事項を取得するには、次のタブのいずれかをクリックします: **[高可用性]**、**[セキュリティ]**、**[パフォーマンス]**、または **[コスト]**。
 
> [!NOTE]
> Advisor の推奨事項にアクセスするには、最初にサブスクリプションを Advisor に**登録**する必要があります。 **サブスクリプションの所有者**が Advisor ダッシュボードを起動して **[推奨の取得]** ボタンをクリックすると、サブスクリプションが登録されます。 これは **1 回限りの操作**です。 サブスクリプションが登録されると、サブスクリプション、リソース グループ、特定のリソースの**所有者**、**共同作成者**、または**閲覧者**が Advisor の推奨事項にアクセスできます。

  ![Azure Advisor ダッシュボード](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-recommendation"></a>Advisor の推奨事項の詳細を取得し、推奨設定を実装する

Advisor の **[推奨事項]** ブレードには、Advisor の推奨事項の詳細情報が表示されます。 

1. [Azure Portal](https://portal.azure.com) にサインインし、[Azure Advisor](https://aka.ms/azureadvisordashboard) を起動します。
2. **[Advisor の推奨事項]** ダッシュボードで、**[推奨事項の取得]** をクリックします。
3. 推奨事項の一覧から、詳細を確認する推奨事項をクリックします。 これにより、[推奨事項] ブレードが起動します。
4. [推奨事項] ブレードで、潜在的な問題を解決するために実行できるアクションやコスト削減の機会を活用できるアクションに関する情報を確認します。 
  
  ![Advisor が推奨するアクションの例](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Advisor の推奨事項を検索する

特定のサブスクリプションまたはリソース グループ向けの推奨事項を検索できます。 推奨事項は、状態によって検索することもできます。

1. Azure Portal にサインインし、Azure Advisor を起動します。
2. サブスクリプション、リソース グループ、推奨事項の状態 (**[アクティブ]** または **[再通知]**) によってフィルター処理することで、推奨事項を検索します。
3. **[推奨事項の取得]** をクリックして、検索フィルターに基づく Advisor の推奨事項の一覧を取得します。

  ![](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-advisor-recommendations"></a>Advisor の推奨事項を再通知する

1. Azure Portal にサインインし、Azure Advisor を起動します。
2. **[推奨事項の取得]** をクリックし、推奨事項の一覧から&1; つの推奨事項をクリックします。
3. **[推奨事項]** ブレードで、**[再通知]** をクリックします。  再通知期間を指定するか、**[Never]** を選択して推奨事項を無視できます。

  ![Advisor が推奨するアクションの例](./media/advisor-get-started/advisor-snooze.png)



## <a name="next-steps"></a>次のステップ

Advisor の詳細については、次のリソースをご覧ください。
-  [Azure Advisor の概要](advisor-overview.md)
-  [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)
-  [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
-  [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
-  [Advisor のコストに関する推奨事項](advisor-performance-recommendations.md)

