---
title: Azure のリージョン クォータの引き上げ要求 | Microsoft Docs
description: リージョン クォータの引き上げ要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7ac6dce9aec1c363fa9772caabad9ce542d43888
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310646"
---
# <a name="total-regional-vcpu-limit-increase"></a>リージョンにおける合計 vCPU の上限の引き上げ 

仮想マシンと仮想マシン スケール セットに対する Resource Manager の vCPU クォータは、各リージョンでサブスクリプションごとに 2 つのレベルで適用されます。 

最初のレベルは (すべての VM シリーズにわたる) **リージョンの vCPU の合計の制限**であり、2 つ目のレベルは **VM シリーズあたりの vCPU の制限** (D シリーズの vCPU など) です。 新しい VM をデプロイするときはいつでも、その VM シリーズに対する新規および既存の vCPU 使用量の合計が、その特定の VM シリーズに対して承認されている vCPU クォータを超えてはなりません。 さらに、すべての VM シリーズにわたってデプロイされる新規および既存の vCPU 数の合計が、サブスクリプションに対して承認されているリージョンの vCPU の合計のクォータを超えてはなりません。 いずれかのクォータを超えている場合、VM のデプロイは許可されません。
Azure Portal から VM シリーズの vCPU クォータ制限の増加を要求できます。 VM シリーズのクォータを増やすと、リージョンの vCPU の合計の制限が同じ量だけ自動的に増えます。 

新しいサブスクリプションが作成されるとき、リージョンの vCPU の合計の既定値が、すべての個別 VM シリーズに対する既定の vCPU クォータの合計と等しくない場合があります。 この結果、サブスクリプションで、デプロイしたい個別の VM シリーズごとのクォータは十分な一方、すべてのデプロイに対するリージョンの vCPU の合計のクォータは十分でない可能性があります。 この場合、リージョンの vCPU の合計の制限を明示的に増やすための要求を送信する必要があります。 リージョンの vCPU の合計の制限が、そのリージョンのすべての VM シリーズにわたって承認されたクォータの合計を超えることはできません。

クォータの詳細については、「[仮想マシンの vCPU クォータ](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)」のページおよび [Azure サブスクリプションとサービスの制限](https://aka.ms/quotalimits)に関するページを参照してください。 

増加は現在、ポータルの **[ヘルプとサポート]** ブレードまたは **[Usages + Quota] (使用量とクォータ)** ブレード経由で要求できます。 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>**[ヘルプとサポート]** ブレードを使用してサブスクリプション レベルでリージョンの vCPU の合計のクォータ引き上げを要求する

Azure Portal で使用可能な Azure の [ヘルプとサポート] ブレード経由でサポート要求を作成するには、次の手順に従います。 

1. https://portal.azure.com から、 **[ヘルプとサポート]** を選択します。

![[ヘルプとサポート]](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **[新しいサポート リクエスト]** を選択します。 

![新しいサポート要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. [問題の種類] ドロップダウンで、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。

![[問題の種類] ドロップダウン](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. クォータの追加が必要なサブスクリプションを選択します。

![サブスクリプション newSR を選択する](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. **[クォータの種類]** ドロップダウンで **[Other Requests]\(その他の要求\)** を選択します。

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. **[詳細]** ウィンドウで、要求の処理とケース作成の継続に役立つよう、次の例のように追加情報を提供します。 
    1.  **[デプロイ モデル]** – "Resource Manager" を指定します
    2.  **[Requested region]\(要求されたリージョン\)** – 必要なリージョン (例: "米国東部 2") を指定します
    3.  **[New limit Value]\(新しい制限値\)** – 新しいリージョンの制限を指定します。 これは、このサブスクリプションに対する個々の SKU ファミリの承認済みクォータの合計を超えてはなりません

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>**[Usages + Quota]\(使用量とクォータ\)** ブレードを使用してサブスクリプション レベルでリージョンの vCPU の合計のクォータ引き上げを要求する

Azure Portal で使用可能な Azure の [Usages + Quota] (使用量とクォータ) ブレード経由でサポート要求を作成するには、次の手順に従います。 

1. https://portal.azure.com から **[サブスクリプション]** を選択します。

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. クォータの追加が必要なサブスクリプションを選択します。

![サブスクリプションを選択します。](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **[使用量 + クォータ]** を選択します。

![使用量とクォータを選択します。](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 右上の **[引き上げを依頼する]** を選択します。

![引き上げを依頼する](./media/resource-manager-core-quotas-request/request-increase.png)

5. **[クォータの種類]** ドロップダウンで **[Other Requests]\(その他の要求\)** を選択します。

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. **[詳細]** ウィンドウで、要求の処理とケース作成の継続に役立つよう、次の例のように追加情報を提供します。 
    1.  **[デプロイ モデル]** – "Resource Manager" を指定します
    2.  **[Requested region]\(要求されたリージョン\)** – 必要なリージョン (例: "米国東部 2") を指定します
    3.  **[New limit Value]\(新しい制限値\)** – 新しいリージョンの制限を指定します。 これは、このサブスクリプションに対する個々の SKU ファミリの承認済みクォータの合計を超えてはなりません

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



