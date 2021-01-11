---
title: Azure portal での請求先アカウントの表示
description: Azure portal で課金アカウントを表示する方法について説明します。 Enterprise Agreement、Microsoft 顧客契約、Microsoft Partner Agreement のスコープ情報を参照してください。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b18e08f93df143f9844fe774eee59452978d7437
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686396"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Azure portal での課金アカウントとスコープ

Azure を使用するためにサインアップすると、課金アカウントが作成されます。 課金アカウントを使用して請求書と支払いを管理し、コストを追跡します。 複数の請求先アカウントにアクセスできます。 たとえば、個人的なプロジェクトのために Azure にサインアップしたとします。 この場合、組織の Enterprise Agreement または Microsoft Customer Agreement を通じてアクセスすることもできます。 これらのシナリオごとに、個別の請求先アカウントが提供されます。

Azure portal では、次の種類の課金アカウントがサポートされています。

- **Microsoft Online Services Program**: Azure Web サイトから Azure にサインアップすると、Microsoft Online Services Program の課金アカウントが作成されます。 たとえば、[Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)または[従量課金制料金のアカウント](https://azure.microsoft.com/offers/ms-azr-0003p/)にサインアップした場合や、[Visual Studio サブスクライバー](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)としてサインアップした場合です。

- **マイクロソフト エンタープライズ契約**:組織が Azure を使用するために [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) を締結すると、Enterprise Agreement の課金アカウントが作成されます。 マイクロソフトエンタープライズ契約には、最大 2000 個のサブスクリプションを含めることができます。

- **Microsoft Customer Agreement**: 組織が Microsoft 担当者と連携して Microsoft Customer Agreement を締結すると、Microsoft Customer Agreement の課金アカウントが作成されます。 一部のリージョンでは、Azure Web サイトから[従量課金制料金のアカウント](https://azure.microsoft.com/offers/ms-azr-0003p/)または [Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)にサインアップしたお客様にも、Microsoft Customer Agreement の課金アカウントが作成される場合があります。 Microsoft 顧客契約には、最大 20 のサブスクリプションを含めることができます。 詳細については、「[Get started with your billing account for Microsoft Customer Agreement (Microsoft Customer Agreement の請求先アカウントの概要)](../understand/mca-overview.md)」をご覧ください。

- **Microsoft Partner Agreement**: Microsoft Partner Agreement の課金アカウントは、クラウド ソリューション プロバイダー (CSP) パートナーが新しいコマース エクスペリエンスで顧客を管理する目的で作成します。 パートナーが Azure portal で課金アカウントを管理するためには、[Azure プラン](https://docs.microsoft.com/partner-center/purchase-azure-plan)に少なくとも 1 件の顧客が必要です。 詳細については、[Microsoft Partner Agreement の課金アカウントの概要](../understand/mpa-overview.md)に関するページを参照してください。

請求先アカウントの種類を確認するには、「[請求先アカウントの種類を確認する](#check-the-type-of-your-account)」を参照してください。

## <a name="scopes-for-billing-accounts"></a>課金アカウントのスコープ
スコープとは、ユーザーが請求先を表示および管理する際に使用する、課金アカウント内のノードです。 ユーザーが課金データ、支払い、請求書を管理し、アカウントの全般的な管理を行う場所です。

### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

![MOSP の階層を示すスクリーンショット](./media/view-all-accounts/mosp-hierarchy.png)

|Scope  |定義  |
|---------|---------|
|請求先アカウント     | Azure を使用するために顧客が承諾する契約を表します。 1 つまたは複数のサブスクリプションが含まれます。  |
|サブスクリプション     |  Azure リソースのグループを表します。 請求書はこのスコープで生成されます。 このスコープには、その他の課金情報 (支払い方法、利用先住所など) が関連付けられます。|

### <a name="enterprise-agreement"></a>Enterprise Agreement

![EA の階層を示すスクリーンショット](./media/view-all-accounts/ea-hierarchy.png)

|Scope  |定義  |
|---------|---------|
|請求先アカウント    | Enterprise Agreement 加入契約を表します。 1 つまたは複数の部署およびアカウントが含まれます。 請求書はこのスコープで生成されます。 |
|部署     |  アカウントのグループ。コストを論理上のグループに分けて予算を設定する目的で必要に応じて使用されます。     |
|Account     |  1 人のアカウント所有者を表します。 加入契約に対して課金される Azure サブスクリプションを作成、管理するためのアクセス許可がアカウント オーナーには与えられます。 |

### <a name="microsoft-customer-agreement"></a>Microsoft 顧客契約

![MCA の階層を示すスクリーンショット](./media/view-all-accounts/mca-hierarchy.png)

|Scope  |タスク  |
|---------|---------|
|請求先アカウント     |   Microsoft の製品およびサービスを使用するために顧客が承諾する契約を表します。 1 つまたは複数の課金プロファイルが含まれます。 |
|請求プロファイル     |   請求書とそれに関連する課金情報 (支払い方法、請求先住所など) を表します。 1 つまたは複数の請求書セクションが含まれます。 |
|請求書セクション     |   請求書内のコストのグループを表します。 Azure サブスクリプションとその他の購入 (Azure Marketplace や App Source の製品) は、このスコープに関連付けられます。    |

### <a name="microsoft-partner-agreement"></a>Microsoft Partner Agreement

![MPA の階層を示すスクリーンショット](./media/view-all-accounts/mpa-hierarchy.png)

|Scope  |タスク  |
|---------|---------|
|請求先アカウント     |   新しいコマース エクスペリエンスで顧客の Microsoft 製品およびサービスを管理するためのパートナー契約を表します。 1 つまたは複数の課金プロファイルおよび顧客が含まれます。   |
|請求プロファイル     |   通貨の請求書を表します。     |
|Customer    |   クラウド ソリューション プロバイダー (CSP) パートナーの顧客を表します。  Azure サブスクリプションとその他の購入 (Azure Marketplace や App Source の製品) は、このスコープに関連付けられます。  |
|Reseller    |   顧客にサービスを提供するリセラー。 これはサブスクリプションのオプション フィールドです。CSP の 2 層モデルの間接プロバイダーにのみ関係します。     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Azure portal で課金スコープを切り替える

1. [Azure portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. 概要ページで **[Switch scope]\(スコープの切り替え\)** を選択します。

   ![課金スコープを示すスクリーンショット](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > アクセスできるスコープが 1 つしかない場合、[Switch scope]\(スコープの切り替え\) は表示されません。

4. 詳細を表示するスコープを選択します。

   ![課金スコープを示すスクリーンショット](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>アカウントの種類を確認する
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ
- [コストの分析](../costs/quick-acm-cost-analysis.md)を開始する方法を確認します。
