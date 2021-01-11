---
title: Azure サブスクリプションの課金所有権を譲渡する
description: Azure サブスクリプションの課金所有権を別のアカウントに譲渡する方法と、そのプロセスに関してよく寄せられる質問 (FAQ) について説明します
keywords: Azure サブスクリプションを譲渡する, Azure サブスクリプションの譲渡, Azure サブスクリプションを別のアカウントに移動する, Azure サブスクリプション所有者の変更, Azure サブスクリプションを別のアカウントに譲渡する, Azure 課金所有権を譲渡する
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27ae816e3930ee0e5948c2daae40a0961a6cf474
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685036"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure サブスクリプションの課金所有権を別のアカウントに譲渡する

自分が退職するときや、Azure サブスクリプションの請求先を別のアカウントに変える場合には、サブスクリプションの課金所有権を譲渡する必要があります。 別のアカウントに課金所有権を譲渡すると、管理者には請求タスク用の新しいアカウントのアクセス許可が付与されます。 これにより、支払い方法の変更、料金の表示、サブスクリプションの取り消しを行うことができます。

課金所有権を維持したまま、サブスクリプションの種類を変更する場合は、[別のオファーへの Azure サブスクリプションの切り替え](switch-azure-offer.md)に関するページを参照してください。 サブスクリプション内のリソースにアクセスできるユーザーを制御する場合は、「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

