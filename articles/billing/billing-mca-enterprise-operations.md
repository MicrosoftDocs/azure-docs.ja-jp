---
title: Microsoft 顧客契約における Enterprise Agreement タスクを実行する - Azure
description: 新しい課金アカウントでの Enterprise Agreement タスクを実行する方法について説明します。
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
ms.openlocfilehash: 9404908b7c486801480474c5a2c9ff7688e1de48
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490709"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft 顧客契約用の課金アカウントでの Enterprise Agreement タスクを実行する

お客様の組織が Enterprise Agreement 加入契約を更新する Microsoft 顧客契約を結んでいる場合は、その契約用の新しい課金アカウントが作成されます。 新しいアカウントの課金は、Enterprise Agreement とは異なる方法で構成されています。 この記事では、新しい課金アカウントを使用して、Enterprise Agreement で実行していたタスクを実行する方法について説明します。

## <a name="billing-organization-in-the-new-account"></a>新しいアカウントでの課金構成

次の図は、新しい課金アカウントで課金がどのように構成されているかについて説明しています。

![EA から MCA への移行後の階層の図](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| Enterprise Agreement   | Microsoft 顧客契約    |
|------------------------|--------------------------------------------------------|
| 加入            | Enterprise Agreement 加入契約と同様に、組織の課金は課金プロファイルを使用して管理されます。 エンタープライズ管理者が課金プロファイルの所有者になります。 課金プロファイルについて詳しくは、「[Understand billing profiles (課金プロファイルについて)](billing-mca-overview.md#billing-profiles)」をご覧ください。
| 部署            | Enterprise Agreement 加入契約の部署と同様に、コストは、請求書セクションを使用して整理されます。 部署が請求書セクションになり、部署管理者がそれぞれの請求書セクションの所有者になります。 請求書セクションについて詳しくは、「[Understand invoice sections (請求書セクションについて)](billing-mca-overview.md#invoice-sections)」をご覧ください。 |
| Account               | Enterprise Agreement で作成されたアカウントは、新しい課金アカウントではサポートされません。 アカウントのサブスクリプションは、各部署の請求書セクションに属します。 アカウント オーナーは、各自の請求書セクションのサブスクリプションを作成および管理できます。 |

## <a name="changes-for-enterprise-administrators"></a>エンタープライズ管理者への変更

Microsoft 顧客契約に更新された Enterprise Agreement のエンタープライズ管理者には、次の変更が適用されます。

- お客様の加入契約用の課金プロファイルが作成されます。 Enterprise Agreement 加入契約の場合と同様に、組織の課金は、課金プロファイルを使用して管理します。 課金プロファイルについて詳しくは、「[Understand billing profiles (課金プロファイルについて)](billing-mca-overview.md#billing-profiles)」をご覧ください。
- Enterprise Agreement 加入契約の各部署に対して、請求書セクションが作成されます。 請求書セクションを使用して部署を管理します。 新しい請求書セクションを作成して、追加の部署を設定できます。 請求書セクションについて詳しくは、「[Understand invoice sections (請求書セクションについて)](billing-mca-overview.md#invoice-sections)」をご覧ください。
- Enterprise Agreement 加入契約で作成されたアカウントと同様に、請求書セクションに対する Azure サブスクリプション作成者ロールを使用して、他のユーザーに Azure サブスクリプションを作成する許可を与えます。
- Azure EA ポータルではなく [Azure portal](https://portal.azure.com) を使用して、組織に対する課金を管理します。

新しい課金アカウントでは、次のロールが与えられます。

**課金プロファイル所有者** - 契約時に作成された課金プロファイルに対する課金プロファイル所有者ロールが割り当てられます。 このロールでは、組織に対する課金を管理できます。 料金と請求書の表示、請求書のコストの整理、支払い方法の管理、および組織の課金に対するアクセスの制御を実行できます。

**請求書セクション所有者** - Enterprise Agreement 加入契約の部署用に作成されたすべての請求書セクションに対する請求書セクション所有者ロールが割り当てられます。 このロールでは、Azure サブスクリプションを作成して他の製品を購入できるユーザーを制御できます。

### <a name="view-charges-and-credits-balance-for-your-organization"></a>組織に対する請求金額とクレジット残高を表示する

Enterprise Agreement 加入契約と同様に、課金プロファイルを使用して、組織に対する請求金額と Azure クレジット残高を追跡します。

課金プロファイルのクレジット残高を表示する方法については、[課金プロファイルの Azure クレジット残高の追跡](billing-mca-check-azure-credits-balance.md)に関する記事を参照してください。

課金プロファイルに対する請求金額を表示する方法については、「[Understand the charges on your Microsoft Customer Agreement's invoice (Microsoft 顧客契約の請求書の料金を理解する)](billing-mca-understand-your-bill.md)」を参照してください。

### <a name="view-charges-for-a-department"></a>部署に対する請求金額を表示する

Enterprise Agreement に含まれていた各部署に対して、請求書セクションが作成されます。 Azure portal で、請求書セクションに対する請求金額を表示できます。 詳細については、[請求書セクション別のトランザクションの表示](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections)に関するセクションを参照してください。

### <a name="view-charges-for-an-account"></a>アカウントに対する請求金額を表示する

Enterprise Agreement 加入契約で作成されたアカウントは、新しい請求先アカウントではサポートされません。 アカウントのサブスクリプションは、各部署の請求書セクションに属します。 アカウント所有者は、各自の請求書セクションのサブスクリプションを作成および管理できます。

アカウントに属していたサブスクリプションの集計コストを表示するには、各サブスクリプション用のコスト センターを設定する必要があります。 その後、Azure の利用状況と請求金額を含む csv ファイルを使用して、コスト センターによってサブスクリプションをフィルター処理できます。

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>利用状況と請求金額を含む csv、価格シート、および税務書類をダウンロードする

課金アカウントの各課金プロファイルに対して、月次請求書が生成されます。 請求書ごとに、Azure の利用状況と請求金額を含む csv ファイル、価格シート、および税務書類をダウンロードできます (該当する場合)。 当月の料金に対する Azure の利用状況と請求金額を含む csv ファイルをダウンロードすることもできます。

Azure の利用状況と請求金額を含む csv ファイルをダウンロードする方法については、「[Download usage for your Microsoft Customer Agreement (Microsoft 顧客契約の利用状況のダウンロード)](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement)」を参照してください。

価格シートをダウンロードする方法については、[お客様の Microsoft 顧客契約向けの価格のダウンロード](billing-ea-pricing.md#microsoft-customer-agreement-pricing)に関する記事を参照してください。

税務書類をダウンロードする方法については、[お客様の Microsoft 顧客契約向けの税務書類の表示](billing-mca-download-tax-document.md#view-and-download-tax-documents)に関する記事を参照してください。

### <a name="add-an-additional-enterprise-administrator"></a>さらにエンタープライズ管理者を追加する

課金プロファイルへのアクセス権をユーザーに与えて、それらのユーザーが組織の課金を表示および管理できるようにすることができます。 Azure portal の **[アクセス制御 (IAM)]** ページを使用して、アクセス権を与えることができます。  課金プロファイルのロールの詳細については、「[Billing profile roles and tasks (課金プロファイルのロールとタスク)](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)」を参照してください。

課金プロファイルへのアクセスを提供する方法については、「[Manage billing roles in the Azure portal (Azure portal で課金ロールを管理する)](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)」を参照してください。

### <a name="create-a-new-department"></a>新しい部署を作成する

Enterprise Agreement 加入契約の部署と同様に、ニーズに基づいてコストを整理する請求書セクションを作成します。 Azure portal で新しい請求書セクションを作成できます。 詳細については、「[Create sections on your invoice to organize your costs (請求書セクションを作成してコストを整理する)](billing-mca-section-invoice.md)」を参照してください。

### <a name="create-a-new-account"></a>新しいアカウントを作成する

Enterprise Agreement 加入契約で作成されたアカウントと同様に、請求書セクションに対する Azure サブスクリプション作成者ロールをユーザーに割り当てて、Azure サブスクリプションへのアクセス許可をユーザーに与えます。 詳細については、「[Give others permission to create Azure subscriptions (Azure サブスクリプションを作成する権限を他のユーザーに付与する)](billing-mca-create-subscription.md#give-others-permission)」を参照してください。

## <a name="changes-for-department-administrators"></a>部署管理者への変更

Microsoft 顧客契約に更新された Enterprise Agreement の部署管理者には、次の変更が適用されます。

- Enterprise Agreement 加入契約の各部署に対して、請求書セクションが作成されます。 請求書セクションを使用して部署を管理します。 請求書セクションについて詳しくは、「[Understand invoice sections (請求書セクションについて)](billing-mca-overview.md#invoice-sections)」をご覧ください。
- Enterprise Agreement 加入契約で作成されたアカウントと同様に、請求書セクションに対する Azure サブスクリプション作成者ロールを使用して、他のユーザーに Azure サブスクリプションを作成する権限を与えます。
- Azure EA ポータルではなく Azure portal を使用して、組織に対する課金を管理します。

新しい課金アカウントでは、次のロールが与えられます。

**請求書セクション所有者** - Enterprise Agreement 加入契約に含まれていた部署用に作成された請求書セクションに対する、請求書セクション所有者ロールが割り当てられます。 このロールでは、請求金額の表示と追跡を実行でき、Azure サブスクリプションを作成して他の製品を購入できるユーザーを制御できます。

### <a name="view-charges-for-your-department"></a>部署に対する請求金額を表示する

Azure portal の [[コストの管理と請求] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)で、部署用に作成された請求書セクションに対する請求金額を確認できます。

### <a name="add-an-additional-department-administrator"></a>さらに部署管理者を追加する

Enterprise Agreement に含まれていた各部署に対して、請求書セクションが作成されます。 Azure portal の **[アクセス制御 (IAM)]** ページを使用して、請求書セクションの表示と管理を行うためのアクセス権を与えることができます。 請求書セクションのロールの詳細については、「[Invoice section roles and tasks (請求書セクションのロールとタスク)](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)」を参照してください。

請求書セクションへのアクセスを提供する方法については、「[Manage billing roles in the Azure portal (Azure portal で課金ロールを管理する)](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)」を参照してください。

### <a name="create-a-new-account-in-your-department"></a>部署内に新しいアカウントを作成する

部署用に作成された請求書セクションに対する Azure サブスクリプション作成者ロールをユーザーに割り当てます。 詳細については、「[Give others permission to create Azure subscriptions (Azure サブスクリプションを作成する権限を他のユーザーに付与する)](billing-mca-create-subscription.md#give-others-permission)」を参照してください。

### <a name="view-charges-for-accounts-in-your-departments"></a>部署内のアカウントに対する請求料金を表示する

Enterprise Agreement 加入契約で作成されたアカウントは、新しい請求先アカウントではサポートされません。 アカウントのサブスクリプションは、各部署の請求書セクションに属します。 アカウント所有者は、各自の請求書セクションのサブスクリプションを作成および管理できます。

部署内のアカウントに属していたサブスクリプションの集計コストを表示するには、各サブスクリプション用のコスト センターを設定する必要があります。 その後、Azure の利用状況と請求金額を含むファイルを使用して、コスト センターによってサブスクリプションをフィルター処理できます。

## <a name="changes-for-account-owners"></a>アカウント所有者への変更

Enterprise Agreement のアカウント所有者は、新しい課金アカウントの Azure サブスクリプションを作成する権限を取得します。 既存の Azure サブスクリプションは、部署用に作成された請求書セクションに属します。 アカウントが部署に属していない場合、サブスクリプションは [既定の請求書セクション] という名前の請求書セクションに属します。

追加の Azure サブスクリプションを作成するために、新しい請求先アカウントに対する次のロールが与えられます。

**Azure サブスクリプション作成者** - Enterprise Agreement の部署用に作成された請求書セクションに対する Azure サブスクリプション作成者ロールが割り当てられます。 アカウントが部署に属していない場合は、[既定の請求書セクション] という名前のセクションに対して Azure サブスクリプション作成者ロールが与えられます。 このロールでは、請求書セクションの Azure サブスクリプションを作成できます。

### <a name="create-an-azure-subscription"></a>Azure サブスクリプションを作成します。

Azure portal で請求書セクションの Azure サブスクリプションを作成できます。 詳細については、「[Create an additional Azure subscription for Microsoft Customer Agreement (Microsoft 顧客契約の追加の Azure サブスクリプションを作成する)](billing-mca-create-subscription.md)」を参照してください。

### <a name="view-charges-for-your-account"></a>アカウントに対する請求金額を表示する

アカウントに属していたサブスクリプションに対する請求金額を表示するには、Azure portal で [[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) に移動します。 [サブスクリプション] ページには、すべてのサブスクリプションに対する請求金額が表示されます。

### <a name="view-charges-for-a-subscription"></a>サブスクリプションに対する請求金額を表示する

[[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) または Azure コスト分析のいずれかで、サブスクリプションに対する請求金額を表示できます。 Azure コスト分析の詳細については、「[コスト分析を使用してコストを調査および分析する](../cost-management/quick-acm-cost-analysis.md)」を参照してください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次の手順

- [Microsoft 顧客契約の課金アカウントについて理解する](billing-mca-overview.md)
- [請求書を理解する](billing-understand-your-bill.md)
- [課金内容の確認](billing-understand-your-invoice.md)
- [他のユーザーから Azure サブスクリプションの課金所有権を取得する](billing-mca-request-billing-ownership.md)
