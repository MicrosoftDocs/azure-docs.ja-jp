---
title: "Azure の課金とコスト管理に関するガイダンス | Microsoft Docs"
description: "課金を最適化するためのベスト プラクティスと最初に行うべきことについて説明します。"
services: 
documentationcenter: 
author: jlian
manager: mattstee
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: jlian
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: af53cea83d2e348b2c73e56cf4b6ce5a875bce18
ms.lasthandoff: 03/22/2017


---
# <a name="prevent-unexpected-costs-with-azure-billing-and-cost-management"></a>Azure の課金とコスト管理で予想外のコストを防ぐ

Azure にサインアップしたら、支出を把握するために行えることがいくつかあります。 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) で、サブスクリプションを選択するときに、現在のコストの内訳とバーン レートを確認できます。 また、[過去の請求書や詳細な使用状況ファイルをダウンロード](billing-download-azure-invoice-daily-usage-date.md)することもできます。 さまざまなプロジェクトやチームで使用したリソースのコストをグループ化する場合は、[リソースのタグ付け](../azure-resource-manager/resource-group-using-tags.md)を検討します。 使用したいレポート システムが組織にある場合は、[課金 API](billing-usage-rate-card-overview.md) を確認します。 

毎日の使用状況の詳細については、「[Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)」をご覧ください。

