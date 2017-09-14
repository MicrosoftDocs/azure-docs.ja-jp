---
title: "Azure PowerShell のサンプル スクリプト - Web アプリへのカスタム SSL 証明書のバインド | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - Web アプリへのカスタム SSL 証明書のバインド"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: f82f4e7e647ac9b07f25041d65cc505e4e486a0b
ms.contentlocale: ja-jp
ms.lasthandoff: 04/27/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>カスタム SSL 証明書を Web アプリにバインドする

このサンプル スクリプトは、関連するリソースを使用して App Service に Web アプリを作成し、そこにカスタム ドメイン名の SSL 証明書をバインドします。 

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールします。 また、次のことを確認します。

- Azure との接続が、`az login` コマンドを使用して作成されている。
- ドメイン レジストラーの DNS 構成ページにアクセスできる。
- アップロードしてバインドする SSL 証明書の有効な .PFX ファイルとパスワードを持っている。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "カスタム SSL 証明書を Web アプリにバインドする")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | App Service プランを作成します。 |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Web アプリを作成します。 |
| [Set-AzureRmAppServicePlan](/powershell/module/azurerm.websites/set-azurermappserviceplan) | App Service プランを変更して価格レベルを変更します。 |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Web アプリの構成を変更します。 |
| [New-AzureRmWebAppSSLBinding](/powershell/module/azurerm.websites/new-azurermwebappsslbinding) | Web アプリの SSL 証明書のバインディングを作成します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../app-service-powershell-samples.md)のページにあります。

