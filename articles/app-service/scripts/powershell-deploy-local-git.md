---
title: 'PowerShell: ローカル Git リポジトリからデプロイする'
description: Azure PowerShell を使用して App Service のデプロイと管理を自動化する方法について説明します。 このサンプルでは、ローカル Git リポジトリからコードをデプロイする方法を紹介しています。
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 03d2f2d3928393f8a251640a03d82b5963140141
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89070294"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Web アプリを作成してローカル Git リポジトリからコードをデプロイする

このサンプル スクリプトでは、App Service で Web アプリを関連リソースと合わせて作成し、ローカル Git リポジトリから Web アプリのコードをデプロイします。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/)の手順に従い、Azure PowerShell を最新版に更新し、`Connect-AzAccount` を実行して、Azure との接続を作成します。 アプリケーション コードがローカル Git リポジトリにコミットしている必要もあります。

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 必要なリソース グループと App Service グループと共に Web アプリを作成します。 現在のディレクトリに Git リポジトリが含まれている場合は、`azure` リモートも追加してください。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../samples-powershell.md)のページにあります。
