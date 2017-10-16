---
title: "Azure App Service の PremiumV2 レベルの構成 |Microsoft ドキュメント"
description: "新しい PremiumV2 価格レベルにスケーリングすることによって、Azure App Service で Web、モバイル、および API アプリのパフォーマンスを向上させる方法について説明します。"
keywords: "App Service, Azure App Service, スケール, スケーラブル, App Service プラン, App Service コスト"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: cephalin
ms.openlocfilehash: 92cc8d8b0f67dde95ea2e3fc2f0f083bd8ac8aab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Azure App Service の PremiumV2 レベルの構成

新しい **PremiumV2** 価格レベル は、**標準** レベルと比較して高速なプロセッサ、SSD ストレージ、メモリコア比が 2 倍の [Dv2 シリーズ VM](../virtual-machines/windows/sizes-general.md#dv2-series) を提供します。 この記事では、**PremiumV2** レベルでアプリを作成する方法またはアプリを **PremiumV2** レベルにスケール アップする方法について説明します。

## <a name="prerequisites"></a>前提条件

Web アプリを **PremiumV2** にスケール アップするには、Azure App Service 内の Web アプリを **PremiumV2** より低い価格レベルで実行する必要があります。

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 の利用可能性

PremiumV2 レベルは現在、_Windows_ の App Service でのみ利用可能です。 Linux コンテナーはまだサポートされていません。

PremiumV2 は既にほとんどの Azure リージョンで利用可能であり、 拡大し続けています。 利用可能なリージョンかどうかを調べるには、[Azure Cloud Shell](../cloud-shell/overview.md) で次の Azure CLI コマンドを実行します。

```azurecli-interactive
az appservice list-locations --sku P1V2
```

アプリ作成中または App Service プラン作成中にエラーが発生した場合、選択したリージョンでは **PremiumV2** を利用できない可能性が高いです。

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>PremiumV2 レベルでアプリを作成する

App Service アプリの価格レベルは、実行されている [App Service プラン](azure-web-sites-web-hosting-plans-in-depth-overview.md)で定義されます。 App Service プランは、単体で作成したり、Web アプリ作成の一部として作成したりできます。

<a href="https://portal.azure.com" target="_blank">Azure ポータル</a>で App Service プランを構成する場合は、 **[価格レベル]** を選択します。 

**PremiumV2** オプションのいずれかを選択し、 **[選択]** をクリックします。

![](media/app-service-configure-premium-tier/pick-premium-tier.png)

> [!IMPORTANT] 
> **P1V2**、 **P2V2**、および **P3V2** がオプションとして表示されない場合は、**PremiumV2** が選択したリージョンで利用できないか、**PremiumV2** をサポートしない Linux App Service プランを構成しているかのいずれかです。

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>既存のアプリを PremiumV2 レベルにスケール アップする

既存のアプリを **PremiumV2** レベルにスケーリングする前に、**PremiumV2** をリージョンで使用できることを確認してください。 詳細については、「[PremiumV2 の利用可能性](#availability)」を参照してください。 です。 リージョンで使用できない場合は、「[サポートされていないリージョンからのスケール アップ](#unsupported)」を参照してください。

ホスティング環境によっては、スケール アップに追加の手順が必要になる場合があります。 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a> で、App Service アプリ ページを開きます。

App Service アプリ ページの左側のナビゲーションで、**[スケール アップ (App Service のプラン)]** を選択します。

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

**PremiumV2** サイズのいずれかを選択して、 **[選択]** をクリックします。

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

操作が正常に完了すると、アプリの概要ページに **PremiumV2** レベルであることが表示されます。

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>エラーが発生した場合

一部の App Service プランは PremiumV2 レベルにスケール アップできません。 スケール アップ操作でエラーが発生した場合、アプリには新しい App Service プランが必要です。

同じリージョンおよびリソース グループ内で _Windows_ App Service アプリを既存の App Service アプリとして作成します。 「[PremiumV2 レベルでアプリを作成する](#create)」 の手順に従って、**PremiumV2** レベルに設定します。 必要な場合は、既存の App Service プランと同じスケール アウト構成 (インスタンスの数、自動スケールなど) を使用します。

App Service アプリ ページをもう一度開きます。 App Service アプリの左側のナビゲーションで、**[App Service プランの変更]** を選択します。

![](media/app-service-configure-premium-tier/change-plan.png)

作成した App Service プランを選択します。

![](media/app-service-configure-premium-tier/select-plan.png)

変更操作が完了すると、 アプリは **PremiumV2** レベルで実行されます。

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-region"></a>サポートされていないリージョンからのスケール アップ

**PremiumV2** がまだ利用できないリージョンでアプリが実行されている場合、**PremiumV2** を利用するためにアプリを別のリージョンに移動することができます。 2 つのオプションがあります。

- 新しい **PremiumV2** プランでアプリを作成し、アプリケーション コードを再デプロイします。 「[PremiumV2 レベルでアプリを作成する](#create)」 の手順に従って、**PremiumV2** レベルに設定します。 必要な場合は、既存の App Service プランと同じスケール アウト構成 (インスタンスの数、自動スケールなど) を使用します。
- 既存の **Premium** レベルでアプリが既に実行されている場合、すべてのアプリ設定、接続文字列、およびデプロイ構成を使ってアプリを複製できます。

    ![](media/app-service-configure-premium-tier/clone-app.png)

    **アプリの複製**ページで、新しい App Service プランを指定のリージョンで作成し、複製する設定を指定することができます。

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

次のコマンドは、App Service プランを _P1V2_ で作成します。 `-WorkerSize` のオプションは _[小]_、_[中]_、および _[大]_ です。

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>その他のリソース

[Azure でのアプリのスケールアップ](web-sites-scale.md)  
[手動または自動によるインスタンス数のスケール変更](../monitoring-and-diagnostics/insights-how-to-scale.md)