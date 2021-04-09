---
title: スポット仮想マシン クォータ
description: Azure が必要に応じて VM を削除できるようにする代わりに、コストの削減を想定できる Azure 使用モデルを提供するスポット VM のクォータ制限を増やします。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c8b9a2251d7923fe1919b7b934f6c97877cd5b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745487"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>スポット クォータ: すべての VM シリーズの制限を増やす

スポット仮想マシン (VM) は、Azure の使用法の別のモデルを提供します。 これを使用すると、従量課金制または予約 VM インスタンスのデプロイに応じて Azure が必要に応じて仮想マシンを削除できるようにする代わりに、コストの削減を想定できます。 スポット VM の詳細については、「[仮想マシン スケール セット用の Azure スポット VM](../../virtual-machine-scale-sets/use-spot.md)」を参照してください。

Azure Resource Manager は、仮想マシンの次の 2 種類の vCPU クォータをサポートしています。

* *従量課金制 VM* と *予約 VM インスタンス* には、*標準 vCPU クォータ* が適用されます。
* *スポット VM* には、*スポット vCPU クォータ* が適用されます。

スポット vCPU クォータの種類の場合、Resource Manager の vCPU クォータは、単一のリージョン制限として使用可能なすべての仮想マシン シリーズにわたって適用されます。

新しいスポット VM をデプロイする場合は常に、すべてのスポット VM インスタンスの新規および既存の vCPU 使用量の合計が、承認されたスポット vCPU クォータ制限を超えてはいけません。 スポット クォータを超えた場合、スポット VM のデプロイは許可されません。

この記事では、Azure portal を使用してスポット vCPU クォータ制限の増加を要求する方法について説明します。

標準 vCPU クォータの詳細については、「[仮想マシンの vCPU クォータ](../../virtual-machines/windows/quotas.md)」および「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md)」を参照してください。

リージョンごとに vCPU の制限を増やす方法については、[標準クォータ: リージョンごとの制限の引き上げ」](regional-quota-requests.md)を参照してください。

## <a name="request-a-quota-limit-increase-from-help--support"></a>[ヘルプとサポート] からクォータ制限の引き上げを要求する

**[ヘルプとサポート]** を使用して、すべての仮想マシン シリーズのスポット クォータ制限の引き上げを要求するには、次のようにします。

> [!NOTE]
> また、1 つのサポート ケースを使用して、複数のリージョンのクォータ制限の増加を要求することもできます。 詳細については、ステップ 8 を参照してください。

1. [Azure portal](https://portal.azure.com) のメニューで、 **[ヘルプとサポート]** を選択します。

   ![[ヘルプとサポート] リンク](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **[ヘルプとサポート]** で、 **[新しいサポート リクエスト]** を選択します

    ![新しいサポート リクエストを作成する](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。

   ![問題の種類を選択する](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **[サブスクリプション]** で、クォータを引き上げるサブスクリプションを選択します。

   ![クォータを引き上げるサブスクリプションを選択する](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **[クォータの種類]** で、 **[Compute-VM (cores-vCPUs) subscription limit increases]/(Compute-VM (cores-vCPU) サブスクリプションの制限の引き上げ/)** を選択します。

   ![クォータの種類を選択する](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **ソリューション** を選択して **問題の詳細** を開きます。 **[詳細の指定]** を選択して、追加情報を入力します。

   ![[詳細の指定] リンク](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **[クォータの詳細]** で次の手順を実行します。

   1. **[Deployment model]/(デプロイ モデル/)** で適切なモデルを選択し、 **[Locations]/(場所/)** で場所を選択します。

      ![追加のクォータの詳細を指定する](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 選択した場所について、 **[種類]** の **[種類の選択]** で **[スポット]** を選択します。

      ![スポットの種類を選択する](./media/resource-manager-core-quotas-request/select-spot-type.png)

       **[タイプ]** では、複数選択サポートを使用して、1 つのサポート ケースから標準とスポットの両方のクォータのタイプを要求できます。

       詳細については、[標準クォータ: VM シリーズでの制限の引き上げ](per-vm-quota-requests.md)に関するページの説明に従って引き上げをリクエストしてください。

   1. このサブスクリプションに適用する新しいクォータ制限を入力します。

      ![スポット VM の新しいクォータを選択する](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 複数の場所のクォータの引き上げを要求するには、 **[Locations]/(場所/)** で追加の場所を選択してから、適切な VM の種類を選択します。 その後に、追加の場所に適用する制限を入力できます。

   ![クォータの詳細で追加の場所を指定する](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. サポート要求の作成を続行するには、 **[保存して続行]** を選択します。

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>[サブスクリプション] ペインからクォータ制限の引き上げを要求する

**[サブスクリプション]** ペインからすべての VM シリーズのスポット クォータ制限の引き上げを要求するには、次の操作を行います。

> [!NOTE]
> また、1 つのサポート ケースを使用して、複数のリージョンのクォータ制限の増加を要求することもできます。 詳細については、ステップ 7 を参照してください。

1. [Azure portal](https://portal.azure.com) で、 **[サブスクリプション]** を検索して選択します。

   ![Azure portal の検索でのサブスクリプション](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. クォータを増やしたいサブスクリプションを選択します。

   ![変更するために選択するサブスクリプション](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 左側のペインで、 **[使用量 + クォータ]** を選択します。

   ![[使用量 + クォータ] リンク](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 右上の **[引き上げを要求する]** を選択します。

   ![クォータを引き上げるために選択する](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **[クォータの種類]** で、 **[Compute-VM (cores-vCPUs) subscription limit increases]/(Compute-VM (cores-vCPU) サブスクリプションの制限の引き上げ/)** を選択します。

   ![クォータの種類を選択する](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **ソリューション** を選択して **問題の詳細** を開きます。 **[詳細の指定]** を選択して、追加情報を入力します。 **[クォータの詳細]** で次の情報を入力します。

   1. **[Deployment model]/(デプロイ モデル/)** で適切なモデルを選択し、 **[Locations]/(場所/)** で場所を選択します。

      ![クォータの詳細を入力する](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 選択した場所について、 **[種類]** の **[種類の選択]** で **[スポット]** を選択します。

      ![スポットの種類を選択する](./media/resource-manager-core-quotas-request/select-spot-type.png)

      詳細については、[標準クォータ: VM シリーズでの制限の引き上げ](per-vm-quota-requests.md)に関するページの説明に従って引き上げをリクエストしてください。

   1. このサブスクリプションに適用する新しいクォータ制限を入力します。

      ![vCPU 制限の新しい値を入力する](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 複数の場所のクォータの引き上げを要求するには、 **[Locations]/(場所/)** で追加の場所を選択してから、適切な VM の種類を選択します。 その後に、追加の場所に適用する制限を入力できます。

   ![クォータの詳細で追加の場所を選択する](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. サポート要求の作成を続行するには、 **[保存して続行]** を選択します。
