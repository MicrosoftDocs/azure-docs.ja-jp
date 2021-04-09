---
title: Azure サブスクリプションの課金所有権を譲渡する
description: Azure サブスクリプションの課金所有権を別のアカウントに譲渡する方法について説明します。
keywords: Azure サブスクリプションを譲渡する, Azure サブスクリプションの譲渡, Azure サブスクリプションを別のアカウントに移動する, Azure サブスクリプション所有者の変更, Azure サブスクリプションを別のアカウントに譲渡する, Azure 課金所有権を譲渡する
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/05/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: c3142fd41830487453a3cc980a87cdca72cf7213
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094136"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure サブスクリプションの課金所有権を別のアカウントに譲渡する

この記事では、Azure サブスクリプションの課金所有権を別のアカウントに譲渡するために必要な手順について説明します。 サブスクリプションの課金所有権を譲渡する前に、「[Azure サブスクリプションの課金所有権の譲渡の概要](../understand/subscription-transfer.md)」をお読みください。

課金所有権を維持したまま、サブスクリプションの種類を変更する場合は、[別のオファーへの Azure サブスクリプションの切り替え](switch-azure-offer.md)に関するページを参照してください。 サブスクリプション内のリソースにアクセスできるユーザーを制御する場合は、「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

マイクロソフト エンタープライズ契約 (EA) のお客様であれば、お客様のエンタープライズ管理者が、アカウント間でサブスクリプションの課金所有権を移すことができます。 詳細については、「[Azure サブスクリプションまたはアカウントの所有権を変更する](ea-portal-administration.md#change-azure-subscription-or-account-ownership)」をご覧ください。

アカウントの課金管理者のみがサブスクリプションの所有権を譲渡できます。

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure サブスクリプションの課金所有権を譲渡する

1. 譲渡するサブスクリプションがある課金アカウントの管理者として、[Azure portal](https://portal.azure.com) にサインインします。 自分が管理者かどうかわからない場合、または誰であるかを判断する必要がある場合は、「[アカウントの課金管理者を判断する](../understand/subscription-transfer.md#whoisaa)」を参照してください。
1. "**コスト管理 + 請求**" を検索します。  
   ![Azure portal の検索を表示しているスクリーンショット](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. 左側のウィンドウで、 **[サブスクリプション]** を選択します。 アクセス権によっては、課金スコープを選択してから、 **[サブスクリプション]** または **[Azure サブスクリプション]** を選択しなければならない場合があります。
1. 譲渡するサブスクリプションの **[課金所有権の譲渡]** を選択します。  
   ![譲渡するサブスクリプションの選択](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. サブスクリプションの新しい所有者となる、アカウントの課金管理者であるユーザーのメール アドレスを入力します。
1. 別の Azure AD テナントのアカウントにサブスクリプションを譲渡する場合は、サブスクリプションを新しいアカウントのテナントに移行するかどうかを選択します。 詳細については、「[サブスクリプションを別の Azure AD テナント内のアカウントに譲渡する](#transfer-a-subscription-to-another-azure-ad-tenant-account)」を参照してください。
    > [!IMPORTANT]
    > 新しいアカウントの Azure AD テナントにサブスクリプションを移行することを選択した場合は、サブスクリプション内のリソースにアクセスするためのすべての [Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)が完全に削除されます。 譲渡要求を受け入れた新しいアカウントのユーザーにのみ、サブスクリプションのリソースの管理アクセス権が付与されます。 または、 **[サブスクリプション Azure AD テナント]** オプションをオフにし、サブスクリプションを新しいアカウントのテナントに移行せずに課金所有権を譲渡することもできます。 これを行うと、Azure リソースにアクセスするための既存の Azure ロールの割り当てが維持されます。  
    ![譲渡の送信ページ](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. **[譲渡要求を送信します]** を選択します。
1. ユーザーは、譲渡要求を確認する手順を示す電子メールを受信します。  
   ![受信者に送信されるサブスクリプションの譲渡メール](./media/billing-subscription-transfer/billing-receiver-email.png)
1. 譲渡要求を承認するには、ユーザーは、電子メールのリンクを選択し、指示に従います。 次に、サブスクリプションの支払いに使用する支払い方法を選択します。 Azure アカウントを持っていない場合は、新しいアカウントにサインアップする必要があります。  
   ![1 ページ目の [サブスクリプションの譲渡] Web ページ](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![2 ページ目の [サブスクリプションの譲渡] Web ページ](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![3 ページ目の [サブスクリプションの譲渡] Web ページ](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. Success! サブスクリプションが譲渡されました。

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>サブスクリプションを別の Azure AD テナント アカウントに譲渡する

Azure へのサインアップ時に、Azure Active Directory (AD) テナントが自動的に作成されます。 このテナントは、自分のアカウントを表すものです。 サブスクリプションとリソースへのアクセスの管理には、このテナントを使用します。

新しいサブスクリプションを作成すると、そのサブスクリプションは自分のアカウントの Azure AD テナントでホストされます。 サブスクリプションまたはリソースへのアクセス権を別のユーザーに付与するには、自分のテナントに参加するように招待する必要があります。 そうすることで、サブスクリプションとリソースへのアクセスを制御できます。

サブスクリプションの課金所有権を別の Azure AD テナント内のアカウントに譲渡すると、サブスクリプションをその新しいアカウントのテナントに移動することができます。 これを行うと、サブスクリプションとそのリソースを管理するための [Azure ロールが割り当て](../../role-based-access-control/role-assignments-portal.md)られたすべてのユーザー、グループ、またはサービス プリンシパルのアクセス権が失われます。 譲渡要求を受け入れた新しいアカウントのユーザーにのみ、リソースの管理アクセス権が付与されます。 新しい所有者は、アクセス権を失ったユーザーにアクセス権を付与するために、これらのユーザーをサブスクリプションに手動で追加する必要があります。 詳細については、「[Azure サブスクリプションを別の Azure AD ディレクトリに移転する](../../role-based-access-control/transfer-subscription.md)」を参照してください。

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Visual Studio と Partner Network のサブスクリプションを譲渡する

Visual Studio と Microsoft Partner Network のサブスクリプションでは、付随する Azure クレジットが毎月提供されます。 こうしたサブスクリプションを譲渡した場合、譲渡元のクレジットは、譲渡先の課金アカウントでは使用できません。 サブスクリプションでは、譲渡先の課金アカウントにあるクレジットが使用されます。 たとえば、9 月 9 日に Bob が Visual Studio Enterprise サブスクリプションを Jane のアカウントに譲渡し、Jane がその譲渡を受け入れたとします。 譲渡の完了後、そのサブスクリプションでは、Jane のアカウントのクレジットが使用されるようになります。 クレジットは毎月 9 日にリセットされます。

## <a name="next-steps-after-accepting-billing-ownership"></a>所有権を受け取った後の手順

Azure サブスクリプションの課金所有権を受け取った場合は、以下の手順を確認することをお勧めします。

1. サービス管理者、共同管理者、Azure ロールの割り当てを確認して更新します。 詳細については、「[Azure サブスクリプション管理者を追加または変更する](add-change-subscription-administrator.md)」と「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
1. このサブスクリプションのサービスに関連付けられている以下の資格情報を更新します。
   1. サブスクリプションのリソースに対する管理者権限をユーザーに付与する管理証明書。 詳細については、「 [Azure の管理証明書の作成とアップロード](../../cloud-services/cloud-services-certs-create.md) を参照してください。
   1. Storage などのサービス用のアクセス キー。 詳細については、「[Azure ストレージ アカウントについて](../../storage/common/storage-account-create.md)」を参照してください。
   1. Azure Virtual Machines などのサービス用のリモート アクセス資格情報。
1. パートナーがいる場合は、このサブスクリプションのパートナー ID を更新することを検討します。 パートナー ID は、[Azure Portal](https://portal.azure.com) で更新できます。 詳細については、「[Azure アカウントにパートナー ID をリンクする](link-partner-id.md)」を参照してください

## <a name="cancel-a-transfer-request"></a>譲渡要求のキャンセル

アクティブにできる移転リクエストは一度に 1 つだけです。 移転リクエストは 15 日間有効です。 15 日が経過すると、移転リクエストは期限切れとなります。

移転リクエストを取り消すには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[サブスクリプション]** に移動して、移転リクエストを送信するサブスクリプションを選択し、 **[Transfer billing ownership]\(課金所有権の譲渡\)** を選択します。
1. ページ下部の **[Cancel the transfer request]\(譲渡要求を取り消す\)** を選択します。

:::image type="content" source="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" alt-text="[Transfer billing ownership]\(課金所有権の譲渡\) ウィンドウと [Cancel the transfer request]\(譲渡要求を取り消す\) オプションの表示例" lightbox="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" :::

## <a name="troubleshooting"></a>トラブルシューティング

サブスクリプションの譲渡に問題がある場合は、次のトラブルシューティング情報を参照してください。

### <a name="original-azure-subscription-billing-owner-leaves-your-organization"></a>Azure サブスクリプションの元の課金所有者が退職した

> [!Note]
> この記事は、あくまで Microsoft 顧客契約の課金アカウントについて記載しています。 [Microsoft 顧客契約](mca-request-billing-ownership.md#check-for-access)にアクセスできるかどうかを確認してください。

Azure アカウントと Azure サブスクリプションを作成した元の課金アカウント所有者が退職する可能性があります。 その場合、組織の Azure Active Directory には、対応するユーザー ID が存在しません。 Azure サブスクリプションには課金所有者が不在という状況になります。 この状況では、請求書の表示と支払いなど、アカウントへの請求にまつわる操作をだれも実行できなくなります。 サブスクリプションは期限切れの状態になる可能性があります。 やがて料金の不払いから、サブスクリプションが無効になるでしょう。 最終的にはサブスクリプションは削除され、そのサブスクリプションで実行されるすべてのサービスに影響が及びます。

サブスクリプションに有効な課金アカウント所有者が存在しない場合、その状況を伝えるメールが Azure から別の課金アカウント所有者、サービス管理者 (存在する場合)、共同管理者 (存在する場合)、サブスクリプション所有者宛てに送信されます。そのメールの中に、サブスクリプションの課金所有権を引き受けるためのリンクが記載されています。 いずれかのユーザーがそのリンクを選択することで、課金所有権を引き受けることができます。 課金ロールについて詳しくは、[課金ロール](understand-mca-roles.md)に関するページ、および[従来のロールと Azure RBAC ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)に関するページを参照してください。

次に示すのは、実際メールの例です。

:::image type="content" source="./media/billing-subscription-transfer/orphaned-subscription-email.png" alt-text="課金所有権を引き受けるためのメールの例を示すスクリーンショット。" lightbox="./media/billing-subscription-transfer/orphaned-subscription-email.png" :::

加えて、Azure portal のサブスクリプションの詳細ウィンドウには、課金所有者、サービス管理者、共同管理者、サブスクリプション所有者に向けたバナーが表示されます。 課金所有権を引き受けるには、バナーのリンクをクリックしてください。

:::image type="content" source="./media/billing-subscription-transfer/orphaned-subscription-example.png" alt-text="有効な課金所有者が不在となっているサブスクリプションの例を示すスクリーンショット。" lightbox="./media/billing-subscription-transfer/orphaned-subscription-example.png" :::

### <a name="the-transfer-subscription-option-is-unavailable"></a>[サブスクリプションの譲渡] オプションを使用できない

<a name="no-button"></a> 

セルフサービスのサブスクリプションの譲渡は、ご利用の課金アカウントでは行えません。 現時点では、Azure portal で Enterprise Agreement (EA) アカウントのサブスクリプションの課金所有権の譲渡はサポートされていません。 また、Microsoft の担当者と協力して作成された Microsoft 顧客契約アカウントでも、課金所有権の譲渡はサポートされていません。

###  <a name="not-all-subscription-types-can-transfer"></a>すべてのサブスクリプションの種類で譲渡できない

一部のサブスクリプションでは課金所有権の譲渡はサポートされていません。 譲渡がサポートされているサブスクリプションの種類一覧については、「[サポートされているサブスクリプションの種類](../understand/subscription-transfer.md#supported-subscription-types)」を参照してください

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>サブスクリプションの課金所有権を譲渡しようとしたときにアクセス拒否エラーが表示される

このエラーは、Microsoft Azure プランのサブスクリプションを譲渡しようとしたときに、必要な権限がない場合に表示されます。 Microsoft Azure プランのサブスクリプションを譲渡するには、サブスクリプションの請求先となる請求書セクションで、ご自分が所有者または共同作成者になっている必要があります。 詳細については、「[請求書セクションのサブスクリプションを管理する](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section)」を参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- サービス管理者、共同管理者、Azure ロールの割り当てを確認して更新します。 詳細については、「[Azure サブスクリプション管理者を追加または変更する](add-change-subscription-administrator.md)」と「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。