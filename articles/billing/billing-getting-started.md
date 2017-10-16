---
title: "課金を管理して予想外のコストを防ぐ - Azure | Microsoft Docs"
description: "Azure の課金内容が予想外の金額となるのを防ぐ方法について説明します。 Microsoft Azure サブスクリプションに対して、コスト管理機能を使用します。"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: tonguyen
ms.openlocfilehash: d74f649a8b5f35ffe16479576959e5ebb2857bf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Azure の課金とコスト管理で想定外の料金を防ぐ

Azure にサインアップしたら、支出を把握するために行えることがいくつかあります。 [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)では、Azure リソースを作成する前に、料金を見積もることができます。 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) は、サブスクリプションに関する現在のコスト内訳と予測を提供します。 プロジェクトまたはチームごとのコストをグループ化して把握したい場合は、[リソースのタグ付け](../azure-resource-manager/resource-group-using-tags.md)を検討します。 使用したいレポート システムが組織にある場合は、[課金 API](billing-usage-rate-card-overview.md) を確認します。 

- サブスクリプションが Enterprise Agreement (EA) の場合は、Azure Portal で、コストを表示するパブリック プレビューを使うことができます。 クラウド ソリューション プロバイダー (CSP) または Azure スポンサー プランを介したサブスクリプションの場合は、以下の機能の一部が適用されない場合があります。 詳しくは、「[EA、CSP、およびスポンサー プラン向けのその他のリソース](#other-offers)」をご覧ください。

- サブスクリプションが無料試用版、[Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)、Azure イン オープン プラン (AIO)、または BizSpark の場合、クレジットをすべて使うと、サブスクリプションは自動的に無効になります。 サブスクリプションが予期せず無効化されないようにするには、[使用制限](#spending-limit)を確認してください。

## <a name="get-estimated-costs-before-adding-azure-services"></a>Azure サービスを追加する前にコストを見積もる

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>料金計算ツールを使用してオンラインでコストを見積もる

関心のあるサービスの月額料金を見積もるには、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を確認します。 ファースト パーティ Azure リソースを追加すると、推定料金が得られます。

![料金計算ツール メニューのスクリーンショット](./media/billing-getting-started/pricing-calc.png)

たとえば、A1 Windows 仮想マシン (VM) を常に実行したままにしておく場合、コンピューティング時間での推定月額料金は 66.96 米ドルになります。

![A1 Windows VM の推定月額料金が 66.96 米ドルであることを示す料金計算ツールのスクリーンショット](./media/billing-getting-started/pricing-calcVM.png)

料金の詳細については、この [FAQ](https://azure.microsoft.com/pricing/faq/) を参照してください。 Azure 営業担当者と話したい場合は、1-800-867-1389 にご連絡ください。

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Azure Portal で推定料金を検討する

通常、Azure Portal でサービスを追加すると、類似する推定月額料金を示すビューが表示されます。 たとえば、Windows VM のサイズを選択すると、コンピューティング時間の推定月額料金が表示されます。

![例: A1 Windows VM の推定月額料金は 66.96 米ドル](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="set-up-billing-alerts"></a>課金アラートのセットアップ

使用料金が指定した金額を超えたときに電子メールを受け取るには、課金アラートを設定します。 毎月のクレジットがある場合は、指定した金額まで使用したときのアラートを設定します。 詳細については、「[Microsoft Azure サブスクリプションの課金アラートの設定](billing-set-up-alerts.md)」を参照してください。

![課金アラートの電子メールのスクリーンショット](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> この機能はまだプレビュー段階であるため、使用状況を定期的に確認してください。

最初のアラートのガイドラインとして、料金計算ツールで取得したコスト見積もりを使用することをお勧めします。

### <a name="spending-limit"></a>使用制限が有効になっているかどうかを確認する

クレジットを使用するサブスクリプションがある場合、使用制限が既定で有効になっています。 この場合、すべてのクレジットを使い果たすと、クレジット カードに課金されなくなります。 Azure プランの一覧と使用制限の提供状況については、[こちら](https://azure.microsoft.com/support/legal/offer-details/)をご覧ください。

ただし、使用制限に達すると、サービスは無効になります。 つまり、VM の割り当てが解除されます。 サービスのダウンタイムを回避するには、使用制限を無効にする必要があります。 超過分は記録されているクレジット カードに課金されます。 

使用制限が有効になっているかどうかを確認するには、[アカウント センターの [サブスクリプション] ビュー](https://account.windowsazure.com/Subscriptions)に移動します。 使用制限が有効になっている場合、次のようなバナーが表示されます。

![使用制限が有効になっていることに関するアカウント センターの警告を示すスクリーンショット](./media/billing-getting-started/spending-limit-banner.PNG)

バナーをクリックし、画面の指示に従って使用制限を削除します。 サインアップ時にクレジット カード情報を入力しなかった場合、使用制限を削除するには、この情報を入力する必要があります。 詳細については、「[Azure 使用制限 - しくみと有効化または削除する方法](https://azure.microsoft.com/pricing/spending-limits/)」をご覧ください。

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Azure サービス使用時にコストを監視する方法

### <a name="tags"></a>リソースにタグを追加して課金データをグループ化する

タグを使用して、サポートされているサービスの課金データをグループ化できます。 たとえば、さまざまなチーム用に複数の VM を実行している場合、タグを使用して、コスト センター (HR、マーケティング、財務) または環境 (運用、運用前、テスト) でコストを分類できます。 

![ポータルでのタグの設定を示すスクリーンショット](./media/billing-getting-started/tags.PNG)

タグはさまざまなコスト レポート ビューのあらゆる場所に表示されます。 たとえば、最初の請求期間が終了すると、[コスト分析ビュー](#costs)にタグがすぐに表示され、[詳細な使用状況 .csv](#invoice-and-usage) ファイルにも表示されます。

詳細については、 [タグを使用した Azure リソースの整理](../azure-resource-manager/resource-group-using-tags.md)に関するページを参照してください。

### <a name="costs"></a>ポータルでコストの内訳とバーン レートを定期的に確認する

サービスの実行後、現在のコストを定期的に確認します。 Azure Portal で現在の支出とバーン レートを確認できます。 

1. [Azure Portal の [サブスクリプション] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスし、サブスクリプションを選択します。

2. ポップアップ ブレードに、コストの内訳とバーン レートが表示されます。 現在のプランでこの機能がサポートされていない場合があります (上部付近に警告が表示されます)。

    ![Azure Portal に表示されたバーン レートと内訳のスクリーンショット](./media/billing-getting-started/burn-rate.PNG)

3. 一覧の左側にある **[コスト分析]** をクリックして、リソース別にコストの内訳を表示します。 サービスを追加したら、データが設定されるまで 24 時間待ちます。

    ![Azure Portal のコスト分析ビューのスクリーンショット](./media/billing-getting-started/cost-analysis.PNG)

4. [タグ](#tags)、リソース グループ、期間などのさまざまなプロパティでフィルター処理できます。 ビューをコンマ区切り値 (.csv) ファイルにエクスポートする場合は、**[適用]** をクリックしてフィルターを確認し、**[ダウンロード]** をクリックします。

5. また、リソースをクリックして、毎日の支出の履歴とリソース コストを確認できます。

    ![Azure Portal の支出の履歴ビューのスクリーンショット](./media/billing-getting-started/costhistory.PNG)

サービスを選択したときに表示された見積もりと共に、表示されているコストを確認することをお勧めします。 コストが見積もりと大きく異なる場合は、リソースに対して選択した料金プラン (A1 VM と A0 VM など) を再確認します。 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>VM の自動シャットダウンなどのコスト削減機能を有効にすることを検討する

シナリオによっては、Azure Portal で VM の自動シャットダウンを構成できます。 詳細については、[Azure Resource Manager を使用した VM の自動シャットダウン](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)に関するブログをご覧ください。

![ポータルの自動シャットダウン オプションのスクリーンショット](./media/billing-getting-started/auto-shutdown.PNG)

自動シャットダウンは、電源オプションを使用して VM 内でシャットダウンする場合とは異なります。 自動シャットダウンでは、VM を停止して割り当てを解除することで課金を停止します。 詳細については、[Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) および [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の料金に関する FAQ で VM の状態についての質問をご覧ください。

開発およびテスト環境のコスト削減機能については、[Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) のページをご覧ください。

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Azure Advisor の推奨事項を有効にして確認する

[Azure Advisor](../advisor/advisor-overview.md) は、使用量の少ないリソースを特定してコストを削減するのに役立つプレビュー機能です。 Azure Portal で Azure Advisor を有効にします。

![Azure Portal の [Azure Advisor] ボタンのスクリーンショット](./media/billing-getting-started/advisor-button.PNG)

Advisor ダッシュボードの **[コスト]** タブで、実施可能な推奨事項を取得できます。

![Advisor のコストに関する推奨事項の例を示すスクリーンショット](./media/billing-getting-started/advisor-action.PNG)

詳細については、「[Advisor のコストに関する推奨事項](../advisor/advisor-cost-recommendations.md)」を参照してください。

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>請求サイクルの最後でのコストの確認

請求サイクルが終了した後、請求書を見ることができるようになります。 また、[過去の請求書と詳細な使用状況ファイルをダウンロード](billing-download-azure-invoice-daily-usage-date.md)して、課金が正しく行われていることを確認できます。 毎日の使用状況と請求書の比較の詳細については、「[Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)」を参照してください。

### <a name="billing-api"></a>課金 API

課金 API を使用すると、使用状況データをプログラムによって取得できます。 課金対象の使用状況を取得するには、RateCard API と Usage API を組み合わせて使用します。 詳細については、「[Microsoft Azure リソースの消費を把握する](billing-usage-rate-card-overview.md)」をご覧ください。

## <a name="other-offers"></a> その他のリソースと特殊なケース

### <a name="ea-csp-and-sponsorship-customers"></a>EA、CSP、およびスポンサープランのお客様
使用を開始するには、アカウント マネージャーまたは Azure パートナーに連絡してください。

| プラン | リソース |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [EA ポータル](https://ea.azure.com/)、[ヘルプ ドキュメント](https://ea.azure.com/helpdocs)、および [Power BI レポート](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | プロバイダーに連絡してください |
| Azure スポンサー プラン | [スポンサー プラン ポータル](https://www.microsoftazuresponsorships.com/) |

大規模な組織の IT を管理している場合は、[Azure エンタープライズ スキャフォールディング](../azure-resource-manager/resource-manager-subscription-governance.md)に関する記事と、[エンタープライズ IT に関するホワイト ペーパー](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (.pdf をダウンロード、英語のみ) を読むことをお勧めします。

#### <a name="EA"></a>Azure Portal 内の プレビュー Enterprise Agreement のコスト ビュー 

Enterprise コスト ビューは、現在、パブリック プレビューの中にあります。 注意する項目:
- サブスクリプションのコストは、使用量に基づいて計算されており、前払い額、超過、含まれる数量、調整、および税は考慮されません。 実際の料金は、登録レベルで計算されます。 
- Azure ポータルに表示される金額は、エンタープライズ ポータル内の値に比較して遅れる可能性があります。  
- コストが表示されない場合は、次の理由のいずれかが原因として考えられます。
    - お客様はアカウント所有者であり、登録管理者が、"AO ビュー料金" 設定を無効にした。  登録管理者に問い合わせて、コストにアクセスできるように設定してもらってください。 
    - お客様は部門管理者者であり、登録管理者が、"DA ビュー料金" 設定を無効にした。  登録管理者に問い合わせて、アクセスできるように設定してもらってください。 
    - チャネル パートナーを通して Azure を購入し、パートナーが価格情報を公開していない。  
- エンタープライズ ポータル内でコストのアクセスに関連する設定が更新されても、Azure ポータルで、変更が反映されるまで、数分の遅延があります。
- 使用制限、請求の通知と請求書ガイダンスは EA サブスクリプションとは関係ありません。

### <a name="check-your-subscription-and-access"></a>サブスクリプションとアクセスを確認する

コストを表示するには、[課金情報へのサブスクリプション レベルのアクセス権](billing-manage-access.md)が必要ですが、アカウント管理者のみが[アカウント センター](https://account.azure.com/Subscriptions)へのアクセス、課金情報の変更、サブスクリプションの管理を行うことができます。 アカウント管理者は、サインアップ プロセスを完了したユーザーです。 詳細については、「[サブスクリプションまたはサービスを管理する Azure 管理者ロールを追加または変更する](billing-add-change-azure-subscription-administrator.md)」をご覧ください。

自分がアカウント管理者かどうかを確認するには、[Azure Portal の [サブスクリプション] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動し、アクセスできるサブスクリプションの一覧を確認します。 **[自分の役割]** を確認します。 ここに *[アカウント管理者]* と表示されていれば問題ありません。 *[所有者]* などの他の役割が表示されている場合は、すべての権限を持っているわけではありません。

![Azure Portal の [サブスクリプション] ビューに表示された役割のスクリーンショット](./media/billing-getting-started/sub-blade-view.PNG)

アカウント管理者でない場合、別のユーザーが [Azure Active Directory のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md) (RBAC) 使用して部分的なアクセス権を付与していると考えられます。 サブスクリプションを管理し、課金情報を変更するには、[アカウント管理者を見つけて](billing-subscription-transfer.md#whoisaa)、これらのタスクの実行または[サブスクリプションの転送](billing-subscription-transfer.md)を依頼します。

組織にアカウント管理者が存在せず、課金を管理する必要がある場合は、[サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 
## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
