---
title: パートナー向け Azure Cost Management の利用を開始する
description: この記事では、パートナーが Azure Cost Management の機能を使用する方法と、パートナーの顧客のために Cost Management アクセスを有効にする方法について説明します。
author: bandersmsft
ms.author: banders
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: 40c683d98a13bf4810ec4575af3fba5ae64961f0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946993"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>パートナー向け Azure Cost Management の利用を開始する

Azure Cost Management は、お客様を Microsoft 顧客契約にオンボードし、[Azure プランを購入](/partner-center/purchase-azure-plan)している直接パートナーがネイティブに使用できます。 この記事では、パートナーが [Azure Cost Management](../index.yml) 機能を使用して、Azure プランのサブスクリプションのコストを表示する方法について説明します。 また、パートナーが、自分の顧客向けに Cost Management へのアクセスを有効にする方法についても説明します。

直接パートナーおよび間接プロバイダーの場合、全体管理者と管理者エージェントが、パートナー テナントの Cost Management にアクセスして請求価格のコストを管理できます。

リセラーと顧客は、顧客テナントで Cost Management にアクセスし、サブスクリプションの消費コストを確認でき、コストは小売料金で計算されて表示されます。 ただし、コストを表示するためには、顧客テナントのサブスクリプションに対する Azure RBAC アクセス権が必要です。 顧客テナントのプロバイダーによって、コスト可視性ポリシーが有効になっている必要があります。

顧客は、CSP パートナーによって有効化された場合、Cost Management の機能を使用できます。

CSP パートナーは、次の目的に Cost Management を使用します。

