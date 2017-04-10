---
title: "Azure Resource Manager ベースの PowerShell コマンドを使用して Azure Web アプリを管理する | Microsoft Docs"
description: "Azure Resource Manager ベースの新しい PowerShell コマンドを使用して Azure Web アプリを管理する方法について説明します。"
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 4231fbba-61e5-4f92-b958-531c066fb87f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d8f76a2857766c481ec6cdec52274b5bd370ccfc


---
# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>Azure Resource Manager ベースの PowerShell を使用して Azure Web アプリを管理する
> [!div class="op_single_selector"]
> * [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
> 
> 

Microsoft Azure PowerShell Version 1.0.0 では新しいコマンドが追加されました。今後は Azure Resource Manager ベースの PowerShell コマンドを使って Web Apps を管理することができます。

リソース グループの管理については、「 [Azure Resource Manager での Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。 

PowerShell コマンドレットのパラメーターとオプションの全一覧については、[Azure Resource Manager ベースの PowerShell コマンドレットを使って Web アプリを管理するための詳しいコマンドレット リファレンス](https://msdn.microsoft.com/library/mt619237.aspx)を参照してください。

## <a name="managing-app-service-plans"></a>App Service プランの管理
### <a name="create-an-app-service-plan"></a>App Service プランを作成する
App Service プランを作成するには、**New-AzureRmAppServicePlan** コマンドレットを使用します。

以下、各パラメーターについて説明します。

* **Name**: App Service プランの名前。
* **Location**: サービス プランの場所。
* **ResourceGroupName**: 新たに作成された App Service プランが属するリソース グループ。
* **Tier**: 必要な価格レベル (既定値は Free です。それ以外に Shared、Basic、Standard、Premium を選択できます)。
* **WorkerSize**: worker のサイズ (Tier パラメーターに Basic、Standard、Premium が指定された場合の既定値は S です)。 それ以外に M と L を選択できます。
* **NumberofWorkers**: App Service プランの worker の数 (既定値は 1 です)。 

コマンドレットの使用例:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>App Service Environment で App Service プラン を作成する
App Service Environment (ASE) で App Service プランを作成するには、同じ **New-AzureRmAppServicePlan** コマンドに別途パラメーターを追加し、ASE の名前とリソース グループの名前を指定します。

コマンドレットの使用例:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

App Service Environment の詳細については、「 [App Service Environment の概要](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>既存の App Service プランの一覧表示
既存の App Service プランを一覧表示するには、 **Get-AzureRmAppServicePlan** コマンドレットを使用します。

自分のサブスクリプションのすべての App Service プランを一覧表示するには、次のように入力します。 

    Get-AzureRmAppServicePlan

特定のリソース グループのすべての App Service プランを一覧表示するには、次のように入力します。

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

特定の App Service プランを取得するには、次のように入力します。

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>既存の App Service プランの構成
既存の App Service プランの設定を変更するには、 **Set-AzureRmAppServicePlan** コマンドレットを使用します。 価格レベル、worker サイズ、worker 数を変更することができます。 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>App Service プランのスケーリング
既存の App Service プランをスケーリングするには、次のように入力します。

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>App Service プランの worker サイズの変更
既存の App Service プランの worker のサイズを変更するには、次のように入力します。

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>App Service プランの価格レベルの変更
既存の App Service プランの価格レベルを変更するには、次のように入力します。

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>既存の App Service プランの削除
既存の App Service プランを削除するにはまず、割り当てられているすべての Web アプリを移動するか削除する必要があります。 その後、**Remove-AzureRmAppServicePlan** コマンドレットを使用して App Service プランを削除できます。

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>App Service Web Apps の管理
### <a name="create-a-web-app"></a>Web アプリの作成
新しい Web アプリを作成するには、**New-AzureRmWebApp** コマンドレットを使用します。

以下、各パラメーターについて説明します。

* **Name**: Web アプリの名前。
* **AppServicePlan**: Web アプリのホストとなるサービス プランの名前。
* **ResourceGroupName**: App Service プランのホストとなるリソース グループ。
* **Location**: Web アプリの場所。

コマンドレットの使用例:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>App Service 環境での Web アプリの作成
App Service 環境 (ASE) で Web アプリを作成するには、 同じ **New-AzureRmWebApp** コマンドに別途パラメーターを追加し、ASE の名前とその ASE が属するリソース グループの名前を指定します。

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

App Service Environment の詳細については、「 [App Service Environment の概要](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>既存の Web アプリの削除
既存の Web アプリを削除するには、**Remove-AzureRmWebApp** コマンドレットを使用します。Web アプリの名前とリソース グループの名前を指定する必要があります。

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>既存の Web アプリの一覧表示
既存の Web アプリを一覧表示するには、 **Get-AzureRmWebApp** コマンドレットを使用します。

自分のサブスクリプションのすべての Web アプリを一覧表示するには、次のように入力します。

    Get-AzureRmWebApp

特定のリソース グループのすべての Web アプリを一覧表示するには、次のように入力します。

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

特定の Web アプリを取得するには、次のように入力します。

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>既存の Web アプリの構成
既存の Web アプリの設定と構成を変更するには、 **Set-AzureRmWebApp** コマンドレットを使用します。 全パラメーターの一覧については、 [コマンドレットのリファレンス リンク](https://msdn.microsoft.com/library/mt652487.aspx)

例 (1): 接続文字列を変更する

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

例 (2): アプリ設定を追加または変更する

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


例 (3): 64 ビット モードで動作するように Web アプリを設定する

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>既存の Web アプリの状態を変更する
#### <a name="restart-a-web-app"></a>Web アプリの再起動
Web アプリを再起動するには、Web アプリの名前とリソース グループを指定する必要があります。

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Web アプリの停止
Web アプリを停止するには、Web アプリの名前とリソース グループを指定する必要があります。

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Web アプリの起動
Web アプリを起動するには、Web アプリの名前とリソース グループを指定する必要があります。

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Web アプリの発行プロファイルの管理
Web アプリにはそれぞれ、アプリを発行するときに使用する発行プロファイルがあります。発行プロファイルには、さまざまな操作を実行することができます。

#### <a name="get-publishing-profile"></a>発行プロファイルの取得
Web アプリの発行プロファイルを取得するには、次のように入力します。

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

このコマンドでは、発行プロファイルをコマンド ラインにエコーすると共に、発行プロファイルをテキスト ファイルに出力します。

#### <a name="reset-publishing-profile"></a>発行プロファイルのリセット
Web アプリの Web デプロイと FTP の発行パスワードを両方ともリセットするには、次のように入力します。

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>Web アプリの証明書の管理
Web アプリの証明書を管理する方法については、 [PowerShell を使用した SSL 証明書のバインド](app-service-web-app-powershell-ssl-binding.md)

### <a name="next-steps"></a>次のステップ
* Azure Resource Manager の PowerShell 対応については、「 [Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)
* App Service Environment については、「 [App Service 環境の概要](app-service-app-service-environment-intro.md)
* PowerShell を使用した App Service SSL 証明書の管理については、 [PowerShell を使用した SSL 証明書のバインド](app-service-web-app-powershell-ssl-binding.md)
* Azure Web Apps に使用する Azure Resource Manager ベースの PowerShell コマンドレットの全一覧については、 [Azure Resource Manager の PowerShell コマンドレットを使って Web アプリを管理するための Azure コマンドレット リファレンス](https://msdn.microsoft.com/library/mt619237.aspx)
* * CLI を使用した App Service の管理については、「[Azure Resource Manager ベースの XPlat CLI を使用して Azure Web アプリを管理する](app-service-web-app-azure-resource-manager-xplat-cli.md)」を参照してください。




<!--HONumber=Nov16_HO3-->


