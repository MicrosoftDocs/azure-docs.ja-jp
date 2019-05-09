---
title: PremiumV2 レベルの構成 - Azure App Service | Microsoft Docs
description: 新しい PremiumV2 価格レベルにスケーリングすることによって、Azure App Service で Web、モバイル、および API アプリのパフォーマンスを向上させる方法について説明します。
keywords: App Service, Azure App Service, スケール, スケーラブル, App Service プラン, App Service コスト
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c85644e3cab39f9e0864af91722ee54aab6d59f3
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486871"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Azure App Service の PremiumV2 レベルの構成

新しい **PremiumV2** の価格レベルでは、プロセッサ、SSD ストレージが高速化され、メモリ対コア比が既存の価格レベルの 2 倍になっています。 パフォーマンスの利点を生かして、少ないインスタンス上でアプリを実行することで、費用を節約することができます。 この記事では、**PremiumV2** レベルでアプリを作成する方法またはアプリを **PremiumV2** レベルにスケール アップする方法について説明します。

## <a name="prerequisites"></a>前提条件

アプリを **PremiumV2** にスケールアップするには、**PremiumV2** より低い価格レベルの Azure App Service アプリで Azure App Service アプリを実行していること、かつそのアプリが PremiumV2 をサポートしている App Service デプロイで実行されている必要があります。

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 の利用可能性

**PremiumV2** レベルは、_Windows_ と _Linux_ の両方の App Service で利用できます。

**PremiumV2** は、ほとんどの Azure リージョンで利用できます。 利用可能なリージョンかどうかを調べるには、[Azure Cloud Shell](../cloud-shell/overview.md) で次の Azure CLI コマンドを実行します。

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>PremiumV2 レベルでアプリを作成する

App Service アプリの価格レベルは、実行されている [App Service プラン](overview-hosting-plans.md)で定義されます。 App Service プランは、単体で作成したり、アプリ作成の一部として作成したりできます。

<a href="https://portal.azure.com" target="_blank">Azure ポータル</a>で App Service プランを構成する場合は、 **[価格レベル]** を選択します。 

**[Production]\(運用\)** を選択してから **[P1V2]**、**[P2V2]**、または **[P3V2]** を選択し、**[適用]** をクリックします。

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> オプションとして **[P1V2]** **[P2V2]**、および **[P3V2]** が表示されない場合、またはこれらのオプションがグレー表示になっている場合、App Service プランが含まれている基になる App Service デプロイで **PremiumV2** を利用できない可能性があります。 詳細については、「[サポートされてないリソース グループとリージョンの組み合わせからスケール アップする](#unsupported)」を参照してください。

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>既存のアプリを PremiumV2 レベルにスケール アップする

既存のアプリを **PremiumV2** レベルにスケーリングする前に、**PremiumV2** を利用できることを確認してください。 詳細については、「[PremiumV2 の利用可能性](#availability)」を参照してください。 です。 利用できない場合は、「[サポートされてないリソース グループとリージョンの組み合わせからスケール アップする](#unsupported)」を参照してください。

ホスティング環境によっては、スケール アップに追加の手順が必要になる場合があります。 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a> で、App Service アプリ ページを開きます。

App Service アプリ ページの左側のナビゲーションで、**[スケール アップ (App Service のプラン)]** を選択します。

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

**[Production]\(運用\)** を選択してから **[P1V2]**、**[P2V2]**、または **[P3V2]** を選択し、**[適用]** をクリックします。

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

操作が正常に完了すると、アプリの概要ページに **PremiumV2** レベルであることが表示されます。

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>エラーが発生した場合

基になる App Service デプロイが PremiumV2 をサポートしていない場合、一部の App Service プランを PremiumV2 レベルにスケール アップすることはできません。  詳細については、「[サポートされてないリソース グループとリージョンの組み合わせからスケール アップする](#unsupported)」を参照してください。

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>サポートされてないリソース グループとリージョンの組み合わせからスケール アップする

アプリが **PremiumV2** を利用できない App Service デプロイで実行されている場合、または現時点では **PremiumV2** をサポートしていないリージョンで実行されている場合、**PremiumV2** を活用するには、アプリを再デプロイする必要があります。  2 つのオプションがあります。

- **新しい**リソース グループを作成した後、**新しい**リソース グループ内に**新しい**アプリと App Service プランを作成し、作成プロセス中に目的の Azure リージョンを選択します。  新しい App Service プランの作成時に、**PremiumV2** プランを選択する**必要があります**。  これにより、リソース グループ、App Service プラン、および Azure リージョンの組み合わせが、**PremiumV2** をサポートする App Service デプロイ内に作成される App Service プランになります。  その後、新しく作成たアプリと App Service プランに、アプリケーション コードを再デプロイします。 必要に応じて、後で App Service プランを **PremiumV2** からスケール ダウンしてコストを節約できます。将来もう一度 **PremiumV2** を使用するように、問題なくスケール アップできます。
- 既存の **Premium** レベルでアプリが既に実行されている場合は、**PremiumV2** を使用する新しい App Service プランに、すべてのアプリ設定、接続文字列、およびデプロイ構成と共にアプリを複製できます。

    ![](media/app-service-configure-premium-tier/clone-app.png)

    **[アプリの複製]** ページで、**PremiumV2** を使用する App Service プランを目的のリージョンで作成し、複製するアプリ設定と構成を指定できます。

## <a name="automate-with-scripts"></a>スクリプトで自動化する

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/overview) を使用すると、**PremiumV2** レベルでのアプリ作成をスクリプトで自動化することができます。

### <a name="azure-cli"></a>Azure CLI

次のコマンドは、App Service プランを _P1V2_ で作成します。 このコマンドは Cloud Shell で実行することができます。 `--sku` のオプションは P1V2、 _P2V2_、および _P3V2_ です。

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次のコマンドは、App Service プランを _P1V2_ で作成します。 `-WorkerSize` のオプションは _[Small]_、_[Medium]_、および _[Large]_ です。

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>その他のリソース

[Azure でのアプリのスケールアップ](web-sites-scale.md)  
[手動または自動によるインスタンス数のスケール変更](../monitoring-and-diagnostics/insights-how-to-scale.md)
