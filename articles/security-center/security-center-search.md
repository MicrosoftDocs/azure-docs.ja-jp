---
title: Azure Security Center の検索機能 | Microsoft Docs
description: Azure Security Center が Azure Monitor ログ検索を使用してセキュリティ データを取得し、分析するしくみについて説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: 6cbf3d70bd835ce1b838b19c93507f7d9487a418
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58074507"
---
# <a name="azure-security-center-search"></a>Azure Security Center の検索機能
Azure Security Center では、[Azure Monitor ログ検索](../log-analytics/log-analytics-log-searches.md)を使用してセキュリティ データが取得され、分析されます。 Azure Monitor ログには、データをすばやく取得して統合するためのクエリ言語が用意されています。 Security Center から Azure Monitor ログ検索を活用してクエリを作成し、収集したデータを分析できます。

検索機能は、Security Center の Free レベルと Standard レベルの両方で使用できます。  ログ検索に使用できるデータは、ワークスペースに適用されるレベルによって異なります。  詳細については、「[Security Center の価格](../security-center/security-center-pricing.md)」をご覧ください。


> [!NOTE]
> Security Center では、Free レベルのワークスペースにはセキュリティ データを保存しません。 さまざまなログを Free レベルのワークスペースに送信し、そのデータに対して検索を実行できますが、検索結果に Security Center のデータは含まれません。 Security Center では、Standard レベルのワークスペースにのみデータを保存します。
>
>

## <a name="access-search"></a>検索機能へのアクセス
1. Security Center のメイン メニューの **[検索]** を選択します。

   ![ログ検索を選択する][1]

2. Azure サブスクリプションのすべてのワークスペースが一覧表示されます。 ワークスペースを選択します。 (ワークスペースが 1 つしかない場合、このワークスペース セレクターは表示されません。)

   ![ワークスペースを選択する][2]

3. **[ログ検索]** が開きます。 選択したワークスペースでさらに多くのデータを照会するには、次のサンプル クエリを入力します。

   SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

   結果には、ログオンに失敗した (イベント 4625) すべてのアカウントが表示されます。

   ![[検索結果]][3]

選択したワークスペースでデータを照会する方法について詳しくは、[Kusto クエリ言語](../log-analytics/log-analytics-search-reference.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順
この記事では、Security Center の検索機能にアクセスする方法について説明しました。 Security Center では、Azure Monitor ログ検索が使用されます。 Azure Monitor ログ検索について詳しくは、以下を参照してください。

- [Azure Monitor ログとは](../log-analytics/log-analytics-overview.md) – Azure Monitor ログの概要
- [Azure Monitor ログでのログ検索について](../log-analytics/log-analytics-log-search-new.md) - Azure Monitor ログでログ検索を使用する方法と、ログ検索を作成する前に理解しておく必要がある概念について説明します
- [Azure Monitor ログでログ検索を使用してデータを探す](../log-analytics/log-analytics-log-searches.md) - ログ検索の使用方法に関するチュートリアル
- [Kusto 検索リファレンス](../log-analytics/log-analytics-search-reference.md) - Azure Monitor ログのクエリ言語について説明します

Security Center の詳細については、次の記事をご覧ください。

- [Security Center の概要](security-center-intro.md) - Security Center の主な機能について説明します

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