Enterprise Agreement (EA)、クラウド ソリューション プロバイダー (CSP)、または Azure スポンサー プランを介したサブスクリプションの場合は、この記事の機能の多くが適用されません。 代わりに、コスト管理に使用できる別のツール セットが用意されています。 「[EA、CSP、およびスポンサー プラン向けのその他のリソース](#other-offers)」を参照してください。

サブスクリプションが無料試用版、[Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)、Azure イン オープン プラン (AIO)、または BizSpark の場合は、サブスクリプションが無効化されないように[使用制限](#spending-limit)を確認してください。 

## <a name="day-0-before-you-add-azure-services"></a>0 日目: Azure サービスを追加する前

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>料金計算ツールを使用してオンラインでコストを見積もる

関心があるサービスの月額料金を見積もるには、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)と[総保有コスト計算ツール](https://aka.ms/azure-tco-calculator)を確認します。 たとえば、A1 Windows 仮想マシン (VM) を常に実行したままにしておく場合、コンピューティング時間での推定月額料金は 66.96 米ドルになります。

![A1 Windows VM の推定月額料金が 66.96 米ドルであることを示す料金計算ツールのスクリーンショット](./media/billing-getting-started/pricing-calc.PNG)

詳細については、[料金に関する FAQ](https://azure.microsoft.com/pricing/faq/) をご覧ください。 直接問い合わせる場合は、1-800-867-1389 までご連絡ください。

### <a name="check-your-subscription-and-access"></a>サブスクリプションとアクセスを確認する

コストを表示するには、[サブスクリプション レベルのアクセス権](../active-directory/role-based-access-control-configure.md)が必要ですが、[アカウント センター](https://account.windowsazure.com/Home/Index)にアクセスし、課金情報を変更したり、サブスクリプションを管理したりできるのはアカウント管理者だけです。 アカウント管理者は、サインアップ プロセスを完了したユーザーです。 詳細については、「[Azure 管理者ロールを追加または変更する方法](billing-add-change-azure-subscription-administrator.md)」をご覧ください。

自分がアカウント管理者かどうかを確認するには、[Azure Portal の [サブスクリプション] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動し、アクセスできるサブスクリプションの一覧を確認します。 **[自分の役割]** を確認します。 ここに *[アカウント管理者]* と表示されていれば問題ありません。 *[所有者]* などの他の役割が表示されている場合は、すべての権限を持っているわけではありません。

![Azure Portal の [サブスクリプション] ビューに表示された役割のスクリーンショット](./media/billing-getting-started/sub-blade-view.PNG)

アカウント管理者でない場合、別のユーザーが [Azure Active Directory のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md) (RBAC) 使用して部分的なアクセス権を付与していると考えられます。 サブスクリプションを管理し、課金情報を変更するには、[アカウント管理者を見つけて](billing-subscription-transfer.md#whoisaa)、これらのタスクの実行または[サブスクリプションの転送](billing-subscription-transfer.md)を依頼します。

組織にアカウント管理者が存在せず、課金を管理する必要がある場合は、[サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 

### <a name="spending-limit"></a>使用制限が有効になっているかどうかを確認する

クレジットを使用するサブスクリプションがある場合、使用制限が既定で有効になっています。 この場合、すべてのクレジットを使い果たすと、クレジット カードに課金されなくなります。 Azure プランの一覧と使用制限の提供状況については、[こちら](https://azure.microsoft.com/support/legal/offer-details/)をご覧ください。

ただし、使用制限に達すると、サービスは無効になります。 つまり、VM の割り当てが解除されます。 サービスのダウンタイムを回避するには、使用制限を無効にする必要があります。 超過分は記録されているクレジット カードに課金されます。 

使用制限が有効になっているかどうかを確認するには、[アカウント センターの [サブスクリプション] ビュー](https://account.windowsazure.com/Subscriptions)に移動します。 使用制限が有効になっている場合、次のようなバナーが表示されます。

![使用制限が有効になっていることに関するアカウント センターの警告を示すスクリーンショット](./media/billing-getting-started/spending-limit-banner.PNG)

バナーをクリックし、画面の指示に従って使用制限を削除します。 サインアップ時にクレジット カード情報を入力しなかった場合、使用制限を削除するには、この情報を入力する必要があります。 詳細については、「[Azure 使用制限 - しくみと有効化または削除する方法](https://azure.microsoft.com/pricing/spending-limits/)」をご覧ください。

### <a name="set-up-billing-alerts"></a>課金アラートのセットアップ

使用料金が指定した金額を超えたときに電子メールを受け取るには、課金アラートを設定します。 毎月のクレジットがある場合は、指定した金額まで使用したときのアラートを設定します。 詳細については、「[Microsoft Azure サブスクリプションの課金アラートの設定](billing-set-up-alerts.md)」を参照してください。

![課金アラートの電子メールのスクリーンショット](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> この機能はまだプレビュー段階であるため、使用状況を定期的に確認してください。

最初のアラートのガイドラインとして、料金計算ツールで取得したコスト見積もりを使用することをお勧めします。

### <a name="understand-limits-and-quotas-for-your-subscription"></a>サブスクリプションの制限とクォータの理解

各サブスクリプションには、CPU コア数、IP アドレスなどに既定の制限があります。 これらの制限を考慮してください。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。 上限またはクォータの引き上げを希望する場合は、[サポートに連絡](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)してください。

## <a name="day-1-as-you-add-services"></a>1 日目: サービスを追加したとき

### <a name="review-the-estimated-cost-in-the-portal"></a>ポータルで推定コストを確認します。

通常、Azure Portal でサービスを追加すると、類似する推定月額料金を示すビューが表示されます。 たとえば、Windows VM のサイズを選択すると、コンピューティング時間の推定月額料金が表示されます。

![例: A1 Windows VM の推定月額料金は 66.96 米ドル](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="tags"></a>リソースにタグを追加して課金データをグループ化する

タグを使用して、サポートされているサービスの課金データをグループ化できます。 たとえば、さまざまなチーム用に複数の VM を実行している場合、タグを使用して、コスト センター (HR、マーケティング、財務) または環境 (運用、運用前、テスト) でコストを分類できます。 

![ポータルでのタグの設定を示すスクリーンショット](./media/billing-getting-started/tags.PNG)

タグはさまざまなコスト レポート ビューのあらゆる場所に表示されます。 たとえば、最初の請求期間が終了すると、[コスト分析ビュー](#costs)にタグがすぐに表示され、[詳細な使用状況 .csv](#invoice-and-usage) ファイルにも表示されます。

詳細については、 [タグを使用した Azure リソースの整理](../azure-resource-manager/resource-group-using-tags.md)に関するページを参照してください。

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>VM の自動シャットダウンなどのコスト削減機能を有効にすることを検討する

シナリオによっては、Azure Portal で VM の自動シャットダウンを構成できます。 詳細については、[Azure Resource Manager を使用した VM の自動シャットダウン](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)に関するブログをご覧ください。

![ポータルの自動シャットダウン オプションのスクリーンショット](./media/billing-getting-started/auto-shutdown.PNG)

自動シャットダウンは、電源オプションを使用して VM 内でシャットダウンする場合とは異なります。 自動シャットダウンでは、VM を停止して割り当てを解除することで課金を停止します。 詳細については、[Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) および [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の料金に関する FAQ で VM の状態についての質問をご覧ください。

開発およびテスト環境のコスト削減機能については、[Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) のページをご覧ください。

## <a name="cost-reporting"></a> 2 日目以降: サービスの使用後に使用状況を表示する

### <a name="costs"></a>ポータルでコストの内訳とバーン レートを定期的に確認する

サービスの実行後、現在のコストを定期的に確認します。 Azure Portal で現在の支出とバーン レートを確認できます。 

1. [Azure Portal の [サブスクリプション] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスします。

2. 表示するサブスクリプションを選択します。 選択できるサブスクリプションが 1 つしかない場合もあります。

3. ポップアップ ブレードに、コストの内訳とバーン レートが表示されます。 現在のプランでこの機能がサポートされていない場合があります (上部付近に警告が表示されます)。 サービスを追加したら、データが設定されるまで 24 時間待ちます。
    
    ![Azure Portal に表示されたバーン レートと内訳のスクリーンショット](./media/billing-getting-started/burn-rate.PNG)

4. ビューをダッシュボードにピン留めすることができます。

    ![ダッシュボードへのビューのピン留めのスクリーンショット](./media/billing-getting-started/pin.PNG)

5. 一覧の左側にある **[コスト分析]** をクリックして、リソース別にコストの内訳を表示します。

    ![Azure Portal のコスト分析ビューのスクリーンショット](./media/billing-getting-started/cost-analysis.PNG)

6. [タグ](#tags)、リソース グループ、期間などのさまざまなプロパティでフィルター処理できます。 **[適用]** をクリックしてフィルターを確定し、**[ダウンロード]** をクリックしてビューをコンマ区切り値 (.csv) ファイルにエクスポートします。

7. リソースをクリックして支出の履歴と日ごとのコストを確認します。

    ![Azure Portal の支出の履歴ビューのスクリーンショット](./media/billing-getting-started/spend-history.PNG)

サービスを選択したときに表示された見積もりと共に、表示されているコストを確認することをお勧めします。 コストが見積もりと大きく異なる場合は、リソースに対して選択した料金プラン (A1 VM と A0 VM など) を再確認します。 

#### <a name="view-costs-for-all-your-subscriptions-in-the-billing-blade"></a>[課金] 情報ブレードにすべてのサブスクリプションのコストを表示する

アカウント管理者として複数のサブスクリプションを管理している場合は、[[課金]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade) ブレードにすべてのサブスクリプションの課金総額と内訳を表示できます。 

<!-- Add screenshots of multiple subs each with billed usage -->

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Azure Advisor の推奨事項を有効にして確認する

[Azure Advisor](../advisor/advisor-overview.md) は、使用量の少ないリソースを特定してコストを削減するのに役立つプレビュー機能です。 Azure Portal で Azure Advisor を有効にします。

![Azure Portal の [Azure Advisor] ボタンのスクリーンショット](./media/billing-getting-started/advisor-button.PNG)

Advisor ダッシュボードの **[コスト]** タブで、実施可能な推奨事項を取得できます。

![Advisor のコストに関する推奨事項の例を示すスクリーンショット](./media/billing-getting-started/advisor-action.PNG)

詳細については、「[Advisor のコストに関する推奨事項](../advisor/advisor-cost-recommendations.md)」を参照してください。

### <a name="invoice-and-usage"></a> 最初の請求期間の終了後に請求書と使用状況の詳細を取得する

最初の請求期間が終了したら、Portable Document Format (.pdf) の請求書とコンマ区切り値 (.csv) の使用状況の詳細をダウンロードできます。 請求書のメール送信を選ぶこともできます。 これらのファイルは、課税、割引、クレジットを適用した後の最終的な請求金額を把握するのに役立ちます。 サブスクリプションに支払方法が設定されていない場合、これらのファイルを使用できないことがあります。 詳細については、「[Azure の請求書と毎日の使用状況データを取得する方法](billing-download-azure-invoice-daily-usage-date.md)」および「[Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)」をご覧ください。

![.pdf の請求書のスクリーンショット](./media/billing-getting-started/invoice.png)

詳細な使用状況 .csv ファイルには、以前に設定したタグが表示されます。

![使用状況 .csv 内のタグを示すスクリーンショット](./media/billing-getting-started/csv.png)

### <a name="billing-api"></a>課金 API

課金 API を使用すると、使用状況データをプログラムによって取得できます。 課金対象の使用状況を取得するには、RateCard API と Usage API を組み合わせて使用します。 詳細については、「[Microsoft Azure リソースの消費を把握する](billing-usage-rate-card-overview.md)」をご覧ください。

## <a name="other-offers"></a>EA、CSP、およびスポンサー プラン向けのその他のリソース

使用を開始するには、アカウント マネージャーまたは Azure パートナーに連絡してください。

| プラン | リソース |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [EA ポータル](https://ea.azure.com/)、[ヘルプ ドキュメント](https://ea.azure.com/helpdocs)、および [Power BI レポート](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | プロバイダーに連絡してください |
| Azure スポンサー プラン | [スポンサー プラン ポータル](https://www.microsoftazuresponsorships.com/) |

大規模な組織の IT を管理している場合は、[Azure エンタープライズ スキャフォールディング](../azure-resource-manager/resource-manager-subscription-governance.md)に関する記事と、[エンタープライズ IT に関するホワイト ペーパー](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (.pdf をダウンロード、英語のみ) を読むことをお勧めします。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
