---
title: Azure クラシック デプロイ モデル
description: 現在は Resource Manager モデルに置き換えられているクラシック デプロイ モデルでは、VM と仮想マシン スケール セットに対してグローバルな vCPU クォータ制限が適用されます。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b5b5e8c11bfe164aaa3539742dac8c4d267c69e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745606"
---
# <a name="classic-deployment-model"></a>クラシック デプロイ モデル

クラシック デプロイ モデルは、旧世代の Azure デプロイ モデルです。 ここでは、仮想マシンと仮想マシン スケール セットにグローバル vCPU クォータ制限が適用されます。 クラシック デプロイ モデルはもう推奨されていません。現在は Resource Manager モデルに置き換えられています。

この 2 つのデプロイ モデルと Resource Manager を使用する利点の詳細については、[Resource Manager とクラシック デプロイ](../../azure-resource-manager/management/deployment-models.md)に関する記事を参照してください。

新しいサブスクリプションが作成されると、vCPU の既定のクォータが割り当てられます。 新しい仮想マシンをクラシック デプロイ モデルを使用してデプロイする場合は、すべてのリージョンでの新しい vCPU と既存の vCPU の使用量の合計が、クラシック デプロイ モデルで承認されている vCPU のクォータを常に超えないようにする必要があります。

これらのクォータの詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。

クラシック デプロイ モデルの vCPU クォータ制限の引き上げを要求できます。 Azure portal で **[ヘルプとサポート]** または **[使用量 + クォータ]** を使用します。

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>[ヘルプとサポート] を使用してサブスクリプション レベルで VM シリーズあたりの vCPU クォータの引き上げを要求する

Azure portal で **[ヘルプとサポート]** を使用してサポート リクエストを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) のメニューで、 **[ヘルプとサポート]** を選択します。

   ![Azure portal で [ヘルプとサポート] を選択する](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **[新しいサポート リクエスト]** を選択します。

   ![Azure portal で新しいサポート リクエストを作成する](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **[問題の種類]** で、**[サービスとサブスクリプションの制限 (クォータ)]** を選択します。

   ![問題の種類としてクォータを選択する](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. クォータを増やしたいサブスクリプションを選択します。

   ![クォータを増やすサブスクリプションを選択する](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **[クォータの種類]** で、**[Compute-VM (cores-vCPUs) subscription limit increases]/(Compute-VM (cores-vCPU) サブスクリプションの制限の引き上げ/)** を選択します。

   ![増やすクォータの種類を選択する](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **ソリューション** を選択して **問題の詳細** を開きます。 **[詳細の指定]** を選択して、追加情報を入力します。

   ![要求のための詳細情報を入力する](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **[クォータの詳細]** で、**[クラシック]** を選択し、**[場所]** を選択します。

   ![デプロイ モデルと場所を含む詳細を追加する](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. **[SKU ファミリ]** については、増やす SKU ファミリを 1 つ以上選択します。

   ![増やす SKU ファミリを指定する](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. サブスクリプションに対して必要な新しい制限を入力します。 行を削除するには、**[SKU ファミリ]** から SKU の選択を解除するか、破棄 ("X") アイコンを選択します。 各 SKU ファミリにクォータを入力したら、**[クォータの詳細]** で **[保存して続行]** を選択し、サポート リクエストを続行します。

   ![新しい制限を要求する](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>[使用量 + クォータ] を使用してサブスクリプション レベルで VM シリーズあたりの vCPU クォータの引き上げを要求する

Azure portal で **[使用量 + クォータ]** を使用してサポート リクエストを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、 **[サブスクリプション]** を検索して選択します。

   ![Azure portal で [サブスクリプション] に移動する](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. クォータを増やしたいサブスクリプションを選択します。

   ![変更するサブスクリプションを選択する](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. **[使用量 + クォータ]** を選択します。

   ![サブスクリプションの使用量とクォータを選択する](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 右上の **[引き上げを依頼する]** を選択します。

   ![クォータを引き上げるために選択する](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **[クォータの種類]** として **[Compute-VM (cores-vCPUs) subscription limit increases]/(Compute-VM (cores-vCPU) サブスクリプションの制限の引き上げ/)** を選択します。

   ![[クォータの種類] を選択する](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **ソリューション** を選択して **問題の詳細** を開きます。 **[詳細の指定]** を選択して、追加情報を入力します。

   ![要求についての詳細を入力する](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **[クォータの詳細]** で、**[クラシック]** を選択し、**[場所]** を選択します。

   ![デプロイ モデルと場所を含むクォータの詳細を選択する](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 引き上げる対象の SKU ファミリを 1 つまたは複数選択します。

   ![引き上げる対象の SKU ファミリを選択する](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. サブスクリプションに対して必要な新しい制限を入力します。 行を削除するには、**[SKU ファミリ]** から SKU の選択を解除するか、破棄 ("X") アイコンを選択します。 各 SKU ファミリにクォータを入力したら、**[クォータの詳細]** で **[保存して続行]** を選択し、サポート リクエストを続行します。

   ![新しいクォータを入力する](./media/resource-manager-core-quotas-request/new-limits-classic.png)

