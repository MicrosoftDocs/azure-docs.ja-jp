---
title: Microsoft 顧客契約の請求先アカウントから開始する - Azure | Microsoft Docs
description: Microsoft 顧客契約の請求先アカウントについて理解する
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: ea625a61ed600dbaa22fef85987e9570a6fb7dbc
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337415"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft 顧客契約の請求先アカウントから開始する

Azure の使用について Microsoft と署名する契約ごとに請求先アカウントが作成されます。 請求先アカウントを使用して課金を管理し、コストを追跡します。 複数の請求先アカウントにアクセスできます。 たとえば、個人的なプロジェクト用に Azure にサインアップしているとします。 組織の Enterprise Agreement または Microsoft 顧客契約経由で Azure にアクセスすることもできます。 これらのシナリオごとに、個別の請求先アカウントが与えられます。

この記事では、Microsoft 顧客契約の請求先アカウントについて説明します。 [Microsoft の顧客契約にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-customer-agreement)。

## <a name="understand-billing-account"></a>請求先アカウントを理解する

Microsoft 顧客契約の請求先アカウントには、請求書および支払方法を管理できるようにする 1 つまたは複数の課金プロファイルが含まれています。 各課金プロファイルには、課金プロファイルの請求書上でコストを整理できるようにする、1 つまたは複数の請求書セクションが含まれます。

次の図は、請求先アカウント、課金プロファイル、および請求書セクションの関係を示します。

![Microsoft 顧客契約の課金階層を示した図](./media/billing-mca-overview/mca-billing-hierarchy.png)

請求先アカウント上のロールは、最高レベルのアクセス許可を持っています。 既定では、組織の Azure Active Directory のグローバル管理者のみが請求先アカウントへのアクセスを取得します。 これらのロールは、財務または IT マネージャーなど、組織全体の請求書を表示し、コストを追跡する必要のあるユーザーに割り当てる必要があります。 詳細については、[請求先アカウントのロールとタスク](billing-understand-mca-roles.md#billing-account-roles-and-tasks)に関するページを参照してください。

## <a name="understand-billing-profiles"></a>課金プロファイルを理解する

課金プロファイルを使用して、請求書と支払方法を管理できます。 月次請求書は、Azure サブスクリプションと、課金プロファイルを使用して購入した他の製品に対して生成されます。 請求書に対して支払う支払方法を使用します。

課金プロファイルは、請求先アカウントに対して自動的に作成されます。 追加請求書を設定するために、新しい課金プロファイルを作成できます。 たとえば、組織の部門またはプロジェクトごとに異なる請求書が必要だとします。

請求書セクションを使用して、課金プロファイルの請求書上でコストを整理することもできます。 Azure サブスクリプションと、請求書セクションに対して購入した製品の料金がセクションに示されます。 課金プロファイルの請求書には、すべての請求書セクションの料金が含まれます。

課金プロファイルでのロールには、請求書と支払方法を表示および管理するアクセス許可があります。 組織内の会計チームのメンバーなど請求書の支払いを行うユーザーに、これらのロールを割り当てます。 詳細については、[請求先アカウントのロールとタスク](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)に関するページを参照してください。

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>課金プロファイルごとに生成された月次請求書

月次請求書は、課金プロファイルごとに請求日に生成されます。 請求書には、前月のすべての料金が含まれています。

Azure portal で、請求書を表示し、ドキュメントをダウンロードし、電子メールで将来の請求書を得るように設定を変更できます。 詳細については、[Microsoft 顧客契約の請求書のダウンロード](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)に関するページを参照してください。

### <a name="invoices-paid-through-payment-methods"></a>支払方法を通じて支払われた請求書

各課金プロファイルには、その請求書の支払いに使用される独自の支払方法があります。 以下の支払方法がサポートされています。

| type             | 定義  |
|------------------|-------------|
|Azure クレジット    |  クレジットは、支払う必要のある金額を計算するために、請求書の合計請求額に自動的に適用されます。 詳細については、[課金プロファイルの Azure クレジット残高の追跡](billing-mca-check-azure-credits-balance.md)に関するページを参照してください。 |
|小切手または電信送金 | 小切手または電信送金のどちらかで、請求書に記載される請求金額を支払うことができます。 支払いの指示は請求書で与えられます |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>ポリシーを適用することにより Azure Marketplace および予約購入を制御する

課金プロファイルを使用して行われた購入を制御するポリシーを適用します。 Azure の予約と Marketplace の製品の購入を無効にするようにポリシーを設定できます。 ポリシーが適用されると、課金プロファイルで請求書セクションに対して作成されたサブスクリプションは、Azure の予約および Marketplace 製品の購入に使用できません。

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>Azure プランを有効にすることによりユーザーが Azure サブスクリプションを作成できるようにする

課金プロファイルを作成するときに、Azure プランが自動的に有効になります。 課金プロファイル内のすべての請求書セクションは、これらのプランへのアクセス権を取得します。 請求書セクションへのアクセスを持つユーザーは、Azure サブスクリプションを作成するプランを使用します。 課金プロファイルに対して Azure プランが有効になっていない限り、Azure サブスクリプションを作成できません。 Microsoft 顧客契約の請求先アカウントでは、次の Azure プランがサポートされています。

| プラン             | 定義  |
|------------------|-------------|
|Microsoft Azure プラン   | 任意のワークロードを実行できるサブスクリプションをユーザーが作成できるようにします。 詳細については、[Microsoft Azure プラン](https://azure.microsoft.com/offers/ms-azr-0017g/)に関するページを参照してください |
|Dev/Test 用の Microsoft Azure プラン | Visual Studio サブスクライバーが、開発またはテスト ワークロードに制限されているサブスクリプションを作成できるようにします。 これらのサブスクリプションは、お得な料金、Azure portal での専用の仮想マシン画像へのアクセスなどの利点が得られます。 詳細については、[開発/テスト向け Microsoft Azure プラン](https://azure.microsoft.com/offers/ms-azr-0148g/)に関するページを参照してください|

## <a name="understand-invoice-sections"></a>請求書セクションを理解する

課金プロファイルの請求書で、コストを整理するために請求書セクションを作成します。 たとえば、組織の単一の請求書が必要だが、部門、チーム、またはプロジェクト別にコストを整理するとします。 このシナリオの場合、部門、チーム、またはプロジェクトごとに請求書セクションを作成する単一の課金プロファイルが与えられます。

請求書セクションが作成されると、セクションに対して Azure サブスクリプションを作成するアクセス許可を他のユーザーに与えることができます。 サブスクリプションのすべての利用料金および購入が、続いて請求書の適切なセクションに反映されます。

請求書セクションでのロールは、Azure サブスクリプションを作成するユーザーを制御するアクセス許可を持ちます。 これらのロールを、エンジニアリング リードや技術的アーキテクトなど、組織内のチームの Azure 環境を設定したユーザーに割り当てます。 詳細については、[請求書セクションのロールとタスク](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)に関するページを参照してください。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次の手順

請求先アカウントの詳細については、次の記事を参照してください。

- [Azure での Microsoft 顧客契約の管理ロールを理解する](billing-understand-mca-roles.md)
- [Microsoft 顧客契約の追加 Azure サブスクリプションを作成する](billing-mca-create-subscription.md)
- [請求書にセクションを作成してコストを整理する](billing-mca-section-invoice.md)