---
title: PowerShell でアプリを複製する - Azure App Service
description: PowerShell を使用して App Service アプリを新しいアプリに複製する方法について説明します。
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: d31a6ee13965aa326ab8a71b5b5435025bc26057
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705737"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>PowerShell を使用した Azure App Service アプリの複製

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Microsoft Azure PowerShell バージョン 1.1.0 のリリースに伴って新しいオプションが `New-AzWebApp` に追加され、異なるリージョンまたは同じリージョンで新たに作成されたアプリに既存の App Service アプリを複製できるようになりました。 このオプションにより、リージョンをまたいでさまざまなアプリを迅速かつ簡単に展開できるようになります。

アプリの複製は、Standard、Premium、Premium V2、および Isolated の各 App Service プランでサポートされています。 この新機能には App Service バックアップ機能と同じ制限が適用されます。[Azure App Service でのアプリのバックアップ](manage-backup.md)に関するページを参照してください。

## <a name="cloning-an-existing-app"></a>既存のアプリの複製
シナリオ: ユーザーは、米国中南部リージョンに既存のアプリを持っており、そのコンテンツを米国中北部の新しいアプリに複製したいと考えています。 これを実現するには、PowerShell コマンドレットの Azure Resource Manager バージョンを使って新しいアプリを作成します (`-SourceWebApp` オプションを指定)。

ソース アプリを含むリソース グループの名前がわかっていれば、次の PowerShell コマンドを使ってソース アプリの情報を取得できます (この場合の名前は `source-webapp`)。

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

新しい App Service プランを作成するには、次の例のように `New-AzAppServicePlan` コマンドを使います。

```powershell
New-AzAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

`New-AzWebApp` コマンドを使うと、米国中北部リージョンに新しいアプリを作成し、既存の Premium レベル App Service プランに関連付けることができます。 さらに、ソース アプリと同じリソース グループを使うことも、次のコマンドで示すように新しいリソース グループを定義することもできます。

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

関連付けられているすべてのデプロイ スロットを含む既存のアプリを複製するには、`IncludeSourceWebAppSlots` パラメーターを使う必要があります。 次の PowerShell コマンドは、`New-AzWebApp` コマンドでのこのパラメーターの使い方を示したものです。

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

同じリージョン内で既存のアプリを複製するには、同じリージョンに新しいリソース グループと新しい App Service プランを作成したうえで、次の PowerShell コマンドを使ってアプリを複製する必要があります。

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>App Service 環境への既存のアプリの複製
シナリオ: 米国中南部リージョンに既存のアプリがあり、ユーザーはそのコンテンツを既存の App Service 環境 (ASE) の新しいアプリに複製したいと考えています。

ソース アプリを含むリソース グループの名前がわかっていれば、次の PowerShell コマンドを使ってソース アプリの情報を取得できます (この場合の名前は `source-webapp`)。

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

ASE の名前と、ASE が属すリソース グループの名前がわかっていれば、次のコマンドで示すように、既存の ASE に新しいアプリを作成できます。

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

`Location` パラメーターは従来の理由により必要ですが、ASE にアプリを作成するときは無視されます。 

## <a name="cloning-an-existing-app-slot"></a>既存のアプリ スロットの複製
シナリオ: アプリの既存のデプロイ スロットを新しいアプリまたは新しいスロットに複製したいと考えています。 新しいアプリは、元のアプリ スロットと同じリージョン内にあるものでも、別のリージョンにあるものでもかまいません。

ソース アプリを含むリソース グループの名前がわかっていれば、次の PowerShell コマンドを使って、`source-app` に関連付けられたソース アプリ スロット (この場合の名前は `source-appslot`) の情報を取得できます。

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

ソース アプリの複製を新しいアプリに作成するコマンドを次に示します。

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>アプリの複製時における Traffic Manager の構成
複数リージョンのアプリを作成し、これらのアプリすべてにトラフィックをルーティングするように Azure Traffic Manager を構成することは、アプリの高可用性を確保するための重要なシナリオです。 既存のアプリを複製するときは、両方のアプリを新しい Traffic Manager プロファイルまたは既存の Traffic Manager プロファイルのどちらに接続するかを選ぶことができます。 Azure Resource Manager バージョンの Traffic Manager のみがサポートされています。

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>アプリの複製時における新しい Traffic Manager プロファイルの作成
シナリオ: ユーザーは、アプリを別のリージョンに複製しつつ、両方のアプリを含む Azure Resource Manager Traffic Manager プロファイルを構成したいと考えています。 新しい Traffic Manager プロファイルを構成しつつ、ソース アプリの複製を新しいアプリに作成するコマンドを次に示します。

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>既存の Traffic Manager プロファイルへの新しい複製アプリの追加
シナリオ: ユーザーは、既に Azure Resource Manager Traffic Manager プロファイルを作成しており、両方のアプリをエンドポイントとして追加したいと考えています。 そのためには、まず既存の Traffic Manager プロファイル ID を構成する必要があります。 サブスクリプション ID、リソース グループ名、既存の Traffic Manager プロファイル名が必要です。

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Traffic Manager ID を構成した後で、ソース アプリの複製を新しいアプリに作成しつつ、それらを既存の Traffic Manager プロファイルに追加するコマンドを次に示します。

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>現在の制限
アプリ複製に関する既知の制限を次に示します。

* 自動スケールの設定は複製されない
* バックアップ スケジュールの設定は複製されない
* VNET の設定は複製されない
* App Insights は複製先のアプリで自動的にセットアップされない
* 簡単認証の設定は複製されない
* Kudu 拡張機能は複製されない
* TiP ルールは複製されない
* データベースの内容は複製されない
* 異なるスケール ユニットにクローニングした場合に送信 IP アドレスは変更されない
* Linux アプリでは利用できない

### <a name="references"></a>参照
* [App Service の複製](app-service-web-app-cloning.md)
* [Azure App Service でアプリをバックアップする](manage-backup.md)
* [Azure Resource Manager による Azure Traffic Manager プレビューのサポート](../traffic-manager/traffic-manager-powershell-arm.md)
* [App Service 環境の概要](environment/intro.md)
* [Azure リソース マネージャーでの Azure PowerShell の使用](../azure-resource-manager/manage-resources-powershell.md)

