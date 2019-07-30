---
title: Azure サブスクリプションの課金所有権の取得
description: 他のユーザーから Azure サブスクリプションの課金所有権を要求する方法について説明します。
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 4c9a8c866a157757121e6a9dd07a0a8559937c5e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490813"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>他のアカウントから Azure サブスクリプションの課金所有権を取得する

既存の課金所有者が組織を離れる場合や、課金アカウントを通じてサブスクリプションの支払いを行いたい場合は、Azure サブスクリプションの所有権を引き継ぐことができます。 所有権を取得すると、サブスクリプションの課金の責任は、自身のアカウントに移されます。

この記事では、Microsoft 顧客契約の課金アカウントについて説明します。 [Microsoft 顧客契約にアクセスできるかどうかを確認してください](#check-for-access)。

課金所有権を要求するには、**請求書セクション所有者**または**請求書セクション共同作成者**であることが必要です。 詳細については、[請求書セクションのロールとタスク](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)に関するページを参照してください。

## <a name="request-billing-ownership"></a>課金所有権を要求する

1. Microsoft 顧客契約の課金アカウントの請求書セクション所有者または共同作成者として [Azure portal](http://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 左側から **[請求書セクション]** を選択します。 お持ちのアクセスによっては、課金アカウントまたは課金プロファイルを選択する必要があります。 課金アカウントまたは課金プロファイルから、 **[請求書セクション]** を選択します。
   
   ![[請求書セクション] の選択を示すスクリーンショット](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 一覧から [請求書セクション] を選択します。 サブスクリプションの所有権を引き継ぐと、それらはこの [請求書セクション] に対して課金されます。

5. 左下から **[Transfer requests]\(譲渡要求\)** を選択し、 **[追加]** を選択します。
 
   ![[譲渡要求] の選択を示すスクリーンショット](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. 要求している課金所有権を持つユーザーの電子メール アドレスを入力します。 ユーザーは、Microsoft オンライン サービス プログラムの課金アカウントのアカウント管理者またはエンタープライズ契約のアカウント所有者である必要があります。 詳細については、[Azure portal での請求先アカウントの表示](billing-view-all-accounts.md)に関するページを参照してください。 **[譲渡要求を送信します]** を選択します。

   ![譲渡要求の送信を示すスクリーンショット](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. ユーザーは、譲渡要求を確認する手順を示す電子メールを受信します。

   ![譲渡要求電子メールのレビューを示すスクリーンショット](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. 譲渡要求を承認するには、ユーザーは、電子メールのリンクを選択し、指示に従います。

    ![譲渡要求電子メールのレビューを示すスクリーンショット](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>譲渡要求の状態を確認する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. 左側から **[請求書セクション]** を選択します。 お持ちのアクセスによっては、課金アカウントまたは課金プロファイルを選択する必要があります。 課金アカウントまたは課金プロファイルから、 **[請求書セクション]** を選択します。
   
   ![[請求書セクション] の選択を示すスクリーンショット](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 一覧から、譲渡要求を送信した [請求書セクション] を選択します。

5. 左下から **[Transfer requests]** (譲渡要求) を選択します。 譲渡要求ページには、次の情報が表示されます。

    ![譲渡要求の一覧を示すスクリーンショット](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

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

7. 譲渡要求を選択して、詳細を表示します。 譲渡詳細ページには、次の情報が表示されます。
   
   ![譲渡済みサブスクリプションの一覧を示すスクリーンショット](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |列  |定義|
   |---------|---------|
   |譲渡要求 ID|譲渡要求の一意の ID。 サポート要求を送信する場合、ID を Azure サポートに知らせると、サポート要求が迅速に処理されます|
   |譲渡要求日|譲渡要求が送信された日付|
   |譲渡要求者|譲渡要求を送信したユーザーの電子メール アドレス|
   |譲渡要求の有効期限| 譲渡要求の有効が期限が切れる日付|
   |受信者のメール アドレス|課金所有権を譲渡する要求の送信先のユーザーの電子メール アドレス|
   |受信者に送信された譲渡リンク|譲渡要求を確認するようにユーザーに送信された URL|

## <a name="supported-subscription-types"></a>サポートされているサブスクリプションの種類

以下に示すサブスクリプションの種類の課金所有権を要求できます。

- [Action pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure イン オープン プランのライセンス](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass スポンサー プラン](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure プラン](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure がスポンサーのプラン](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise (BizSpark) サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise (MPN) サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* サブスクリプションで使用可能なクレジットは、譲渡後の新しいアカウントでは使用できなくなります。

\*\* Azure Web サイトでサインアップしたときに作成されたアカウントのサブスクリプションに対してのみサポートされます。


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

譲渡中、(Azure RBAC (ロールベースのアクセス制御))[../role-based-access-control/overview.md] を使用して割り当てられた既存のユーザー、グループ、またはサービス プリンシパルのアクセスは影響を受けません。

### <a name="azure-support-plan"></a>Azure サポート プラン

Azure サポートは、サブスクリプションとともには譲渡されません。 ユーザーは、すべての Azure サブスクリプションを譲渡する場合、サポート プランをキャンセルするように依頼してください。

### <a name="charges-for-transferred-subscription"></a>譲渡されたサブスクリプションの料金

サブスクリプションの元の課金所有者は、譲渡が完了する時点までに報告されたすべての料金を負います。 請求書セクションは、譲渡の時点以降に報告された料金を負います。 譲渡する前に発生したにもかかわらず、譲渡後に報告される料金もあります。 これらの料金は請求書セクションに表示されます。

### <a name="cancel-a-transfer-request"></a>譲渡要求のキャンセル

要求が承認または拒否されるまで、譲渡要求をキャンセルできます。 譲渡要求をキャンセルするには、[譲渡詳細ページ](#check-the-transfer-request-status)に移動して、ページの下部からキャンセルを選択します。

### <a name="software-as-a-service-saas-transfer"></a>サービスとしてのソフトウェア (SaaS) の譲渡

SaaS 製品は、サブスクリプションとともには渡されません。 ユーザーに、[Azure サポートに連絡](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)して SaaS 製品の課金所有権を譲渡するように依頼します。 課金所有権とともに、ユーザーはリソースの所有権を譲渡することもできます。 リソースの所有権では、製品の詳細の削除や表示など、管理操作を実行できます。 ユーザーは、リソース所有権を譲渡するために、SaaS 製品でリソース所有者である必要があります。

## <a name="check-for-access"></a>アクセスを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次の手順

- Azure サブスクリプションの課金所有権は、請求書セクションに譲渡されます。 [Azure portal](https://portal.azure.com) でこれらのサブスクリプションの料金を追跡します。
- これらのサブスクリプションの料金を表示および管理するためのアクセス許可を他のユーザーに与えます。 詳細については、[請求書セクションのロールとタスク](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)に関するページを参照してください。
