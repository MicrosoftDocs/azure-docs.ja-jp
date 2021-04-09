---
title: Purview Insights を使用した、データに関する用語集レポート
description: このハウツー ガイドでは、データに関する Purview Insights の用語集レポートを表示して使用する方法について説明します。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: eb1d59ae41b04be60dec90aaee4b2305b6d39ca6
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095852"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Azure Purview でのデータに関する用語集の分析情報

このハウツー ガイドでは、データに関する Purview の用語集の分析情報レポートへのアクセス、表示、フィルター処理を行う方法について説明します。

このハウツー ガイドでは、次の方法について説明します。

> [!div class="checklist"]
> - Purview アカウントから分析情報にアクセスする
> - データの鳥瞰図を取得する

## <a name="prerequisites"></a>前提条件

Purview Insights の使用を開始する前に、次の手順を完了していることをご確認ください。

- Azure リソースを設定し、アカウントにデータを設定する

- ソースの種類に関するスキャンを設定して完了する

- 用語集を設定し、用語集の用語に資産を添付する

詳細については、「[Azure Purview でデータ ソースを管理する (プレビュー)](manage-data-sources.md)」を参照してください。

## <a name="use-purview-glossary-insights"></a>Purview の用語集の分析情報を使用する

Azure Purview では、用語集の用語を作成して資産に添付することができます。 後で、用語集の分析情報で用語集の分布を確認できます。 これには、資産に添付された用語ごとに用語集の状態が表示されます。 また、状態別の用語や、ユーザー数別のロールの分布も表示されます。

**用語集の分析情報を表示するには:**

1. Azure portal で **Azure Purview** [インスタンス画面](https://aka.ms/purviewportal)に移動し、Purview アカウントを選択します。

1. **[概要]** ページの **[開始]** セクションで、 **[Purview アカウントの起動]** タイルを選択します。

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Azure portal から Purview を起動する":::

1. Purview の **[ホーム]** ページで、 **[分析情報の表示]** タイルを選択して **[分析情報]** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: 領域にアクセスします。

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Azure portal で分析情報を表示する":::

1. **[分析情報]** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: 領域で、 **[用語集]** を選択して Purview の **[用語集の分析情報]** レポートを表示します。

**[用語集の分析情報]** を使用すると、ビジネス ユーザーは、自分の組織にための明確に定義された用語集を維持するための重要な情報を得ることができます。

1. このレポートは、**高レベルの KPI** から始まります。ここには、ご使用の Purview アカウントの **_[用語の合計]_ *、* _[アセットなしの承認済みの用語]_ *、* _[期限切れのアセット付きの用語]_** が表示されます。 これらの値はそれぞれ、用語集の正常性を識別するのに役立ちます。

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="用語集の分析情報 KPI の表示"::: 


2. (上に表示されている) **[用語のスナップショット]** セクションには、アセット付き用語とアセットなし用語に対する用語の状態が、**_[下書き]_ *_、_* _[承認済み]_ *_、_* _[アラート]_ *_、_* _[期限切れ]_** として示されます。

3. **[詳細を表示]** をクリックすると、さまざまな状態の用語名と、**_[スチュワード]_ *_ と _* _[エキスパート]_** の詳細が表示されます。 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="アセット付きとアセットなしの用語のスナップショット":::  

4. "**アセット付きの承認済み用語**" の [詳細を表示] をクリックすると、分析情報によって_ *用語集**の用語の詳細ページが表示され、そこからアタッチされた用語を使用してアセットのリストにさらに移動できます。 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="用語集に対する分析情報"::: 

4. 用語集の分析情報ページで、不足している情報の種類ごとの **不完全な用語** の分布を表示します。 グラフには、**_定義が不足している_ *_、_* _エキスパートが不足している_ *_、_* _スチュワードが不足している_ *_、_* _複数のフィールドが不足している_** 用語の数が表示されます。

1. 情報が不足している用語を表示するには、_*不完全な用語** から **[詳細を表示]** をクリックします。 用語集の用語の詳細ページに移動して、不足している情報を入力し、用語集の用語を完全にすることができます。

## <a name="next-steps"></a>次のステップ

[用語集](./how-to-create-import-export-glossary.md)を使用して用語集の用語を作成する方法について学習する