---
title: Azure Cost Management と Billing を利用して想定外の料金を防止および分析する
description: Azure で想定外の料金が発生するのを防止する方法と、Azure アカウントのコスト追跡および管理機能を使用する方法について説明します。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 61071655cfeaebd5a7e32a97abf208d03322164f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689917"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Azure の課金とコスト管理で想定外の料金を防ぐ

Azure にサインアップしたら、支出を把握するために行えることがいくつかあります。

- サービスを追加する前に[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)や Azure 価格シートを使用することで、また、サービスを追加している間は Azure portal で推定コストを把握します。
- [予算](../costs/tutorial-acm-create-budgets.md)、[アラート](../costs/cost-mgt-alerts-monitor-usage-spending.md)、[コスト分析](../costs/quick-acm-cost-analysis.md)を使用してコストを監視します。
- 請求書に記載されている請求金額について、[詳しい使用状況ファイル](download-azure-invoice-daily-usage-date.md)と比較して確認します。
- [Billing](https://docs.microsoft.com/rest/api/billing/) API と [Consumption](https://docs.microsoft.com/rest/api/consumption/) API を使用して課金およびコストのデータを独自のレポート システムに統合します。
- マイクロソフト エンタープライズ契約 (EA)、クラウド ソリューション プロバイダー (CSP)、Azure スポンサー プランのお客様向けに別途用意されているリソースおよびツールを使用します。
- [Azure 無料アカウント](https://azure.microsoft.com/free/)で利用できる[最も人気の高い Azure サービスを 12 か月間無料](create-free-services.md)で活用します。 下記の推奨事項に加えて、[無料アカウントの課金の回避](avoid-charges-free-account.md)に関する記事もご覧ください。

Azure サブスクリプションを取り消す必要がある場合は、「[Azure サブスクリプションの取り消し](cancel-azure-subscription.md)」を参照してください。

## <a name="get-estimated-costs-before-adding-azure-services"></a>Azure サービスを追加する前にコストを見積もる

次のいずれかのツールを使用して、Azure サービスの使用にかかるコストを見積もります。
- Azure 料金計算ツール
- Azure 価格シート
- Azure portal

次のセクションの画像は、米ドルでの価格の例を示しています。

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>料金計算ツールを使用してオンラインでコストを見積もる

[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用して、追加したいサービスにかかる毎月の推定コストを把握します。 通貨を変更することにより、お住まいの地域の通貨で見積もり額を表示することができます。

![料金計算ツール メニューのスクリーンショット](./media/getting-started/pricing-calc.png)

ファースト パーティの Azure サービスについては、すべて推定コストを確認できます。 たとえば、次のスクリーンショットで、A1 Windows 仮想マシン (VM) を常に実行したままにしておく場合、コンピューティング時間での推定月額料金は 66.96 ドルになります。

![A1 Windows VM の推定月額料金を示す料金計算ツールのスクリーンショット](./media/getting-started/pricing-calcvm.png)

料金設定の詳細については、[料金に関する FAQ](https://azure.microsoft.com/pricing/faq/) を参照してください。 Azure 営業担当者と話したい場合は、FAQ ページの上部に表示されている電話番号までご連絡ください。

### <a name="view-and-download-azure-price-sheet"></a>Azure 価格シートを表示してダウンロードする

マイクロソフト エンタープライズ契約 (EA) または Microsoft 顧客契約 (MCA) を通じて Azure にアクセスできる場合は、お使いの Azure アカウントの価格シートを表示してダウンロードすることができます。 この価格シートは、すべての Azure サービスの価格が記載された Excel ファイルです。 詳細については、[Azure 価格の表示とダウンロード](ea-pricing.md)に関するページを参照してください。

### <a name="review-estimated-costs-in-the-azure-portal"></a>Azure portal で推定料金を検討する

サービスを追加している間は、1 か月あたりの推定コストを Azure portal で表示できます。 たとえば、Windows VM のサイズを選択すると、コンピューティング時間の推定月額料金が表示されます。

![例: 推定月額料金を示す A1 Windows VM](./media/getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Azure サービス使用時にコストを監視する
次のツールを使用して、コストを監視できます。

- 予算とコストのアラート
- コスト分析

### <a name="track-costs-with-budgets-and-cost-alerts"></a>予算とコストのアラートを使用してコストを追跡する

[予算](../costs/tutorial-acm-create-budgets.md)を作成して、コストを管理し、異常な支出や浪費について、関係者に自動的に通知する[アラート](../costs/cost-mgt-alerts-monitor-usage-spending.md)を作成できます。

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> コスト分析を使用してコストを調査および分析する

Azure サービスを実行状態に移した後は、定期的にコストをチェックして Azure の利用状況を追跡してください。 コスト分析を使用することで、Azure の利用に関するコストの発生源を把握することができます。

1. [Azure portal で [コストの管理と請求] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade)に移動します。

2. 画面の左側の **[コスト分析]** をクリックすると、サービス、場所、サブスクリプションなど、さまざまな角度から最新のコスト明細が表示されます。 サービスを追加するか購入したら、データが表示されるまで 24 時間待ちます。 既定では、コスト分析には、閲覧しているスコープのコストが表示されます。 たとえば、以下のスクリーンショットでは、Contoso の課金アカウントのコストが表示されています。 コスト分析で別のスコープに切り替えるには、[スコープ] ピルを使用します。 スコープの詳細については、「[スコープを理解して使用する](../costs/understand-work-scopes.md#scopes)」を参照してください。

    ![Azure Portal のコスト分析ビューのスクリーンショット](./media/getting-started/cost-analysis.png)

4. タグ、リソースの種類、期間など、さまざまなプロパティでフィルター処理することができます。 **[フィルターの追加]** をクリックして、プロパティのフィルターを追加し、フィルター処理する値を選択します。 コンマ区切り値 (.csv) ファイルにビューをエクスポートするには、 **[エクスポート]** を選択します。

5. さらに、グラフのラベルをクリックすると、そのラベルの日単位の支出履歴を表示できます。 たとえば、次のスクリーンショットでは、仮想マシンをクリックすると、VM の実行にかかっている 1 日あたりのコストが表示されます。

    ![Azure Portal の支出の履歴ビューのスクリーンショット](./media/getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>コストの最適化と削減
コスト管理の原則をよく理解していない場合は、「[Azure Cost Management でクラウド投資を最適化する方法](../costs/cost-mgt-best-practices.md)」をお読みください。

Azure portal で、VM の自動シャットダウンと Advisor の推奨事項を使用して、Azure のコストを最適化し、削減することもできます。

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>VM の自動シャットダウンなどのコスト削減機能を検討する

シナリオによっては、Azure portal で VM の自動シャットダウンを構成できます。 詳しくは、[Azure Resource Manager を使用した VM の自動シャットダウン](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)に関する記事をご覧ください。

![ポータルの自動シャットダウン オプションのスクリーンショット](./media/getting-started/auto-shutdown.png)

自動シャットダウンは、電源オプションを使用して VM 内でシャットダウンする場合とは異なります。 自動シャットダウンでは、VM を停止して割り当てを解除することで課金を停止します。 詳細については、[Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) および [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の料金に関する FAQ で VM の状態についての質問をご覧ください。

開発およびテスト環境のコスト削減機能については、[Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) のページをご覧ください。

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Azure Advisor の推奨事項を有効にして確認する

[Azure Advisor](../../advisor/advisor-overview.md) を使うと、使用量の少ないリソースを特定してコストを削減できます。 Azure portal で **Advisor** を検索します。

![Azure Portal の [Azure Advisor] ボタンのスクリーンショット](./media/getting-started/advisor-button.png)

左側から **[コスト]** を選択します。 アクションにつながる推奨情報が **[コスト]** タブに表示されます。

![Advisor のコストに関する推奨事項の例を示すスクリーンショット](./media/getting-started/advisor-action.png)

コスト節約のための Advisor の推奨事項に関するガイド付きチュートリアルについては、「[推奨事項に従ってコストを最適化する](../costs/tutorial-acm-opt-recommendations.md)」をご覧ください。


## <a name="integrate-with-billing-and-consumption-apis"></a>Billing および Consumption API と統合する

Azure の [Billing](https://docs.microsoft.com/rest/api/billing/) と [Consumption](https://docs.microsoft.com/rest/api/consumption/) API を使用して、請求データとコスト データをプログラムから入手することもできます。 課金対象の使用状況を取得するには、RateCard API と Usage API を組み合わせて使用します。 詳細については、「[Microsoft Azure リソースの消費を把握する](usage-rate-card-overview.md)」をご覧ください。

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> その他のリソースと特殊なケース

### <a name="ea-csp-and-sponsorship-customers"></a>EA、CSP、およびスポンサープランのお客様
使用を開始するには、アカウント マネージャーまたは Azure パートナーに連絡してください。

| プラン | リソース |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [EA ポータル](https://ea.azure.com/)、[ヘルプ ドキュメント](https://ea.azure.com/helpdocs)、および [Power BI レポート](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | プロバイダーに連絡してください |
| Azure スポンサー プラン | [スポンサー プラン ポータル](https://www.microsoftazuresponsorships.com/) |

大規模な組織の IT を管理している場合は、[Azure エンタープライズ スキャフォールディング](/azure/architecture/cloud-adoption-guide/subscription-governance)に関する記事と、[エンタープライズ IT に関するホワイト ペーパー](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (.pdf をダウンロード、英語のみ) を読むことをお勧めします。

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a>Azure portal での Enterprise Agreement のコスト ビュー

Enterprise コスト ビューは、現在、パブリック プレビューの中にあります。 注意する項目:

- サブスクリプションのコストは、使用量に基づいて計算されており、前払い額、超過、含まれる数量、調整、および税は含まれていません。 実際の料金は、登録レベルで計算されます。
- Azure portal に表示される金額が、エンタープライズ ポータルに表示されるものとは異なっている場合があります。 エンタープライズ ポータルでの更新による変更が Azure portal に表示されるまで、数分かかる可能性があります。
- コストが表示されない場合は、次の理由のいずれかが原因として考えられます。
    - サブスクリプション レベルでアクセス許可がない。 エンタープライズ コスト ビューを表示するには、課金データ閲覧者、閲覧者、共同作業者、所有者のいずれかのサブスクリプション レベルである必要があります。
    - お客様はアカウント所有者であり、[AO ビューの請求金額] 設定が登録管理者によって無効にされている。  登録管理者に問い合わせて、コストにアクセスできるように設定してもらってください。
    - お客様は部門管理者であり、 **[DA ビューの請求金額]** 設定が登録管理者によって無効にされている。  登録管理者に問い合わせて、アクセスできるように設定してもらってください。
    - チャネル パートナーを通して Azure を購入したが、パートナーが価格情報を公開していない。  
- Enterprise Portal でコストのアクセスに関連する設定を更新した場合、変更が Azure portal に表示されるまで数分の遅延があります。
- 使用制限と請求書ガイダンスは、EA サブスクリプションには適用されません。

### <a name="check-your-subscription-and-access"></a>サブスクリプションとアクセスを確認する

コストを表示するには、コストまたは課金の情報へのアカウント レベルまたはサブスクリプション レベルのアクセス権が必要です。 課金アカウントの種類によってアクセス権は異なります。 課金アカウントの詳細および課金アカウントの種類の確認については、「[Azure portal での課金アカウントの表示](view-all-accounts.md)」を参照してください。

Microsoft Online Services プログラム (MOSP) の課金アカウント経由で Azure にアクセスできる場合は、「[Asure の課金情報へのアクセスの管理](manage-billing-access.md)」を参照してください。

マイクロソフト エンタープライズ契約 (EA) の課金アカウント経由で Azure にアクセスできる場合は、「[Azure の Azure Enterprise Agreement 管理者ロールを理解する](understand-ea-roles.md)」を参照してください。

Microsoft 顧客契約 (MCA) の課金アカウント経由で Azure にアクセスできる場合は、「[Azure での Microsoft 顧客契約の管理ロールを理解する](understand-mca-roles.md)」を参照してください。

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>サービス インシデントに対するサービス レベル アグリーメントの控除を要求する

サービス レベル アグリーメント (SLA) では、稼働時間と接続に関する Microsoft の確約内容について説明しています。 Azure サービスで稼働時間や接続に影響を与える問題 (しばしば "*停止*" と呼ばれる) が発生すると、サービス インシデントが報告されます。 SLA に記載されている各サービスのサービス レベルが達成および維持されていない場合は、月額サービス料金の一部が控除対象になる場合があります。

控除を要求するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。 複数のアカウントをお持ちの場合は、Azure のダウンタイムの影響を受けたアカウントを必ず使用してください。
2. 新しいサポート リクエストを作成します。
3. **[問題の種類]** で **[課金]** を選択します。
4. **[問題の種類]** で **[払戻要求]** を選択します。
5. SLA の控除を求めていることを指定する詳細を追加し、日付、時刻、タイムゾーン、および影響を受けたサービス (VM、Web サイトなど) を記載します。
6. 連絡先情報の詳細を確認し、 **[作成]** を選択して要求を送信します。

SLA のしきい値は、サービスによって異なります。 たとえば、SQL Web サービス レベルの SLA は 99.9%、VM の SLA は 99.95%、SQL Standard サービス レベルの SLA は 99.99% です。

一部のサービスには、SLA を適用するための前提条件があります。 たとえば、仮想マシンでは、2 つ以上のインスタンスが同じ可用性セットにデプロイされている必要があります。

詳細については、「[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」および「[Azure サービスの SLA 概要](https://azure.microsoft.com/support/legal/sla/summary/)」ドキュメントを参照してください。

## <a name="analyze-unexpected-charges"></a>想定外の料金を分析する

組織向けに構築されたクラウド リソース インフラストラクチャは、複雑になりがちです。 Azure にはさまざまな種類のリソースがあり、その多くはそれぞれ異なる料金が設定されています。 Azure リソースは、組織内のさまざまなチームによって所有され、各種リソースに適用される課金モデルの種類も多様です。 そうした料金について理解を深めるために、まず、以降のセクションで取り上げる 1 つまたは複数の戦略を使用して分析を行ってみましょう。

### <a name="review-your-invoice-and-identify-the-resource-that-is-responsible-for-the-charge"></a>請求書を確認し、料金の対象となるリソースを特定する

料金に関連付けられているリソースを特定する際に使用できる手法やツールは、Azure サービスの購入方法から明らかにすることができます。 自分に該当する手法を明らかにするには、まず[自分の Azure プランの種類を特定](../costs/understand-cost-mgt-data.md#determine-your-offer-type)します。 次に、[サポートされている Azure プラン](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers)の一覧で、該当する顧客カテゴリを特定します。

該当する顧客タイプに基づいて請求書を確認する方法を示した詳細な手順が、以下の各記事で提供されています。 それぞれの記事には、特定の請求期間における使用状況とコスト明細を含んだ CSV ファイルのダウンロード手順が記載されています。

- [従量課金制請求書の確認プロセス](../understand/review-individual-bill.md#compare-invoiced-charges-with-usage-file)
- [マイクロソフト エンタープライズ契約の請求書の確認プロセス](../understand/review-enterprise-agreement-bill.md)
- [Microsoft 顧客契約の確認プロセス](../understand/review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Microsoft Partner Agreement の確認プロセス](../understand/review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Azure の請求書では、月の料金が "_メーター_" ごとに集計されます。 メーターは、リソースの使用状況を経時的に追跡したり、請求書の計算を行ったりする目的で使用されます。 仮想マシンなど、Azure リソースを 1 つ作成すると、そのリソースに対して 1 つまたは複数のメーター インスタンスが作成されます。

分析対象の請求書に記載されている _MeterName_ に基づいて、使用状況の CSV ファイルをフィルター処理すると、そのメーターに該当するすべての明細項目が表示されます。 その明細項目の _InstanceID_ は、料金の発生元となった実際の Azure リソースに対応します。

該当するリソースが見つかったら、Azure Cost Management のコスト分析を使用して、リソースに関連するコストを詳しく分析できます。 コスト分析の使用について詳しくは、[コスト分析の開始](../costs/quick-acm-cost-analysis.md)に関するページを参照してください。

### <a name="review-invoiced-charges-in-cost-analysis"></a>[コスト分析] で請求された料金を確認する

Azure portal で請求書の詳細を表示するには、分析する請求書に関連付けられているスコープの [コスト分析] に移動します。 **[請求書の詳細]** ビューを選択します。 請求書の詳細には、請求書に記載される料金が表示されます。

[![請求書の詳細を示す例](./media/getting-started/invoice-details.png)](./media/getting-started/invoice-details.png#lightbox)

請求書の詳細を表示すると、予期しないコストがかかるサービスを特定することや、[コスト分析] のリソースに直接関連付けられているリソースを確認することができます。 たとえば、Virtual Machines サービスの料金を分析する場合は、 **[累積コスト]** ビューに移動します。 次に、細分性を **[Daily]\(日単位\)** に設定し、 **[Service name: Virtual machines]\(サービス名: 仮想マシン\)** で料金をフィルター処理し、 **[リソース]** で料金をグループ化します。

[![仮想マシンの累積コストを示す例](./media/getting-started/virtual-machines.png)](./media/getting-started/virtual-machines.png#lightbox)


### <a name="identify-spikes-in-cost-over-time"></a>一定期間におけるコストの急増を特定する

最近発生したどのコストによって請求料金に変化が生じたのかが、明らかでない場合もあります。 どのような変化が起こったのかは、コスト分析を使用して、[日単位または月単位の経時的なコスト内訳を確認](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month)することで把握できます。 ビューの作成後、 **[サービス]** または **[リソース]** で料金をグループして変化を特定します。 データを見やすくするために、ビューを**折れ線**グラフに変更することもできます。

![コスト分析でコストの経時変化を示している例](./media/getting-started/costs-over-time.png)

### <a name="determine-resource-pricing-and-understand-its-billing-model"></a>リソースの価格を特定してその課金モデルを把握する

1 つのリソースから、Azure の複数の製品やサービスにまたがって料金が発生することもあります。 Azure サービスごとの価格について詳しくは、[製品別の Azure の価格](https://azure.microsoft.com/pricing/#product-pricing)に関するページをご覧ください。 たとえば、Azure に作成された 1 つの仮想マシン (VM) に対して、その使用状況を追跡するために次のメーターが作成される場合があります。 価格はそれぞれ異なる場合があります。

- コンピューティング時間
- IP アドレス時間
- データ転送 (受信)
- データ転送 (送信)
- Standard マネージド ディスク
- Standard マネージド ディスク操作
- Standard IO ディスク
- Standard IO - ブロック BLOB (読み取り)
- Standard IO - ブロック BLOB (書き込み)
- Standard IO - ブロック BLOB (削除)

VM が作成されると、各メーターが使用状況レコードの出力を開始します。 この使用状況とメーターの価格が Azure の測定システムで追跡されます。 請求の計算に使用されたメーターは、使用状況 CSV ファイルで確認できます。

### <a name="find-the-people-responsible-for-the-resource-and-engage-them"></a>リソースの担当者を探して協力を促す

リソースに対して行われた変更については、特定のリソースを担当するチームが把握していることは少なくありません。 料金が発生した理由を特定する際には、そうしたチームに協力を促すことが効果的です。 たとえば所有チームが最近、リソースを作成したり、その SKU を更新 (それによってリソースのレートが変化) したり、コードの変更によってリソースに対する負荷を増やしたりした可能性があります。 リソースの所有者を特定する詳しい方法については、引き続き以降のセクションをお読みください。

#### <a name="analyze-the-audit-logs-for-the-resource"></a>リソースの監査ログを分析する

リソースを表示するアクセス許可があれば、その監査ログにアクセスすることができます。 そのログを確認して、リソースへの直近の変更を担当したユーザーを見つけてください。 詳細については、「[Azure アクティビティ ログ イベントを表示して取得する](../../azure-monitor/platform/activity-log-view.md)」を参照してください。

#### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>リソースの親スコープに対するユーザーのアクセス許可を分析する

通常、サブスクリプションまたはリソース グループへの書き込みアクセス権限を持つ人物は、作成されたリソースについての情報を把握しています。 その人物ならリソースの目的を説明したり、事情を知っている担当者を紹介したりできるはずです。 サブスクリプション スコープのアクセス許可を持つ人物を特定する方法については、「[ロールの割り当てを表示する](../../role-based-access-control/check-access.md#view-role-assignments)」を参照してください。 リソース グループについても同じようなプロセスを使用できます。

### <a name="get-help-to-identify-charges"></a>料金を特定するためにサポートを要請する

前述の戦略を使用しても、料金を請求された理由がわからない場合や、課金の問題について別途サポートが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ
- 予算オーバーを防ぐために、[使用制限](spending-limit.md)の使い方を確認します。
- [Azure コストの分析](../costs/quick-acm-cost-analysis.md)を開始します。
