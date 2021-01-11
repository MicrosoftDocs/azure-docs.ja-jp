---
title: Microsoft Partner Agreement の課金アカウントの概要 - Azure CSP
description: Microsoft Partner Agreement の課金アカウントについて理解する (CSP)
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 98941530ba0483e82f43af222e12df7439b6625e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688752"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Microsoft Partner Agreement の課金アカウントの概要

Azure を使用するためにサインアップすると、課金アカウントが作成されます。 課金アカウントを使用して、請求書および支払いを管理し、コストを追跡します。 複数の請求先アカウントにアクセスできます。 たとえば、個人的なプロジェクトのために Azure にサインアップしたとします。 組織の Enterprise Agreement、Microsoft 顧客契約、または Microsoft Partner Agreement 経由で Azure にアクセスすることもできます。 これらのシナリオごとに、個別の請求先アカウントが提供されます。

この記事は、Microsoft Partner Agreement の課金アカウントに適用されます。 これらのアカウントは、新しいコマース エクスペリエンスで顧客の課金を管理するために、クラウド ソリューション プロバイダー (CSP) 向けに作成されています。 新しいエクスペリエンスは、少なくとも 1 人の顧客が Microsoft 顧客契約に同意し、Azure プランを持っているパートナーのみが利用できます。 [Microsoft Partner Agreement にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-partner-agreement)。 [Azure プラン](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)は、Microsoft 顧客契約に基づいて従量課金制の価格で Azure サービスを提供します。

## <a name="your-billing-account"></a>課金アカウント

Microsoft Partner Agreement の課金アカウントには、ビジネスで使用する各通貨に対する課金プロファイルが含まれています。 課金プロファイルでは、請求書とその通貨を管理することができます。 顧客との関係を確立すると、その通貨に応じて、Azure サブスクリプションおよびその他の購入は、それぞれの請求プロファイルに請求されます。

次の図は、課金アカウント、課金プロファイル、顧客、およびリセラーの関係を示しています。

![Microsoft Partner Agreement の課金階層を示した図](./media/mpa-overview/mpa-hierarchy.svg)

組織の**全体管理者**ロールおよび**管理エージェント** ロールを持つユーザーは、課金アカウント、課金プロファイル、および顧客を管理できます。 詳細については、[パートナー センターでのユーザー ロールとアクセス許可の割り当て](https://docs.microsoft.com/partner-center/permissions-overview)に関するページを参照してください。

## <a name="billing-profiles"></a>課金プロファイル

課金プロファイルを使用すると、通貨ごとに請求書を管理できます。 月の初めに、アカウントの各課金プロファイルの月次請求書が生成されます。 請求書には、前月からのすべての Azure サブスクリプションとその他の購入に対する、課金プロファイルの通貨での料金が含まれています。

Azure portal で、請求書を表示し、使用状況ファイルや価格シートなどの関連ドキュメントをダウンロードすることができます。 詳細については、[Microsoft Partner Agreement の請求書のダウンロード](download-azure-invoice.md)に関するページを参照してください。

> [!IMPORTANT]
>
> 課金プロファイルの請求書には、Azure プランを持つ顧客の料金だけでなく、Microsoft 顧客契約に同意しておらず、Azure プランを持たない顧客の SaaS、Azure Marketplace、および予約購入に対する料金も含まれています。

## <a name="customers"></a>顧客

Microsoft 顧客契約に同意し、Azure プランを持っている顧客を、Azure portal で表示および管理できます。 料金とトランザクションを表示したり、これらの顧客の Azure サブスクリプションを作成および管理したりすることができます。

### <a name="enable-policy-to-give-visibility-into-cost"></a>コストを表示できるポリシーを有効にする

顧客の組織のユーザーが Azure 使用量の従量課金制のコストを表示および分析できるかどうかを制御するポリシーを適用します。 既定では、このポリシーは無効になっており、ユーザーはコストを表示できません。 有効にすると、サブスクリプションに対する適切な [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) アクセス権を持つユーザーは、サブスクリプションのコストを表示および分析できます。

ポリシーを有効にするには:

1. [Azure portal](https://portal.azure.com) にサインインする

1. **[コストの管理と請求]** で検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/mpa-overview/search-cmb.png)

1. 左側で **[顧客]** を選択し、一覧から顧客を選択します。

   ![顧客の選択を示すスクリーンショット](./media/mpa-overview/mpa-customers.png)

1. 左側で **[ポリシー]** を選択します。

   ![ポリシーを示すスクリーンショット](./media/mpa-overview/mpa-change-policy.png)

1. **[はい]** を選択します。

## <a name="resellers"></a>リセラー

CSP の [2 層モデル](https://docs.microsoft.com/partner-center)の間接プロバイダーは、Azure portal で顧客のサブスクリプションを作成するときにリセラーを選択できます。 作成後、顧客はリセラーでフィルター処理されたサブスクリプションの一覧を表示し、Azure コスト分析でリセラーごとの顧客のコストを分析することができます。

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft Partner Agreement へのアクセスの確認
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次のステップ

請求先アカウントの詳細については、次の記事を参照してください。

- [ 用に追加の Azure サブスクリプションを作成する](../manage/create-subscription.md)
- [Azure Billing API シリーズ](https://docs.microsoft.com/rest/api/billing/)を使用して、課金データを独自のレポート システムに統合する
- [パートナー向けの Azure Cost Management のクイックスタート ガイド](https://go.microsoft.com/fwlink/?linkid=2106482)
