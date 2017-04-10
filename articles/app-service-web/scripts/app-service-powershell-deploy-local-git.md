---
title: "Azure PowerShell のサンプル スクリプト - Web アプリの作成およびローカル Git リポジトリからのコードのデプロイ | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - Web アプリの作成およびローカル Git リポジトリからのコードのデプロイ"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: ec3407f54c3affc21bde4e2e6fa76a174a46159c
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Web アプリを作成してローカル Git リポジトリからコードをデプロイする

このサンプル スクリプトでは、App Service で Web アプリを関連リソースと合わせて作成し、ローカル Git リポジトリに Web アプリのコードをデプロイします。

必要に応じて、[Azure PowerShell ガイド](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)の手順に従って Azure PowerShell をインストールし、`Login-AzureRmAccount` を実行して、Azure との接続を作成します。 アプリケーション コードがローカル Git リポジトリにコミットしている必要もあります。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Web アプリの作成およびローカル Git リポジトリからのコードのデプロイ")]

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
| [Set-AzureRmResource](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/set-azurermresource) | リソース グループのリソースを変更します。 |
| [Get-AzureRmWebAppPublishingProfile](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/get-azurermwebapppublishingprofile) | Web アプリの発行プロファイルを取得します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../app-service-powershell-samples.md)のページにあります。

