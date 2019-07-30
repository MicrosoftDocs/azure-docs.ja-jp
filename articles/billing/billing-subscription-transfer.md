---
title: Azure サブスクリプションの課金所有権を別のアカウントに譲渡する | Microsoft Docs
description: Azure サブスクリプションの課金所有権を別のアカウントに譲渡する方法と、そのプロセスに関してよく寄せられる質問 (FAQ) について説明します
keywords: Azure サブスクリプションを譲渡する, Azure サブスクリプションの譲渡, Azure サブスクリプションを別のアカウントに移動する, Azure サブスクリプション所有者の変更, Azure サブスクリプションを別のアカウントに譲渡する, Azure 課金所有権を譲渡する
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d41e32065e3de37eb8f01ab1b836040e7f57b12
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657828"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure サブスクリプションの課金所有権を別のアカウントに譲渡する

自分が退職するときや、Azure サブスクリプションの請求先を別のアカウントに変える場合には、サブスクリプションの課金所有権を譲渡する必要があります。 別のアカウントに課金所有権を譲渡することで、譲渡先アカウントの管理者に、支払い方法の変更、料金の確認、サブスクリプションの取り消しといった請求関連のタスクを行う権限が付与されます。

課金所有権を維持したまま、サブスクリプションの種類を変更する場合は、[別のオファーへの Azure サブスクリプションの切り替え](billing-how-to-switch-azure-offer.md)に関するページを参照してください。 サブスクリプションに含まれるリソースを管理可能なユーザーを制御する方法については、「[Azure リソースの組み込みロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)」を参照してください。

マイクロソフト エンタープライズ契約 (EA) のお客様であれば、お客様のエンタープライズ管理者が、アカウント間でサブスクリプションの課金所有権を移すことができます。 詳細については、「[マイクロソフト エンタープライズ契約 (EA) サブスクリプションの課金所有権を譲渡する](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions)」を参照してください。

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure サブスクリプションの課金所有権を譲渡する

