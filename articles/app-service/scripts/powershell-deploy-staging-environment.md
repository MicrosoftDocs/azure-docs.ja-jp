---
title: Azure PowerShell サンプル スクリプト - アプリを作成してステージング スロットにコードをデプロイする | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - Web アプリの作成およびステージング環境へのコードのデプロイ
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 7d65a5977ff8db8d31fe7a46d4f99569a8c2270a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087613"
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Web アプリを作成してステージング環境にコードをデプロイする

このサンプル スクリプトでは、App Service 内で Web アプリを作成し "staging" という名前のデプロイ スロットを追加してから、サンプル アプリを "staging" スロットにデプロイします。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Connect-AzAccount` を実行して、Azure との接続を作成します。

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Create a web app and deploy code to a staging environment")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service プランを作成します。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Web アプリを作成します。 |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | App Service プランを変更して価格レベルを変更します。 |
| [New-AzWebAppSlot](/powershell/module/az.websites/new-azwebappslot) | Web アプリのデプロイ スロットを作成します。 |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | リソース グループのリソースを変更します。 |
| [Switch-AzWebAppSlot](/powershell/module/az.websites/switch-azwebappslot) | Web アプリのデプロイ スロットを運用環境にスワップします。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../samples-powershell.md)のページにあります。
