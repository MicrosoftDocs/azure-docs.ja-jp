---
title: "Azure Advisor のコストに関する推奨事項 | Microsoft Docs"
description: "Azure Advisor を使用して、Azure のデプロイにかかるコストを最適化します。"
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
ms.openlocfilehash: 7999398f69ed0ce5129dd38f8e9e1994db25f9a5
ms.lasthandoff: 02/21/2017

---

# <a name="advisor-cost-recommendations"></a>Advisor のコストに関する推奨事項

Advisor は、アイドル状態にあるリソースや活用されていないリソースを識別することによって Azure を最適化し、総合的な Azure の支出を削減します。 コストに関する推奨事項は、Advisor ダッシュボードの **[コスト]** タブで取得できます。

![Advisor の [コスト] タブ](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="low-utilization-virtual-machines"></a>使用率が低い仮想マシン 

特定のアプリケーション シナリオでは、設計によっては使用率が低くなる場合がありますが、多くの場合、仮想マシンのサイズと数を管理することによってコストを削減できます。 Advisor は、仮想マシンの使用状況を 14 日間にわたって監視して、使用率が低い仮想マシンを識別します。 CPU 使用率が 5% 以下でネットワークの使用率が 7 MB 以下である日が 4 日以上ある仮想マシンが、使用率が低い仮想マシンと見なされます。

Advisor は、そのような仮想マシンの実行を継続した場合の推定コストを示します。 仮想マシンをシャット ダウンするか、サイズを変更することを選択できます。  

![仮想マシンのサイズ変更を推奨している Advisor のコストに関する推奨事項](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="sql-elastic-database-pool-recommendations"></a>SQL エラスティック データベース プールに関する推奨事項

Advisor は、エラスティック データベース プールの作成によってメリットを得る可能性がある SQL Server インスタンスを識別します。 エラスティック データベース プールは、多様な使用パターンを持つ複数のデータベースのパフォーマンス目標を管理するための、簡単でコスト効率に優れたソリューションを提供します。 Azure のエラスティック プールの詳細については、「[Azure エラスティック プールの概要](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)」を参照してください。

![エラスティック データベース プールに対する Advisor のコストに関する推奨事項](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Azure Advisor のコストに関する推奨事項にアクセスする方法

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーション ウィンドウで **[その他のサービス]** をクリックし、サービス メニュー ウィンドウで **[監視と管理]** までスクロールし、**[Azure Advisor]** をクリックします。 これで、Advisor ダッシュボードが起動します。 
3. Advisor ダッシュボードで、**[コスト]** タブをクリックし、推奨事項を受け入れるサブスクリプションを選択し、**[推奨の取得]** をクリックします。

> [!NOTE]
> Advisor の推奨事項にアクセスするには、最初にサブスクリプションを Advisor に**登録**する必要があります。 **サブスクリプションの所有者**が Advisor ダッシュボードを起動して **[推奨の取得]** ボタンをクリックすると、サブスクリプションが登録されます。 これは **1 回限りの操作**です。 サブスクリプションが登録されると、サブスクリプション、リソース グループ、特定のリソースの**所有者**、**共同作成者**、または**閲覧者**が Advisor の推奨事項にアクセスできます。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、次のリソースをご覧ください。
-  [Advisor 入門](advisor-overview.md)
-  [作業の開始](advisor-get-started.md)
-  [Advisor の高可用性に関する推奨事項](advisor-cost-recommendations.md)
-  [Advisor のセキュリティに関する推奨事項](advisor-cost-recommendations.md)
-  [Advisor のパフォーマンスに関する推奨事項](advisor-cost-recommendations.md)

