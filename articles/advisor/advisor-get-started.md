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
ms.openlocfilehash: a662841bebda460d4225e080f16705b3f16fdc46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-advisor"></a>Azure Advisor の使用を開始する

Azure Portal を使用して Advisor にアクセスする方法、推奨事項を取得する方法、推奨事項を実装する方法、推奨事項を検索する方法、および推奨事項を更新する方法を説明します。

## <a name="get-advisor-recommendations"></a>Advisor の推奨事項を取得する

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. 左側のウィンドウで、**[その他のサービス]** をクリックします。

3. サービス メニュー ウィンドウの **[Monitoring and Management (監視と管理)]** で、**[Azure Advisor]** をクリックします。  
 Advisor ダッシュボードが表示されます。

   ![Azure Portal を使用した Azure Advisor へのアクセス](./media/advisor-overview/advisor-azure-portal-menu.png) 

4. Advisor ダッシュボードで、推奨事項を受け入れるサブスクリプションを選択します。  
Advisor ダッシュボードに、選択したサブスクリプションの個人向けの推奨事項が表示されます。 

5. 特定のカテゴリの推奨事項を取得するには、次のタブのいずれかをクリックします: **[高可用性]**、**[セキュリティ]**、**[パフォーマンス]**、または **[コスト]**。
 
> [!NOTE]
> Advisor の推奨事項にアクセスするには、最初に Advisor に*サブスクリプションを登録する*必要があります。 *サブスクリプションの所有者*が Advisor ダッシュボードを起動して **[推奨事項の取得]** ボタンをクリックすると、サブスクリプションが登録されます。 これは *1 回限りの操作*です。 サブスクリプションが登録されると、サブスクリプション、リソース グループ、または特定のリソースの*所有者*、*共同作成者*、または*閲覧者*として Advisor の推奨事項にアクセスできます。

  ![Azure Advisor ダッシュボード](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Advisor の推奨事項の詳細を取得し、推奨設定を実装する

Advisor の **[推奨事項]** ブレードには、Advisor の推奨事項の詳細情報が表示されます。 

1. [Azure Portal](https://portal.azure.com) にサインインし、[Azure Advisor](https://aka.ms/azureadvisordashboard) を起動します。

2. **[Advisor の推奨事項]** ダッシュボードで、**[推奨事項の取得]** をクリックします。

3. 推奨事項の一覧から、詳細を確認する推奨事項をクリックします。  
**[推奨事項]** ブレードが表示されます。

4. **[推奨事項]** ブレードで、潜在的な問題を解決するために実行できるアクションやコスト削減の機会を活用できるアクションに関する情報を確認します。 
  
  ![Advisor の[推奨事項] ブレード](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Advisor の推奨事項を検索する

特定のサブスクリプションまたはリソース グループ向けの推奨事項を検索できます。 推奨事項は、状態によって検索することもできます。

1. [Azure Portal](https://portal.azure.com) にサインインし、[Azure Advisor](https://aka.ms/azureadvisordashboard) を起動します。

2. サブスクリプション、リソース グループ、推奨事項の状態 (**[アクティブ]** または **[再通知]**) によってフィルター処理することで、推奨事項を検索します。

3. 検索フィルターの条件に基づく Advisor 推奨事項の一覧を取得するには、**[推奨事項の取得]** をクリックします。

  ![Advisor 検索フィルターの条件](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Advisor 推奨事項の再通知または無視

1. [Azure Portal](https://portal.azure.com) にサインインし、[Azure Advisor](https://aka.ms/azureadvisordashboard) を起動します。

2. **[推奨事項の取得]** をクリックし、推奨事項の一覧から 1 つの推奨事項をクリックします。

3. **[推奨事項]** ブレードで、**[再通知]** をクリックします。  

   ![Advisor が推奨するアクションの例](./media/advisor-get-started/advisor-snooze.png)

4. 再通知期間を指定するか、**[Never]** を選択して推奨事項を無視します。


## <a name="next-steps"></a>次のステップ

Advisor の詳細については、次を参照してください。
* [Azure Advisor の概要](advisor-overview.md)
* [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
-  [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のコストに関する推奨事項](advisor-performance-recommendations.md)
