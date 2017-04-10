---
title: "Azure PowerShell のサンプル スクリプト - Web アプリへのカスタム ドメインの割り当て | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - Web アプリへのカスタム ドメインの割り当て"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 356f5af9-f62e-411c-8b24-deba05214103
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 2b402bc08b1e682e1c8380ef92fff65414138cad
ms.lasthandoff: 03/21/2017

---

# <a name="assign-a-custom-domain-to-a-web-app"></a>カスタム ドメインを Web アプリに割り当てる

このサンプル スクリプトでは、App Service で Web アプリを関連リソースと合わせて作成し、`www.<yourdomain>` を作成したアプリにマップします。 

必要に応じて、[Azure PowerShell ガイド](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)の手順に従って Azure PowerShell をインストールし、`Login-AzureRmAccount` を実行して、Azure との接続を作成します。 また、ドメイン レジストラーの DNS 構成ページにアクセスできることも必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/app-service/map-custom-domain/map-custom-domain.ps1?highlight=1 "カスタム ドメインを Web アプリに割り当てる")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | App Service プランを作成します。 |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Web アプリを作成します。 |
| [Set-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermappserviceplan) | App Service プランを変更して価格レベルを変更します。 |
| [Set-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermwebapp) | Web アプリの構成を変更します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../app-service-powershell-samples.md)のページにあります。

