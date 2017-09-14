---
title: "Azure CLI スクリプト サンプル - 高可用性アプリケーションのためのトラフィックのルーティング | Microsoft Docs"
description: "Azure CLI スクリプト サンプル - 高可用性アプリケーションのためのトラフィックのルーティング"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: tysonn
tags: azure-infrastructure
ms.assetid: 
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 07/07/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 0593d063a4935d02aae124d83b62b11e37aa3c33
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017

---

# <a name="route-traffic-for-high-availability-of-applications"></a>高可用性アプリケーションのためのトラフィックのルーティング

このスクリプトは、1 つのリソース グループ、2 つの App Service プラン、2 つの Web アプリ、1 つの Traffic Manager プロファイル、および 2 つの Traffic Manager エンドポイントを作成します。 Traffic Manager は、プライマリ リージョンのアプリケーションにトラフィックを誘導し、プライマリ リージョンのアプリケーションが利用できない場合には、セカンダリ リージョンにトラフィックを誘導します。 スクリプトを実行する前に、MyWebApp、MyWebAppL1、および MyWebAppL2 の値を、Azure 全体で一意の値に変更する必要があります。 スクリプトを実行した後は、URL mywebapp.trafficmanager.net でプライマリ リージョンのアプリケーションにアクセスできます。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "高可用性のためのトラフィックのルーティング")]


## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、App Service アプリ、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Web アプリ、Traffic Manager プロファイル、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service プランを作成します。 App Service プランとは、Azure Web アプリ用のサーバー ファームのようなものです。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | App Service プラン内に Azure Web アプリを作成します。 |
| [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#create) | Azure Traffic Manager プロファイルを作成します。 |
| [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#create) | Azure Traffic Manager プロファイルにエンドポイントを追加します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure のネットワークに関するドキュメント](../cli-samples.md)のページにあります。

