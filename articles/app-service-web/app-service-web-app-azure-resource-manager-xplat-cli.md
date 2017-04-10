---
title: "Azure Resource Manager ベースのクロスプラットフォーム コマンド ライン ツールを使用して Azure Web アプリを管理する | Microsoft Docs"
description: "Azure Resource Manager ベースの新しいクロスプラットフォーム コマンド ライン ツールを使用して Azure Web アプリを管理する方法について説明します。"
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: d415b195-4262-416f-b59f-7e1aef200054
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 5ea7095e12b6194556d3cd0baa43ccfed1e087ee
ms.openlocfilehash: 7a03e1417617453c43edcc3787da10d171359757
ms.lasthandoff: 02/27/2017


---
# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-app-service"></a>Azure Resource Manager ベースの XPlat CLI を使用して Azure App Service を管理する
> [!div class="op_single_selector"]
> * [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

Microsoft Azure クロスプラットフォーム コマンドライン ツール バージョン 0.10.5 のリリースに伴い、新しいコマンドが追加されました。 それにより、Azure Resource Manager ベースの PowerShell コマンドを使って App Service を管理することができます。

リソース グループの管理の詳細については、「[Use the Azure CLI to manage Azure resources and resource groups (Azure CLI を使用して Azure のリソースとリソース グループを管理する)](../azure-resource-manager/xplat-cli-azure-resource-manager.md)」を参照してください。 

> [!NOTE] 
> また、[Azure CLI 2.0](https://github.com/Azure/azure-cli) もお試しいただけます。これは、Python で記述された次世代 CLI であり、Resource Manager デプロイメント モデルに使用できます。
>
>

## <a name="managing-app-service-plans"></a>App Service プランの管理
### <a name="create-an-app-service-plan"></a>App Service プランを作成する
App Service プランを作成するには、**azure appserviceplan create** コマンドを使用します。

以下、各パラメーターについて説明します。

* **--resource-group**: 新たに作成された App Service プランが属するリソース グループ。
* **--name**: App Service プランの名前。
* **--location**: App Service プランの場所。
* **--sku**: 目的の価格 SKU (選択肢: F1 (Free)、 D1 (Shared)、 B1 (Basic Small)、B2 (Basic Medium)、B3 (Basic Large)、 S1 (Standard Small)、S2 (Standard Medium)、S3 (Standard Large)、 P1 (Premium Small)、P2 (Premium Medium)、P3 (Premium Large))。
* **--instances**: App Service プランの worker の数 (既定値は 1 です)。

コマンドレットの使用例:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

#### <a name="create-a-linux-app-service-plan"></a>Linux App Service プランの作成

**--islinux true** パラメーターを追加して、同じ **azure appserviceplan create** コマンドを使用します。 「[Introduction to App Service on Linux (App Service on Linux の概要)](app-service-linux-intro.md)」に示されている制限事項とリージョンに注意してください。

### <a name="list-existing-app-service-plans"></a>既存の App Service プランの一覧表示
既存の App Service プランを一覧表示するには、**azure appserviceplan list** コマンドを使用します。

特定のリソース グループのすべての App Service プランを一覧表示するには、次のように入力します。

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

特定の App Service プランを取得するには、**azure appserviceplan show** コマンドを使用します。

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>既存の App Service プランの構成
既存の App Service プランの設定を変更するには、**azure appserviceplan config** コマンドを使用します。 SKU のほか、worker の数を変更することができます。 

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>App Service プランのスケーリング
既存の App Service プランをスケーリングするには、次のように入力します。

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>App Service プランの SKU の変更
既存の App Service プランの SKU を変更するには、次のように入力します。

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>既存の App Service プランの削除
既存の App Service プランを削除するにはまず、割り当てられているすべてのアプリを移動するか削除する必要があります。 その後、**azure webapp delete** コマンドを使用して App Service プランを削除できます。

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-apps"></a>App Service アプリの管理
### <a name="create-a-web-app"></a>Web アプリを作成する
Web アプリを作成するには、**azure webapp create** コマンドを使用します。

以下、各パラメーターについて説明します。

* **--name**: Web アプリの名前。
* **--plan**: Web アプリをホストするために使用するサービス プランの名前。
* **--resource-group**: App Service プランのホストとなるリソース グループ。
* **--location**: Web アプリの場所。

コマンドレットの使用例:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-app"></a>既存のアプリを削除する
既存のアプリを削除するには、**azure webapp delete** コマンドを使用できます。 アプリの名前とリソース グループ名を指定する必要があります。

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-apps"></a>既存のアプリを一覧表示する
既存のアプリを一覧表示するには、**azure webapp list** コマンドを使用します。

特定のリソース グループ内のすべてのアプリを一覧表示するには、次のように入力します。

    azure webapp list --resource-group ContosoAzureResourceGroup

特定のアプリを取得するには、**azure webapp show** コマンドを使用します。

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-app"></a>既存のアプリを構成する
既存のアプリの設定と構成を変更するには、**azure webapp config set** コマンドを使用します。

例 (1): アプリの php のバージョンを変更する 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

例 (2): アプリ設定を追加または変更する

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

変更可能な他の設定を調べるには、**azure webapp config -h** コマンドを使用します。

### <a name="change-the-state-of-an-existing-app"></a>既存のアプリの状態を変更する
#### <a name="restart-an-app"></a>アプリを再起動する
アプリを再起動するには、アプリの名前とリソース グループを指定する必要があります。

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-an-app"></a>アプリを停止する
アプリを停止するには、アプリの名前とリソース グループを指定する必要があります。

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-an-app"></a>アプリを起動する
アプリを起動するには、アプリの名前とリソース グループを指定する必要があります。

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-publishing-profiles-of-an-app"></a>アプリの発行プロファイルを管理する
それぞれのアプリには、コードを発行するために使用できる発行プロファイルがあります。

#### <a name="get-publishing-profile"></a>発行プロファイルの取得
アプリの発行プロファイルを取得するには、次のように入力します。

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

このコマンドは、発行プロファイルのユーザー名とパスワードをコマンド ラインにエコーします。

### <a name="manage-app-hostnames"></a>アプリのホスト名を管理する
アプリのホスト名のバインドを管理するには、**azure webapp config hostnames** コマンドを使用します  

#### <a name="list-hostname-bindings"></a>ホスト名のバインドの一覧表示
アプリの現在のホスト名のバインドを取得するには、次のように入力します。

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>ホスト名のバインドの追加
アプリにホスト名のバインドを追加するには、次のように入力します。

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>ホスト名のバインドの削除
ホスト名のバインドを削除するには、次のように入力します。

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

## <a name="next-steps"></a>次のステップ
* Azure Resource Manager の CLI のサポートについては、「[Use the Azure CLI to manage Azure resources and resource groups (Azure CLI を使用して Azure のリソースとリソース グループを管理する)](../azure-resource-manager/xplat-cli-azure-resource-manager.md)」を参照してください。
* PowerShell を使用した App Service の管理については、「[Azure Resource Manager ベースの PowerShell を使用して Azure Web アプリを管理する](app-service-web-app-azure-resource-manager-powershell.md)」を参照してください。
* Linux での Azure App Service の詳細については、「[Introduction to App Service on Linux (App Service on Linux の概要)](app-service-linux-intro.md)」を参照してください。

