---
title: "PowerShell を使用した Web アプリの複製"
description: "PowerShell を使用して既存の Web Apps を新しい Web Apps に複製する方法を説明します。"
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e238f804d4a96e10101e7aa2e2a1bcb78a44e967
ms.lasthandoff: 11/17/2016


---
# <a name="azure-app-service-app-cloning-using-powershell"></a>PowerShell を使用した Azure App Service アプリの複製
Microsoft Azure PowerShell バージョン 1.1.0 のリリースに伴って新しいオプションが New-AzureRMWebApp に追加され、異なるリージョンまたは同じリージョンで新たに作成されたアプリに既存の Web アプリを複製できるようになりました。 これにより、お客様は、リージョンをまたいでさまざまなアプリを迅速かつ簡単にデプロイできるようになります。

アプリの複製は、現在、Premium レベルの App Service プランでのみサポートされています。 この新機能には Web アプリのバックアップ機能と同じ制限が適用されます。[Azure App Service での Web アプリのバックアップ](web-sites-backup.md)に関するページを参照してください。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Azure Resource Manager ベースの Azure PowerShell コマンドレットを使用して Web アプリを管理する方法については、 [Azure Resource Manager ベースの Azure Web アプリ用 PowerShell コマンド](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="cloning-an-existing-app"></a>既存のアプリの複製
シナリオ: 米国中南部リージョンに既存の Web アプリがあり、ユーザーはそのコンテンツを米国中北部の新しい Web アプリに複製したいと考えています。 これを実現するには、PowerShell コマンドレットの Azure Resource Manager バージョンを使用して新しい Web アプリを作成します (-SourceWebApp オプションを指定)。

ソース Web アプリを含むリソース グループの名前がわかっていれば、次の PowerShell コマンドを使ってソース Web アプリの情報を取得できます (この場合の名前は source-webapp)。

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

新しい App Service プランを作成するには、次の例のように New-AzureRmAppServicePlan コマンドを使用します。

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

New-AzureRmWebApp コマンドを使って米国中北部リージョンに新しい Web アプリを作成し、既存の Premium レベルの App Service プランに関連付けることができます。さらに、ソース Web アプリと同じリソース グループを使うか、新しいリソース グループを定義できます。そのコマンドを次に示します。

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

関連付けられているすべてのデプロイ スロットを含め、既存の Web アプリを複製するには、IncludeSourceWebAppSlots パラメーターを使用する必要があります。次の PowerShell コマンドでは、New-AzureRmWebApp コマンドでこのパラメーターを使用しています。

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots

同じリージョン内で既存の Web アプリを複製するには、同じリージョンに新しいリソース グループと新しい App Service プランを作成したうえで、次の PowerShell コマンドを使って Web アプリを複製する必要があります。

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>App Service 環境への既存のアプリの複製
シナリオ: 米国中南部リージョンに既存の Web アプリがあり、ユーザーはそのコンテンツを既存の App Service 環境 (ASE) の新しい Web アプリに複製したいと考えています。

ソース Web アプリを含むリソース グループの名前がわかっていれば、次の PowerShell コマンドを使ってソース Web アプリの情報を取得できます (この場合の名前は source-webapp)。

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

ASE の名前と、ASE が属するリソース グループの名前がわかっていれば、New-AzureRmWebApp コマンドを使って、既存の ASE に新しい Web アプリを作成できます。そのコマンドを次に示します。

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

旧バージョンとの関係で Location パラメーターが必要ですが、ASE にアプリを作成する場合は、このパラメーターは無視されます。 

## <a name="cloning-an-existing-app-slot"></a>既存のアプリ スロットの複製
シナリオ: ユーザーは、既存の Web アプリ スロットを新しい Web アプリまたは新しい Web アプリ スロットに複製したいと考えています。 新しい Web アプリは、元の Web アプリ スロットと同じリージョン内にあるものでも、別のリージョンにあるものでもかまいません。

ソース Web アプリを含むリソース グループの名前がわかっていれば、次の PowerShell コマンドを使ってソース Web アプリ スロット (この場合は source-webappslot) の情報を Web アプリ source-webapp に関連付けることができます。

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

ソース Web アプリの複製を新しい Web アプリに作成するコードを次に示します。

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>アプリの複製時における Traffic Manager の構成
複数リージョンの Web アプリを作成し、トラフィックをそのすべての Web アプリにルーティングするように Azure Traffic Manager を構成することは、顧客のアプリの高可用性を確保するための重要なシナリオです。既存の Web アプリを複製する際に、両方の Web アプリを新しい Traffic Manager プロファイルと既存の Traffic Manager プロファイルのいずれかに関連付けることができます。サポートされているのは Azure Resource Manager バージョンの Traffic Manager のみであることに注意してください。

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>アプリの複製時における新しい Traffic Manager プロファイルの作成
シナリオ: ユーザーは、Web アプリを別のリージョンに複製しつつ、両方の Web アプリを含む Azure Resource Manager Traffic Manager プロファイルを構成したいと考えています。 新しい Traffic Manager プロファイルを構成しつつ、ソース Web アプリの複製を新しい Web アプリに作成するコードを次に示します。

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>既存の Traffic Manager プロファイルへの新しい複製 Web アプリの追加
シナリオ: ユーザーは、既に Azure Resource Manager Traffic Manager プロファイルを作成しており、両方の Web アプリをエンドポイントとして追加したいと考えています。 そのためには、まず既存の Traffic Manager プロファイル ID を構成する必要があります。その際、サブスクリプション ID、リソース グループ名、既存の Traffic Manager プロファイル名が必要になります。

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Traffic Manager ID を構成した後で、ソース Web アプリの複製を新しい Web アプリに作成しつつ、それらを既存の Traffic Manager プロファイルに追加するコードを次に示します。

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>現在の制限
この機能は現在プレビュー段階にあり、新機能を追加する作業が続けられています。次の一覧に、現在のバージョンのアプリの複製における既知の制限を示します。

* 自動スケールの設定は複製されない
* バックアップ スケジュールの設定は複製されない
* VNET の設定は複製されない
* App Insights は複製先の Web アプリで自動的にセットアップされない
* 簡単認証の設定は複製されない
* Kudu 拡張機能は複製されない
* TiP ルールは複製されない
* データベースの内容は複製されない

### <a name="references"></a>参照
* [Azure Resource Manager ベースの Azure Web アプリ用 PowerShell コマンド](app-service-web-app-azure-resource-manager-powershell.md)
* [Azure ポータルを使用した Web アプリの複製](app-service-web-app-cloning-portal.md)
* [Azure App Service での Web アプリのバックアップ](web-sites-backup.md)
* [Azure Resource Manager による Azure Traffic Manager プレビューのサポート](../traffic-manager/traffic-manager-powershell-arm.md)
* [App Service 環境の概要](app-service-app-service-environment-intro.md)
* [Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)


