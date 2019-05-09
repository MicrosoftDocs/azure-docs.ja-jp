---
title: Azure の Cloudyn に関してよく寄せられる質問 | Microsoft Docs
description: この記事では、Cloudyn についてよく寄せられる質問にお答えします。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: troubleshooting
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 0516c954bd23d5a2e339c5df447a923528e09d93
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011798"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Cloudyn に関してよく寄せられる質問

この記事では、Cloudyn についてよく寄せられる質問にお答えします。 Cloudyn について質問がある場合は、[Cloudyn に関する FAQ](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn) のページで質問してください。

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>間接加入契約のエンタープライズ設定に関する問題全般を解決するにはどうすればよいですか

Cloudyn ポータルを始めて使用する際、Enterprise Agreement またはクラウド ソリューション プロバイダー (CSP) ユーザーのお客様の場合、次のメッセージが表示されることがあります。

- **Cloudyn 設定**ウィザードに "The specified API key is not a top level enrollment key (指定された API キーは、最上位レベルの登録キーではありません)" と表示される。
- Enterprise Agreement ポータルに "Direct Enrollment – No\(直接登録 – いいえ\)" と表示される。
- Cloudyn ポータルに "No usage data was found for the last 30 days. Please contact your distributor to make sure markup was enabled for your Azure account\(過去 30 日間に使用状況データが見つかりませんでした。ディストリビューターに連絡してお使いの Azure アカウントに対してマークアップが有効になっていることを確認してください\)" と表示される。

前述のメッセージは、Azure Enterprise Agreement を再販業者または CSP から購入したことを示します。 Cloudyn でデータを確認するには、再販業者または CSP がユーザーの Azure アカウントの "_マークアップ_" を有効にする必要があります。

問題の修正方法は次のとおりです。

1. 再販業者がユーザーのアカウントの "_マークアップ_" を有効にする。 手順については、「[間接加入契約者向けオンボード ガイド](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide)」をご覧ください。

