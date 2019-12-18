---
title: Azure の VM あたり vCPU クォータの引き上げ要求 | Microsoft Docs
description: VM あたり vCPU クォータの引き上げ要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 280561126186e4c70399b3a1ddd177ff4eb54400
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850042"
---
# <a name="standard-quota-per-vm-series-vcpu-limit-increase"></a>標準のクォータ: VM シリーズあたりの vCPU の上限引き上げ

Resource Manager では、仮想マシンに対して 2 種類の vCPU クォータがサポートされています。 **従量課金制の VM と予約 VM インスタンス**では、標準のクォータが使用されます。 **スポット VM** は、スポットクォータを使用します。 従量課金制の標準 vCPU クォータと Reserved VM Instances は、各リージョンのサブスクリプションごとに 2 つのレベルで適用されます。

最初のレベルは (すべての VM シリーズにわたる) **リージョンの vCPU の合計の制限**であり、2 つ目のレベルは **VM シリーズあたりの vCPU の制限** (Dv3 シリーズの vCPU など) です。 新しい VM をデプロイするときはいつでも、その VM シリーズに対する新規および既存の vCPU 使用量の合計が、その特定の VM シリーズに対して承認されている vCPU クォータを超えてはなりません。 さらに、すべての VM シリーズにわたってデプロイされる新規および既存の vCPU 数の合計が、サブスクリプションに対して承認されているリージョンの vCPU の合計のクォータを超えてはなりません。 いずれかのクォータを超えている場合、VM のデプロイは許可されません。
Azure Portal から VM シリーズの vCPU クォータ制限の増加を要求できます。 VM シリーズのクォータを増やすと、リージョンの vCPU の合計の制限が同じ量だけ自動的に増えます。 

標準の vCPU クォータの詳細については、「[仮想マシンの vCPU クォータ](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)」および [Azure サブスクリプションとサービスの制限](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)に関するページを参照してください。 

標準のクォータに対するリージョンの vCPU の上限引き上げの詳細については、[こちら](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)をご覧ください。 

**Spot VM vCPU の制限の引き上げ** についての詳細を学習するには、[こちら](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota) を参照してください。

ポータルの **[ヘルプとサポート]** ブレードまたは **[Usages + Quota]\(使用量とクォータ\)** ブレードから、**VM シリーズあたりの標準の vCPU クォータの上限**を引き上げることを要求できます。

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-the-help--support-blade"></a>[ヘルプとサポート] ブレードを使用してサブスクリプション レベルで VM シリーズあたりの標準の vCPU クォータの増加を要求する

Azure Portal で使用可能な Azure の [ヘルプとサポート] ブレード経由でサポート要求を作成するには、次の手順に従います。 

1 つのサポート ケースを使用して、複数のリージョンのクォータを要求することもできます。 詳細については、以下の手順 11 を参照してください。

1. [https://portal.azure.com](https://portal.azure.com ) から、 **[ヘルプとサポート]** を選択します。

   ![[ヘルプとサポート]](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **[新しいサポート リクエスト]** を選択します。 

     ![新しいサポート要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. [問題の種類] ドロップダウンで、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。

   ![[問題の種類] ドロップダウン](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. クォータの追加が必要なサブスクリプションを選択します。

   ![サブスクリプション newSR を選択する](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. **[クォータの種類]** ドロップダウンで **[Compute -VM (cores-vCPUs) subscription limit increases]/(コンピューティング-VM (コア-vCPU) サブスクリプション制限の増加/)** を選択します。 

   ![[クォータの種類] を選択する](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. **[Problem Details] (問題の詳細)** で、 **[詳細の指定]** をクリックして、要求の処理に役立つ追加情報を指定します。

   ![[詳細の指定]](./media/resource-manager-core-quotas-request/provide-details.png)

7. **[クォータの詳細]** パネルで、 **[デプロイ モデル]** と **[場所]** を選択します。

   ![[クォータの詳細] の DM](./media/resource-manager-core-quotas-request/1-7.png)

8. 選択した場所で、 **[タイプ]** の値に **[Standard]** を選択します。 **[タイプ]** フィールドで複数選択サポートを使用して、単一のサポート ケースから Website Standard とスポットの両方のクォータ タイプを要求できます。 **スポット クォータの制限の引き上げ**の詳細については、この [ページ](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot) を参照

   ![SKU ファミリ](./media/resource-manager-core-quotas-request/1-8.png)

9. 増やす必要のある **SKU ファミリ**を選択します。

   ![SKU ファミリ](./media/resource-manager-core-quotas-request/1-9.png)

10. サブスクリプションに対して必要な新しい制限を入力します。 行を削除するには、[SKU ファミリ] ドロップダウンから SKU をオフにするか、破棄 "x" アイコンをクリックします。 

   ![新しい制限](./media/resource-manager-core-quotas-request/1-10.png)

11. 複数の場所に対してクォータを要求するには、ドロップダウンから別の**場所**をチェックし、該当する VM タイプを選択します。 この手順では、新しい場所に対して以前の**場所**に選択している SKU ファミリをプリロードします。また、必要な新しい制限をシンプルに入力することもできます。

   ![複数の場所](./media/resource-manager-core-quotas-request/1-11.png)
   
12. 各 SKU ファミリの目的のクォータを入力したら、[クォータの詳細] パネルの **[保存して続行]** をクリックしてサポート要求の作成を続行します。

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-usages--quota-blade"></a>[Usages + Quota]\(使用量とクォータ\) ブレードを使用してサブスクリプション レベルで VM シリーズあたりの標準の vCPU クォータの増加を要求する

Azure Portal で使用可能な Azure の [Usages + Quota] (使用量とクォータ) ブレード経由でサポート要求を作成するには、次の手順に従います。

1 つのサポート ケースを使用して、**複数のリージョンのクォータを要求**することもできます。 詳細については、以下の手順 10 を参照してください。

1. [https://portal.azure.com](https://portal.azure.com ) から **[サブスクリプション]** を選択します。

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

   ![クォータの [問題] ブレード](./media/resource-manager-core-quotas-request/1-1-6.png)

7. 選択した場所で、 **[タイプ]** の値に **[Standard]** を選択します。 **[タイプ]** フィールドで複数選択のサポートを使用して、単一のサポート ケースから標準と低優先度の両方のクォータ タイプを要求できます。 **Spot vCPU の制限の引き上げ**の詳細については、この [ページ](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)を参照してください。

   ![選択した SKU シリーズ](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   
8. 増やす必要のある **SKU ファミリ**を選択します。

   ![選択した SKU シリーズ](./media/resource-manager-core-quotas-request/1-1-8.png)

9. サブスクリプションに対して必要な新しい制限を入力します。 行を削除するには、[SKU ファミリ] ドロップダウンから SKU をオフにするか、破棄 "x" アイコンをクリックします。 

   ![SKU の新しいクォータ要求](./media/resource-manager-core-quotas-request/1-1-9.png)
   

10. 複数の場所に対してクォータを要求するには、ドロップダウンから別の**場所**をチェックし、該当する VM タイプを選択します。 この手順では、新しい場所に対して以前の**場所**に選択している SKU ファミリをプリロードします。また、必要な新しい制限をシンプルに入力することもできます。
   
    ![SKU の新しいクォータ要求](./media/resource-manager-core-quotas-request/1-1-10.png)
 
