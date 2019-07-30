---
title: Azure の VM あたり vCPU クォータの引き上げ要求 | Microsoft Docs
description: VM あたり vCPU クォータの引き上げ要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9caf82d2fa4683e845d23b5496f9e7b8f9682ec7
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234818"
---
# <a name="vm-series-vcpu-limit-increase"></a>VM シリーズ vCPU の上限の引き上げ

仮想マシンと仮想マシン スケール セットに対する Resource Manager の vCPU クォータは、各リージョンでサブスクリプションごとに 2 つのレベルで適用されます。 

最初のレベルは (すべての VM シリーズにわたる) **リージョンの vCPU の合計の制限**であり、2 つ目のレベルは **VM シリーズあたりの vCPU の制限** (D シリーズの vCPU など) です。 新しい VM をデプロイするときはいつでも、その VM シリーズに対する新規および既存の vCPU 使用量の合計が、その特定の VM シリーズに対して承認されている vCPU クォータを超えてはなりません。 さらに、すべての VM シリーズにわたってデプロイされる新規および既存の vCPU 数の合計が、サブスクリプションに対して承認されているリージョンの vCPU の合計のクォータを超えてはなりません。 いずれかのクォータを超えている場合、VM のデプロイは許可されません。
Azure Portal から VM シリーズの vCPU クォータ制限の増加を要求できます。 VM シリーズのクォータを増やすと、リージョンの vCPU の合計の制限が同じ量だけ自動的に増えます。 

クォータの詳細については、「[仮想マシンの vCPU クォータ](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)」のページおよび [Azure サブスクリプションとサービスの制限](https://aka.ms/quotalimits)に関するページを参照してください。 

増加は現在、ポータルの **[ヘルプとサポート]** ブレードまたは **[Usages + Quota] (使用量とクォータ)** ブレード経由で要求できます。 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>**[ヘルプとサポート]** ブレードを使用してサブスクリプション レベルで VM シリーズあたりの vCPU クォータの増加を要求する

Azure Portal で使用可能な Azure の [ヘルプとサポート] ブレード経由でサポート要求を作成するには、次の手順に従います。 

1. https://portal.azure.com から、 **[ヘルプとサポート]** を選択します。

   ![[ヘルプとサポート]](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **[新しいサポート リクエスト]** を選択します。 

     ![新しいサポート要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. [問題の種類] ドロップダウンで、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。

   ![[問題の種類] ドロップダウン](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. クォータの追加が必要なサブスクリプションを選択します。

   ![サブスクリプション newSR を選択する](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. **[クォータの種類]** ドロップダウンで **[Compute-VM (cores-vCPUs) subscription limit increases] (Compute-VM (cores-vCPU) サブスクリプションの制限の増加)** を選択します。 

   ![[クォータの種類] を選択する](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. **[Problem Details] (問題の詳細)** で、 **[詳細の指定]** をクリックして、要求の処理に役立つ追加情報を指定します。

   ![[詳細の指定]](./media/resource-manager-core-quotas-request/provide-details.png)

7. **[クォータの詳細]** パネルで、デプロイ モデルと場所を選択します。

   ![[クォータの詳細] の DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. 増やす必要のある **SKU ファミリ**を選択します。 

   ![SKU ファミリ](./media/resource-manager-core-quotas-request/sku-family.png)

9. サブスクリプションに対して必要な新しい制限を入力します。 行を削除するには、[SKU family (SKU ファミリ)] ドロップダウン リストで該当する SKU をオフにするか、破棄アイコン (x) をクリックします。 各 SKU ファミリの目的のクォータを入力したら、[クォータの詳細] パネルの **[保存して続行]** をクリックしてサポート要求の作成を続行します。

   ![新しい制限](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>**[Usages + Quota]\(使用量とクォータ\)** ブレードを使用してサブスクリプション レベルで VM シリーズあたりの vCPU クォータの増加を要求する

Azure Portal で使用可能な Azure の [Usages + Quota] (使用量とクォータ) ブレード経由でサポート要求を作成するには、次の手順に従います。 

1. https://portal.azure.com から **[サブスクリプション]** を選択します。

   ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. クォータの追加が必要なサブスクリプションを選択します。

   ![サブスクリプションを選択します。](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **[使用量 + クォータ]** を選択します。

   ![使用量とクォータを選択します。](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 右上の **[引き上げを依頼する]** を選択します。

   ![引き上げを依頼する](./media/resource-manager-core-quotas-request/request-increase.png)

5. クォータの種類として **[Compute-VM (cores-vCPUs) subscription limit increases] (Compute-VM (cores-vCPU) サブスクリプションの制限の増加)** を選択します。 

   ![フォームに入力する](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. **[クォータの詳細]** パネルで、デプロイ モデルと場所を選択します。

   ![クォータの [問題] ブレード](./media/resource-manager-core-quotas-request/quota-details.png)

7. 増やす必要のある **SKU ファミリ**を選択します。

   ![選択した SKU シリーズ](./media/resource-manager-core-quotas-request/sku-family.png)

8. サブスクリプションに対して必要な新しい制限を入力します。 行を削除するには、[SKU family (SKU ファミリ)] ドロップダウン リストで該当する SKU をオフにするか、破棄アイコン (x) をクリックします。 各 SKU ファミリの目的のクォータを入力したら、[クォータの詳細] パネルの **[保存して続行]** をクリックしてサポート要求の作成を続行します。

   ![SKU の新しいクォータ要求](./media/resource-manager-core-quotas-request/new-limits.png)
 
