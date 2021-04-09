---
title: Azure Security Center でセキュア スコアを追跡する
description: Azure Security Center でセキュア スコアにアクセスして追跡する複数の方法について説明します。
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 5efc48d348e9cfceab590bcfba8c621e7721376f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107223"
---
# <a name="access-and-track-your-secure-score"></a>セキュリティで保護されたスコアにアクセスして追跡する

次のセクションで説明するように、Azure portal またはプログラムによって、全体的なセキュリティ スコアとサブスクリプションごとのスコアを確認できます。

> [!TIP]
> スコア計算方法の詳しい説明については、「[計算 - スコアを理解する](secure-score-security-controls.md#calculations---understanding-your-score)」を参照してください。

## <a name="get-your-secure-score-from-the-portal"></a>ポータルからセキュリティ スコアを取得する

ポータルで、Security Center にはスコアが目立つように表示されます。これは、Security Center の [概要] ページの最初の主なタイルです。 このタイルを選択すると、スコアがサブスクリプション別に分類されている専用のセキュリティ スコア ページに移動ます。 サブスクリプションを 1 つ選択すると、優先度が設定された推奨事項と、修復によるスコアへの影響を示す詳細な一覧が表示されます。 

要約すると、Security Center のポータル ページの次の場所に、セキュリティ スコアが表示されます。

- Security Center の **[概要]** にあるタイル (メイン ダッシュボード):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Security Center のダッシュボード上のセキュリティ スコア":::

- 専用の **[セキュリティ スコア]** ページで、サブスクリプションと管理グループのセキュリティ スコアを確認できます。

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Security Center の [セキュリティ スコア] ページのサブスクリプションのセキュリティ スコア":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Security Center の [セキュリティ スコア] ページの管理グループのセキュリティ スコア":::

    > [!NOTE]
    > 十分なアクセス許可を持っていない管理グループについては、スコアが "制限付き" として表示されます。 

- **[推奨事項]** ページの上部:

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Security Center の [推奨事項] ページのセキュリティ スコア":::

## <a name="get-your-secure-score-from-the-rest-api"></a>REST API からセキュリティ スコアを取得する

スコアには、Secure Scores API を使用してアクセスできます。 この API メソッドを使用すると、データに対してクエリを実行したり、一定期間のセキュリティ スコアをレポートする独自のメカニズムを構築したりするための柔軟性が得られます。 たとえば、[Secure Scores API](/rest/api/securitycenter/securescores) を使用して、特定のサブスクリプションのスコアを取得できます。 また、[Secure Score Controls API](/rest/api/securitycenter/securescorecontrols) を使用して、サブスクリプションのセキュリティ コントロールと現在のスコアを一覧表示できます。

![API を使用して 1 つのセキュリティ スコアを取得する](media/secure-score-security-controls/single-secure-score-via-api.png)

Secure Scores API を使用して構築されたツールの例については、[GitHub コミュニティのセキュリティ スコアの領域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)を参照してください。 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Azure Resource Graph からセキュア スコアを取得する

Azure Resource Graph を使用すると、堅牢なフィルター処理、グループ化、および並べ替え機能を使用して、クラウド環境全体のリソース情報にすばやくアクセスできます。 これは、Azure サブスクリプション全体の情報を、プログラムから、または Azure portal 内ですばやく効率的に照会する方法です。 [Azure Resource Graph の詳細についてさらに学習します](../governance/resource-graph/index.yml)。

Azure Resource Graph を使用し、複数のサブスクリプションのセキュア スコアにアクセスするには:

1. Azure portal から **Azure Resource Graph Explorer** を開きます。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph エクスプローラーの起動** 推奨ページ" :::

1. Kusto クエリを入力します (詳細については、次の例を参照してください)。

    - このクエリによって、サブスクリプション ID、現在のスコア (ポイントおよびパーセント表記)、およびサブスクリプションの最大スコアが返されます。 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - このクエリによって、すべてのセキュリティ コントロールの状態が返されます。 各コントロールについて、異常なリソースの数、現在のスコア、および最大スコアを取得します。 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. **[クエリの実行]** を選択します。


## <a name="tracking-your-secure-score-over-time"></a>セキュリティ スコアの経時的な追跡

### <a name="secure-score-over-time-report-in-workbooks-page"></a>ブック ページのセキュア スコア推移レポート

Security Center のブック ページには、サブスクリプションやセキュリティ コントロールなどのスコアを視覚的に追跡する目的で作成されたレポートが含まれています。 詳細については、「[Security Center データの豊富な対話型レポートを作成する](custom-dashboards-azure-workbooks.md)」を参照してください。

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Azure Security Center のブック ギャラリーからのセキュア スコア推移レポートのセクション":::

### <a name="power-bi-pro-dashboards"></a>Power BI Pro ダッシュボード

Pro アカウントを使用している Power BI ユーザーの場合は、 **[Secure Score Over Time]\(経時的なセキュリティ スコア\)** Power BI ダッシュボードを使用して、セキュリティ スコアを経時的に追跡し、変更がないか調査できます。

> [!TIP]
> このダッシュボードや、セキュリティ スコアをプログラムで操作する他のツールは、GitHub の Azure Security Center コミュニティの専用領域を参照してください: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

ダッシュボードには、セキュリティの状態を分析するために役立つ次の 2 つのレポートが含まれています。

- **[リソースの概要]** - リソースの正常性に関する概要データが提供されます。
- **[Secure Score Summary]\(セキュリティ スコアの概要\)** - スコアの進捗状況に関する集計データが提供されます。 スコアの変化を確認するには、[Secure score over time per subscription]\(サブスクリプションごとの経時的なセキュリティ スコア\) グラフを使用します。 スコアが大幅に変化した場合は、[detected changes that may affect your secure score]\(検出された変更の中でセキュリティ スコアに影響する可能性のあるもの\) の表を調べ、変更の原因となった可能性のある変更を確認します。 この表は、削除されたリソース、新しくデプロイされたリソース、または推奨事項のいずれかについてセキュリティの状態が変化したリソースを示しています。

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="経時的なセキュリティ スコアを追跡し、変更を調査するためのオプションの [Secure Score Over Time]\(経時的なセキュリティ スコア\) Power BI ダッシュボード":::


## <a name="next-steps"></a>次のステップ

この記事では、セキュア スコアにアクセスし、追跡する方法について説明しました。 関連資料については、次の記事をご覧ください。

- [推奨事項のさまざまな要素に関する詳細](security-center-recommendations.md)
- [推奨事項を修復する方法を確認する](security-center-remediate-recommendations.md)
- [セキュリティ スコアを使用してプログラムで作業するための GitHub ベースのツールを表示する](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)