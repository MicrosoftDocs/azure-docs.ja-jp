---
title: "ロールを使用した Azure 課金情報へのアクセスの管理 | Microsoft Docs"
description: 
services: 
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: vikdesai
ms.openlocfilehash: c70904097f139bc2178feed83f1cf1274f3c738d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>ロールに基づくアクセス制御を使用した Azure の課金情報へのアクセスの管理

Azure 課金情報へのアクセス権をチームのメンバーに与えるには、いずれかのユーザー ロール (アカウント管理者、サービス管理者、共同管理者、所有者、共同作成者、閲覧者または請求閲覧者) をサブスクリプションに割り当てます。 メンバーに [Azure Portal](https://portal.azure.com/) での課金情報へのアクセス権が与えられ、[請求 API](billing-usage-rate-card-overview.md) を使用して請求書 (オプトイン済み) と使用明細をプログラムによって取得できるようになります。 ロールを付与できるユーザーの詳細や、ロールごとに行うことができる操作については、[Azure RBAC のロール](../active-directory/role-based-access-built-in-roles.md)に関する記事を参照してください。

## <a name="opt-in"></a> その他のユーザーの請求書へのアクセスを許可する

アカウント管理者は、[Azure Portal](https://portal.azure.com/) の使用をオプトインして、他のユーザーによる請求書へのアクセスや API を介した請求書へのアクセスを許可する必要があります。

1. アカウント管理者として、Azure Portal の[[サブスクリプション] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)でサブスクリプションを選択します。

1. **[請求書]** を選択し、**[Access to invoices (請求書へのアクセス)]** を選択します。

    ![請求書へのアクセスの委任方法を示すスクリーンショット](./media/billing-manage-access/AA-optin.png)

1. アクセスを**有効**にして変更を保存し、サブスクリプション スコープ ロール内のユーザーが請求書をダウンロードできるようにします。

    ![請求書へのアクセスの委任のオン/オフを示すスクリーンショット](./media/billing-manage-access/AA-optinAllow.png)

オプトインにより、サブスクリプションのサービス管理者、共同管理者、所有者、共同作成者、閲覧者または請求閲覧者が、Azure Portal で PDF 請求書をダウンロードできるようになります。 ただし、現時点では、2016 年 12 月よりも前の請求書を入手できるのはアカウント管理者のみです。

アカウント管理者は、請求書を電子メールで受け取るように構成することもできます。 詳細については、「[メールで請求書を入手する](billing-download-azure-invoice-daily-usage-date.md)」を参照してください。

## <a name="adding-users-to-the-billing-reader-role"></a>ユーザーを請求閲覧者ロールに追加する

請求閲覧者ロールには、Azure Portal でサブスクリプションの課金情報に対する読み取り専用アクセス権がありますが、VM やストレージ アカウントなどのサービスへのアクセス権はありません。 サブスクリプションの課金情報へのアクセス件を必要とするが、Azure サービスを管理する権限は必要ないユーザーに、請求閲覧者ロールを割り当てます。 このロールが適しているのは、組織で Azure サブスクリプションの財務とコストの管理のみを行うユーザーです。

1. Azure Portal の [[サブスクリプション] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、自分のサブスクリプションを選択します。

1. **[アクセス制御 (IAM)]** を選択し、**[追加]** をクリックします。

    ![[サブスクリプション] ブレードの IAM を表示するスクリーンショット](./media/billing-manage-access/select-iam.PNG)

1. **[請求閲覧者]** を **[ロールの選択]** ページで選択します。

    ![ポップアップ ビューの [請求閲覧者] を表示するスクリーンショット](./media/billing-manage-access/select-roles.PNG)

1. 招待するユーザーの電子メール アドレスを入力し、**[OK]** をクリックして招待メールを送信します。

    ![ユーザーを招待するために電子メール アドレスを入力するスクリーンショット](./media/billing-manage-access/add-user.PNG)

1. 招待メールの指示に従って、請求閲覧者としてログインします。

    ![Azure Portal で請求閲覧者が見られる情報を表示するスクリーンショット](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> 請求閲覧者機能はプレビュー段階であり、エンタープライズ (EA) サブスクリプションや非グローバル クラウドはまだサポートされていません。

## <a name="adding-users-to-other-roles"></a>ユーザーをその他のロールに追加する

所有者または共同作成者など他のロールのユーザーは、課金状況だけではなく、Azure サービスにもアクセスできます。 これらのロールを管理するには、「[サブスクリプションまたはサービスを管理する Azure 管理者ロールを追加または変更する](billing-add-change-azure-subscription-administrator.md)」を参照してください。

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>[アカウント センター](https://account.windowsazure.com)にアクセスできるユーザー

アカウント センターにログインできるのは、アカウント管理者のみです。 アカウント管理者はサブスクリプションの法律上の所有者です。 既定では、他のユーザーに[サブスクリプション所有権が移され](billing-subscription-transfer.md)ない限り、Azure サブスクリプションにサインアップした人物つまりサブスクリプションを購入した人物がアカウント管理者です。 アカウント管理者は、サブスクリプションの作成、サブスクリプションのキャンセル、サブスクリプションの請求先住所の変更、サブスクリプションのアクセス ポリシーの管理を行うことができます。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。
