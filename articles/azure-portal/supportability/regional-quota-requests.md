---
title: Azure リージョンの vCPU クォータ制限の引き上げを要求する
description: Azure portal でリージョンの vCPU クォータ制限の引き上げを要求する方法。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745419"
---
# <a name="standard-quota-increase-limits-by-region"></a>標準クォータ: リージョンごとの制限の引き上げ

Azure Resource Manager は、仮想マシンの次の 2 種類の vCPU クォータをサポートしています。

* *従量課金制 VM* と *予約 VM インスタンス* には、*標準 vCPU クォータ* が適用されます。
* *スポット VM* には、*スポット vCPU クォータ* が適用されます。

従量課金制の標準 vCPU クォータと予約仮想マシン インスタンスは、各リージョンのサブスクリプションごとに 2 つのレベルで適用されます。

* 1 つ目のレベルは、すべての VM シリーズでの "*リージョンの vCPU の合計の制限*" です。
* 2 つ目のレベルは、D シリーズの vCPU など、"*VM シリーズあたりの vCPU の制限*" です。

新しいスポット VM をデプロイする場合、その VM シリーズの新規および既存の vCPU 使用量の合計が、その特定の VM シリーズの承認されたスポット vCPU クォータを超えることはできません。 さらに、すべての VM シリーズにわたってデプロイされる新規および既存の vCPU の合計数が、サブスクリプションに対して承認されているリージョンの vCPU の合計のクォータを超えることはできません。 これらのクォータのいずれかを超えている場合には、VM のデプロイは許可されません。

VM シリーズの vCPU クォータ制限の引き上げは、Azure portal を使用して要求できます。 VM シリーズのクォータを増やすと、リージョンの vCPU の合計の制限が同じ量だけ自動的に増えます。

新しいサブスクリプションを作成するとき、リージョンの vCPU の合計の既定値が、すべての個別 VM シリーズに対する既定の vCPU クォータの合計と等しくない場合があります。 この不一致により、サブスクリプションで、デプロイしたい個別の VM シリーズごとのクォータは十分になる可能性があります。 一方、すべてのデプロイに対するリージョンの vCPU の合計に対応するクォータは十分でない可能性があります。 この場合、リージョンの vCPU の合計数の制限を明示的に増やすための要求を送信する必要があります。 リージョンの vCPU の合計の制限が、そのリージョンのすべての VM シリーズにわたって承認されたクォータの合計を超えることはできません。

標準 vCPU クォータの詳細については、「[仮想マシンの vCPU クォータ](../../virtual-machines/windows/quotas.md)」および「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md)」を参照してください。

Spot VM vCPU の制限の引き上げの詳細については、「[スポット クォータ: すべての VM シリーズでの制限の引き上げ](low-priority-quota.md)」を参照してください。

次の 2 つの方法のいずれかで、vCPU の標準クォータ制限の引き上げをリージョン別に要求できます。

## <a name="request-a-quota-increase-by-region-from-help--support"></a>[ヘルプとサポート] からリージョンごとにクォータの引き上げを要求する

**[ヘルプとサポート]** からリージョンごとに vCPU クォータの引き上げを要求するには:

1. [Azure portal](https://portal.azure.com) のメニューで、 **[ヘルプとサポート]** を選択します。

   ![[ヘルプとサポート] リンク](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **[ヘルプとサポート]** で、 **[新しいサポート リクエスト]** を選択します

    ![新しいサポート要求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。

   ![問題の種類を選択する](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **[サブスクリプション]** で、クォータを引き上げるサブスクリプションを選択します。

   ![サブスクリプションの選択](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **[クォータの種類]** で、**[その他の要求]** を選択します。

   ![クォータの種類を選択する](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. **ソリューション** を選択して **問題の詳細** を開きます。 **[説明]** に次の情報を指定します。

    1. **[デプロイ モデル]** には、**[Resource Manager]** を指定します。  
    1. **[リージョン]** には、必要なリージョン (例: **[米国東部 2]**) を指定します。  
    1. **[新しい制限]** には、そのリージョンに対する新しい vCPU の制限を指定します。 この値は、このサブスクリプションに対する個々の SKU シナリオの承認済みクォータの合計を超えてはなりません。

    ![クォータ要求の詳細を入力する](./media/resource-manager-core-quotas-request/regional-details.png)

1. **[確認と作成]** を選択して、サポート要求の作成を続行します。

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>[サブスクリプション] からリージョンごとにクォータの引き上げを要求する

**[サブスクリプション]** からリージョンごとに vCPU クォータの引き上げを要求するには:

1. [Azure portal](https://portal.azure.com) で、 **[サブスクリプション]** を検索して選択します。

   ![Azure portal で [サブスクリプション] に移動する](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. クォータを増やしたいサブスクリプションを選択します。

   ![変更するサブスクリプションを選択する](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 左側のペインで、 **[使用量 + クォータ]** を選択します。

   ![使用状況とクォータのリンクを選択する](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 右上の **[引き上げを要求する]** を選択します。

   ![クォータを引き上げるために選択する](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **[クォータの種類]** で **[その他の要求]** を選択します。

   ![クォータの種類を選択する](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. **ソリューション** を選択して **問題の詳細** を開きます。 **[説明]** ボックスに、次の追加情報を指定します。

    1. **[デプロイ モデル]** には、**[Resource Manager]** を指定します。  
    1. **[リージョン]** には、必要なリージョン (例: **[米国東部 2]**) を指定します。  
    1. **[新しい制限]** には、そのリージョンに対する新しい vCPU の制限を指定します。 この値は、このサブスクリプションに対する個々の SKU シナリオの承認済みクォータの合計を超えてはなりません。

    ![詳細情報を入力する](./media/resource-manager-core-quotas-request/regional-details.png)

1. **[確認と作成]** を選択して、サポート要求の作成を続行します。
