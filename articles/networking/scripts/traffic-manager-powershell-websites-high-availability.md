---
title: "Azure PowerShell スクリプト サンプル - 高可用性アプリケーションのためのトラフィックのルーティング | Microsoft Docs"
description: "Azure PowerShell スクリプト サンプル - 高可用性アプリケーションのためのトラフィックのルーティング"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: georgewallace
tags: azure-infrastructure
ms.assetid: 
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 05/16/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 2f0ac4fd1779661aab04bafb217e64af5d619a2f
ms.contentlocale: ja-jp
ms.lasthandoff: 05/27/2017

---

# <a name="route-traffic-for-high-availability-of-applications"></a>高可用性アプリケーションのためのトラフィックのルーティング

このスクリプトは、1 つのリソース グループ、2 つの App Service プラン、2 つの Web アプリ、1 つの Traffic Manager プロファイル、および 2 つの Traffic Manager エンドポイントを作成します。 Traffic Manager は、プライマリ リージョンのアプリケーションにトラフィックを誘導し、プライマリ リージョンのアプリケーションが利用できない場合には、セカンダリ リージョンにトラフィックを誘導します。 スクリプトを実行する前に、MyWebApp、MyWebAppL1、および MyWebAppL2 の値を、Azure 全体で一意の値に変更する必要があります。 スクリプトを実行した後は、URL mywebapp.trafficmanager.net でプライマリ リージョンのアプリケーションにアクセスできます。

必要に応じて、[Azure PowerShell ガイド](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)の手順に従って Azure PowerShell をインストールし、`Login-AzureRmAccount` を実行して、Azure との接続を作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "高可用性のためのトラフィックのルーティング")]


次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup1
Remove-AzureRmResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Web アプリ、Traffic Manager プロファイル、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | App Service プランを作成します。 App Service プランとは、Azure Web アプリ用のサーバー ファームのようなものです。 |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | App Service プラン内に Azure Web アプリを作成します。 |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | App Service プラン内に Azure Web アプリを作成します。 |
| [New-AzureRmTrafficManagerProfile](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile) | Azure Traffic Manager プロファイルを作成します。 |
| [New-AzureRmTrafficManagerEndpoint](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerendpoint) | Azure Traffic Manager プロファイルにエンドポイントを追加します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/overview)を参照してください。

その他のネットワーク PowerShell のサンプル スクリプトは、[Azure のネットワーク概要のドキュメント](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)に関するページで確認できます。
