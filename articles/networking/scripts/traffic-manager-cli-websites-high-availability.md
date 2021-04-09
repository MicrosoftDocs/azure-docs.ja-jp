---
title: Azure CLI スクリプト サンプル - 高可用性アプリケーションのためのトラフィックのルーティング | Microsoft Docs
description: Azure CLI スクリプト サンプル - 高可用性アプリケーションのためのトラフィックのルーティング
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: allensu
ms.openlocfilehash: 90fcba21ad6f44b5a420cb15b95ef278f9b5b9b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211075"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-cli"></a>高可用性アプリケーションのためのトラフィックのルーティング - Azure CLI

このスクリプトは、1 つのリソース グループ、2 つの App Service プラン、2 つの Web アプリ、1 つの Traffic Manager プロファイル、および 2 つの Traffic Manager エンドポイントを作成します。 Traffic Manager は、プライマリ リージョンのアプリケーションにトラフィックを誘導し、プライマリ リージョンのアプリケーションが利用できない場合には、セカンダリ リージョンにトラフィックを誘導します。 スクリプトを実行する前に、MyWebApp、MyWebAppL1、および MyWebAppL2 の値を、Azure 全体で一意の値に変更する必要があります。 スクリプトを実行した後は、URL mywebapp.trafficmanager.net でプライマリ リージョンのアプリケーションにアクセスできます。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、App Service アプリ、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Web アプリ、Traffic Manager プロファイル、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](/cli/azure/appservice/plan) | App Service プランを作成します。 App Service プランとは、Azure Web アプリ用のサーバー ファームのようなものです。 |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | App Service プラン内に Azure Web アプリを作成します。 |
| [az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile) | Azure Traffic Manager プロファイルを作成します。 |
| [az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint) | Azure Traffic Manager プロファイルにエンドポイントを追加します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure のネットワークに関するドキュメント](../cli-samples.md)のページにあります。