2. ユーザーが Cloudyn で使用する Azure Enterprise Agreement キーを生成する。 手順については、「[Adding Your Azure EA (Azure EA を追加する)](quick-register-ea.md#register-with-cloudyn)」または[自分の EA 登録 ID と API キーを探す方法](https://youtu.be/u_phLs_udig)に関するビデオをご覧ください。

Cloudyn を有効にできるのは、Azure サービス管理者だけです。 共同管理者のアクセス許可では不十分です。

Azure Enterprise Agreement API キーを生成して Cloudyn を設定する前に、次の手順に従って Azure Billing API を有効にする必要があります。

- [企業ユーザー向けの Reporting API の概要](../billing/billing-enterprise-api.md)
- 「**Enabling data access to the API (API へのデータ アクセスを有効にする)**」の「[Microsoft Azure Enterprise Portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI)」


また、部門管理者、アカウント所有者、およびエンタープライズ管理者に、Billing API による "_請求を確認する_" アクセス許可を付与する必要がある場合もあります。

## <a name="why-dont-i-see-optimizer-recommendations"></a>オプティマイザの推奨事項が表示されない理由

推奨事項の情報は、アクティブ化されているアカウントでのみ使用できます。 *アクティブ化されていない*アカウントの**オプティマイザ** レポート カテゴリに推奨事項の情報は表示されません。これには次のものが含まれます。

- 最適化マネージャー
- サイズ設定の最適化
- 非効率性

オプティマイザの推奨事項データを表示できない場合は、たいてい、アクティブ化されていないアカウントを使用しています。 アカウントをアクティブ化するには、それを Azure の資格情報に登録する必要があります。

アカウントをアクティブ化するには:

1.  Cloudyn ポータルで、右上にある **[設定]** をクリックして **[クラウド アカウント]** を選択します。
2.  [Microsoft Azure Accounts] \(Microsoft Azure アカウント) タブで、**アクティブ化されていない**サブスクリプションを持つアカウントを探します。
3.  アクティブ化されていないアカウントの右にある、鉛筆に似た **[編集]** 記号をクリックします。
4.  テナント ID とレート ID が自動的に検出されます。 **[次へ]** をクリックします。
5.  Azure Portal にリダイレクトされます。 ポータルにサインインし、Cloudyn Collector が Azure データにアクセスすることを承認します。
6.  次に、[Cloudyn Accounts] (Cloudyn アカウント) 管理ページにリダイレクトされ、サブスクリプションが**アクティブな**アカウント状態に更新されます。 そこには緑色のチェックマーク記号が表示されます。
7.  1 つ以上のサブスクリプションに緑色のチェックマーク記号が表示されていない場合は、そのサブスクリプションのリーダー アプリ (CloudynCollector) を作成するためのアクセス許可がないことを示しています。 そのサブスクリプションのより高いアクセス許可を持つユーザーが手順 3 と 4 を繰り返す必要があります。  

前の手順を完了すると、1 ～ 2 日以内にオプティマイザの推奨事項を表示できます。 ただし、完全な最適化データが入手可能になるまでに最大 5 日かかる場合があります。


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>中断またはロックアウトされたユーザーを有効にするにはどうすればよいですか

最初に、ユーザー アカウントが*initiallySuspended*する原因となる最も一般的なシナリオを見てみましょうです。

> Admin1 は、Microsoft Cloud ソリューション プロバイダーまたはエンタープライズ契約ユーザーの可能性があります。 彼の組織では、Cloudyn の使用を開始する準備が整いました。  彼は、Microsoft Azure portal で登録し、Cloudyn ポータルにサインインします。 彼は、Cloudyn サービスを登録し、Cloudyn ポータルにサインインする人として、"*プライマリ管理者*" になります。 Admin1 はユーザーアカウントを作成しません。 ただし、Cloudyn ポータルを使用すると、Azure アカウントを作成およびエンティティ階層を設定できます。 Admin1 は、テナント管理者である Admin2 に Cloudyn で登録し、Cloudyn ポータルにサインインするよう通知します。
> 
> Admin2 は Azure portal を使用して登録します。 しかし、 Cloudyn ポータルにサインインしようとした時、彼はアカウントの**中断**を示すエラーを受け取ります。 プライマリ管理者の Admin1 に、アカウントの中断が通知されます。 Admin1 は、Admin2 のアカウントをアクティブにし、適したエンティティへの *管理エンティティへのアクセス*を付与して、ユーザーの管理アクセス権とユーザー アカウントをアクティブにするアクセスを許可する必要があります。


ユーザーのアクセスの許可を要求するアラートを受け取った場合、そのユーザーのアカウントをアクティブにする必要があります。

ユーザー アカウントをアクティブにするには:

1. Cloudyn の設定に使用した Azure の管理ユーザーのアカウントを使用して Cloudyn にサインインします。 または、管理者アクセスが付与されたユーザー アカウントでサインインします。
2. 右上にある歯車記号を選択し、**[ユーザー管理]** を選択します。
3. ユーザーを検索し、鉛筆記号を選択してユーザーを編集します。
4. **[ユーザーの状態]** で、状態を **[中断]** から **[アクティブ]** に変更します。

Cloudyn のユーザー アカウントが Azure のシングル サインオンを使用して接続されます。 パスワードの入力を間違えると、Azure にはアクセスできても、Cloudyn からロックアウトされることがあります。

Cloudyn の電子メール アドレスを Azure の既定のアドレスから変更すると、アカウントがロックアウトされる場合があります。"status initiallySuspended\(ステータス initiallySuspended\)" というメッセージが表示される場合があります。 お使いのユーザー アカウントがロックアウトされた場合は、別の管理者にアカウントのリセットを依頼します。

一方のアカウントがロックアウトされた場合を考慮して、Cloudyn の管理者アカウントを 2 つ以上作成することをお勧めします。

Cloudyn ポータルにサインインできない場合は、正しい URL を使用して Cloudyn にサインインしていることを確認します。 [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade) を使用します。

Cloudyn のダイレクト URL https://app.cloudyn.com は使用しないでください。

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Azure の資格情報でアクティブになっていないアカウントをアクティブにする方法を教えてください

Cloudyn で Azure のアカウントが見つかるとすぐに、コスト データがコストベースのレポートに提供されます。 ただし、Cloudyn が使用状況データとパフォーマンス データを提供するには、そのアカウントの Azure の資格情報を登録する必要があります。 手順については、「[アカウントを追加するか、サブスクリプションを更新する](activate-subs-accounts.md#add-an-account-or-update-a-subscription)」を参照してください。

アカウントの Azure の資格情報を追加するには、Cloudyn ポータルで、サブスクリプションではなく、アカウント名の右にある編集記号を選択します。

Cloudyn にお使いの Azure の資格情報が追加されるまで、アカウントは "_非アクティブ_" として表示されます。

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>既存のサブスクリプションに複数のアカウントやエンティティを追加するにはどうすればよいですか

追加のエンティティは、Cloudyn のサブスクリプションに Enterprise Agreement をさらに追加するために使用されます。 詳細については、「[エンティティを作成および管理する](tutorial-user-access.md#create-and-manage-entities)」を参照してください。

CSP の場合:

エンティティに CSP のアカウントをさらに追加するには、新しいエンティティを作成するときに、**[エンタープライズ]** の代わりに **[MSP Access]\(MSP アクセス\)** を選択します。 Enterprise Agreement として登録されているアカウントに CSP の資格情報を追加するには、Cloudyn のサポート担当者がそのアカウントの設定を変更する必要がある場合があります。 有料の Azure サブスクライバーの場合は、Azure Portal で新しいサポート要求を作成できます。 **[ヘルプとサポート]** を選択し、**[新しいサポート要求]** を選択します。

## <a name="currency-symbols-in-cloudyn-reports"></a>Cloudyn レポートの通貨記号

異なる通貨を使用する複数の Azure アカウントがある場合があります。 ただし、Cloudyn のコスト レポートでは、レポートあたり複数の通貨の種類は表示されません。

異なる通貨を使用している複数のサブスクリプションがある場合は、親エンティティとその子エンティティの通貨が米国ドル **$** で表示されます。 推奨されるベスト プラクティスは、同じエンティティ階層で異なる通貨を使用しないことです。 つまり、エンティティ構造に整理されているすべてのサブスクリプションで同一の通貨を使用する必要があります。

Cloudyn は Enterprise Agreement サブスクリプションの通貨を自動的に検出し、レポートに正しく表示します。  ただし Cloudyn では、CSP および Web ダイレクト Azure アカウントに対しては米国ドル **$** のみが表示されます。

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Cloudyn のデータ更新タイムラインは何ですか

Cloudyn のデータ更新タイムラインは次のとおりです。

- **初期**:設定後、Cloudyn でコスト データが表示されるまで最大で 24 時間かかる場合があります。 また、サイズ変更に関する推奨事項を表示するために Cloudyn が十分なデータを収集するのに、最大で 10 日かかります。
- **日次**:Cloudyn では、各月の 10 日から末日まで、ご自分の前日からの最新データがその次の日の UTC + 3 くらいに表示されます。
- **月次**:Cloudyn では、各月の 1 日から 10 日まで、ご自分の前月の終わりまでのデータのみが表示される場合があります。

Cloudyn は前日の全データが利用可能になったタイミングで、前日のデータを処理します。 通常、前日のデータは各日の UTC+3 くらいに Cloudyn で使用できるようになります。 タグなどの一部のデータの処理には、さらに 24 時間かかる場合があります。

毎月月初に、当月のデータは収集できません。 その間に、サービス プロバイダーは前月の請求額を確定します。 前月のデータは、各月の月初から 5 ～ 10 日後に Cloudyn に表示されます。 この間は、前月からの償却されたコストのみが表示される場合があります。 請求や使用状況に関する日次データは表示されない場合があります。 データが利用可能になると、Cloudyn はそれをさかのぼって処理します。 処理が終わると、すべての月次データが各月の 5 日から 10 日までの間に表示されます。

Azure から Cloudyn へのデータの送信が遅延した場合、データは Azure に記録されたままになります。 データは接続が復元されたタイミングで Cloudyn に転送されます。

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Cloudyn コスト レポートでのコストの変動

コスト レポートは、更新した課金ファイルをクラウド サービス プロバイダーが送信するたびに、コストの変動を表示できます。 コストの変動は、通常の日単位または月単位のレポート スケジュール以外でクラウド サービス プロバイダーから新しいファイルを受信したときに発生します。 Cloudyn の再計算に起因するコストの変更は発生しません。

1 か月にわたってクラウド サービス プロバイダーによって送信されるすべての課金ファイルは、毎日のコストの概算値です。 データが頻繁に更新される場合があり、1 日に複数回更新されることもあります。 更新は、Azure よりも AWS のほうが頻繁に行われます。 コストの合計は、前月の課金計算が完了し、最後の課金ファイルが受信されたときに、変動しなくなります。 通常は、毎月 10 日までには安定します。

変更は、クラウド サービス プロバイダーからコスト調整を受信したときに発生します。 クレジットの受信は、その一例です。 変更は、関連する月が終わった後の数か月後に発生する可能性があります。 変更は、クラウド サービス プロバイダーによって再計算が行われるたびに表示されます。 Cloudyn は、履歴データを更新して、すべての調整が再計算されることを確認します。 それらのコストがレポートに正確に表示されていることも確認します。

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>直接 CSP が間接 CSP のお客様やパートナーの Cloudyn のアクセスを構成するにはどうすればよいですか

手順については、「[Cloudyn で間接 CSP アクセスを構成する](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn)」をご覧ください。

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>オプティマイザーのメニュー項目はどのようにしたら表示されますか

Azure Resource Manager アクセスを追加し、データが収集されると、**オプティマイザー** オプションが表示されます。 Azure Resource Manager アクセスをアクティブにするには、「[Azure の資格情報でアクティブになっていないアカウントをアクティブにする方法を教えてください](#how-do-i-activate-unactivated-accounts-with-azure-credentials)」を参照してください

## <a name="is-cloudyn-agent-based"></a>Cloudyn エージェントがベースになっていますか

いいえ。 エージェントは使用されていません。 VM の Azure 仮想マシンのメトリック データは、Microsoft Insights API から収集されます。 Azure VM からメトリック データを収集する場合は、診断設定を有効にする必要があります。

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Cloudyn レポートでは、レポートあたり複数の AD テナントが表示されますか

はい。 自身の AD テナントごとに[対応するクラウド アカウント エンティティを作成](tutorial-user-access.md#create-and-manage-entities)できます。 その後、すべての Azure AD テナント データと、アマゾン ウェブ サービス、Google Cloud Platform など、他のクラウド プラットフォーム プロバイダーを表示できます。
