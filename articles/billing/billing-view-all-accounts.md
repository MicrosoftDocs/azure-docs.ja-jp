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
ms.openlocfilehash: 31e4eb1d6493c8de99ac48e09dfb2b8b6481b70a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57854281"
---
# <a name="view-your-billing-accounts-in-azure-portal"></a>Azure portal での請求先アカウントの表示  

請求先アカウントは、Azure を使用するために Microsoft と締結した契約ごとに作成されます。 請求先アカウントを使用して、請求書と支払方法を管理し、Azure サービスのコストを追跡します。 複数の請求先アカウントにアクセスできます。 たとえば、個人的なプロジェクトのために Azure にサインアップしたとします。 この場合、組織の Enterprise Agreement または Microsoft Customer Agreement を通じてアクセスすることもできます。 これらのシナリオごとに、個別の請求先アカウントが提供されます。

現在、Azure portal では、次の種類の請求先アカウントがサポートされています。

- **Microsoft Online Services Program**: Azure Web サイトから Azure にサインアップすると、Microsoft Online Services Program の請求先アカウントが作成されます。 たとえば、[Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)または [Azure 従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0003p/)にサインアップした場合や、[Visual Studio サブスクライバー](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)としてサインアップした場合です。

- **マイクロソフト エンタープライズ契約**:組織が Azure を使用するために Enterprise Agreement を締結すると、Enterprise Agreement の請求先アカウントが作成されます。 Enterprise Agreement の請求先アカウントの管理者は、部門およびアカウント課金スコープを使用して、組織の請求先を管理および整理します。

- **Microsoft Customer Agreement**: 組織が Azure を使用するために Microsoft Customer Agreement を締結すると、Microsoft Customer Agreement の請求先アカウントが作成されます。 Microsoft Customer Agreement の請求先アカウントの管理者は、課金プロファイルおよび請求書セクション課金スコープを使用して、組織の請求先を管理および整理します。 詳細については、「[Get started with your billing account for Microsoft Customer Agreement (Microsoft Customer Agreement の請求先アカウントの概要)](billing-mca-overview.md)」をご覧ください。

## <a name="change-billing-account-view-in-the-azure-portal"></a>Azure portal で請求先アカウントのビューを変更する

スコープとは、ユーザーが請求先を表示および管理する際に使用する、請求先アカウント内のノードです。 たとえば、Microsoft Online Services Program の請求先アカウントのスコープは請求先アカウントだけですが、Enterprise Agreement の請求先アカウントには、請求先アカウント、部門、アカウントの各スコープがあります。 複数の課金スコープにアクセスできる場合、既定では、いずれかのスコープが [Azure Cost Management + Billing (Azure Cost Management + 請求)] ページに表示されます。 スコープを変更するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[コストの管理と請求]** で検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. 左側の **[すべての課金スコープ]** を選択します。

   ![すべての課金スコープを示すスクリーンショット](./media/billing-view-all-accounts/billing-list-of-accounts.png)

4. 詳細を表示するスコープを選択します。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
