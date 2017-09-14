---
title: "Azure Advisor のセキュリティに関する推奨事項 | Microsoft Docs"
description: "Azure Advisor を使用して、Azure のデプロイのセキュリティを向上させます。"
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
ms.openlocfilehash: 53be05593c3733ccb27979a3a026414013125779
ms.contentlocale: ja-jp
ms.lasthandoff: 03/31/2017

---
# <a name="advisor-security-recommendations"></a>Advisor のセキュリティに関する推奨事項

Azure Advisor は、すべての Azure リソースに関する推奨事項を、一貫性がある統合された形で提示します。 Azure Security Center と統合して、セキュリティに関する推奨事項を生成します。 セキュリティに関する推奨事項は、Advisor ダッシュボードの **[セキュリティ]** タブで取得できます。

![Advisor の [セキュリティ] ボタン](./media/advisor-security-recommendations/advisor-security-tab.png)

セキュリティ センターは、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応することに役立ちます。 それは、Azure リソースのセキュリティの状態を定期的に分析します。 セキュリティ センターでは、潜在的なセキュリティの脆弱性が特定されると、推奨事項が作成されます。 推奨事項では、必要なコントロールを構成するプロセスを説明します。 

![Advisor の [セキュリティ] タブ](./media/advisor-security-recommendations/advisor-security-recommendations.png)

セキュリティの推奨事項の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項の管理](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/)」を参照してください。

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>Azure Advisor のセキュリティに関する推奨事項にアクセスする方法

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. 左側のウィンドウで、**[その他のサービス]** をクリックします。

3. サービス メニュー ウィンドウの **[Monitoring and Management (監視と管理)]** で、**[Azure Advisor]** をクリックします。  
 Advisor ダッシュボードが表示されます。

4. Advisor ダッシュボードで、**[セキュリティ]** タブをクリックします。

5. 推奨事項を受け入れるサブスクリプションを選択し、**[推奨事項を取得します]** をクリックします。

> [!NOTE]
> Advisor の推奨事項にアクセスするには、最初に Advisor に*サブスクリプションを登録する*必要があります。 *サブスクリプションの所有者*が Advisor ダッシュボードを起動して **[推奨事項を取得します]** ボタンをクリックすると、サブスクリプションが登録されます。 これは *1 回限りの操作*です。 サブスクリプションが登録されると、サブスクリプション、リソース グループ、または特定のリソースの*所有者*、*共同作成者*、または*閲覧者*として Advisor の推奨事項にアクセスできます。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、以下を参照してください。
* [Advisor 入門](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)


 

