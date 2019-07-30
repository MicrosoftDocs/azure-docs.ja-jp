---
title: ネットワークの上限の引き上げ | Microsoft Docs
description: ネットワークの上限の引き上げ
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 48d7e9cc4a3034e149901931f2addbc7df78e2bc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296875"
---
# <a name="networking-limit-increase"></a>ネットワークの上限の引き上げ

ネットワークの現在の使用量とクォータは、Azure portal の **[Usages + Quota]\(使用量とクォータ\)** ブレードで確認できます。 使用量 [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages)、[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0)、または[ネットワーク使用量 API](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) を使用してネットワークの使用量と制限を確認することもできます。

引き上げは、ポータルの **[ヘルプとサポート]** ブレードまたは **[Usages + Quota]\(使用量とクォータ\)** ブレードから要求できます。

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>**[ヘルプとサポート]** ブレードを使用してサブスクリプション レベルでネットワーク クォータの引き上げを要求する

Azure Portal で使用可能な Azure の [ヘルプとサポート] ブレード経由でサポート要求を作成するには、次の手順に従います。 

1. https://portal.azure.com から、 **[ヘルプとサポート]** を選択します。

    ![[ヘルプとサポート]](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **[新しいサポート リクエスト]** を選択します。 

    ![新しいサポート要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. [問題の種類] ドロップダウンで、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。

    ![[問題の種類] ドロップダウン](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. クォータの追加が必要なサブスクリプションを選択します。

    ![サブスクリプション newSR を選択する](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. **[クォータの種類]** ドロップダウン リストで **[ネットワーク]** を選択します。 

    ![[クォータの種類] を選択する](./media/networking-quota-request/select-quota-type-network.png)

6. **[Problem Details] (問題の詳細)** で、 **[詳細の指定]** をクリックして、要求の処理に役立つ追加情報を指定します。

    ![[詳細の指定]](./media/resource-manager-core-quotas-request/provide-details.png)

7. **[クォータの詳細]** パネルで、引き上げを要求する対象のデプロイ モデル、場所、およびリソースを選択します。

    ![[クォータの詳細] の DM](./media/networking-quota-request/quota-details-network.png)

8.  サブスクリプションに対して必要な新しい制限を入力します。 行を削除するには、[リソース] ドロップダウン リストで該当するリソースをオフにするか、破棄アイコン (x) をクリックします。 各リソースの目的のクォータを入力したら、[クォータの詳細] パネルの **[保存して続行]** をクリックしてサポート要求の作成を続行します。

    ![新しい制限](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>**[Usages + Quota]\(使用量とクォータ\)** ブレードを使用してサブスクリプション レベルでネットワーク クォータの引き上げを要求する

Azure Portal で使用可能な Azure の [Usages + Quota] (使用量とクォータ) ブレード経由でサポート要求を作成するには、次の手順に従います。 

1. https://portal.azure.com から **[サブスクリプション]** を選択します。

    ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. クォータの追加が必要なサブスクリプションを選択します。

    ![サブスクリプションを選択します。](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **[使用量 + クォータ]** を選択します。

    ![使用量とクォータを選択します。](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 右上の **[引き上げを依頼する]** を選択します。

    ![引き上げを依頼する](./media/resource-manager-core-quotas-request/request-increase.png)

5. 「 **[ヘルプとサポート]** ブレードを使用して*サブスクリプション レベルでネットワーク クォータの引き上げを要求する*」節の手順 3 以降に従います

## <a name="about-networking-limits"></a>ネットワークの制限について

ネットワークの制限の詳細については、制限に関するページの[ネットワークの節](../azure-subscription-service-limits.md#networking-limits)、またはネットワークの制限に関する FAQ を参照してください