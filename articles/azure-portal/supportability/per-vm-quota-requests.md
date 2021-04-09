---
title: Azure VM シリーズあたりの vCPU クォータ制限の引き上げを要求する
description: Azure portal で VM シリーズの vCPU クォータ制限の引き上げ (これにより、リージョン全体の vCPU の制限が同じ量だけ引き上げられる) を要求する方法。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5db3e538a64e275313e1e0ab01f6cc6350eabb77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745436"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>標準クォータ: VM シリーズでの制限の引き上げ

Azure Resource Manager は、仮想マシン向けに次の 2 タイプの vCPU クォータをサポートしています。

* *従量課金制 VM* と *予約 VM インスタンス* には、*標準 vCPU クォータ* が適用されます。
* *スポット VM* には、*スポット vCPU クォータ* が適用されます。

従量課金制の標準 vCPU クォータと予約仮想マシン インスタンスは、各リージョンのサブスクリプションごとに 2 つのレベルで適用されます。

* 1 つ目のレベルは、すべての VM シリーズでの "*リージョンの vCPU の合計の制限*" です。
* 2 つ目のレベルは、Dv3 シリーズの vCPU などの、"*VM シリーズあたりの vCPU の制限*" です。

新しいスポット VM をデプロイする場合は常に、すべてのスポット VM インスタンスの新規および既存の vCPU 使用量の合計が、承認されたスポット vCPU クォータ制限を超えてはいけません。 スポット クォータを超えた場合、スポット VM のデプロイは許可されません。

VM シリーズの vCPU クォータ制限の引き上げは、Azure portal を使用して要求できます。 VM シリーズのクォータを増やすと、リージョンの vCPU の合計の制限が同じ量だけ自動的に増えます。

標準 vCPU クォータの詳細については、「[仮想マシンの vCPU クォータ](../../virtual-machines/windows/quotas.md)」および「[Azure サブスクリプションとサービスの制限](./classic-deployment-model-quota-increase-requests.md)」に関するページを参照してください。

リージョンごとの vCPU 制限を標準クォータに引き上げる方法については、[「標準クォータ: リージョンごとの制限の引き上げ」](regional-quota-requests.md)を参照してください。

Spot VM vCPU の制限の引き上げの詳細については、「[スポット クォータ: すべての VM シリーズでの制限の引き上げ](low-priority-quota.md)」を参照してください。

次のセクションで説明するように、2 つの方法のいずれかで、VM シリーズあたりの標準 vCPU クォータ制限の引き上げを要求できます。

## <a name="request-a-standard-quota-increase-from-help--support"></a>[ヘルプとサポート] から標準クォータの引き上げを要求する

**[ヘルプとサポート]** から VM シリーズあたりの標準 vCPU クォータの引き上げを要求するには、次の手順を実行します。

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

   ![追加のクォータの詳細を指定する](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. **[デプロイ モデル]** で、適切なモデルを選択します。

   1. **[場所]** で、場所を選択します。 選択した場所について、 **[種類]** の **[種類の選択]** で **[標準]** を選択します。

      ![[クォータの詳細] - クォータの種類](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      **[タイプ]** では、複数選択サポートを使用して、1 つのサポート ケースから標準とスポットの両方のクォータのタイプを要求できます。

      スポットクォータ制限の増加の詳細については、「[仮想マシンスケールセット用の Azure スポット VM](../../virtual-machine-scale-sets/use-spot.md)」を参照してください。

   1. **[標準]** で、クォータを引き上げる SKU シリーズを選択します。

      ![[クォータの詳細] - SKU シリーズ](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. このサブスクリプションに適用する新しいクォータ制限を入力します。 SKU を一覧から削除するには、SKU の横にあるチェックボックスをオフにするか、削除 ("X") アイコンを選択します。

      ![新しい vCPU 制限を選択する](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 複数の場所のクォータの引き上げを要求するには、 **[Locations]/(場所/)** で追加の場所を選択してから、適切な VM の種類を選択します。 その後に、追加の場所に適用する制限を入力できます。

   ![クォータの詳細で追加の場所を指定する](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. サポート要求の作成を続行するには、 **[保存して続行]** を選択します。

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>[サブスクリプション] から標準クォータの引き上げを要求する

**[サブスクリプション]** から VM シリーズあたりの標準 vCPU クォータの引き上げを要求するには、次の手順を実行します。

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

1. **[クォータの詳細]** で次の手順を実行します。

   1. **[Deployment model]/(デプロイ モデル/)** で適切なモデルを選択し、 **[Locations]/(場所/)** で場所を選択します。

      ![クォータの詳細を入力する](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 選択した場所について、 **[種類]** から **[種類の選択]** を選択し、 **[標準]** を選択します。

      ![[標準] 種類を選択する](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      **[タイプ]** では、複数選択サポートを使用して、1 つのサポート ケースから標準とスポットの両方のクォータのタイプを要求できます。

      スポットクォータ制限の増加の詳細については、「[仮想マシンスケールセット用の Azure スポット VM](../../virtual-machine-scale-sets/use-spot.md)」を参照してください。

   1. **[標準]** で、クォータを引き上げる SKU シリーズを選択します。

      ![[クォータの詳細] - SKU シリーズ](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. このサブスクリプションに適用する新しいクォータ制限を入力します。 SKU を一覧から削除するには、SKU の横にあるチェックボックスを選択解除するか、削除 ("X") アイコンを選択します。

      ![新しい vCPU 制限を選択する](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 複数の場所のクォータの引き上げを要求するには、 **[Locations]/(場所/)** で追加の場所を選択してから、適切な VM の種類を選択します。

   このステップでは、これ以前の場所であなたが選択した SKU シリーズがプリロードされます。 追加のシリーズに適用したいクォータ制限を入力します。

   ![クォータの詳細で追加の場所を選択する](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. サポート要求の作成を続行するには、 **[保存して続行]** を選択します。