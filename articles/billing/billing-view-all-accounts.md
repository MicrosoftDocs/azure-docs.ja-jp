---
title: Azure portal での請求先アカウントの表示 | Microsoft Docs
description: Azure portal で請求先アカウントを表示する方法について説明します。
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 36430e9b0a4554761d53b537d3c32fa57068eabb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490213"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Azure portal での課金アカウントの表示  

Azure を使用するためにサインアップすると、課金アカウントが作成されます。 課金アカウントを使用して請求書と支払いを管理し、コストを追跡します。 複数の請求先アカウントにアクセスできます。 たとえば、個人的なプロジェクトのために Azure にサインアップしたとします。 この場合、組織の Enterprise Agreement または Microsoft Customer Agreement を通じてアクセスすることもできます。 これらのシナリオごとに、個別の請求先アカウントが提供されます。

現在、Azure portal では、次の種類の請求先アカウントがサポートされています。

- **Microsoft Online Services Program**: Azure Web サイトから Azure にサインアップすると、Microsoft Online Services Program の課金アカウントが作成されます。 たとえば、[Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)または[従量課金制料金のアカウント](https://azure.microsoft.com/offers/ms-azr-0003p/)にサインアップした場合や、[Visual Studio サブスクライバー](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)としてサインアップした場合です。

- **マイクロソフト エンタープライズ契約**:組織が Azure を使用するために [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) を締結すると、Enterprise Agreement の課金アカウントが作成されます。

- **Microsoft Customer Agreement**: 組織が Microsoft 担当者と連携して Microsoft Customer Agreement を締結すると、Microsoft Customer Agreement の課金アカウントが作成されます。 一部のリージョンでは、Azure Web サイトから[従量課金制料金のアカウント](https://azure.microsoft.com/offers/ms-azr-0003p/)にサインアップしたお客様や、[Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)をアップグレードしたお客様にも、Microsoft Customer Agreement の課金アカウントが作成される場合があります。 詳細については、「[Get started with your billing account for Microsoft Customer Agreement (Microsoft Customer Agreement の請求先アカウントの概要)](billing-mca-overview.md)」をご覧ください。

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>課金アカウントのスコープ
スコープとは、ユーザーが請求先を表示および管理する際に使用する、請求先アカウント内のノードです。 ユーザーが課金データ、支払い、および請求書を管理し、アカウントの全般的な管理を行う場所です。 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

|Scope (スコープ)  |定義  |
|---------|---------|
|請求先アカウント     | 1 つまたは複数の Azure サブスクリプションの 1 人の所有者 (アカウント管理者) を表します。 アカウント管理者には、サブスクリプションの作成、請求書の表示、サブスクリプションの課金の変更など、さまざまな課金タスクを実行する権限があります。  |
|サブスクリプション     |  Azure リソースのグループを表します。 請求書はこのスコープで生成されます。 請求書の支払いに使用される独自の支払方法があります。|


### <a name="enterprise-agreement"></a>Enterprise Agreement

|Scope (スコープ)  |定義  |
|---------|---------|
|請求先アカウント    | Enterprise Agreement 加入契約を表します。 請求書はこのスコープで生成されます。 部署と登録アカウントを使用して構成されます。  |
|部署     |  登録アカウントの任意のグループです。      |
|登録アカウント     |  1 人のアカウント所有者を表します。 Azure サブスクリプションはこのスコープで作成されます。  |


### <a name="microsoft-customer-agreement"></a>Microsoft 顧客契約

|Scope (スコープ)  |タスク  |
|---------|---------|
|請求先アカウント     |   複数の Microsoft 製品およびサービスの顧客契約を表します。 課金プロファイルと請求書セクションを使用して構成されます。   |
|請求プロファイル     |  請求書とその支払い方法を表します。 請求書はこのスコープで生成されます。 複数の請求書セクションを含めることができます。      |
|請求書セクション     |   請求書内のコストのグループを表します。 サブスクリプションとその他の購入は、このスコープに関連付けられます。    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Azure portal で課金スコープを切り替える


1. [Azure Portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. 左側の **[すべての課金スコープ]** を選択します。

   ![すべての課金スコープを示すスクリーンショット](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** アクセスできるスコープが 1 つしかない場合、 **[すべての課金スコープ]** は表示されません。

4. 詳細を表示するスコープを選択します。



## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順
- [コストの分析](../cost-management/quick-acm-cost-analysis.md)を開始する方法を確認します。