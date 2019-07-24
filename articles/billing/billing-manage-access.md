---
title: Azure 課金情報へのアクセスの管理 | Microsoft Docs
description: チームのメンバーが Azure 課金情報にアクセスできるようにする方法について説明します。
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 8c2843840790d1e0dbfd4a789775c6c7ceb51a54
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838257"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Asure の課金情報へのアクセスの管理

ほとんどのサブスクリプションの場合、Azure portal の **[サブスクリプション]** からチームのメンバーに課金情報へのアクセス権を付与できます。 エンタープライズ契約の Azure のお客様 (EA のお客様) で、エンタープライズ管理者の場合は、Enterprise Portal で部門管理者とアカウント所有者にアクセス許可を付与できます。

## <a name="give-access-to-billing"></a>課金情報へのアクセス権の付与

EA のお客様を除くすべてのユーザーは、チームのメンバーに次のユーザー ロールのいずれかを割り当てることで、Azure 課金情報へのアクセス権を付与できます。

- アカウント管理者
- サービス管理者
- 共同管理者
- Owner
- Contributor
- Reader
- Billing Reader

ロールの割り当てについては、「[RBAC と Azure portal を使用してアクセスを管理する](../role-based-access-control/role-assignments-portal.md)」を参照してください。

これらのロールは、[Azure portal](https://portal.azure.com/) で請求情報にアクセスすることができます。 これらのロールが割り当てられているユーザーは、[Billing API](billing-usage-rate-card-overview.md) を使用して請求書と使用状況の詳細をプログラムで取得することもできます。 詳細については、[Azure RBAC のロール](../role-based-access-control/built-in-roles.md)に関するページを参照してください。

### <a name="opt-in"></a> ユーザーに請求書のダウンロードを許可する

アカウント管理者は、ご自分のチームのメンバーに適切なロールを割り当てた後、Azure portal 内で請求書をダウンロードするためのアクセスを有効にする必要があります。 2016 年 12 月よりも前の請求書を入手できるのはアカウント管理者のみです。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. アカウント管理者として、Azure Portal の[[サブスクリプション] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)でサブスクリプションを選択します。

1. **[請求書]** を選択し、**[Access to invoices (請求書へのアクセス)]** を選択します。

    ![請求書へのアクセスの委任方法を示すスクリーンショット](./media/billing-manage-access/AA-optin.png)

1. **[オン]** を選択して、保存します。

    ![請求書へのアクセスの委任のオン/オフを示すスクリーンショット](./media/billing-manage-access/AA-optinAllow.png)

アカウント管理者は、請求書を電子メールで受け取るように構成することもできます。 詳細については、「[メールで請求書を入手する](billing-download-azure-invoice-daily-usage-date.md)」を参照してください。

## <a name="give-read-only-access-to-billing"></a>課金情報への読み取り専用アクセス権の付与

サブスクリプションの課金情報への読み取り専用アクセス権は必要であっても、Azure サービスを管理または作成する権限は必要ないユーザーに、請求閲覧者ロールを割り当てます。 このロールが適しているのは、組織で Azure サブスクリプションの財務とコストの管理を担当しているユーザーです。

EA のお客様の場合は、アカウント所有者または部門管理者がチーム メンバーに請求閲覧者ロールを割り当てることができます。 ただし、その請求閲覧者が部門またはアカウントの課金情報を表示する場合は、エンタープライズ管理者が Enterprise Portal で **AO ビューの請求額**または **DA ビューの請求額**のポリシーを有効にする必要があります。

請求閲覧者機能はプレビュー段階であり、非グローバル クラウドはまだサポートされていません。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. Azure Portal の [[サブスクリプション] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、自分のサブスクリプションを選択します。

1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当て]** を選択して、このサブスクリプションのすべてのロールの割り当てを表示します。
1. **[ロールの割り当ての追加]** を選択します。
1. **[ロール]** ドロップダウン リストで、**[請求閲覧者]** を選択します。
1. **[選択]** テキストボックスに、追加するユーザーの名前またはメール アドレスを入力します。
1. ユーザーを選択します。
1. **[保存]** を選択します。
1. 数分後に、サブスクリプション スコープで、ユーザーに請求閲覧者ロールが割り当てられます。
1. 請求閲覧者は、サインイン用リンクが含まれている電子メールを受信します。

    ![Azure Portal で請求閲覧者が見られる情報を表示するスクリーンショット](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>部門管理者またはアカウント所有者に課金情報へのアクセスを許可する

エンタープライズ管理者は、部門管理者とアカウント所有者に、管理している部門やアカウントに関連付けられている使用状況の詳細とコストの表示を許可することができます。

1. エンタープライズ管理者として [EA ポータル](https://ea.azure.com/)にサインインします。
1. **[管理]** を選択します。
1. **[登録]** で、部門管理者が使用状況とコストを表示できるようにする場合は、**[DA ビューの請求額]** を **[有効]** に変更します。
1. アカウント所有者が使用状況とコストを表示できるようにする場合は、**[AO ビューの請求額]** を **[有効]** に変更します。


詳細については、「[Azure の Azure Enterprise Agreement 管理者ロールを理解する](billing-understand-ea-roles.md)」を参照してください。

## <a name="only-account-admins-can-access-account-center"></a>アカウント センターにはアカウント管理者のみがアクセス可能

アカウント管理者はサブスクリプションの法律上の所有者です。 既定では、他のユーザーに[サブスクリプション所有権が移され](billing-subscription-transfer.md)ない限り、Azure サブスクリプションにサインアップした人物つまりサブスクリプションを購入した人物がアカウント管理者です。 アカウント管理者は、[アカウント センター](https://account.azure.com/Subscriptions)から、サブスクリプションの作成、サブスクリプションのキャンセル、サブスクリプションの請求先住所の変更、サブスクリプションのアクセス ポリシーの管理を行うことができます。

## <a name="next-steps"></a>次の手順

- 所有者または共同作成者など他のロールのユーザーは、課金状況だけではなく、Azure サービスにもアクセスできます。 これらのロールの管理については、「[RBAC と Azure portal を使用してアクセスを管理する](../role-based-access-control/role-assignments-portal.md)」を参照してください。
- ロールの詳細については、「[Azure リソースの組み込みロール](../role-based-access-control/built-in-roles.md)」を参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
