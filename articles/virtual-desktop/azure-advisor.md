---
title: Windows Virtual Desktop を Azure Advisor と統合する - Azure
description: Windows Virtual Desktop の展開で Azure Advisor を使用する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76b7f97b6020a3a0d5571a3a105d15f7d7893485
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89147312"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>Windows Virtual Desktop で Azure Advisor を使用する

Azure Advisor は、サポート ケースを提出しなくても、ユーザーが一般的な問題を自分で解決するのに役立ちます。 推奨事項により、ヘルプ要求を送信する必要性が減り、時間とコストを節約できます。

この記事では、ユーザーを支援するために Windows Virtual Desktop の展開で Azure Advisor を設定する方法について説明します。

## <a name="what-is-azure-advisor"></a>Azure Advisor とは

Azure Advisor は、構成とテレメトリを分析して、一般的な問題を解決するためのパーソナライズされた推奨事項を提供します。 これらの推奨事項により、信頼性、セキュリティ、オペレーショナル エクセレンス、パフォーマンス、コストに応じて Azure リソースを最適化できます。 詳細については、[Azure Advisor の Web サイト](https://azure.microsoft.com/services/advisor/)を参照してください。

## <a name="how-to-start-using-azure-advisor"></a>Azure Advisor の使用を開始する方法

開始するために必要なのは、Azure portal の Azure アカウントだけです。 まず、<https://portal.azure.com/#home> で Azure portal を開き、次の図に示すように、 **[Azure サービス]** で **[Advisor]** を選択します。 また、Azure portal の検索バーに「Azure Advisor」と入力することもできます。

> [!div class="mx-imgBorder"]
> ![Azure portal のスクリーンショット。 ユーザーが Azure Advisor リンクの上にマウス カーソルを置いて、ドロップダウン メニューが表示されています。](media/azure-advisor.png)

Azure Advisor を開くと、次の 5 つのカテゴリが表示されます。

- コスト
- セキュリティ
- [信頼性]
- オペレーショナル エクセレンス
- パフォーマンス

> [!div class="mx-imgBorder"]
> ![各カテゴリの 5 つのメニューが表示されている Azure Advisor のスクリーンショット。 それぞれのボックスに表示される 5 つの項目は、コスト、セキュリティ、信頼性、オペレーショナル エクセレンス、パフォーマンスです。](media/advisor-categories.png)

カテゴリを選択すると、アクティブな推奨事項のページが表示されます。 このページでは、次の図に示すように、Azure Advisor の推奨事項を表示できます。

> [!div class="mx-imgBorder"]
> ![オペレーショナル エクセレンスのアクティブな推奨事項のリストのスクリーンショット。 このリストには、優先度レベルが異なる 7 つの推奨事項が示されています。](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Azure Advisor に関するその他のヒント

- 推奨事項は頻繁に (少なくとも 1 週間に 1 回以上) 確認してください。 Azure Advisor では、1 日に複数回、アクティブな推奨事項が更新されます。 新しい推奨事項を確認することで、小さな問題を見つけて解決できるため、より大きな問題を防ぐことができます。

- 常に Azure Advisor で最も優先度の高いレベルの問題を解決するようにしてください。 優先度の高い問題は赤色でマークされます。 優先度の高い推奨事項を未解決のままにしておくと、将来問題が発生する可能性があります。

- 推奨事項の重要性が低いと思われる場合は、無視するか延期することができます。 推奨事項を無視または延期するには、 **[アクション]** 列に移動し、項目の状態を変更します。

- 推奨事項は、それが表示されている理由がわかるまで、およびユーザーに悪影響が及ばないことが確認できるまで、無視しないでください。 必ず **[詳細]** を選択して、問題が何かを確認してください。 Azure Advisor の指示に従って問題を解決すると、その問題はリストに表示されなくなります。 問題を解決する方が、繰り返し延期するよりも得策です。

- Windows Virtual Desktop で問題が発生した場合は、必ず Azure Advisor を最初に確認してください。 Azure Advisor では、問題を解決する方法の指示が提供されるか、少なくとも役立つリソースが示されます。

## <a name="next-steps"></a>次のステップ

推奨事項を解決する方法については、「[Azure Advisor の推奨事項を解決する方法](azure-advisor-recommendations.md)」を参照してください。

新しい推奨事項についてご提案がある場合は、[Azure Advisor User Voice フォーラム](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations)に投稿してください。
