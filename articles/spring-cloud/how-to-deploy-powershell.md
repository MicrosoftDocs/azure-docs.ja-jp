---
title: Azure PowerShell を使用して Azure Spring Cloud をテプロイする方法
description: Azure PowerShell を使用して Azure Spring Cloud をテプロイする方法
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877452"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Azure PowerShell を使用して Azure Spring Cloud をテプロイする

この記事では、[Az.SpringCloud](/powershell/module/Az.SpringCloud) PowerShell モジュールを使用して Azure Spring Cloud のインスタンスを作成する方法について説明します。

## <a name="requirements"></a>必要条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **Az.SpringCloud** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを別途インストールする必要があります。 この PowerShell モジュールは、一般提供されると、将来の Az PowerShell モジュール リリースに含まれ、既定で Azure Cloud Shell 内から使用できるようになります。

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* 複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、特定のサブスクリプションを選択します。

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して、[Azure リソース グループ](../azure-resource-manager/management/overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、指定された名前のリソース グループを、指定された場所に作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Azure Spring Cloud の新しいインスタンスをプロビジョニングする

Azure Spring Cloud の新しいインスタンスを作成するには、[New-AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) コマンドレットを使用します。 次の例では、以前に作成したリソース グループ内で指定した名前を使用して Azure Spring Cloud サービスを作成します。

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>新しい Azure Spring Cloud アプリを作成する

新しいアプリを作成するには、[New-AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) コマンドレットを使用します。 次の例では、`gateway` という名前の Azure Spring Cloud アプリを作成します。

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>新しい Azure Spring Cloud デプロイを作成する

新しいデプロイを作成するには、[New-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) コマンドレットを使用します。 次の例では、`gateway` アプリ用に `default` という名前の Azure Spring Cloud デプロイを作成します。

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Azure Spring Cloud サービスを取得する

Azure Spring Cloud サービスとそのプロパティを取得するには、[Get-AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) コマンドレットを使用します。 次の例では、指定した Azure Spring Cloud サービスに関する情報を取得します。

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Azure Spring Cloud アプリを取得する

Azure Spring Cloud アプリとそのプロパティを取得するには、[Get-AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) コマンドレットを使用します。 次の例では、`gateway` Spring Cloud アプリに関する情報を取得します。

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Azure Spring Cloud デプロイを取得する

Azure Spring Cloud デプロイとそのプロパティを取得するには、[Get-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) コマンドレットを使用します。 次の例では、`default` Spring Cloud デプロイに関する情報を取得します。

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースが不要であれば、次の例を実行して削除できます。

### <a name="delete-an-azure-spring-cloud-deployment"></a>Azure Spring Cloud デプロイを削除する

Azure Spring Cloud デプロイを削除するには、[Remove-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) コマンドレットを使用します。 次の例では、指定したサービスおよびアプリ用の `default` という名前の Azure Spring Cloud アプリのデプロイを削除します。

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Azure Spring Cloud アプリを削除する

Spring Cloud アプリを削除するには、[Remove-AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) コマンドレットを使用します。 次の例では、指定したサービスおよびリソース グループ内の `gateway` アプリを削除します。

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Azure Spring Cloud サービスを削除する

Azure Spring Cloud サービスを削除するには、[Remove-AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) コマンドレットを使用します。 次の例では、指定した Azure Spring Cloud サービスを削除します。

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>リソース グループを削除します

> [!CAUTION]
> 次の例では、指定されたリソース グループとそれに含まれるすべてのリソースを削除します。
> 指定したリソース グループにこの記事の範囲外のリソースが含まれている場合、それらも削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>次のステップ

[Azure Spring Cloud の開発者向けリソース](spring-cloud-resources.md)
