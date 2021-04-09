---
title: PremiumV3 レベルの構成
description: 新しい PremiumV3 価格レベルにスケーリングすることによって、Azure App Service で Web、モバイル、および API アプリのパフォーマンスを向上させる方法について説明します。
keywords: App Service, Azure App Service, スケール, スケーラブル, App Service プラン, App Service コスト
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: c187a1521762c0b69d42c81f464694729d3c02f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "100594215"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Azure App Service の PremiumV3 レベルの構成

新しい **PremiumV3** の価格レベルでは、プロセッサ、SSD ストレージが高速化され、メモリ対コア比が既存の価格レベルの 4 倍 (**PremiumV2** レベルの 2 倍) になっています。 パフォーマンスの利点を生かして、少ないインスタンス上でアプリを実行することで、費用を節約することができます。 この記事では、**PremiumV3** レベルでアプリを作成する方法またはアプリを **PremiumV3** レベルにスケール アップする方法について説明します。

## <a name="prerequisites"></a>前提条件

アプリを **PremiumV3** にスケールアップするには、**PremiumV3** より低い価格レベルの Azure App Service アプリで Azure App Service アプリを実行していること、かつそのアプリが PremiumV3 をサポートしている App Service デプロイで実行されている必要があります。

<a name="availability"></a>

## <a name="premiumv3-availability"></a>PremiumV3 の利用可能性

**PremiumV3** レベルは、Windows コンテナーと Linux コンテナーの両方を含むネイティブとコンテナー アプリの両方で使用できます。

> [!NOTE]
> プレビュー期間中に **Premium コンテナー** レベルで実行されているすべての Windows コンテナーは引き続き同様に機能しますが、**Premium コンテナー** レベルは引き続きプレビューのままです。 **PremiumV3** レベルは、**Premium コンテナー** レベルの公式の後継です。 

**PremiumV3** は一部の Azure リージョンで利用可能ですが、追加のリージョンが継続的に追加されています。 利用可能なリージョンかどうかを調べるには、[Azure Cloud Shell](../cloud-shell/overview.md) で次の Azure CLI コマンドを実行します。

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>PremiumV3 レベルでアプリを作成する

App Service アプリの価格レベルは、実行されている [App Service プラン](overview-hosting-plans.md)で定義されます。 App Service プランは、単体で作成したり、アプリ作成の一部として作成したりできます。

<a href="https://portal.azure.com" target="_blank">Azure ポータル</a>で App Service プランを構成する場合は、 **[価格レベル]** を選択します。 

**[運用]** を選択してから **[P1V3]** 、 **[P2V3]** 、または **[P3V3]** を選択し、 **[適用]** をクリックします。

![アプリの推奨価格レベルを示すスクリーンショット。](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> オプションとして **[P1V3]** 、 **[P2V3]** 、および **[P3V3]** が表示されない場合、またはこれらのオプションがグレー表示になっている場合、App Service プランが含まれている基になる App Service デプロイで **PremiumV3** を利用できない可能性があります。 詳細については、「[サポートされてないリソース グループとリージョンの組み合わせからスケール アップする](#unsupported)」を参照してください。

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>既存のアプリを PremiumV3 レベルにスケール アップする

既存のアプリを **PremiumV3** レベルにスケーリングする前に、**PremiumV3** を利用できることを確認してください。 詳細については、「[PremiumV3 の利用可能性](#availability)」を参照してください。 利用できない場合は、「[サポートされてないリソース グループとリージョンの組み合わせからスケール アップする](#unsupported)」を参照してください。

ホスティング環境によっては、スケール アップに追加の手順が必要になる場合があります。 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a> で、App Service アプリ ページを開きます。

App Service アプリ ページの左側のナビゲーションで、 **[スケール アップ (App Service のプラン)]** を選択します。

![App Service プランをスケールアップする方法を示すスクリーンショット。](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

**[運用]** を選択してから **[P1V3]** 、 **[P2V3]** 、または **[P3V3]** を選択し、 **[適用]** をクリックします。

![アプリの推奨価格レベルを示すスクリーンショット。](media/app-service-configure-premium-tier/scale-up-tier-select.png)

操作が正常に完了すると、アプリの概要ページに **PremiumV3** レベルであることが表示されます。

![PremiumV3 価格レベルを示すアプリの [概要] ページのスクリーンショット。](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>エラーが表示された場合

基になる App Service デプロイが PremiumV3 をサポートしていない場合、一部の App Service プランを PremiumV3 レベルにスケール アップすることはできません。 詳細については、「[サポートされてないリソース グループとリージョンの組み合わせからスケール アップする](#unsupported)」を参照してください。

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>サポートされてないリソース グループとリージョンの組み合わせからスケール アップする

アプリが **PremiumV3** を利用できない App Service デプロイで実行されている場合、または現時点では **PremiumV3** をサポートしていないリージョンで実行されている場合、**PremiumV3** を活用するには、アプリを再デプロイする必要があります。  2 つのオプションがあります。

- 新しいリソース グループと新しい App Service プランでアプリを作成します。 App Service プランを作成するときに、**PremiumV3** レベルを選択します。 この手順により、App Service プランが **PremiumV3** をサポートする展開単位にデプロイされます。 その後、新しく作成したアプリに、アプリケーション コードを再デプロイします。 コストを削減するために App Service プランを下位レベルにスケール ダウンした場合でも、展開単位でサポートされているので、**PremiumV3** にいつでもスケール アップできます。
- 既存の **Premium** レベルでアプリが既に実行されている場合は、**PremiumV3** を使用する新しい App Service プランの新しいリソース グループに、すべてのアプリ設定、接続文字列、デプロイ構成と共にアプリを複製できます。

    ![アプリを複製する方法を示すスクリーンショット。](media/app-service-configure-premium-tier/clone-app.png)

    **[アプリの複製]** ページで、**PremiumV3** を使用する App Service プランを目的のリージョンで作成し、複製するアプリ設定と構成を指定できます。

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Premium コンテナーから Premium V3 SKU への移行

プレビュー Premium コンテナー SKU を使用しているアプリがあり、新しい Premium V3 SKU に移行する場合は、**PremiumV3** を利用できるように、アプリを再デプロイする必要があります。 これを行うには、「[サポートされてないリソース グループとリージョンの組み合わせからスケール アップする](#scale-up-from-an-unsupported-resource-group-and-region-combination)」の最初のオプションを参照してください。

## <a name="automate-with-scripts"></a>スクリプトで自動化する

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/) を使用すると、**PremiumV3** レベルでのアプリ作成をスクリプトで自動化することができます。

### <a name="azure-cli"></a>Azure CLI

次のコマンドは、App Service プランを _P1V3_ で作成します。 このコマンドは Cloud Shell で実行することができます。 `--sku` のオプションは P1V3、 _P2V3_、および _P3V3_ です。

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次のコマンドは、App Service プランを _P1V3_ で作成します。 `-WorkerSize` のオプションは _[Small]_ 、 _[Medium]_ 、および _[Large]_ です。

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>その他のリソース

[Azure でのアプリのスケールアップ](manage-scale-up.md)
[手動または自動によるインスタンス数のスケール変更](../azure-monitor/autoscale/autoscale-get-started.md)