1. 譲渡するサブスクリプションがある課金アカウントの管理者として、[Azure portal](https://portal.azure.com) にサインインします。 自分が管理者であるかどうかを調べる方法については、[よく寄せられる質問](#faq)に関するセクションを参照してください。

1. **[コストの管理と請求]** で検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. 左側のウィンドウで、 **[サブスクリプション]** を選択します。 アクセス権によっては、課金スコープを選択してから、 **[サブスクリプション]** または **[Azure サブスクリプション]** を選択しなければならない場合があります。

1. 譲渡するサブスクリプションの **[課金所有権の譲渡]** を選択します。 

   ![譲渡するサブスクリプションの選択](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. サブスクリプションの新しい所有者になるアカウントの課金管理者であるユーザーのメール アドレスを入力して、 **[Send transfer request]\(譲渡要求の送信\)** を選択します。

    > [!IMPORTANT]
    >
    > 自分のサブスクリプションの課金所有権を別の Azure AD テナント内のユーザー アカウントに譲渡した場合は、そのサブスクリプションに含まれるリソースを管理するための、[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) による全割り当てが完全に削除されます。 サブスクリプション内のリソースを管理するためのアクセス権は、新しい所有者のみに付与されます。 詳細については、[別の Azure AD テナント内のユーザーへのサブスクリプションの譲渡](../active-directory/managed-identities-azure-resources/known-issues.md)に関するページを参照してください。
  
    ![譲渡の送信ページ](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. ユーザーは、譲渡要求を確認する手順を示す電子メールを受信します。

   ![Subscription transfer email to recipient](./media/billing-subscription-transfer/billing-receiver-email.png)

1. 譲渡要求を承認するには、ユーザーは、電子メールのリンクを選択し、指示に従います。 ユーザーは、サブスクリプション料金の支払いに使用する支払い方法を選択するように求められることがあります。 また、Azure アカウントを持っていない場合は、新しいアカウントにサインアップする必要があります。 

   ![First subscription transfer web page](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Second subscription transfer web page](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Second subscription transfer web page](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. 成功です。 サブスクリプションが譲渡されました。

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>サブスクリプションを別の Azure AD テナント内のアカウントに譲渡する

Azure へのサインアップ時に、Azure Active Directory (AD) テナントが自動的に作成されます。 このテナントは、自分のアカウントを表すものです。 サブスクリプションとリソースへのアクセスの管理には、このテナントを使用します。

新しいサブスクリプションを作成すると、そのサブスクリプションは自分のアカウントの Azure AD テナントでホストされます。 サブスクリプションまたはリソースへのアクセス権を別のユーザーに付与するには、そのユーザーを自分のテナントに招待する必要があります。 これにより、サブスクリプションとリソースへのアクセスを制御できます。

サブスクリプションの課金所有権を別の Azure AD テナント内のアカウントに譲渡すると、サブスクリプションがその新しいアカウントのテナントに移動されます。 サブスクリプションに含まれるリソースを管理するための[ロールベースのアクセス権 (RBAC)](../role-based-access-control/overview.md) が割り当てられていたユーザー、グループ、サービス プリンシパルはすべて、そのアクセス権を失います。 譲渡要求を受け入れた新しいアカウントのユーザーにのみ、リソースの管理アクセス権が付与されます。 もともとアクセス権を持っていたユーザーにアクセス権を付与するには、新しい所有者が、[それらのユーザーをサブスクリプションに手動で追加する](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)必要があります。


## <a name="transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Visual Studio、Microsoft Partner Network (MPN)、開発テスト用の従量課金制プランの各サブスクリプションを譲渡する

Visual Studio と Microsoft Partner Network のサブスクリプションでは、付随する Azure クレジットが毎月提供されます。 こうしたサブスクリプションを譲渡した場合、譲渡元のクレジットは、譲渡先の課金アカウントでは使用できません。 サブスクリプションでは、譲渡先の課金アカウントにあるクレジットが使用されます。 たとえば、9 月 9 日に Bob が Visual Studio Enterprise サブスクリプションを Jane のアカウントに譲渡し、Jane がその譲渡を受け入れたとします。 譲渡の完了後、そのサブスクリプションでは、Jane のアカウントのクレジットが使用されるようになります。 クレジットは毎月 9 日にリセットされます。 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>マイクロソフト エンタープライズ契約 (EA) サブスクリプションの課金所有権を譲渡する

エンタープライズ管理者は、加入契約に含まれるアカウント間でサブスクリプションの所有権を譲渡できます。 詳細については、EA ポータルで「[アカウントの所有権の譲渡](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)」を参照してください。

<a id="CSP"></a>

## <a name="next-steps-after-accepting-billing-ownership"></a>課金所有権を受け取った後の次の手順

Azure サブスクリプションの課金所有権を受け取った場合は、以下の手順を確認することをお勧めします。

1. サービス管理者、共同管理者、その他の RBAC ロールを見直して更新します。 詳細については、「[Azure サブスクリプション管理者を追加または変更する](billing-add-change-azure-subscription-administrator.md)」と「[RBAC と Azure portal を使用してアクセスを管理する](../role-based-access-control/role-assignments-portal.md)」を参照してください。
1. このサブスクリプションのサービスに関連付けられている以下の資格情報を更新します。
   1. サブスクリプションのリソースに対する管理者権限をユーザーに付与する管理証明書。 詳細については、「 [Azure の管理証明書の作成とアップロード](../cloud-services/cloud-services-certs-create.md)
   1. Storage などのサービス用のアクセス キー。 詳細については、「[Azure ストレージ アカウントについて](../storage/common/storage-create-storage-account.md)」を参照してください。
   1. Azure Virtual Machines などのサービス用のリモート アクセス資格情報。
1. パートナーがいる場合は、このサブスクリプションのパートナー ID を更新することを検討します。 パートナー ID は、[Azure Portal](https://portal.azure.com) で更新できます。 詳細については、「[Azure アカウントにパートナー ID をリンクする](billing-partner-admin-link-started.md)」を参照してください

<a id="supported"></a>

## <a name="supported-subscription-types"></a>サポートされているサブスクリプションの種類

Azure portal でのサブスクリプションの譲渡は、下記の種類のサブスクリプションについて行うことができます。 現在、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)と [Azure イン オープン プラン (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) のサブスクリプションについては、譲渡はサポートされていません。 対処法ついては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md)」を参照してください。 [スポンサー プラン](https://azure.microsoft.com/offers/ms-azr-0036p/)、サポート プランなどの他のサブスクリプションを譲渡する場合は、[Azure サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

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

\*[EA ポータル経由](#EA)。

\*\*Azure Web サイトでサインアップしたときに作成されたアカウントのみでサポート。 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>よく寄せられる質問 (FAQ) (譲渡元向け)

以下の FAQ は、Azure サブスクリプションの課金所有権を別のアカウントに譲渡するユーザー向けのものです。

### <a name="whoisaa"></a> アカウントの課金管理者とはだれのことですか。

課金管理者とは、アカウントに対する課金を管理する権限が付与されているユーザーのことです。 [Azure portal](https://portal.azure.com) で課金情報にアクセスする権限のほか、サブスクリプションの作成、請求書の表示と支払い、支払い方法の更新など、さまざまな課金関連の作業を行う権限が付与されています。

自分が課金管理者になっているサブスクリプションを特定するには、次の手順に従います。

1. [Azure portal で [コストの管理と請求] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)に移動します。
1. 左側のウィンドウで、 **[サブスクリプション]** を選択します。 アクセス権によっては、課金スコープを選択してから、 **[サブスクリプション]** または **[Azure サブスクリプション]** を選択しなければならない場合があります
1. [サブスクリプション] ページに、自分が課金管理者となっているサブスクリプションがすべて一覧表示されます。

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>あらゆるものが譲渡されるのでしょうか。 リソース グループ、VM、ディスク、他の実行中のサービスは含まれますか?

VM、ディスク、Web サイトなどのすべてのリソースが、新しいアカウントに譲渡されます。 ただし、別の Azure AD テナント内のアカウントにサブスクリプションを譲渡した場合は、そのサブスクリプションへの[管理者ロール](billing-add-change-azure-subscription-administrator.md)と[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/role-assignments-portal.md) による割り当ては[譲渡されません](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)。 また、[アプリの登録](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)とテナント固有のその他のサービスも、サブスクリプションと共に譲渡されることはありません。

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>別の国のアカウントに所有権を譲渡できますか。
申し訳ありませんが、Azure portal では国外への譲渡は実行できません。 サブスクリプションを国外に譲渡する場合は、[サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>2 つのアカウントで管理者になっています。 片方のアカウントのサブスクリプションを、もう片方のアカウントに譲渡することはできますか。
はい。お使いのアカウント間でサブスクリプションを譲渡することは可能です。 概念上、そうしたアカウントは 2 人の異なるユーザーのアカウントと見なされるので、上記の手順を利用してアカウント間でサブスクリプションを譲渡できます。

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>サブスクリプションの譲渡により、サービスのダウンタイムは発生しますか。

同じ Azure AD テナント内のユーザーにサブスクリプションを譲渡する場合、そのサブスクリプションで実行されているリソースに影響は生じません。  ただし、別のテナント内のユーザーにサブスクリプションを譲渡した場合は、そのサブスクリプションに含まれるリソースを管理するための[ロールベースのアクセス権 (RBAC)](../role-based-access-control/overview.md) が付与されていたユーザー、グループ、サービス プリンシパルはすべて、そのアクセス権を失います。 

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>譲渡先は、使用履歴と請求履歴にアクセスできますか。

譲渡先に提供される情報は、過去 1 か月分のサブスクリプションのコストのみです。 使用履歴と請求履歴の他の部分が、サブスクリプションと共に譲渡されることはありません

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Azure サブスクリプションのデータとサービスを新しいサブスクリプションに移行するにはどうすればよいですか。

サブスクリプションの所有権を譲渡できない場合、リソースを手動で移行することもできます。 「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md)」を参照してください。

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Visual Studio または Microsoft Partner Network のサブスクリプションを譲渡する場合、クレジットは新しいアカウントのサブスクリプションに繰り越されますか。

いいえ。譲渡元のクレジットは新しいアカウントでは利用できません。 譲渡要求を受け取ったユーザーが、譲渡要求を受け入れるには、Visual Studio ライセンスが必要になります。 サブスクリプションでは、ユーザーのアカウントで利用可能な Visual Studio クレジットが使用されます。 詳細については、「[Visual Studio、Microsoft Partner Network (MPN)、開発テスト用の従量課金制プランの各サブスクリプションを譲渡する](#transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions)」を参照してください


## <a name="frequently-asked-questions-faq-for-recipients"></a>よく寄せられる質問 (FAQ) (譲渡先向け)

以下の FAQ は、別のアカウントから Azure サブスクリプションの課金所有権を引き継ぐユーザー向けのものです。

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>別のアカウントからサブスクリプションの課金所有権を引き継ぐ場合、そのアカウントのユーザーが引き続きそのリソースにアクセスすることはできますか。

同じ Azure AD テナント内のアカウントにサブスクリプションを譲渡した場合は、そのサブスクリプションに含まれるリソースを管理するための[ロールベースのアクセス権 (RBAC)](../role-based-access-control/overview.md) が付与されていたユーザー、グループ、サービス プリンシパルはすべて、そのアクセス権を維持します。 サブスクリプションに対する RBAC アクセス権が付与されているユーザーを確認するには、次の手順に従います。

1. [Azure portal の [サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスします。
1. チェックするサブスクリプションを選択して、左側のウィンドウで **[アクセス制御 (IAM)]** を選択します。
1. ページの上部で、 **[ロールの割り当て]** を選択します。 [ロールの割り当て] ページに、サブスクリプションに対する RBAC アクセス権が付与されているすべてのユーザーが一覧表示されます。

別の Azure AD テナント内のユーザーにサブスクリプションを譲渡した場合は、そのサブスクリプションに含まれるリソースを管理するための[ロールベースのアクセス権 (RBAC)](../role-based-access-control/overview.md) が付与されていたユーザー、グループ、サービス プリンシパルはすべて、そのアクセス権を失います。 ただし、RBAC アクセス権を失ったユーザーでも、下記のようなセキュリティ メカニズムを介して、まだサブスクリプションにアクセスできることがあります。

* サブスクリプションのリソースに対する管理者権限をユーザーに付与する管理証明書。 詳細については、「[Azure Cloud Services の証明書の概要](../cloud-services/cloud-services-certs-create.md)」を参照してください。
* Storage などのサービス用のアクセス キー。 詳細については、「[Azure ストレージ アカウントについて](../storage/common/storage-create-storage-account.md)」を参照してください。
* Azure Virtual Machines などのサービス用のリモート アクセス資格情報。

譲渡先でリソースへのアクセスを制限する必要がある場合、サービスに関連付けられているすべてのシークレットの更新を検討する必要があります。 ほとんどのリソースは、次の手順を使って更新できます。

  1. [Azure Portal](https://portal.azure.com) にサインインします。
  2. ハブ メニューで、 **[すべてのリソース]** を選択します。
  3. リソースを選択します。
  4. リソースのページで **[設定]** をクリックします。 ここで、既存のシークレットを表示して更新できます。

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>請求サイクルの途中でサブスクリプションの課金所有権を引き継いだ場合、請求サイクル全体の料金を支払うことになりますか。

お使いのアカウントでは、譲渡時点より後に報告された使用量についてお支払いいただくことになります。 譲渡する前に発生したにもかかわらず、譲渡後に報告される使用量もあります。 この使用量は、お客様のアカウントの請求書に記載されます。

### <a name="can-i-use-a-different-payment-method"></a>別の支払い方法を使用できますか。

はい。 譲渡要求を受け入れるときに、アカウントに関連付けられている既存の支払い方法を選択することも、新しい支払い方法を追加することもできます。

## <a name="troubleshooting"></a>トラブルシューティング

### <a id="no-button"></a>[サブスクリプションの譲渡] ボタンが表示されないのはなぜですか。

申し訳ありませんが、セルフサービスのサブスクリプションの譲渡は、課金アカウントでは行えません。 現在、Azure portal では、マイクロソフト エンタープライズ契約 (EA) アカウントの課金所有権の譲渡はサポートされていません。 また、Microsoft の営業担当者を通じて作成された Microsoft 顧客契約アカウントでも、サブスクリプションの課金所有権の譲渡はサポートされていません。 

### <a id="no-button"></a> 自分のサブスクリプションで譲渡がサポートされていないのはなぜですか。 

申し訳ありませんが、一部のサブスクリプションでは課金所有権の譲渡はサポートされていません。 譲渡がサポートされているサブスクリプションの種類一覧については、「[サポートされているサブスクリプションの種類](#supported-subscription-types)」を参照してください

### <a id="no-button"></a> サブスクリプションの課金所有権を譲渡しようとすると、アクセス拒否エラーが発生しました。理由を教えてください。 

このエラーは、Microsoft Azure プランのサブスクリプションを譲渡しようとしたときに、必要な権限がない場合に表示されます。 Microsoft Azure プランのサブスクリプションを譲渡するには、サブスクリプションの請求先となる請求書セクションで、ご自分が所有者または共同作成者になっている必要があります。 詳細については、「[請求書セクションのサブスクリプションを管理する](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section)」を参照してください。


## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

- サービス管理者、共同管理者、その他の RBAC ロールを見直して更新します。 詳細については、「[Azure サブスクリプション管理者を追加または変更する](billing-add-change-azure-subscription-administrator.md)」と「[RBAC と Azure portal を使用してアクセスを管理する](../role-based-access-control/role-assignments-portal.md)」を参照してください。