マイクロソフト エンタープライズ契約 (EA) のお客様であれば、お客様のエンタープライズ管理者が、アカウント間でサブスクリプションの課金所有権を移すことができます。 詳細については、「[マイクロソフト エンタープライズ契約 (EA) サブスクリプションの課金所有権を譲渡する](#EA)」を参照してください。

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure サブスクリプションの課金所有権を譲渡する

1. 譲渡するサブスクリプションがある課金アカウントの管理者として、[Azure portal](https://portal.azure.com) にサインインします。 自分が管理者であるかどうかを調べる方法については、[よく寄せられる質問](#faq)に関するセクションを参照してください。

1. **[コストの管理と請求]** で検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. 左側のウィンドウで、 **[サブスクリプション]** を選択します。 アクセス権によっては、課金スコープを選択してから、 **[サブスクリプション]** または **[Azure サブスクリプション]** を選択しなければならない場合があります。

1. 譲渡するサブスクリプションの **[課金所有権の譲渡]** を選択します。

   ![譲渡するサブスクリプションの選択](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. サブスクリプションの新しい所有者となる、アカウントの課金管理者であるユーザーのメール アドレスを入力します。

1. 別の Azure AD テナントのアカウントにサブスクリプションを譲渡する場合は、サブスクリプションを新しいアカウントのテナントに移行するかどうかを選択します。 詳細については、「[サブスクリプションを別の Azure AD テナント内のアカウントに譲渡する](#transfer-a-subscription-to-another-azure-ad-tenant-account)」を参照してください。

    > [!IMPORTANT]
    >
    > 新しいアカウントの Azure AD テナントにサブスクリプションを移行することを選択した場合は、サブスクリプション内のリソースにアクセスするためのすべての [Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)が完全に削除されます。 譲渡要求を受け入れた新しいアカウントのユーザーにのみ、サブスクリプションのリソースの管理アクセス権が付与されます。 詳細については、次のセクションの「[サブスクリプションを別の Azure AD テナント アカウントに譲渡する](#transfer-a-subscription-to-another-azure-ad-tenant-account)」を参照してください。 または、 **[サブスクリプション Azure AD テナント]** のチェック ボックスをオフにし、サブスクリプションを新しいアカウントのテナントに移行せずに課金所有権を譲渡することもできます。 これを行うと、Azure リソースにアクセスするための既存の Azure ロールの割り当てが維持されます。

    ![譲渡の送信ページ](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. **[譲渡要求を送信します]** を選択します。

1. ユーザーは、譲渡要求を確認する手順を示す電子メールを受信します。

   ![受信者に送信されるサブスクリプションの譲渡メール](./media/billing-subscription-transfer/billing-receiver-email.png)

1. 譲渡要求を承認するには、ユーザーは、電子メールのリンクを選択し、指示に従います。 次に、サブスクリプションの支払いに使用する支払い方法を選択します。 Azure アカウントを持っていない場合は、新しいアカウントにサインアップする必要があります。

   ![First subscription transfer web page](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Second subscription transfer web page](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Second subscription transfer web page](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Success! サブスクリプションが譲渡されました。

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>サブスクリプションを別の Azure AD テナント アカウントに譲渡する

Azure へのサインアップ時に、Azure Active Directory (AD) テナントが自動的に作成されます。 このテナントは、自分のアカウントを表すものです。 サブスクリプションとリソースへのアクセスの管理には、このテナントを使用します。

新しいサブスクリプションを作成すると、そのサブスクリプションは自分のアカウントの Azure AD テナントでホストされます。 サブスクリプションまたはリソースへのアクセス権を別のユーザーに付与するには、自分のテナントに参加するように招待する必要があります。 そうすることで、サブスクリプションとリソースへのアクセスを制御できます。

サブスクリプションの課金所有権を別の Azure AD テナント内のアカウントに譲渡すると、サブスクリプションをその新しいアカウントのテナントに移動することができます。 これを行うと、サブスクリプションとそのリソースを管理するための [Azure ロールが割り当て](../../role-based-access-control/role-assignments-portal.md)られたすべてのユーザー、グループ、またはサービス プリンシパルのアクセス権が失われます。 譲渡要求を受け入れた新しいアカウントのユーザーにのみ、リソースの管理アクセス権が付与されます。 新しい所有者は、アクセス権を失ったユーザーにアクセス権を付与するために、これらのユーザーをサブスクリプションに手動で追加する必要があります。 詳細については、「[Azure サブスクリプションを別の Azure AD ディレクトリに譲渡する (プレビュー)](../../role-based-access-control/transfer-subscription.md)」を参照してください。


## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Visual Studio と Partner Network のサブスクリプションを譲渡する

Visual Studio と Microsoft Partner Network のサブスクリプションでは、付随する Azure クレジットが毎月提供されます。 こうしたサブスクリプションを譲渡した場合、譲渡元のクレジットは、譲渡先の課金アカウントでは使用できません。 サブスクリプションでは、譲渡先の課金アカウントにあるクレジットが使用されます。 たとえば、9 月 9 日に Bob が Visual Studio Enterprise サブスクリプションを Jane のアカウントに譲渡し、Jane がその譲渡を受け入れたとします。 譲渡の完了後、そのサブスクリプションでは、Jane のアカウントのクレジットが使用されるようになります。 クレジットは毎月 9 日にリセットされます。


<a id="EA"></a>

## <a name="transfer-ea-subscription-billing-ownership"></a>EA サブスクリプションの課金所有権を譲渡する

エンタープライズ管理者は、加入契約に含まれるアカウント間でサブスクリプションの所有権を譲渡できます。 詳細については、EA Portal の「[アカウント所有者を変更する](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#change-account-owner)」を参照してください。

## <a name="next-steps-after-accepting-billing-ownership"></a>所有権を受け取った後の手順

Azure サブスクリプションの課金所有権を受け取った場合は、以下の手順を確認することをお勧めします。

1. サービス管理者、共同管理者、Azure ロールの割り当てを確認して更新します。 詳細については、「[Azure サブスクリプション管理者を追加または変更する](add-change-subscription-administrator.md)」と「[Azure portal を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
1. このサブスクリプションのサービスに関連付けられている以下の資格情報を更新します。
   1. サブスクリプションのリソースに対する管理者権限をユーザーに付与する管理証明書。 詳細については、「 [Azure の管理証明書の作成とアップロード](../../cloud-services/cloud-services-certs-create.md) を参照してください。
   1. Storage などのサービス用のアクセス キー。 詳細については、「[Azure ストレージ アカウントについて](../../storage/common/storage-create-storage-account.md)」を参照してください。
   1. Azure Virtual Machines などのサービス用のリモート アクセス資格情報。
1. パートナーがいる場合は、このサブスクリプションのパートナー ID を更新することを検討します。 パートナー ID は、[Azure Portal](https://portal.azure.com) で更新できます。 詳細については、「[Azure アカウントにパートナー ID をリンクする](link-partner-id.md)」を参照してください

<a id="supported"></a>

## <a name="supported-subscription-types"></a>サポートされているサブスクリプションの種類

Azure portal でのサブスクリプションの譲渡は、下記の種類のサブスクリプションについて行うことができます。 現在、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)と [Azure イン オープン プラン (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) のサブスクリプションについては、譲渡はサポートされていません。 対処法ついては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。 サポート プランなどの他のサブスクリプションを譲渡する場合は、[Azure サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise (MPN) サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise:BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure プラン](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [EA ポータル経由](#EA)。

\*\* Azure Web サイトでサインアップしたときに作成されたアカウントのみでサポート。

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>よく寄せられる質問 (FAQ) (譲渡元向け)

以下の FAQ は、Azure サブスクリプションの課金所有権を別のアカウントに譲渡するユーザー向けのものです。

### <a name="who-is-a-billing-administrator-of-an-account"></a><a name="whoisaa"></a> アカウントの課金管理者とはだれのことですか。

課金管理者とは、アカウントに対する課金を管理する権限が付与されているユーザーのことです。 [Azure portal](https://portal.azure.com) で課金情報にアクセスする権限のほか、サブスクリプションの作成、請求書の表示と支払い、支払い方法の更新など、さまざまな課金関連の作業を行う権限が付与されています。

自分が課金管理者になっているアカウントを特定するには、次の手順を行います。

1. [Azure portal で [コストの管理と請求] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)に移動します。
1. 左側のウィンドウの **[すべての課金スコープ]** を選択します。
1. [サブスクリプション] ページに、自分が課金管理者となっているサブスクリプションがすべて一覧表示されます。

サブスクリプションのアカウント管理者が不明な場合は、次の手順で確認します。

1. [Azure Portal の [サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスします。
1. 確認するサブスクリプションを選択し、 **[設定]** を調べます。
1. **[プロパティ]** を選択します。 サブスクリプションのアカウント管理者が、 **[アカウント管理者]** ボックスに表示されます。

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>あらゆるものが譲渡されるのでしょうか。 リソース グループ、VM、ディスク、他の実行中のサービスは含まれますか?

VM、ディスク、Web サイトなどのすべてのリソースが、新しいアカウントに譲渡されます。 しかし、別の Azure AD テナント内のアカウントにサブスクリプションを譲渡する場合、そのサブスクリプションへの[管理者ロール](add-change-subscription-administrator.md)と [Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)は[譲渡されません](#transfer-a-subscription-to-another-azure-ad-tenant-account)。 また、[アプリの登録](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)とテナント固有のその他のサービスも、サブスクリプションと共に譲渡されることはありません。

### <a name="can-i-transfer-ownership-to-an-account-in-another-countryregion"></a>別の国/地域のアカウントに所有権を譲渡できますか。
申し訳ありませんが、Azure portal では国/地域外への譲渡はできません。 サブスクリプションを国/地域外に譲渡する場合は、[サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>2 つのアカウントで管理者になっています。 片方のアカウントのサブスクリプションを、もう片方のアカウントに譲渡することはできますか。
はい。お使いのアカウント間でサブスクリプションを譲渡することは可能です。 概念上、そうしたアカウントは 2 人の異なるユーザーのアカウントと見なされるので、上記の手順を利用してアカウント間でサブスクリプションを譲渡できます。

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>サブスクリプションの譲渡により、サービスのダウンタイムは発生しますか。

同じ Azure AD テナント内のアカウントにサブスクリプションを譲渡する場合、そのサブスクリプションで実行されているリソースに影響は生じません。 ただし、PowerShell に保存されたコンテキスト情報は更新されないため、それをクリアするか、設定を変更しなければならない場合があります。 サブスクリプションを別のテナントのアカウントに譲渡し、サブスクリプションをそのテナントに移行することにした場合、そのサブスクリプション内のリソースにアクセスするための [Azure ロールが割り当て](../../role-based-access-control/role-assignments-portal.md)られたすべてのユーザー、グループ、およびサービス プリンシパルのアクセス権は失われます。 サービスのダウンタイムが発生する可能性があります。

### <a name="can-users-in-new-account-access-usage-and-billing-history"></a>新しいアカウントのユーザーは、使用状況と請求履歴にアクセスできますか。

新しいアカウントのユーザーが利用できる情報は、サブスクリプションの先月の料金のみです。 使用履歴と請求履歴の他の部分が、サブスクリプションと共に譲渡されることはありません

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Azure サブスクリプションのデータとサービスを新しいサブスクリプションに移行するにはどうすればよいですか。

サブスクリプションの所有権を譲渡できない場合、リソースを手動で移行することもできます。 「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Visual Studio または Microsoft Partner Network のサブスクリプションを譲渡する場合、クレジットは新しいアカウントのサブスクリプションに繰り越されますか。

いいえ。譲渡元のクレジットは新しいアカウントでは利用できません。 譲渡要求を受け取ったユーザーが、譲渡要求を受け入れるには、Visual Studio ライセンスが必要になります。 サブスクリプションでは、ユーザーのアカウントで利用可能な Visual Studio クレジットが使用されます。 詳細については、[Visual Studio と Partner Network のサブスクリプションの譲渡](#transfer-visual-studio-and-partner-network-subscriptions)に関するセクションを参照してください。


## <a name="frequently-asked-questions-faq-for-recipients"></a>よく寄せられる質問 (FAQ) (譲渡先向け)

以下の FAQ は、別のアカウントから Azure サブスクリプションの課金所有権を受け入れるユーザー向けのものです。

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>別のアカウントからサブスクリプションの課金所有権を引き継ぐ場合、そのアカウントのユーザーが引き続きそのリソースにアクセスすることはできますか。

はい。 しかし、[管理者ロール](add-change-subscription-administrator.md)と [Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)が削除される可能性があります。 ご自分のアカウントがサブスクリプションのテナントとは異なる Azure AD テナント内にあり、譲渡要求を送信したユーザーがサブスクリプションをご自分のアカウントのテナントに移行した場合、アクセス権が失われます。 サブスクリプション内のリソースにアクセスするための Azure ロールが割り当てられているユーザーを表示するには、次の手順を使用します。

1. [Azure portal の [サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスします。
1. チェックするサブスクリプションを選択して、左側のウィンドウで **[アクセス制御 (IAM)]** を選択します。
1. ページの上部で、 **[ロールの割り当て]** を選択します。 [ロールの割り当て] ページに、サブスクリプションに対するアクセス権が付与されているすべてのユーザーが一覧表示されます。

[Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)が譲渡中に削除された場合でも、元の所有者アカウントのユーザーは、次のような他のセキュリティ メカニズムを通じて、引き続きサブスクリプションにアクセスできます。

* サブスクリプションのリソースに対する管理者権限をユーザーに付与する管理証明書。 詳細については、「[Azure の管理証明書の作成とアップロード](../../cloud-services/cloud-services-certs-create.md)」をご覧ください。
* Storage などのサービス用のアクセス キー。 詳細については、「[Azure ストレージ アカウントについて](../../storage/common/storage-create-storage-account.md)」を参照してください。
* Azure Virtual Machines などのサービス用のリモート アクセス資格情報。

譲渡先でリソースへのアクセスを制限する必要がある場合、サービスに関連付けられているすべてのシークレットの更新を検討する必要があります。 ほとんどのリソースは、次の手順を使って更新できます。

  1. [Azure portal](https://portal.azure.com) にサインインします。
  2. ハブ メニューで、 **[すべてのリソース]** を選択します。
  3. リソースを選択します。
  4. リソースのページで **[設定]** を選択します。 ここで、既存のシークレットを表示して更新できます。

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>請求サイクルの途中でサブスクリプションの課金所有権を引き継いだ場合、請求サイクル全体の料金を支払うことになりますか。

お使いのアカウントでは、譲渡時点より後に報告された使用量についてお支払いいただくことになります。 譲渡する前に発生したにもかかわらず、譲渡後に報告される使用量もあります。 この使用量は、お客様のアカウントの請求書に記載されます。

### <a name="can-i-use-a-different-payment-method"></a>別の支払い方法を使用できますか。

はい。 譲渡要求を受け入れるときに、アカウントに関連付けられている既存の支払い方法を選択することも、新しい支払い方法を追加することもできます。

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>元のアカウント所有者が組織からいなくなった場合、私のエンタープライズ契約 (EA) のサブスクリプション アカウント所有権を譲渡するにはどうすればよいですか?

元のアカウント所有者が組織の一員ではなくなった後でも、エンタープライズ管理者は、任意のアカウントのアカウント所有権を更新できます。 このためには、EA Portal で[すべてのサブスクリプションのアカウント所有権を譲渡する](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)ための手順に従います。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="why-dont-i-see-the-transfer-subscription-button"></a><a id="no-button"></a>[サブスクリプションの譲渡] ボタンが表示されないのはなぜですか。

セルフサービスのサブスクリプションの譲渡は、ご利用の課金アカウントでは行えません。 現時点では、Azure portal で Enterprise Agreement (EA) アカウントのサブスクリプションの課金所有権の譲渡はサポートされていません。 また、Microsoft の担当者と協力して作成された Microsoft 顧客契約アカウントでも、課金所有権の譲渡はサポートされていません。

### <a name="why-doesnt-my-subscription-type-support-transfer"></a><a id="no-button"></a> 自分のサブスクリプションで譲渡がサポートされていないのはなぜですか。

一部のサブスクリプションでは課金所有権の譲渡はサポートされていません。 譲渡がサポートされているサブスクリプションの種類一覧については、「[サポートされているサブスクリプションの種類](#supported-subscription-types)」を参照してください

### <a name="why-am-i-receiving-an-access-denied-error-when-i-try-to-transfer-billing-ownership-of-a-subscription"></a><a id="no-button"></a> サブスクリプションの課金所有権を譲渡しようとすると、アクセス拒否エラーが発生しました。理由を教えてください。

このエラーは、Microsoft Azure プランのサブスクリプションを譲渡しようとしたときに、必要な権限がない場合に表示されます。 Microsoft Azure プランのサブスクリプションを譲渡するには、サブスクリプションの請求先となる請求書セクションで、ご自分が所有者または共同作成者になっている必要があります。 詳細については、「[請求書セクションのサブスクリプションを管理する](understand-mca-roles.md#manage-subscriptions-for-invoice-section)」を参照してください。


## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- サービス管理者、共同管理者、Azure ロールの割り当てを確認して更新します。 詳細については、「[Azure サブスクリプション管理者を追加または変更する](add-change-subscription-administrator.md)」と「[Azure portal を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
