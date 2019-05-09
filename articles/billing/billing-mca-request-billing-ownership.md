---
title: Azure サブスクリプションの課金所有権を取得する | Microsoft Docs
description: 他のユーザーから Azure サブスクリプションの課金所有権を要求する方法について説明します。
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: be8c7fcebca224196d9eac7d22387989b1bdfd46
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890935"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>他のユーザーから Azure サブスクリプションの課金所有権を取得する

既存の課金所有者が組織を離れる場合や、請求先アカウントと通じてサブスクリプション費を支払う場合、Azure サブスクリプションの所有権を取得できます。

他の請求先アカウントの既存の所有者から Azure サブスクリプションの所有権を取得する要求を送信できます。 所有権を取得すると、サブスクリプションの課金の責任は、自身の請求書セクションに移されます。

課金所有権を要求するには、**請求書セクション所有者**または**請求書セクション共同作成者**であることが必要です。 詳細については、[請求書セクションのロールとタスク](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)に関するページを参照してください。

この記事では、Microsoft 顧客契約の請求先アカウントについて説明します。 [Microsoft の顧客契約にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-customer-agreement)。

## <a name="request-billing-ownership-in-the-azure-portal"></a>Azure portal で課金所有権を要求する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[コストの管理と請求]** で検索します。

   ![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 請求書セクションに移動します。 アクセス方法によっては、請求先アカウントまたは課金プロファイルを選択する必要があります。 請求先アカウントまたは課金プロファイルから、**[請求書セクション]** を選択し、請求書セクションを選択します。
   <!-- Todo - Add a screenshot -->

4. 左下から **[Transfer requests]**(譲渡要求) を選択します。

5. ページの最上部から **[追加]** を選択します。

6. 要求している課金所有権を持つユーザーの電子メール アドレスを入力します。 ユーザーは、Microsoft オンライン サービス プログラムの請求先アカウントのアカウント管理者またはエンタープライズ契約のアカウント所有者である必要があります。 詳細については、[Azure portal での請求先アカウントの表示](billing-view-all-accounts.md)に関するページを参照してください。

   ![新しい譲渡要求の追加を示すスクリーンショット](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. **[譲渡要求を送信します]** を選択します。

8. ユーザーは、譲渡要求を確認する手順を示す電子メールを受信します。

   ![譲渡要求電子メールのレビューを示すスクリーンショット](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. 譲渡要求を承認するには、ユーザーは、電子メールのリンクを選択し、指示に従います。

    ![譲渡要求電子メールのレビューを示すスクリーンショット](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>Azure portal で譲渡要求のステータスを調べる

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[コストの管理と請求]** で検索します。

   ![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 請求書セクションに移動します。 アクセス方法によっては、請求先アカウントまたは課金プロファイルを選択する必要があります。 請求先アカウントまたは課金プロファイルから、**[請求書セクション]** を選択し、請求書セクションを選択します。
   <!-- Todo - Add a screenshot -->

4. 左下から **[Transfer requests]**(譲渡要求) を選択します。

5. 譲渡要求ページには、次の情報が表示されます。

    ![譲渡要求の一覧を示すスクリーンショット](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

   |列|定義|
   |---------|---------|
   |要求日|譲渡要求が送信された日付|
   |Recipient|課金所有権を譲渡する要求の送信先のユーザーの電子メール アドレス|
   |有効期限|要求の有効期限が切れる日付|
   |Status|譲渡要求の状態|

    譲渡要求は、次の状態のいずれかになります。

   |Status|定義|
   |---------|---------|
   |進行中|ユーザーは譲渡要求を承諾していません|
   |処理中|ユーザーは譲渡要求を承諾しました。 ユーザーが選択したサブスクリプションの課金が、請求書セクションに移されています|
   |完了| ユーザーが選択したサブスクリプションの課金が、請求書セクションに移されています|
   |エラーで終了|要求が完了しましたが、ユーザーが選択した一部のサブスクリプションの課金を移せませんでした|
   |有効期限切れ|ユーザーは予定どおりには要求を受諾せず、期限切れになりました|
   |Canceled|譲渡要求へのアクセス権を持つユーザーが要求を取り消しました|
   |拒否|ユーザーは譲渡要求を拒否しました|

6. 譲渡要求を選択して、詳細を表示します。 譲渡詳細ページには、次の情報が表示されます。
   <!-- Todo - Add a screenshot -->

   |列  |定義|
   |---------|---------|
   |譲渡要求 ID|譲渡要求の一意の ID。 サポート要求を送信する場合、ID を Azure サポートに知らせると、サポート要求が迅速に処理されます|
   |譲渡要求日|譲渡要求が送信された日付|
   |譲渡要求者|譲渡要求を送信したユーザーの電子メール アドレス|
   |譲渡要求の有効期限| 譲渡要求の有効が期限が切れる日付|
   |受信者のメール アドレス|課金所有権を譲渡する要求の送信先のユーザーの電子メール アドレス|
   |受信者に送信された譲渡リンク|譲渡要求を確認するようにユーザーに送信された URL|

## <a name="additional-information"></a>追加情報

次のセクションでは、譲渡サブスクリプションに関する追加情報が示されます。

### <a name="no-service-downtime"></a>サービスのダウンタイムは発生しない

サブスクリプションの Azure サービスは、中断することなく引き続き実行されます。 ユーザーが譲渡するように選択する Azure サブスクリプションの課金関係だけを移します。

### <a name="disabled-subscriptions"></a>無効なサブスクリプション

無効なサブスクリプションは譲渡できません。 サブスクリプションは、課金所有権を譲渡するにはアクティブな状態になっている必要があります。

### <a name="azure-resources-transfer"></a>Azure リソースの譲渡

VM、ディスク、Web サイトなどのサブスクリプションからすべてのリソースが譲渡されます。

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 製品の譲渡

Azure Marketplace 製品は、それぞれのサブスクリプションとともに譲渡されます。

### <a name="azure-reservations-transfer"></a>Azure 予約の譲渡

Azure 予約は、自動的にはサブスクリプションとともに移動しません。 予約を移動するように [Azure サポートに連絡](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)してください。

### <a name="access-to-azure-services"></a>Azure サービスへのアクセス

Azure RBAC (ロールベースのアクセス制御) を使用して設定された Azure リソースへのアクセスは、譲渡中には影響を受けません。

### <a name="azure-support-plan"></a>Azure サポート プラン

Azure サポートは、サブスクリプションとともには譲渡されません。 ユーザーは、すべての Azure サブスクリプションを譲渡する場合、サポート プランをキャンセルするように依頼してください。

### <a name="charges-for-transferred-subscription"></a>譲渡されたサブスクリプションの料金

サブスクリプションの元の課金所有者は、譲渡が完了する時点までに報告されたすべての料金を負います。 請求書セクションは、譲渡の時点以降に報告された料金を負います。 譲渡する前に発生したにもかかわらず、譲渡後に報告される料金もあります。 これらの料金は請求書セクションに表示されます。

### <a name="supported-offers"></a>サポートされるオファー

CSP オファーを除き、あらゆる種類またはオファーのサブスクリプションを譲渡できます。

### <a name="cancel-a-transfer-request"></a>譲渡要求のキャンセル

要求が承認または拒否されるまで、譲渡要求をキャンセルできます。 譲渡要求をキャンセルするには、譲渡詳細ページに移動して、ページの下部からキャンセルを選択します。

### <a name="software-as-a-service-saas-transfer"></a>サービスとしてのソフトウェア (SaaS) の譲渡

SaaS 製品は、サブスクリプションとともには渡されません。 ユーザーに、[Azure サポートに連絡](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)して SaaS 製品の課金所有権を譲渡するように依頼します。 課金所有権とともに、ユーザーはリソースの所有権を譲渡することもできます。 リソースの所有権では、製品の詳細の削除や表示など、管理操作を実行できます。 ユーザーは、リソース所有権を譲渡するために、SaaS 製品でリソース所有者である必要があります。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次の手順

- Azure サブスクリプションの課金所有権は、請求書セクションに譲渡されます。 [Azure portal](https://portal.azure.com) でこれらのサブスクリプションの料金を追跡します。
- これらのサブスクリプションの料金を表示および管理するためのアクセス許可を他のユーザーに与えます。 詳細については、[請求書セクションのロールとタスク](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)に関するページを参照してください。
