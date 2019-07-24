---
title: Azure で課金を管理して予想外のコストを防ぐ | Microsoft Docs
description: Azure の課金内容が予想外の金額となるのを防ぐ方法について説明します。 Microsoft Azure サブスクリプションに対して、コスト管理機能を使用します。
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: banders
ms.openlocfilehash: d215e2ba5d650b532b9d7554ccca9ad5537cf4de
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842502"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Azure の課金とコスト管理で想定外の料金を防ぐ

Azure にサインアップしたら、支出を把握するために行えることがいくつかあります。 [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)では、Azure リソースを作成する前に、料金を見積もることができます。 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) は、サブスクリプションに関する現在のコスト内訳と予測を提供します。 プロジェクトまたはチームごとのコストをグループ化して把握したい場合は、[リソースのタグ付け](../azure-resource-manager/resource-group-using-tags.md)を検討します。 使用したいレポート システムが組織にある場合は、[課金 API](billing-usage-rate-card-overview.md) を確認します。

- サブスクリプションが Enterprise Agreement (EA) の場合は、Azure Portal で、コストを表示するパブリック プレビューを使うことができます。 クラウド ソリューション プロバイダー (CSP) または Azure スポンサー プランを介したサブスクリプションの場合は、以下の機能の一部が適用されない場合があります。 詳しくは、「[EA、CSP、およびスポンサー プラン向けのその他のリソース](#other-offers)」をご覧ください。

- サブスクリプションが無料試用版、[Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)、Azure イン オープン プラン (AIO)、または BizSpark の場合、クレジットをすべて使うと、サブスクリプションは自動的に無効になります。 サブスクリプションが予期せず無効化されないようにするには、[使用制限](#spending-limit)を確認してください。

- [Azure の無料アカウント](https://azure.microsoft.com/free/)にサインアップしている場合、[最も一般的な Azure サービスのいくつかを 12 か月間無料で使用できます](billing-create-free-services-included-free-account.md)。 下記の推奨事項に加えて、[無料アカウントの課金の回避](billing-avoid-charges-free-account.md)に関する記事もご覧ください。

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

### <a name="spending-limit"></a>使用制限が有効になっているかどうかを確認する

クレジットを使用するサブスクリプションがある場合、使用制限が既定で有効になっています。 この場合、すべてのクレジットを使い果たすと、クレジット カードに課金されなくなります。 Azure プランの一覧と使用制限の提供状況については、[こちら](https://azure.microsoft.com/support/legal/offer-details/)をご覧ください。

ただし、使用制限に達すると、サービスは無効になります。 つまり、VM の割り当てが解除されます。 サービスのダウンタイムを回避するには、使用制限を無効にする必要があります。 超過分は記録されているクレジット カードに課金されます。 

使用制限が有効になっているかどうかを確認するには、[アカウント センターの [サブスクリプション] ビュー](https://account.windowsazure.com/Subscriptions)に移動します。 使用制限が有効になっている場合、次のようなバナーが表示されます。

![使用制限が有効になっていることに関するアカウント センターの警告を示すスクリーンショット](./media/billing-getting-started/spending-limit-banner.PNG)

バナーをクリックし、画面の指示に従って使用制限を削除します。 サインアップ時にクレジット カード情報を入力しなかった場合、使用制限を削除するには、この情報を入力する必要があります。 詳細については、「[Azure 使用制限 - しくみと有効化または削除する方法](https://azure.microsoft.com/pricing/spending-limits/)」をご覧ください。

[Cloudyn](https://www.cloudyn.com/) サービスを使用すると、異常な支出や浪費のリスクについて、関係者に自動的に通知するアラートを作成できます。 予算やコストのしきい値に基づくアラートをサポートするレポートを使用することで、アラートを作成できます。 Cloudyn の使用の詳細については、「[チュートリアル: 使用状況とコストを確認する](../cost-management/tutorial-review-usage.md)」を参照してください。

この例では、**実績経過コスト** レポートを使用して、Azure VM の支出が合計予算に近づいたときに通知を送信します。 このシナリオでは、合計予算が 20,000 ドルで、コストが予算の半分 (9,000 ドル) に近づいたときに通知を受け取り、コストが 10,000 ドルに達したときに追加のアラートを受け取ります。

1. Cloudyn ポータルの上部にあるメニューで、**[コスト]** > **[コスト分析]** > **[実績経過コスト]** の順に選択します。 
2. **[グループ]** を **[サービス]** に設定し、**[Filter on the service]\(サービスのフィルター\)** を **[Azure/VM]** に設定します。 
3. レポートの右上にある **[アクション]** を選択してから **[レポートのスケジュール]** を選択します。
4. スケジュールされた間隔でレポートのメールを自分に送信するには、**[Save or Schedule this report]\(このレポートを保存またはスケジュールする\)** ダイアログの **[スケジューリング]** タブを選択します。 必ず **[メールで送信]** を選択してください。 使用するタグ、グループ、およびフィルターは、メールで送信されるレポートに含まれます。 
5. **[しきい値]** タブを選択して、**[Actual Cost vs.Threshold]\(実際のコスト対しきい値\)** を選択します。 
   1. **[Red alert]\(赤色のアラート\)** しきい値ボックスに、「10000」と入力します。 
   2. **[Yellow alert]\(黄色のアラート\)** しきい値ボックスに、「9000」と入力します。 
   3. **[Number of consecutive alerts]\(連続アラートの回数\)** ボックスに、受信する連続アラートの回数を入力します。 指定したアラートの合計数に達すると、それ以上アラートは送信されません。 
6. **[保存]** を選択します。

    ![支出しきい値に基づいて赤色と黄色のアラートが示された例](./media/billing-getting-started/schedule-alert01.png)

**原価率対予算**のしきい値メトリックを選択して、アラートを作成することもできます。 これにより、しきい値を通貨値ではなく予算の割合として指定できます。

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Azure サービス使用時にコストを監視する方法

### <a name="tags"></a>リソースにタグを追加して課金データをグループ化する

タグを使用して、サポートされているサービスの課金データをグループ化できます。 たとえば、さまざまなチーム用に複数の VM を実行している場合、タグを使用して、コスト センター (HR、マーケティング、財務) または環境 (運用、運用前、テスト) でコストを分類できます。 

![ポータルでのタグの設定を示すスクリーンショット](./media/billing-getting-started/tags.PNG)

タグはさまざまなコスト レポート ビューのあらゆる場所に表示されます。 たとえば、[コスト分析ビュー](#costs)に今すぐ表示され、最初の請求期間の終了後に詳細な使用状況を示す .csv に表示されます。

詳細については、 [タグを使用した Azure リソースの整理](../azure-resource-manager/resource-group-using-tags.md)に関するページを参照してください。

### <a name="costs"></a>ポータルでコストの内訳とバーン レートを定期的に確認する

サービスの実行後、現在のコストを定期的に確認します。 Azure Portal で現在の支出とバーン レートを確認できます。

1. [Azure portal の [サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) にアクセスし、サブスクリプションを選択します。

2. お使いのサブスクリプションでサポートされていれば、コストの内訳とバーン レートが表示されます。

    ![Azure Portal に表示されたバーン レートと内訳のスクリーンショット](./media/billing-getting-started/burn-rate.PNG)

3. 一覧の左側にある **[コスト分析]** をクリックして、リソース別にコストの内訳を表示します。 サービスを追加したら、データが設定されるまで 24 時間待ちます。

    ![Azure Portal のコスト分析ビューのスクリーンショット](./media/billing-getting-started/cost-analysis.PNG)

4. [タグ](#tags)、リソースの種類、リソース グループ、期間などのさまざまなプロパティでフィルター処理できます。 ビューをコンマ区切り値 (.csv) ファイルにエクスポートする場合は、**[適用]** をクリックしてフィルターを確認し、**[ダウンロード]** をクリックします。

5. また、リソースをクリックして、毎日の支出の履歴とリソースのコストを確認できます。

    ![Azure Portal の支出の履歴ビューのスクリーンショット](./media/billing-getting-started/costhistory.PNG)

サービスを選択したときに表示された見積もりと共に、表示されているコストを確認することをお勧めします。 コストが見積もりと大きく異なる場合は、リソースに対して選択した料金プランを再確認します。

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>VM の自動シャットダウンなどのコスト削減機能を有効にすることを検討する

シナリオによっては、Azure portal で VM の自動シャットダウンを構成できます。 詳しくは、[Azure Resource Manager を使用した VM の自動シャットダウン](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)に関する記事をご覧ください。

![ポータルの自動シャットダウン オプションのスクリーンショット](./media/billing-getting-started/auto-shutdown.PNG)

自動シャットダウンは、電源オプションを使用して VM 内でシャットダウンする場合とは異なります。 自動シャットダウンでは、VM を停止して割り当てを解除することで課金を停止します。 詳細については、[Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) および [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の料金に関する FAQ で VM の状態についての質問をご覧ください。

開発およびテスト環境のコスト削減機能については、[Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) のページをご覧ください。

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Azure Advisor の推奨事項を有効にして確認する

[Azure Advisor](../advisor/advisor-overview.md) は、使用量の少ないリソースを特定してコストを削減するのに役立つ機能です。 Azure Portal で Advisor にアクセスします。

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

大規模な組織の IT を管理している場合は、[Azure エンタープライズ スキャフォールディング](/azure/architecture/cloud-adoption-guide/subscription-governance)に関する記事と、[エンタープライズ IT に関するホワイト ペーパー](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (.pdf をダウンロード、英語のみ) を読むことをお勧めします。

#### <a name="EA"></a>Azure portal での Enterprise Agreement のコスト ビューのプレビュー 

Enterprise コスト ビューは、現在、パブリック プレビューの中にあります。 注意する項目:

- サブスクリプションのコストは、使用量に基づいて計算されており、前払い額、超過、含まれる数量、調整、および税は含まれていません。 実際の料金は、登録レベルで計算されます。
- Azure portal に表示される金額が、エンタープライズ ポータルに表示されるものとは異なっている場合があります。 エンタープライズ ポータルでの更新による変更が Azure portal に表示されるまで、数分かかる可能性があります。
- コストが表示されない場合は、次の理由のいずれかが原因として考えられます。
    - サブスクリプション レベルでアクセス許可がない。 エンタープライズ コスト ビューを表示するには、課金データ閲覧者、閲覧者、共同作業者、所有者のいずれかのサブスクリプション レベルである必要があります。
    - お客様はアカウント所有者であり、[AO ビューの請求金額] 設定が登録管理者によって無効にされている。  登録管理者に問い合わせて、コストにアクセスできるように設定してもらってください。 
    - お客様は部門管理者者であり、[DA ビューの請求金額] 設定が登録管理者によって無効にされている。  登録管理者に問い合わせて、アクセスできるように設定してもらってください。
    - チャネル パートナーを通して Azure を購入したが、パートナーが価格情報を公開していない。  
- エンタープライズ ポータルでコストのアクセスに関連する設定を更新した場合、変更が Azure portal に表示されるまで数分の遅延があります。
- 使用制限と請求書ガイダンスは、EA サブスクリプションには適用されません。

### <a name="check-your-subscription-and-access"></a>サブスクリプションとアクセスを確認する

コストを表示するには、[課金情報へのサブスクリプション レベルのアクセス権](billing-manage-access.md)をお客様が所有している必要があります。 [アカウント管理者だけ](https://account.azure.com/Subscriptions)が、アカウント センターにアクセスして、課金情報を変更したり、サブスクリプションを管理したりできます。 アカウント管理者とは、サインアップ プロセスを完了したユーザーです。 詳細については、「[サブスクリプションまたはサービスを管理する Azure 管理者ロールを追加または変更する](billing-add-change-azure-subscription-administrator.md)」をご覧ください。

ご自身がアカウント管理者であるかどうかを確認するには、Azure portal で [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) に移動します。 ご自身がアクセス権を所有しているサブスクリプションの一覧を確認します。 **[自分の役割]** を確認します。 ここに *[アカウント管理者]* と表示されていれば問題ありません。 *[所有者]* などの他の役割が表示されている場合は、すべての権限を持っているわけではありません。

![Azure Portal の [サブスクリプション] ビューに表示された役割のスクリーンショット](./media/billing-getting-started/sub-blade-view.PNG)

ご自身がアカウント管理者でない場合は、[Azure Active Directory のロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md) (RBAC) によって、部分的なアクセス権が与えられていると考えられます。 サブスクリプションを管理して課金情報を変更するには、[アカウント管理者を探します](billing-subscription-transfer.md#whoisaa)。アカウント管理者にタスクを実行してもらうか、[サブスクリプションを譲渡](billing-subscription-transfer.md)してもらうように依頼します。

組織にアカウント管理者が存在せず、課金を管理する必要がある場合は、[お問い合わせください](https://go.microsoft.com/fwlink/?linkid=2083458)。


### <a name="how-to-request-a-service-level-agreement-credit-for-a-service-incident"></a>サービス インシデントに対するサービス レベル アグリーメントの控除を要求する方法

サービス レベル アグリーメント (SLA) では、稼働時間と接続に関するマイクロソフトの確約内容について説明しています。 Azure サービスで稼働時間や接続に影響を与える問題 (しばしば “停止” と呼ばれる) が発生すると、サービス インシデントが報告されます。 SLA に記載されている各サービスのサービス レベルが達成および維持されていない場合は、月額サービス料金の一部が控除対象になる場合があります。

控除を要求するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。 複数のアカウントをお持ちの場合は、Azure のダウンタイムの影響を受けたアカウントを必ず使用してください。 これにより、サポート担当者は必要な背景情報を自動的に収集して、お客様のケースをより迅速に解決することができます。
2. 新しいサポート リクエストを作成します。
3. **[問題の種類]** で **[課金]** を選択します。
4. **[問題の種類]** で **[払戻要求]** を選択します。
5. SLA の控除を求めていることを指定する詳細を追加し、日付/時刻/タイムゾーン、および影響を受けたサービス (VM、Web サイトなど) を記載します。
6. 連絡先情報の詳細を確認し、**[作成]** ボタンを選択して要求を送信します。

SLA のしきい値は、サービスによって異なります。 たとえば、SQL Web サービス レベルの SLA は 99.9%、VM の SLA は 99.95%、SQL Standard サービス レベルの SLA は 99.99% です。

一部のサービスには、SLA を適用するための前提条件があります。 たとえば、仮想マシンでは、2 つ以上のインスタンスが同じ可用性セットにデプロイされている必要があります。

詳細については、「[サービス レベル アグリーメント](https://azure.microsoft.com/en-us/support/legal/sla/)」ドキュメントと「[Azure サービスの SLA 概要](https://azure.microsoft.com/en-us/support/legal/sla/summary/)」ドキュメントを参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
