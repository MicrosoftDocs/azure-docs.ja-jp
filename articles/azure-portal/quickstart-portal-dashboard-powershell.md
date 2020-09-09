---
title: PowerShell を使用して Azure portal ダッシュボードを作成する
description: Azure PowerShell を使用して Azure portal でダッシュボードを作成する方法について説明します。
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.author: mblythe
ms.date: 07/24/2020
ms.openlocfilehash: 6b7a4f6d4ad7f5e94d19b9d531992f54ff13fec0
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87440725"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>クイック スタート:PowerShell を使用して Azure portal ダッシュボードを作成する

Azure portal のダッシュボードでは、お使いのクラウド リソースが集中的に整理して表示されます。 この記事では、Az.Portal PowerShell モジュールを使用してダッシュボードを作成するプロセスに焦点を当てます。
このダッシュボードには、仮想マシン (VM) のパフォーマンスに加え、一部の静的情報とリンクが表示されます。

## <a name="requirements"></a>必要条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

ローカルで PowerShell を使用する場合は、Az PowerShell モジュールをインストールしたうえで、[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して自分の Azure アカウントに接続する必要があります。 Az PowerShell モジュールのインストールの詳細については、「[Azure PowerShell のインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)」を参照してください。

> [!IMPORTANT]
> **Az.Portal** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、Az PowerShell モジュールとは別にインストールする必要があります。 この PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>特定の Azure サブスクリプションを選択する

複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、特定のサブスクリプションを選択します。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>変数の定義

いくつかの情報は、繰り返し使用することになります。 それらの情報を格納する変数を作成しましょう。

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して、[Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、`$location` 変数に指定されたリージョンに、`$resourceGroupName` 変数内の名前に基づいてリソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

このクイックスタートの次の部分で作成するダッシュボードには既存の VM が必要です。 次の手順に従って VM を作成します。

VM のログイン資格情報を変数に格納します。 パスワードは複雑なものにする必要があります。 これは新しいユーザー名とパスワードです。たとえば Azure にサインインするためのアカウントではありません。 詳細については、[ユーザー名の要件](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)に関するページと[パスワードの要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)に関するページを参照してください。

```azurepowershell-interactive
$Cred = Get-Credential
```

VM を作成します。

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

これで VM のデプロイが開始されます。通常、完了するまでに数分かかります。 デプロイが完了したら、次のセクションに進みます。

## <a name="download-the-dashboard-template"></a>ダッシュボード テンプレートをダウンロードする

Azure ダッシュボードはリソースであるため、JSON として表すことができます。 次のコードでは、サンプル ダッシュボードの JSON 表現がダウンロードされます。 詳細については、「[Azure ダッシュボードの構造](/azure/azure-portal/azure-portal-dashboards-structure)」を参照してください。

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$env:TEMP\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>テンプレートのカスタマイズ

次のコードを実行して、ダウンロードしたテンプレートをカスタマイズします。

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

詳細については、[Microsoft portal ダッシュボード テンプレート リファレンス](/azure/templates/microsoft.portal/dashboards)に関するページを参照してください。

## <a name="deploy-the-dashboard-template"></a>ダッシュボード テンプレートをデプロイする

Az.Portal モジュールの一部である `New-AzPortalDashboard` コマンドレットを使用して、PowerShell からテンプレートを直接デプロイできます。

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>デプロイされたリソースを確認する

ダッシュボードが正常に作成されたことを確認します。

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

Azure portal 内から VM に関するデータを表示できることを確認します。

1. Azure portal で、 **[ダッシュボード]** を選択します。

   ![Azure portal でダッシュボードに移動する](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. ダッシュボード ページで、 **[Simple VM Dashboard]\(シンプル VM ダッシュボード\)** を選択します。

   ![シンプル VM ダッシュボードに移動する](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. ダッシュボードを確認します。 一部のコンテンツは静的ですが、VM のパフォーマンスを示すグラフもあることがわかります。

   ![シンプル VM ダッシュボードを確認する](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

VM と関連付けられているダッシュボードを削除するには、それらが含まれているリソース グループを削除します。

> [!CAUTION]
> 次の例では、指定されたリソース グループとそれに含まれるすべてのリソースを削除します。
> 指定したリソース グループにこの記事の範囲外のリソースが含まれている場合、それらも削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>次のステップ

Az.Portal PowerShell モジュールに含まれるコマンドレットの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell: ポータル ダッシュボードのコマンドレット](https://docs.microsoft.com/powershell/module/Az.Portal/)
