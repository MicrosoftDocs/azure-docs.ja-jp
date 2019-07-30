---
title: Microsoft 顧客契約の課金アカウントの概要 - Azure
description: Microsoft 顧客契約の課金アカウントについて理解する
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 87483c967641489e9548f38c99eebbf121d0d252
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490750"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Microsoft 顧客契約の課金アカウントの概要

Azure を使用するためにサインアップすると、課金アカウントが作成されます。 課金アカウントを使用して、請求書および支払いを管理し、コストを追跡します。 複数の請求先アカウントにアクセスできます。 たとえば、個人的なプロジェクト用に Azure にサインアップしているとします。 組織の Enterprise Agreement または Microsoft 顧客契約経由で Azure にアクセスすることもできます。 これらのシナリオごとに、個別の請求先アカウントが与えられます。

この記事では、Microsoft 顧客契約の請求先アカウントについて説明します。 [Microsoft 顧客契約にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-customer-agreement)。

## <a name="your-billing-account"></a>課金アカウント

Microsoft 顧客契約の請求先アカウントには、請求書および支払方法を管理できるようにする 1 つまたは複数の課金プロファイルが含まれています。 各課金プロファイルには、課金プロファイルの請求書上でコストを整理できるようにする、1 つまたは複数の請求書セクションが含まれます。

次の図は、課金アカウント、課金プロファイル、および請求書セクションの関係を示します。

![Microsoft 顧客契約の課金階層を示した図](./media/billing-mca-overview/mca-billing-hierarchy.png)

請求先アカウント上のロールは、最高レベルのアクセス許可を持っています。 既定では、Azure にサインアップしたユーザーのみが課金アカウントにアクセスできます。 これらのロールは、財務または IT マネージャーなど、組織全体の請求書を表示し、コストを追跡する必要のあるユーザーに割り当てる必要があります。 詳細については、[請求先アカウントのロールとタスク](billing-understand-mca-roles.md#billing-account-roles-and-tasks)に関するページを参照してください。

## <a name="billing-profiles"></a>課金プロファイル

課金プロファイルを使用して、請求書と支払方法を管理できます。 月の初めに、アカウントの各課金プロファイルの月次請求書が生成されます。 請求書には、前月からのすべての Azure サブスクリプションとその他の購入に対するそれぞれの料金が含まれています。

課金プロファイルは、請求先アカウントに対して自動的に作成されます。 既定では、これには 1 つの請求書セクションがあります。 プロジェクト、部門、開発環境ごとなど、ニーズに応じてコストを簡単に追跡および編成するため、追加のセクションを作成することもできます。 これらのセクションは課金プロファイルの請求書に表示され、各サブスクリプションの使用量、およびそれに割り当てられている購入を反映します。

課金プロファイルでのロールには、請求書と支払方法を表示および管理するアクセス許可があります。 組織内の会計チームのメンバーなど請求書の支払いを行うユーザーに、これらのロールを割り当てます。 詳細については、[請求先アカウントのロールとタスク](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)に関するページを参照してください。

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>課金プロファイルごとに月次請求書が生成される

月の初めに、各課金プロファイルの月次請求書が生成されます。 請求書には、前月からのすべての料金が含まれています。

Azure portal で、請求書を表示し、ドキュメントをダウンロードし、電子メールで将来の請求書を得るように設定を変更できます。 詳細については、[Microsoft 顧客契約の請求書のダウンロード](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)に関するページを参照してください。

### <a name="invoice-payment-methods"></a>請求書の支払い方法

各課金プロファイルには、その請求書の支払いに使用される独自の支払方法があります。 以下の支払方法がサポートされています。

| Type             | 定義  |
|------------------|-------------|
|Azure クレジット    |  クレジットは請求書の対象料金に自動的に適用され、支払う必要のある金額が減額されます。 詳細については、[課金プロファイルの Azure クレジット残高の追跡](billing-mca-check-azure-credits-balance.md)に関するページを参照してください。 |
|小切手/電信送金 | アカウントで小切手/電信送金による支払いが承認されている場合、 小切手/電信送金で請求書に記載される請求金額を支払うことができます。 支払いの指示は請求書で与えられます |
|クレジット カード | Azure の Web サイトから Azure にサインアップしたお客様は、クレジット カードで支払うことができます。 |

### <a name="apply-policies-to-control-purchases"></a>購入を制御するポリシーを適用する

課金プロファイルを使用して、Azure Marketplace および予約購入を制御するポリシーを適用します。 Azure の予約と Marketplace の製品の購入を無効にするようにポリシーを設定できます。 ポリシーが適用されると、課金プロファイルに対して課金されるサブスクリプションは、これらの購入に使用できなくなります。

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Azure プランによるサブスクリプションの価格とサービス レベル アグリーメントの決定

Azure プランは Azure サブスクリプションの価格とサービス レベル アグリーメントを決定します。 これらは課金プロファイルを作成すると自動的に有効になります。 課金プロファイルに関連するすべての請求書のセクションで、これらのプランを使用できます。 請求書セクションへのアクセスを持つユーザーは、Azure サブスクリプションを作成するプランを使用します。 Microsoft 顧客契約の請求先アカウントでは、次の Azure プランがサポートされています。

| プラン             | 定義  |
|------------------|-------------|
|Microsoft Azure プラン   | 任意のワークロードを実行できるサブスクリプションをユーザーが作成できるようにします。 詳細については、[Microsoft Azure プラン](https://azure.microsoft.com/offers/ms-azr-0017g/)に関するページを参照してください |
|Dev/Test 用の Microsoft Azure プラン | Visual Studio サブスクライバーが、開発またはテスト ワークロードに制限されているサブスクリプションを作成できるようにします。 これらのサブスクリプションは、お得な料金、Azure portal での専用の仮想マシン画像へのアクセスなどの利点が得られます。 詳細については、[開発/テスト向け Microsoft Azure プラン](https://azure.microsoft.com/offers/ms-azr-0148g/)に関するページを参照してください|

## <a name="invoice-sections"></a>請求書セクション

請求書のコストを整理するために、請求書セクションを作成します。 たとえば、組織の単一の請求書が必要だが、部門、チーム、またはプロジェクト別にコストを整理するとします。 このシナリオの場合、部門、チーム、またはプロジェクトごとに請求書セクションを作成する単一の課金プロファイルが与えられます。

請求書セクションが作成されると、そのセクションに対して課金される Azure サブスクリプションを作成するためのアクセス許可を他のユーザーに与えることができます。 サブスクリプションのすべての利用料金や購入がそのセクションに対して課金されるようになります。

請求書セクションでのロールは、Azure サブスクリプションを作成するユーザーを制御するアクセス許可を持ちます。 これらのロールを、エンジニアリング リードや技術的アーキテクトなど、組織内のチームの Azure 環境を設定したユーザーに割り当てます。 詳細については、[請求書セクションのロールとタスク](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)に関するページを参照してください。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次の手順

請求先アカウントの詳細については、次の記事を参照してください。

- [Azure での Microsoft 顧客契約の管理ロールを理解する](billing-understand-mca-roles.md)
- [Microsoft 顧客契約の追加 Azure サブスクリプションを作成する](billing-mca-create-subscription.md)
- [請求書にセクションを作成してコストを整理する](billing-mca-section-invoice.md)
