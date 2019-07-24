---
title: Azure Resource Manager の vCPU クォータを増やす要求 | Microsoft Docs
description: Azure Resource Manager の vCPU クォータを増やす要求
author: ganganarayanan
ms.author: gangan
ms.date: 6/13/2018
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 204deaf3a67984c0dd5eca5352686719e7767885
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792295"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Resource Manager の vCPU クォータを増やす要求

Resource Manager の vCPU クォータは、リージョン レベルおよび SKU のファミリ レベルで適用されます。
クォータを適用する方法について詳しくは、[Azure のサブスクリプションとサービスの制限](https://aka.ms/quotalimits)に関するページをご覧ください。
SKU のファミリについては、[Virtual Machines の料金](https://aka.ms/pricingcompute)ページでコストとパフォーマンスを比較できます。

引き上げを依頼するには、以下の手順に従い、Azure portal で使用できる Azure の [使用量 + クォータ] ブレードでサポート要求を作成します。 

## <a name="request-quota-increase-at-subscription-level"></a>サブスクリプション レベルでクォータの引き上げを依頼する

1. https://portal.azure.com から **[サブスクリプション]** を選択します。

   ![サブスクリプション](./media/resource-manager-core-quotas-request/subscriptions.png)

2. クォータの追加が必要なサブスクリプションを選択します。

   ![サブスクリプションを選択します。](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **[使用量 + クォータ]** を選択します。

   ![使用量とクォータを選択します。](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 右上の **[引き上げを依頼する]** を選択します。

   ![引き上げを依頼する](./media/resource-manager-core-quotas-request/request-increase.png)

5. 手順 1: クォータの種類として、**[コア]** を選択します。 

   ![フォームに入力する](./media/resource-manager-core-quotas-request/forms.png)
   
6. 手順 2: [デプロイ モデル] として [リソース マネージャー] を選択して [場所] を選択します。

    ![クォータの [問題] ブレード](./media/resource-manager-core-quotas-request/Problem-step.png)

3. 増加する必要がある SKU のファミリを選択します。

    ![選択した SKU シリーズ](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. サブスクリプションに対して必要な新しい制限を入力します。

    ![SKU の新しいクォータ要求](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- 行を削除するには、[SKU family (SKU ファミリ)] ドロップダウン リストで該当する SKU をオフにするか、破棄アイコン (x) をクリックします。
各 SKU ファミリに必要なクォータを入力した後に、[問題] ステップ ページの [次へ] をクリックしてサポート要求の作成を続行します。

