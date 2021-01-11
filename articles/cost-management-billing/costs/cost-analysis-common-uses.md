---
title: Azure Cost Management での一般的なコスト分析の使用
description: この記事では、Azure Cost Management で一般的なコスト分析タスクの結果を取得する方法について説明します。
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: c03bf5e52ae7f6b259c7b744e6033d760af23dd0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683676"
---
# <a name="common-cost-analysis-uses"></a>一般的なコスト分析の使用

多くの場合、Azure Cost Management のユーザーは、他の多くのユーザーが持つ疑問に対する答えを必要としています。 この記事では、Cost Management での一般的なコスト分析タスクの結果を取得する手順について説明します。

## <a name="view-forecasted-costs"></a>予測コストを表示する

予測コストは、面グラフと積み上げ縦棒ビューのコスト分析領域に表示されます。 予測は、リソースの使用履歴に基づいています。 リソースの使用に対する変更は、予測コストに影響します。

Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。

既定のビューでは、最上位のグラフに、実績/償却コストと予測コストのセクションがあります。 グラフの純色は、実際の/償却コストを示しています。 影付きの色は、予測コストを示します。

[![予測コスト](./media/cost-analysis-common-uses/enrollment-forecast.png)](./media/cost-analysis-common-uses/enrollment-forecast.png#lightbox)

## <a name="view-forecasted-costs-grouped-by-service"></a>サービス別にグループ化された予測コストを表示する

既定のビューには、サービスによってグループ化された予測コストが表示されません。そのため、グループを選択して追加する必要があります。

Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。

**[グループ化]**  >  **[サービス名]** を選択します。

ビューには、各サービスのコストがグループ化されて表示されます。 予測コストは、サービスごとに計算されません。 これは、すべてのサービスの **[合計]** に対して推定されています。

[![グループ化された予測コスト](./media/cost-analysis-common-uses/forecast-group-by-service.png)](./media/cost-analysis-common-uses/forecast-group-by-service.png#lightbox)

## <a name="view-forecasted-costs-for-a-service"></a>サービスの予測コストを表示する

1 つのサービスに限定された予測コストを表示できます。 たとえば、仮想マシンだけの予測コストを表示することができます。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. **[フィルターの追加]** を選択し **[サービス名]** を選択します。
1. **選択**の一覧で、サービスを選択します。 たとえば、 **[仮想マシン]** を選択します。

選択に対する実際のコストと予想コストを確認します。

ビューにさらに多くのカスタマイズを追加できます。

1. **[メーター]** の2 番目のフィルターを追加し、選択したサービス名の下で個々の種類のメーターをフィルター処理するための値を選択します。
1. **[リソース]** でグループ化して、コストを発生させる特定のリソースを確認します。 予測コストは、サービスごとに計算されません。 これは、すべてのリソースの **[合計]** に対して推定されています。

[![サービスの予測コスト](./media/cost-analysis-common-uses/forecast-by-service.png)](./media/cost-analysis-common-uses/forecast-by-service.png#lightbox)

## <a name="view-your-azure-and-aws-costs-together"></a>Azure と AWS のコストをまとめて表示する  

Azure と AWS のコストをまとめて表示するには、Azure で管理グループのスコープを使用します。

1. 管理グループを作成するか、既存のものを選択します。
1. 既存の必要な Azure サブスクリプションを管理グループに割り当てます。
1. コネクタのリンクされたアカウントに "*同じ*" 管理グループを割り当てます。
1. コスト分析に移動して、 **[累積コスト]** を選択します
1. **[グループ化]**  -  **[プロバイダー]** を選択します。

## <a name="view-cost-breakdown-by-azure-service"></a>Azure サービス別のコストの内訳を表示する

Azure サービス別にコストを表示すると、インフラストラクチャで最もコストがかかっている部分をよりよく把握するのに役立ちます。 たとえば、VM のコンピューティング コストは小さい場合があります。 しかし、VM から生成される情報の量によって、かなりのネットワーク コストが生じる可能性があります。 必要に応じてサービスの使用を調整できるように、Azure サービスの主要なコスト要因を把握することが重要です。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. **[サービスごとのコスト]** を選択し、 **[サービス レベル]** でグループ化します。
1. ビューを **[テーブル]** に変更します。

[![Azure サービス別のコストの内訳](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="review-invoiced-charges-in-cost-analysis"></a>[コスト分析] で請求された料金を確認する

Azure portal で請求書の詳細を表示するには、分析する請求書に関連付けられているスコープの [コスト分析] に移動します。 **[請求書の詳細]** ビューを選択します。 請求書の詳細には、請求書に記載される料金が表示されます。

[![請求書の詳細を示す例](./media/cost-analysis-common-uses/invoice-details.png)](./media/cost-analysis-common-uses/invoice-details.png#lightbox)

請求書の詳細を表示すると、予期しないコストがかかるサービスを特定することや、[コスト分析] のリソースに直接関連付けられているリソースを確認することができます。 たとえば、Virtual Machines サービスの料金を分析する場合は、 **[累積コスト]** ビューに移動します。 次に、細分性を **[Daily]\(日単位\)** に設定し、 **[Service name: Virtual machines]\(サービス名: 仮想マシン\)** で料金をフィルター処理し、 **[リソース]** で料金をグループ化します。

[![仮想マシンの累積コストを示す例](./media/cost-analysis-common-uses/virtual-machines.png)](./media/cost-analysis-common-uses/virtual-machines.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Azure リソース別のコストの内訳を表示する

サービスは、Azure リソースを使用して構築されています。 リソースに基づいてコストを確認すると、主要なコスト要因をすばやく特定するのに役立ちます。 サービスに高コストのリソースがある場合は、コストを削減するための変更を検討してください。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. **[リソースごとのコスト]** を選択します。
1. ビューを **[テーブル]** に変更します。

[![Azure リソース別のコストの内訳を表示する](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>選択したディメンション別のコストの内訳を表示する

ディメンションを使用すると、料金に表示されるさまざまなメタデータ値に基づいてコストを整理できます。 たとえば、場所別にコストをグループ化することができます。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. **[Group by]\(グループ化\)** フィルターを選択します。  
    [![グループ化項目を選択する](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. 必要に応じて、後で使用するためにビューを保存します。
1. グラフの下にある円グラフをクリックすると、より詳細なデータが表示されます。  
    [![選択したディメンション別のコストの内訳を表示する](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>1 日あたりまたは月別のコストを表示する

日単位または月単位のコストを確認すると、1 週間または 1 年間のうちでコストが高くなる時期があるかどうかをよりよく把握できます。 休日期間により多くの顧客トラフィックがある場合、それに対応して Azure のコストが増加していますか。 金曜日は月曜日よりもコストがかかりますか。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. **[細分性]** を **[Monthly]\(毎月\)** または **[Daily]\(毎日\)** に設定します。

[![1 日あたりのコストを表示する](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>スポット VM の料金を表示する

中断があってもかまわないワークロードには、スポット VM を利用することで大きなコスト削減効果が得られます。 ワークロードは、使用されていない Azure 容量で実行されます。 スポット VM は、無効になる可能性が随時伴うため、大幅な割引が適用されます。 スポット VM の料金を表示するには、次の手順に従います。

1. Azure portal で、スコープの [コスト分析] に移動します。 たとえば、 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** の順に移動します。
2. "**価格モデル: スポット**" のフィルターを追加します。

![スポット VM のフィルターを示す例](./media/cost-analysis-common-uses/spot-vm-filter.png)

"価格モデル" ディメンションは、オンデマンド料金と予約料金を表示する際にも使用されます。

## <a name="view-your-reservation-charges"></a>予約料金を表示する

予約インスタンスには、Azure に関するコストを削減する効果があります。 予約では、一定期間にわたる特定のリソース数の料金を前払いで納めることになります。 請求書に表示される料金は、コスト分析によって示されます。 料金は、実績コストとして表示されるか、または予約期間にわたって分割されます。

1. Azure portal で、スコープの [コスト分析] に移動します。 たとえば、 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** の順に移動します。
1. "**価格モデル: 予約**" のフィルターを追加します。
1. **[Scope]\(スコープ\)** で表示されるコストの横にある下矢印記号をクリックし、 **[実際のコスト]** と **[分散コスト]** のどちらかのメトリックを選択します。

![コストのメトリックを選択する](./media/cost-analysis-common-uses/metric-cost.png)

予約料金のデータがどのように表示されるかは、メトリックごとに異なります。

**[実際のコスト]** - 請求書の記載どおりに購入額が表示されます。 たとえば、1 年分 1,200 ドルの予約を 1 月に購入した場合、コスト分析には、その予約に関する 1,200 ドルのコストが 1 月に表示されます。 その年の他の月には、予約コストは表示されません。 実績コストを VM でグループ化した場合、特定の月に予約の特典が適用された VM は、その月のコストがゼロになります。

**[分散コスト]** - 予約の購入額が償却コストとして、予約期間にわたって分割されます。 先ほどと同じ例で、1 年分 1,200 ドルの予約を 1 月に購入した場合、コスト分析には、その年を通じて毎月 100 ドルのコストが表示されます。 この例で、コストを VM でグループ化した場合、予約の特典が適用された各 VM に帰属するコストが表示されます。

## <a name="view-your-reservation-utilization"></a>予約の使用率を表示する

予約を購入した後は、その使用率を追跡して、支払いの対象を把握することが大切です。 たとえば 1 年分 10 台の VM を購入し、うち 5 台しか使用していなかったとしたら、実質的に購入額の半分は無駄になっていることになります。 使用率は、次の 2 とおりの方法で評価できます。

### <a name="view-unused-ri-costs-in-cost-analysis"></a>使用されていない RI のコストをコスト分析で表示する

予約購入コストが毎月どの程度無駄になっているかを明らかにするには、次の手順に従います。

1. Azure portal で、予約が適用されているスコープのコスト分析に移動します。 たとえば、 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** の順に移動します。
1. "**価格モデル: 予約**" のフィルターを追加します。
1. **[分散コスト]** ビューを選択します。
1. 細分性を **[Monthly]\(毎月\)** に設定します。
1. 期間を現在の年または予約期間に設定します。
1. グラフの種類を **[縦棒 (積み上げ)]** に設定します。
1. 料金を **[料金の種類]** でグループ化します。
1. `unusedreservation` の値の結果を確認します。

[![予約の使用状況を示す例](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>予約の使用率を表示する

詳しい手順については、「[予約の使用を最適化する](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use)」を参照してください。

## <a name="view-costs-for-a-specific-tag"></a>特定のタグのコストを表示する

多くの Azure ユーザーは、料金をより適切に分類するために、コスト センターや開発環境 (運用およびテスト) などのリソースにタグを適用します。 コスト分析では、タグはディメンションとして表示されます。 ディメンションを使用すると、カスタムのタグ付けの分類に関する洞察を得ることができます。

タグのサポートは、タグがリソースに適用された "*後*" にレポートされた使用状況に適用されます。 タグは、過去のコスト データには適用されません。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. タグに対して **[Group by]\(グループ化\)** を選択します。

[![特定のタグのコストを表示する](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>使用量の詳細のダウンロード

使用状況の詳細レポート ファイル (CSV 形式) には、請求書に対して計上されたすべての料金の内訳が記載されています。 このレポートを使用して、請求書と比較して理解を深めることができます。 請求書に記載されている各請求料金は、使用状況レポートの料金の内訳に対応しています。

1. Azure portal で、課金アカウントまたはサブスクリプションの **[使用状況と料金]** タブに移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[請求]**  >  **[使用状況と料金]** 。
1. ダウンロード元の行項目を選択し、ダウンロード記号をクリックします。  
    [![使用状況と料金をダウンロードする](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  ダウンロードする使用状況ファイルを選択します。  
    ![ダウンロードする使用状況ファイルを選択する](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>毎月の EA コストの内訳を表示する

EA 加入契約では、組織全体のコストが計上されます。 コストがどのように計上され、一定期間にわたって請求されるかを把握すると、適切な利害関係者と協力して、コストが責任を持って管理されるようにするのに役立ちます。

コストは、アクティブな加入契約に対してのみ表示されます。 加入契約 (非アクティブ) を新しいもの (アクティブ) に転送した場合、以前の加入契約のコストは Cost Management に表示されません。


1. Azure portal で、 **[コスト管理と請求]**  >  **[概要]** に移動します。
1. 今月の **[内訳]** をクリックし、年額コミットメントのバーンダウンを表示します。  
    [![EA コストの概要 - 内訳の概要](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  **[使用状況と料金]** タブをクリックし、選択した期間の前月の内訳を表示します。  
    [![[使用状況と料金] タブ](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>期間別の加入契約の月間コストを表示する

特定の期間のコスト傾向と請求金額を把握するには、加入契約の月額コストのグラフィカル ビューを使用します。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. 加入契約を選択し、加入契約の期間を設定します。
1. 細分性を月単位に設定し、ビューを **[縦棒     (積み上げ)]** に設定します。

より詳細に分析するには、データをグループ化し、フィルターに掛けることができます。

[![期間別の毎月の加入契約コスト](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>EA 加入契約の累積コスト

一定期間にわたる正味累計料金を表示して、指定した期間の組織の総支出を把握します。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. 加入契約を選択し、現在の累積コストを表示します。

[![加入契約の累積コスト](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>次のステップ
- Cost Management の最初のクイック スタートをまだ完了していない場合は、[コスト分析の開始](quick-acm-cost-analysis.md)に関する記事をご覧ください。
- [Cost Management のドキュメント](../index.yml)をお読みください。
