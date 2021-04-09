---
title: クイックスタート - PowerShell を使用して Azure Analysis Services を作成する - Azure Analysis Services | Microsoft Docs
description: このクイックスタートでは、PowerShell を使って Azure Analysis Services サーバーを作成する方法について説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 737649538aaf82352e27aec6220b13ba355a7a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89229346"
---
# <a name="quickstart-create-a-server---powershell"></a>クイック スタート: サーバーを作成する - PowerShell

このクイック スタートでは、コマンド ラインから PowerShell を使って、Azure サブスクリプション内に Azure Analysis Services サーバーを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure サブスクリプション**: [Azure 無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)でアカウントを作成します。
- **Azure Active Directory**: サブスクリプションは、Azure Active Directory テナントに関連付けられている必要があります。また、そのディレクトリのアカウントが必要となります。 詳細については、「[認証とユーザーのアクセス許可](analysis-services-manage-users.md)」を参照してください。
- **Azure PowerShell**。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 インストールまたはアップグレードするには、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。

## <a name="import-azanalysisservices-module"></a>Az.AnalysisServices モジュールをインポートする

サブスクリプション内にサーバーを作成するには、[Az.AnalysisServices](/powershell/module/az.analysisservices) モジュールを使用します。 PowerShell セッションに Az.AnalysisServices モジュールを読み込みます。

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドを使用して Azure サブスクリプションにサインインします。 画面の指示に従います。

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[Azure リソース グループ](../azure-resource-manager/management/overview.md)とは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 サーバーを作成するときは、自分のサブスクリプション内のリソース グループを指定する必要があります。 リソース グループがまだない場合は、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドを使用して新しく作成できます。 次の例は、米国西部リージョンに `myResourceGroup` という名前のリソース グループを作成します。

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>サーバーの作成

[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) コマンドを使って新しいサーバーを作成します。 次の例では、米国西部リージョンの myResourceGroup に myServer という D1 (無料) レベルのサーバーを作成し、philipc@adventureworks.com をサーバー管理者として指定します。

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

サブスクリプションからサーバーを削除するには、[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) コマンドを使います。 続けてこのコレクション内の他のクイック スタートやチュートリアルに取り組む場合は、サーバーを削除しないでください。 次の例では、前の手順で作成したサーバーを削除します。


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、PowerShell を使用して Azure サブスクリプションにサーバーを作成する方法について学習しました。 サーバーを作成したら、(必要に応じて) サーバー ファイアウォールを構成することによってサーバーをセキュリティで保護できます。 また、ポータルからサーバーにデータ モデルの基本的なサンプルを追加することもできます。 サンプル モデルがあると、モデル データベース ロールの構成とクライアント接続のテストについて理解するのに役立ちます。 詳しくは、サンプル モデルの追加のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: サーバーのファイアウォールの構成 - ポータル](analysis-services-qs-firewall.md)      