- 請求済みコストを把握し、コストを顧客、サブスクリプション、リソース グループ、およびサービスに関連付けます。
- [コスト分析](quick-acm-cost-analysis.md)で、Azure のコストを直感的に把握します。顧客、サブスクリプション、リソース グループ、リソース、メーター、サービス、その他多数のディメンションによってコストを分析する機能を備えています。
- コスト分析で、パートナー獲得クレジット (PEC) が適用されているリソース コストを表示します。
- プログラムによる[予算](tutorial-acm-create-budgets.md)と、コストが予算を超過したときのアラートを使用して、通知と自動化を設定します。
- Cost Management データへのアクセスを顧客に提供する Azure Resource Manager ポリシーを有効にします。 顧客はその後、[従量課金制料金](https://azure.microsoft.com/pricing/calculator/)を使用するサブスクリプションの使用量コスト データを表示できます。
- 従量課金制サブスクリプションを使用して、そのコストと使用状況データをストレージ BLOB にエクスポートします。

すべての顧客のコストを示す例を次に示します。

[![すべての顧客のコストを示す例](./media/get-started-partners/customer-costs1.png)](./media/get-started-partners/customer-costs1.png#lightbox)

1 人の顧客のコストを示す例を次に示します。

[![1 人の顧客のコストを示す例](./media/get-started-partners/customer-costs2.png)](./media/get-started-partners/customer-costs2.png#lightbox)

Azure Cost Management で使用できるすべての機能は、REST API でも使用できます。 API を使用してコスト管理タスクを自動化します。

## <a name="prerequisites"></a>前提条件

パートナーの場合、Azure Cost Management は、Azure プランのサブスクリプションでのみネイティブに使用できます。

Azure portal で Azure Cost Management を有効にするには、(顧客に代わって) Microsoft 顧客契約に対する顧客の同意を確認し、その顧客を Azure プランに移行している必要があります。 Azure Cost Management では、Azure プランに移行されているサブスクリプションのコストのみを使用できます。

Azure Cost Management には、課金アカウントまたはサブスクリプションへの読み取りアクセスが必要です。

課金アカウントでの Azure Cost Management へのアクセスの有効化と割り当ての詳細については、「[ユーザー ロールとアクセス許可の割り当て](/partner-center/permissions-overview)」を参照してください。 **全体管理者** ロールと **管理エージェント** ロールは、課金アカウントのコストを管理できます。

サブスクリプション スコープで Azure Cost Management にアクセスする場合、サブスクリプションに対して Azure RBAC アクセス権を持つユーザーは、小売 (従量課金制) レートでコストを表示できます。 ただし、[顧客テナントのコスト可視性ポリシー](#enable-the-policy-to-view-azure-usage-charges)が有効になっている必要があります。 サポートされているアカウントの種類の完全な一覧については、「[Cost Management のデータを理解する](understand-cost-mgt-data.md)」を参照してください。

既存の課金契約を新しいパートナーに転送する際、コスト管理機能は、そのパートナーとの現在の課金リレーションシップについてのみ利用できます。 新しいパートナーへの転送前に生じた過去のコストが、新しい課金アカウントに移動することはありません。 ただしコスト履歴は、関連する元の課金アカウントと共に残ります。

## <a name="how-cost-management-uses-scopes"></a>Cost Management でのスコープの使用方法

スコープは、お客様が課金データの管理、請求書の表示、およびアカウント全般の管理を行う場所であり、支払い専用のロールを持ちます。 課金とアカウントに関するロールは、Azure RBAC を使用するリソース管理用のスコープとは別々に管理されます。 アクセス制御の違いを含め、各スコープの意図を明確に区別するために、両者をそれぞれ "課金スコープ" および "Azure RBAC スコープ" と呼びます。

課金スコープ、Azure RBAC スコープ、コスト管理とスコープの関係について理解するには、「[スコープを理解して使用する](understand-work-scopes.md)」を参照してください。

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>パートナー テナントの課金スコープを使ってコストを管理する

顧客を Microsoft 顧客契約にオンボードした後、次の _課金スコープ_ をテナントで利用できます。 スコープを使用して Cost Management でコストを管理します。

### <a name="billing-account-scope"></a>課金アカウントのスコープ

課金アカウントのスコープを使用して、すべての顧客および課金プロファイルの課税前コストを表示します。 請求書コストは、Microsoft 顧客契約にある、顧客の使用量に基づいた製品についてのみ表示されます。 ただし、請求書コストは、Microsoft 顧客契約と CSP オファーの両方にある、購入に基づいた顧客向け製品について表示されます。 現在、スコープ内のコストを表示する既定の通貨は米ドルです。 スコープに対して設定される予算も米ドル単位です。

さまざまな請求通貨に関係なく、パートナーは課金アカウント スコープを使用して、米国ドルで顧客、サブスクリプション、リソース、リソース グループ全体の予算の設定とコストの管理を行います。

またパートナーはコスト分析ビューで、顧客全体を対象に、特定の請求通貨でコストをフィルター処理します。 **[Actual cost]\(実際のコスト\)** リストを選択して、サポートされている請求通貨でコストを表示します。

![実際のコストの通貨選択を示す例](./media/get-started-partners/actual-cost-selector.png)

課金スコープの[償却コスト ビュー](quick-acm-cost-analysis.md#customize-cost-views)を使用して、予約期間全体を対象に予約インスタンスの償却コストを表示します。

### <a name="billing-profile-scope"></a>課金プロファイル スコープ

請求書に含まれるすべての製品およびサブスクリプションのすべての顧客にわたって請求通貨で課税前コストを表示するには、課金プロファイル スコープを使用します。 **InvoiceID** フィルターを使用して、特定の請求書の課金プロファイルでコストをフィルター処理できます。 このフィルターは、特定の請求書の使用量コストと製品購入コストを示します。 請求書で特定の顧客のコストをフィルター処理して、課税前コストを確認することもできます。

顧客を Microsoft 顧客契約にオンボードすると、Microsoft 顧客契約にある、それらの顧客向けの全製品 (消費、購入、エンタイトルメント) のすべての料金を含む請求書を受け取ります。 同じ通貨で請求される場合、これらの請求書には、引き続き Azure プランにはない従来の CSP オファーに含まれている顧客のエンタイトルメントと購入済み製品 (SaaS、Azure Marketplace、予約など) の料金も含まれます。

顧客請求書に対する料金の調整に役立つよう、課金プロファイル スコープを使用して、顧客に対する請求書で発生するすべてのコストを確認できます。 請求書と同様に、スコープは、新しい Microsoft 顧客契約のすべての顧客に対するコストを示します。 スコープは、現在の CSP オファーに残っている顧客エンタイトルメント製品に対するすべての料金も示します。

課金プロファイルと課金アカウントのスコープは、Azure Marketplace や予約の購入など、エンタイトルメントと購入ベースの製品の料金を示す、唯一適用可能なスコープです。

課金プロファイルは、請求書に含まれるサブスクリプションを定義します。 課金プロファイルは、エンタープライズ契約の加入契約に相当する機能です。 課金プロファイルは、請求書が生成されるスコープです。

現在、課金プロファイル スコープでコストを表示するとき、請求通貨が既定の通貨です。 課金プロファイル スコープで設定される予算は請求通貨になります。

パートナーはスコープを使用して請求書との照合を実行できます。 また、スコープを使用して、請求通貨で次の項目の予算を設定します。

- 特定のフィルター処理された請求書
- Customer
- サブスクリプション
- Resource group
- リソース
- Azure サービス
- 測定
- ResellerMPNID

### <a name="customer-scope"></a>顧客スコープ

パートナーはスコープを使用して、Microsoft 顧客契約にオンボードされている顧客に関連付けられたコストを管理します。 スコープにより、パートナーは特定の顧客の課税前コストを請求通貨で表示できます。 特定のサブスクリプション、リソース グループ、またはリソースの課税前コストをフィルター処理することもできます。

顧客スコープには、現在の CSP オファーに載っている顧客は含まれません。 このスコープには、Microsoft 顧客契約がある顧客のみが含まれます。 現在の CSP オファー顧客のエンタイトルメント コスト (Azure 使用量ではない) は、顧客フィルターを適用したときに課金アカウント スコープと課金プロファイル スコープで確認できます。 このスコープで設定される予算は請求通貨になります。

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Cost Management での課金スコープへのパートナー アクセス

**グローバル管理者** および **管理エージェント** のロールを持つユーザーのみが、パートナーの Azure テナントで直接、課金アカウント、課金プロファイル、顧客のコストを管理および表示できます。 パートナー センターのロールの詳細については、「[ユーザー ロールとアクセス許可の割り当て](/partner-center/permissions-overview)」を参照してください。

## <a name="enable-cost-management-for-customer-tenant-subscriptions"></a>顧客テナントのサブスクリプションのコスト管理ができるようにする

顧客が Microsoft 顧客契約にオンボードされた後、Cost Management へのアクセスをパートナーが有効化できます。 パートナーはその後、従量課金制の小売料金で計算された顧客コストを顧客が表示することを許可するポリシーを有効化できます。 コストは顧客の請求通貨で、Azure RBAC サブスクリプションおよびリソース グループのスコープで顧客が使用した分量に対して示されます。

コスト可視性のポリシーをパートナーが有効にすると、Azure Resource Manager でサブスクリプションにアクセスできるユーザーが、従量課金制の料金でコストを管理および分析できます。 実質的には、Azure サブスクリプションへの適切な Azure RBAC アクセスを持つリセラーと顧客が、コストを表示できます。

ポリシーに関係なく、プロバイダーのグローバル管理者と管理エージェントは、サブスクリプションとリソー スグループにアクセスできる場合、サブスクリプションのコストを表示できます。

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Azure の使用料金を表示するためのポリシーを有効にする

ポリシーを表示および更新するには、**管理エージェント** グループのメンバーである必要があります。 次の情報を使用して、顧客が Azure 使用料金を表示できるポリシーを有効にします。

Azure portal で、"*パートナー テナント*" にサインインし、 **[Cost Management + Billing]** を選択します。 [課金スコープ] 領域内で、関連する課金スコープを選択し、 **[顧客]** を選択します。 顧客の一覧は課金アカウントに関連付けられています。 *誤って顧客テナントにサインインした場合、 **[顧客]** 一覧は表示されません。*

顧客の一覧で、コストの表示を許可する顧客を選択します。

[![Cost Management で顧客を選択する](./media/get-started-partners/customer-list.png)](./media/get-started-partners/customer-list.png#lightbox)

**[設定]** で、 **[ポリシー]** を選択します。

選択した顧客のサブスクリプションに関連付けられている **Azure 使用** 料金について、現在のコスト可視性ポリシーが表示されます。
![従量課金制の料金を表示することを顧客に許可するポリシー](./media/get-started-partners/cost-management-billing-policies.png)

ポリシーが **[いいえ]** に設定されている場合、顧客に関連付けられているサブスクリプション ユーザーに対して Azure Cost Management は使用できません。 パートナーによって有効化されていない限り、コスト可視性ポリシーはすべてのサブスクリプション ユーザーに対して既定で無効になっています。

コスト ポリシーが **[はい]** に設定されている場合、顧客テナントに関連付けられているサブスクリプション ユーザーは、従量課金制の料金で使用料金を確認できます。

コスト可視性ポリシーが有効になっている場合、サブスクリプション使用量のあるすべてのサービスが、従量課金制の料金でコストを示します。 予約使用量は、実際のコストも償却済みコストも料金がゼロと表示されます。 購入およびエンタイトルメントは特定のサブスクリプションに関連付けられません。 そのため、サブスクリプション スコープでは購入は表示されません。 直接パートナーまたは間接プロバイダーの全体管理者/管理エージェントは、[顧客更新 API](/rest/api/billing/2019-10-01-preview/policies/updatecustomer) を使用して、各顧客のコスト表示ポリシーを大規模に設定することもできます。

### <a name="view-subscription-costs-in-the-customer-tenant"></a>顧客テナントのサブスクリプション コストを表示する

サブスクリプションのコストを表示するには、顧客の Azure テナントで **[コストの管理と請求]** を開きます。 必要なサブスクリプションで **[コスト分析]** を選択し、コストの確認を開始します。 各サブスクリプションの消費コストは、個別に顧客テナントで確認できます。

[![顧客としてコスト分析を表示する](./media/get-started-partners/subscription-costs.png)](./media/get-started-partners/subscription-costs.png#lightbox)

コスト分析、予算、アラートは、従量課金制の料金に基づいたコストで、サブスクリプションとリソース グループの Azure RBAC スコープで利用できます。

Azure RBAC スコープの予約インスタンスの償却ビューおよび実際のコストでは、料金がゼロと表示されます。 予約インスタンスや Marketplace 料金などのエンタイトルメントの購入コストは、購入が行われたパートナーのテナントの課金スコープにのみ表示されます。

このビューに表示されるコスト計算に使用される小売料金は、すべてのお客様の Azure 料金計算ツールに表示される料金と同じです。 表示されるコストには、パートナーが持っているパートナー獲得クレジット、階層割引、グローバル サービス割引などの割引やクレジットは含まれません。

 

## <a name="analyze-costs-in-cost-analysis"></a>コスト分析でコストを分析する

パートナー テナント内の課金スコープにアクセスできるパートナーは、特定の顧客または請求書に関する顧客全体にまたがるコスト分析で、請求されたコストを調査および分析できます。 [コスト分析](quick-acm-cost-analysis.md)ビューでは、[ビューを保存](quick-acm-cost-analysis.md#saving-and-sharing-customized-views)したり、[CSV および PNG](quick-acm-cost-analysis.md#download-usage-data) のファイルにデータをエクスポートしたりすることもできます。

顧客テナント内のサブスクリプションにアクセスできる Azure RBAC ユーザーは、その顧客テナント内のサブスクリプションの小売コストを分析し、ビューを保存して、データを CSV および PNG ファイルにエクスポートすることもできます。

コスト分析でフィルターとグループ化の機能を使用して、複数のフィールドでコストを分析できます。 次のセクションでは、パートナー固有のフィールドを示します。

## <a name="data-fields"></a>データ フィールド

使用状況の詳細ファイルと Cost Management API には、次のデータ フィールドがあります。 可能な場合は、パートナー センターの同等の情報が示されています。 次の太字のフィールドについては、パートナーはコスト分析でフィルターとグループ化の機能を使用して、複数のフィールドでコストを分析できます。 太字のフィールドは、パートナーがサポートしている Microsoft 顧客契約にのみ適用されます。

| **フィールド名** | **説明** | **パートナー センターの同等の情報** |
| --- | --- | --- |
| invoiceld | 特定の取引の請求書に表示される請求書 ID。 | トランザクションが表示される請求書番号。 |
| previousInvoiceID | 返金 (負のコスト) がある、元の請求書への参照。 返金がある場合にのみ設定されます。 | 該当なし |
| billingAccountName | パートナーを表す課金アカウントの名前。 Microsoft 顧客契約にオンボードした顧客と、SaaS、Azure Marketplace、予約などのエンタイトルメント購入を行った CSP 顧客のすべてのコストを計上します。 | 該当なし |
| billingAccountID | パートナーを表す課金アカウントの ID。 | MCAPI パートナー コマース ルート ID。 要求で使用されますが、応答には含まれません。|
| billingProfileID | Microsoft 顧客契約にオンボードした顧客と、SaaS、Azure Marketplace、予約などのエンタイトルメント購入を行った CSP 顧客の全請求書のコストを 1 つの請求通貨でグループ化するための請求プロファイルの ID。 | MCAPI パートナー課金グループ ID。 要求で使用されますが、応答には含まれません。 |
| billingProfileName | Microsoft 顧客契約にオンボードした顧客と、SaaS、Azure Marketplace、予約などのエンタイトルメント購入を行った CSP 顧客の全請求書のコストを 1 つの請求通貨でグループ化するための請求プロファイルの名前。 | 該当なし |
| invoiceSectionName | 請求書で課金されているプロジェクトの名前。 パートナーによってオンボードされた Microsoft 顧客契約には適用されません。 | 該当なし |
| invoiceSectionID | 請求書で課金されているプロジェクトの識別子。 パートナーによってオンボードされた Microsoft 顧客契約には適用されません。 | 該当なし |
| **CustomerTenantID** | 顧客のサブスクリプションの Azure Active Directory テナントの識別子。 | 顧客の組織の ID - 顧客の Azure Active Directory TenantID。 |
| **CustomerName** | 顧客のサブスクリプションの Azure Active Directory テナントの名前。 | パートナー センターに表示される顧客の組織名。 システムの情報を使って請求書を調整するために重要です。 |
| **CustomerTenantDomainName** | 顧客のサブスクリプションの Azure Active Directory テナントのドメイン名。 | 顧客の Azure Active Directory テナント ドメイン。 |
| **PartnerTenantID** | パートナーの Azure Active Directory テナントの識別子。 | パートナー ID と呼ばれるパートナーの Azure Active Directory テナント ID (GUID 形式)。 |
| **PartnerName** | パートナーの Azure Active Directory テナントの名前。 | パートナー名。 |
| **ResellerMPNID** | サブスクリプションに関連付けられているリセラーの MPNID。 | サブスクリプションのレコードにあるリセラーの MPN ID。 現在のアクティビティでは使用できません。 |
| costCenter | サブスクリプションに関連付けられているコスト センター。 | 該当なし |
| billingPeriodStartDate | 請求書に示されている請求期間の開始日。 | 該当なし |
| billingPeriodEndDate | 請求書に示されている請求期間の終了日。 | 該当なし |
| servicePeriodStartDate | 料金についてサービス使用量の評価が行われた評価期間の開始日。 Azure サービスの料金は、評価期間に対して決定されます。 | パートナー センターの ChargeStartDate。  請求サイクルの開始日。ただし、前の請求サイクルからの以前に請求されていない潜在的な使用状況データの日付を提示する場合を除く。 この時間は常に、1 日の開始時刻である 0:00 です。 |
| servicePeriodEndDate | 料金についてサービス使用量の評価が行われた期間の終了日。 Azure サービスの料金は、評価期間に基づいて決定されます。 | 該当なし |
| date | Azure の消費データについては、評価された使用日が表示されます。 予約済みインスタンスについては、購入日が表示されます。 定期的な料金と、Marketplace やサポートなどの 1 回限りの料金については、購入日が表示されます。 | 該当なし |
| productID | 消費または購入によって料金が発生した製品の識別子。 これは、パートナー センターに示されている、productID と SKuID の連結キーです。 | 製品の ID。 |
| product | 請求書に示されている、消費または購入によって料金が発生した製品の名前。 | カタログ内の製品名。 |
| serviceFamily | 購入または課金された製品のサービス ファミリが表示されます。 たとえば、Storage や Compute など。 | 該当なし |
| productOrderID | サブスクリプションが属している資産または Azure プラン名の識別子。 たとえば、Azure Plan など。 | CommerceSubscriptionID |
| productOrderName | サブスクリプションが属している Azure プランの名前。 たとえば、Azure Plan など。 | 該当なし|
| consumedService | 従来の EA 使用量の詳細で使用される消費済みサービス (レガシ分類)。 | パートナー センターに表示されるサービス。 たとえば、Microsoft.Storage、Microsoft.Compute、microsoft.operationalinsights など。 |
| meterID | 測定された使用量の測定の ID。 | 使用されたメーターの ID。 |
| meterName | 測定された使用量の測定の名前を識別します。 | 使用されたメーターの名前。 |
| meterCategory | 使用量の最上位レベルのサービスを識別します。 | 使用状況の最上位レベルのサービス。 |
| meterSubCategory | 料金に影響する可能性のある Azure サービスの種類またはサブカテゴリを定義します。 | 料金に影響を与える可能性のある Azure サービスの種類。|
| meterRegion | データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します。 | サービスのデータ センターのリージョンの場所 (該当し、かつ設定されている場合)。 |
| サブスクリプション ID | Microsoft が生成する、Azure サブスクリプションの一意の識別子。 | EntitlementID |
| subscriptionName | Azure サブスクリプションの名前。 | 該当なし |
| 期間 | プランの有効期間を表示します。 たとえば、予約インスタンスには、予約インスタンスの年間期間の 12 か月が表示されます。 1 回限りの購入または定期的な購入の場合、期間には、SaaS、Azure Marketplace、サポートについて 1 か月が表示されます。 Azure の消費には適用されません。 | 該当なし |
| publisherType (firstParty、thirdPartyReseller、thirdPartyAgency) | 発行元をファースト パーティ、サード パーティ リセラー、またはサード パーティ機関として識別する発行元の種類。 | 該当なし |
| partNumber | 未使用の予約インスタンスと Azure Marketplace サービスの部品番号。 | 該当なし |
| publisherName | Microsoft またはサードパーティの発行元を含む、サービスの発行元の名前。 | 製品の発行元の名前。|
| reservationId | 予約インスタンス購入の識別子。 | 該当なし |
| reservationName | 予約インスタンスの名前。 | 該当なし |
| reservationOrderId | 予約インスタンスの OrderID。 | 該当なし |
| frequency | 予約インスタンスの支払い頻度。 | 該当なし |
| resourceGroup | ライフサイクル リソース管理に使用される Azure リソース グループの名前。 | リソース グループの名前。 |
| instanceID (または) ResourceID | リソース インスタンスの識別子。 | 完全なリソース プロパティを含む ResourceURI として表示されます。 |
| resourceLocation | リソースの場所の名前。 | リソースの場所。 |
| 場所 | リソースの正規化された場所。 | 該当なし |
| effectivePrice | 価格設定通貨でのサービスの実効単価。 製品、サービス ファミリ、測定、オファーに対して一意です。 課金アカウントの価格シートの価格で使用されます。 階層化された価格または含まれている数量がある場合は、使用量のブレンド価格が表示されます。 | 調整が行われた後の単価。 |
| Quantity | 購入または消費された測定量。 請求期間中に使用された測定の量。 | ユニットの数。 調整中に、これが課金システム内の情報に一致していることを確認してください。 |
| unitOfMeasure | サービスが課金される単位を特定します。 たとえば、GB や時間数など。 | サービスが課金される単位を特定します。 たとえば、GB、時間、および 10,000 単位など。 |
| pricingCurrency | 単価を定義する通貨。 | 価格表の通貨。|
| billingCurrency | 請求されるコストを定義する通貨。 | 請求書上の請求通貨として定義されている通貨。 |
| chargeType | 購入や返金など、Azure Cost Management でコストが表す料金の種類を定義します。 | 料金または調整の種類。 現在のアクティビティでは使用できません。 |
| costinBillingCurrency | 請求通貨での税引き前の ExtendedCost またはブレンド コスト。 | 該当なし |
| costinPricingCurrency | 価格に関連する価格通貨での税引き前の ExtendedCost またはブレンド コスト。 | 該当なし |
| **costinUSD** | 税引き前の、予測される ExtendedCost またはブレンド コスト (米国ドル)。 | 該当なし |
| **paygCostInBillingCurrency** | 価格が小売価格の場合のコストを表示します。 従量課金制の料金を請求通貨で表示します。 Azure RBAC スコープでのみ使用できます。 | 該当なし |
| **paygCostInUSD** | 価格が小売価格の場合のコストを表示します。 従量課金制の料金を米国ドルで表示します。 Azure RBAC スコープでのみ使用できます。 | 該当なし |
| exchangeRate | 価格通貨から請求通貨への換算に使用される為替レート。 | パートナー センターでは PCToBCExchangeRate と呼ばれます。 価格通貨から請求通貨への為替レート。|
| exchangeRateDate | 価格通貨から請求通貨に変換するために使用される為替レートの日付。 | パートナー センターでは PCToBCExchangeRateDat と呼ばれます。 価格通貨から請求通貨への為替レートの日付。|
| isAzureCreditEligible | コストが Azure クレジットでの支払い対象かどうかを示します。 | 該当なし |
| serviceInfo1 | これは、サービス固有の省略可能なメタデータをキャプチャする、以前から使用されているフィールドです。 | Azure サービスの内部メタデータ。 |
| serviceInfo2 | これは、サービス固有の省略可能なメタデータをキャプチャする、以前から使用されているフィールドです。 | サービス情報。 たとえば、仮想マシンのイメージの種類や ExpressRoute の ISP 名です。|
| additionalInfo | サービス固有のメタデータ。 たとえば、仮想マシンのイメージの種類です。 | 他の列で説明されていない任意の追加情報。 サービス固有のメタデータ。 たとえば、仮想マシンのイメージの種類です。|
| tags | ユーザーが測定に割り当てるタグです。 タグは、課金記録のグループ化に使用できます。 たとえば、タグを使用して、メーターを使用する部門ごとにコストを配分することができます。 | 顧客によって追加されたタグ。|
| **partnerEarnedCreditRate** | パートナー管理者リンク アクセスに基づいたパートナー獲得クレジット (PEC) がある場合に適用される割引率。 | パートナー獲得クレジット (PEC) の割合。 たとえば、0% または 15%。 |
| **partnerEarnedCreditApplied** | パートナー獲得クレジットが適用されているかどうかを示します。 | 該当なし |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>パートナー獲得クレジット (PEC) リソースのコストを表示する

Azure Cost Management では、パートナーはコスト分析を使用して、PEC 特典を受けたコストを表示できます。

Azure portal で、パートナー テナントにサインインし、 **[Cost Management + Billing]\(コスト管理と課金\)** を選択します。 **[コスト管理]** で、 **[コスト分析]** を選択します。

[コスト分析] ビューには、パートナーの課金アカウントのコストが表示されます。 請求書を照合するために、パートナー、特定の顧客、または課金プロファイルの **[スコープ]** を必要に応じて選択します。

ドーナツ グラフで、ドロップダウン リストを選択し、 **[PartnerEarnedCreditApplied]** を選択して PEC コストにドリル ダウンします。

![パートナー獲得クレジットの表示方法を示す例](./media/get-started-partners/cost-analysis-pec1.png)

**PartnerEarnedCreditApplied** プロパティが _True_ の場合、関連付けられたコストには、パートナーが獲得した管理者アクセスの特典があります。

**PartnerEarnedCreditApplied** プロパティが _False_ の場合、関連付けられたコストはクレジットの必要資格を満たしていません。 または、購入したサービスはパートナー獲得クレジットの対象ではありません。

通常、サービス使用状況データが Cost Management に表示されるまでに 8 ～ 24 時間かかります。 詳細については、[コストと使用状況データの更新と保持](understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention)に関する記事を参照してください。 PEC クレジットは、Azure Cost Management にアクセスしてから 48 時間以内に表示されます。


**[Group by]\(グループ化\)** オプションを使用して、**PartnerEarnedCreditApplied** プロパティによるグループ化およびフィルター処理を行うこともできます。 オプションを使用して、PEC があるコストとないコストを調べます。

![パートナー獲得クレジットによるグループ化またはフィルター](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>コスト データを Azure Storage にエクスポートする

パートナー テナント内の課金スコープにアクセスできるパートナーは、そのコストと使用状況データを Azure Storage BLOB にエクスポートできます。 この BLOB は、[共有サービス サブスクリプション](/partner-center/shared-services)や顧客のサブスクリプションではないパートナー テナント内のサブスクリプションに存在する必要があります。 コスト データのエクスポートを有効にするには、エクスポートされたコスト データをホストするための独立した従量課金制サブスクリプションをパートナー テナントに設定することをお勧めします。 エクスポート ストレージ アカウントは、従量課金制サブスクリプションでホストされている Azure Storage BLOB に作成されます。 パートナーがエクスポートを作成するスコープに基づいて、関連付けられたデータが、定期的にストレージ アカウントに自動的にエクスポートされます。

サブスクリプションへの Azure RBAC アクセス権を持つユーザーは、顧客テナント内の任意のサブスクリプションでホストされている Azure Storage BLOB にコスト データをエクスポートすることもできます。

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>パートナー テナントまたは顧客テナントでエクスポートを作成する

Azure portal で、パートナー テナントまたは顧客テナントにサインインし、 **[コストの管理と請求]** を選択します。 適切なスコープ (たとえば、Microsoft Partner Agreement の課金アカウント) を選択してから、 **[コスト分析]** を選択します。 ページが読み込まれたら、 **[エクスポート]** を選択します。 [エクスポートのスケジュール] で、 **[エクスポートをすべて表示する]** を選択します。

![[エクスポート]、[エクスポートをすべて表示する] を選択する](./media/get-started-partners/export01.png)

次に、 **[追加]** を選択し、名前を入力してエクスポートの種類を選択します。 **[ストレージ]** タブを選択し、必要な情報を入力します。

![新しいエクスポートを追加し、[ストレージ] タブを選択する](./media/get-started-partners/export02.png)

パートナー テナントでエクスポートを作成する場合は、パートナー テナント内の従量課金制サブスクリプションを選択します。 そのサブスクリプションを使用して Azure Storage アカウントを作成します。

顧客テナント内の Azure RBAC ユーザーの場合は、顧客テナント内のサブスクリプションを選択します。 そのサブスクリプションを使用して Azure Storage アカウントを作成します。

内容を確認し、 **[作成]** を選択してエクスポートをスケジュールします。

エクスポートの一覧内のデータを確認するには、ストレージ アカウント名を選択します。 ストレージ アカウント ページで、 **[コンテナー]** を選択してからコンテナーを選択します。 対応するフォルダーに移動し、CSV ファイルを選択します。 **[ダウンロード]** を選択して CSV ファイルを取得し、それを開きます。 エクスポートされたデータは、Azure portal から得られた使用状況の詳細と同様のコスト データになります。

![エクスポートされたデータの例](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>Cost Management REST API

パートナーと顧客は、次のセクションに記載されている Cost Management API を一般的なタスクに対して使用できます。

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management API - 直接プロバイダーと間接プロバイダー

パートナー テナントの課金スコープにアクセスできるパートナーは、次の API を使用して請求されたコストを表示できます。

パートナーは、サブスクリプションにアクセスできる場合、コスト ポリシーに関係なく、サブスクリプション スコープの API を呼び出すことができます。 サブスクリプションにアクセスできる他のユーザー (顧客やリセラーなど) は、パートナーが顧客テナントのコスト ポリシーを有効にした後でのみ、API を呼び出すことができます。


#### <a name="to-get-a-list-of-billing-accounts"></a>課金アカウントの一覧を取得するには

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>顧客の一覧を取得するには

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>サブスクリプションの一覧を取得するには

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>一定期間の請求書の一覧を取得するには

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

この API 呼び出しは、次の JSON コードのような要素を持つ一連の請求書を返します。

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

返された前の ID フィールドの値を使用し、次の例で使用状況の詳細のクエリを実行するためのスコープとして置き換えます。

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

この例は、特定の請求書に関連付けられた使用状況レコードを返します。


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>顧客がコストを表示するためのポリシーを取得するには

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>顧客がコストを表示するためのポリシーを設定するには

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>課金アカウントの Azure サービスの使用状況を取得するには

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>顧客の Azure サービスの使用状況をダウンロードするには

次の GET 呼び出しは非同期操作です。

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

応答で返された `Location` URI を呼び出して、操作の状態を確認します。 状態が *[完了]* の場合、`downloadUrl` プロパティには、生成されたレポートをダウンロードするために使用できるリンクが含まれています。


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>使用された Azure サービスの価格シートを取得またはダウンロードするには

まず、次の POST を使用します。

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

次に、非同期操作プロパティの値を呼び出します。 次に例を示します。

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
前の GET 呼び出しでは、価格シートを含むダウンロード リンクが返されます。


#### <a name="to-get-aggregated-costs"></a>集計コストを取得するには

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>パートナーの予算を作成する

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>顧客の予算を作成する

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>予算を削除する

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>次のステップ
- Cost Management で[コストの分析を開始する](quick-acm-cost-analysis.md)
- Cost Management で[予算を作成および管理する](tutorial-acm-create-budgets.md)
