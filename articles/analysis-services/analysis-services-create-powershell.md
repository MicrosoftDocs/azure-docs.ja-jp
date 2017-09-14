---
title: "PowerShell を使って Azure Analysis Services サーバーを作成する | Microsoft Docs"
description: "PowerShell を使って Azure Analysis Services サーバーを作成する方法について説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/01/2017
ms.author: owend
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 43700de27ca643bd699159b86fe80c2e45e4acc7
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---

# <a name="create-an-azure-analysis-services-server-by-using-powershell"></a>PowerShell を使って Azure Analysis Services サーバーを作成する

このクイック スタートでは、コマンド ラインから PowerShell を使って、Azure サブスクリプション内の [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)に Azure Analysis Services サーバーを作成する方法について説明します。

この作業には、Azure PowerShell モジュール バージョン 4.0 以降が必要です。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードするには、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 

> [!NOTE]
> サーバーを作成すると、新しい課金対象サービスを使用することになる場合があります。 詳しくは、「[Azure Analysis Services の価格](https://azure.microsoft.com/pricing/details/analysis-services/)」をご覧ください。

## <a name="prerequisites"></a>前提条件
このクイック スタートを完了するには、次のものが必要です。

* **Azure サブスクリプション**: [Azure 無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)でアカウントを作成します。
* **Azure Active Directory**: サブスクリプションは、Azure Active Directory テナントに関連付けられている必要があります。また、そのディレクトリのアカウントが必要となります。 詳細については、「[認証とユーザーのアクセス許可](analysis-services-manage-users.md)」を参照してください。

## <a name="import-azurermanalysisservices-module"></a>AzureRm.AnalysisServices モジュールのインポート
サブスクリプションにサーバーを作成するには、[AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) コンポーネント モジュールを使います。 PowerShell セッションに AzureRm.AnalysisServices モジュールを読み込みます。

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) コマンドを使用して Azure サブスクリプションにサインインします。 画面の指示に従います。

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>リソース グループの作成
 
[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)とは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 サーバーを作成するときは、自分のサブスクリプション内のリソース グループを指定する必要があります。 リソース グループがまだない場合は、[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドを使って新しく作成してください。 次の例は、米国西部リージョンに `myResourceGroup` という名前のリソース グループを作成します。

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
```

## <a name="create-a-server"></a>サーバーの作成

[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) コマンドを使って新しいサーバーを作成します。 次の例では、米国西部リージョンの myResourceGroup に myServer という D1 レベルのサーバーを作成し、philipc@adventureworks.com をサーバー管理者として指定します。

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myServer" -Location West US -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サブスクリプションからサーバーを削除するには、[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) コマンドを使います。 続けてこのコレクション内の他のクイック スタートやチュートリアルに取り組む場合は、サーバーを削除しないでください。 次の例では、前の手順で作成したサーバーを削除します。


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myServer" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ
[PowerShell で Azure Analysis Services を管理する](analysis-services-powershell.md)   
[SSDT からのモデルのデプロイ](analysis-services-deploy.md)   
[Azure Portal でモデルを作成する](analysis-services-create-model-portal.md)
