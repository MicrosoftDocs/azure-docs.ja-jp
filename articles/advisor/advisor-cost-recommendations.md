---
title: "Azure Advisor のコストに関する推奨事項 | Microsoft Docs"
description: "Azure Advisor を使用して、Azure のデプロイにかかるコストを最適化します。"
services: advisor
documentationcenter: NA
author: KumudD
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5eef2116f238b477fa8de46ce7b25728c393739c
ms.contentlocale: ja-jp
ms.lasthandoff: 03/31/2017

---

# <a name="advisor-cost-recommendations"></a>Advisor のコストに関する推奨事項

Advisor は、アイドル状態にあるリソースや活用されていないリソースを識別することによって Azure を最適化し、総合的な Azure の支出を削減します。 コストに関する推奨事項は、Advisor ダッシュボードの **[コスト]** タブで取得できます。

![Advisor の [コスト] タブ](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="optimize-virtual-machine-spend-by-resizing-underutilized-instances"></a>使用率が低いインスタンスのサイズ変更による仮想マシン費用の最適化 
特定のアプリケーション シナリオでは、設計によっては使用率が低くなる場合がありますが、多くの場合、仮想マシンのサイズと数を管理することによってコストを削減できます。 Advisor は、仮想マシンの使用状況を 14 日間にわたって監視して、使用率が低い仮想マシンを識別します。 CPU 使用率が 5% 以下で、ネットワークの使用率が 7 MB 以下である日が 4 日以上ある仮想マシンは、使用率が低い仮想マシンと見なされます。

Advisor には、そのような仮想マシンの使用を継続した場合の推定コストが示され、シャット ダウンまたはサイズ変更を選択できます。  

![仮想マシンのサイズ変更を推奨している Advisor のコストに関する推奨事項](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>コスト効率に優れたソリューションを使用して、複数の SQL データベースのパフォーマンス目標を管理してください。
Advisor は、エラスティック データベース プールの作成によってメリットを得る可能性がある SQL Server インスタンスを識別します。 エラスティック データベース プールは、多様な使用パターンを持つ複数のデータベースのパフォーマンス目標を管理するための、簡単でコスト効率に優れたソリューションを提供します。 Azure エラスティック プールの詳細については、「[Azure エラスティック プールの概要](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)」を参照してください。

![エラスティック データベース プールに対する Advisor のコストに関する推奨事項](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Azure Advisor のコストに関する推奨事項にアクセスする方法

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. 左側のウィンドウで、**[その他のサービス]** をクリックします。

3. サービス メニュー ウィンドウの **[監視と管理]** で、**[Azure Advisor]** をクリックします。  
 Advisor ダッシュボードが表示されます。

4. Advisor ダッシュボードで、**[コスト]** タブをクリックします。

5. 推奨事項を受け入れるサブスクリプションを選択し、**[推奨事項の取得]** をクリックします。

> [!NOTE]
> Advisor の推奨事項にアクセスするには、最初に Advisor に*サブスクリプションを登録する*必要があります。 *サブスクリプションの所有者*が Advisor ダッシュボードを起動して **[推奨事項の取得]** ボタンをクリックすると、サブスクリプションが登録されます。 これは *1 回限りの操作*です。 サブスクリプションが登録されると、サブスクリプション、リソース グループ、または特定のリソースの*所有者*、*共同作成者*、または*閲覧者*として Advisor の推奨事項にアクセスできます。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、以下を参照してください。
* [Advisor 入門](advisor-overview.md)
* [作業の開始](advisor-get-started.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor の高可用性に関する推奨事項](advisor-cost-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-cost-recommendations.md)

