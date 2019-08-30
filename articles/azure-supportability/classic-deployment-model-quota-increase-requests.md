---
title: Azure クラシック デプロイ モデル | Microsoft Docs
description: Azure クラシック デプロイ モデル
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 70ad44eade871d52591014ee24e645b95c52f1e5
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234864"
---
# <a name="classic-deployment-model"></a>クラシック デプロイ モデル

クラシック デプロイ モデルとは、Azure の古い世代のデプロイ モードであり、仮想マシンと仮想マシン スケール セットに対してグローバルな vCPU クォータ制限が適用されます。 クラシック デプロイ モデルはもう推奨されていません。現在は Resource Manager モデルに置き換えられています。 この 2 つのデプロイ モデルと Resource Manager の利点の詳細については、Resource Manager デプロイ モデルのページを参照してください。 新しいサブスクリプションが作成されると、vCPU の既定のクォータが割り当てられます。 新しい VM をクラシック デプロイ モデルを使用してデプロイする場合は、常にすべてのリージョンでの新しい vCPU と既存の vCPU の使用量の合計が、クラシック デプロイ モデルで承認されている vCPU のクォータを超えないようにする必要があります。 [Azure サブスクリプションのクォータとサービスの制限に関するページ](https://aka.ms/quotalimits)で詳細を確認してください。

ポータルの [ヘルプとサポート] ブレードまたは [Usages + Quota]\(使用量とクォータ\) ブレードから、クラシック デプロイ モデルに対する vCPU の制限を引き上げることを要求できます。

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>**[ヘルプとサポート]** ブレードを使用してサブスクリプション レベルで VM シリーズあたりの vCPU クォータの増加を要求する

Azure Portal で使用可能な Azure の [ヘルプとサポート] ブレード経由でサポート要求を作成するには、次の手順に従います。 

1. [https://portal.azure.com](https://portal.azure.com ) から、 **[ヘルプとサポート]** を選択します。

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

7. **[クォータの詳細]** パネルで、[クラシック] と場所を選択します。

   ![[クォータの詳細] の DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. 増やす必要のある **SKU ファミリ**を選択します。 

   ![SKU ファミリ](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. サブスクリプションに対して必要な新しい制限を入力します。 行を削除するには、[SKU family (SKU ファミリ)] ドロップダウン リストで該当する SKU をオフにするか、破棄アイコン (x) をクリックします。 各 SKU ファミリの目的のクォータを入力したら、[クォータの詳細] パネルの **[保存して続行]** をクリックしてサポート要求の作成を続行します。

   ![新しい制限](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>**[Usages + Quota]\(使用量とクォータ\)** ブレードを使用してサブスクリプション レベルで VM シリーズあたりの vCPU クォータの増加を要求する

Azure Portal で使用可能な Azure の [Usages + Quota] (使用量とクォータ) ブレード経由でサポート要求を作成するには、次の手順に従います。 

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
   
6. **[Problem Details] (問題の詳細)** で、 **[詳細の指定]** をクリックして、要求の処理に役立つ追加情報を指定します。

   ![[詳細の指定]](./media/resource-manager-core-quotas-request/provide-details.png)

7. **[クォータの詳細]** パネルで、[クラシック] と場所を選択します。

   ![[クォータの詳細] の DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. 増やす必要のある **SKU ファミリ**を選択します。 

   ![SKU ファミリ](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. サブスクリプションに対して必要な新しい制限を入力します。 行を削除するには、[SKU family (SKU ファミリ)] ドロップダウン リストで該当する SKU をオフにするか、破棄アイコン (x) をクリックします。 各 SKU ファミリの目的のクォータを入力したら、[クォータの詳細] パネルの **[保存して続行]** をクリックしてサポート要求の作成を続行します。

   ![新しい制限](./media/resource-manager-core-quotas-request/new-limits-classic.png)

