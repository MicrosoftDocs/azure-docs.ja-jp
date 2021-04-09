---
title: Azure CLI サンプル スクリプト - App Service を Application Gateway に統合する | Microsoft Docs
description: Azure CLI サンプル スクリプト - App Service を Application Gateway に統合する
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3820e7bf00f99a846dd2be0edeaf4248e0dfd8ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97006085"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>CLI を使用して App Service を Application Gateway に統合する

このサンプル スクリプトでは、Azure App Service Web アプリ、Azure Virtual Network、Application Gateway を作成します。 その後、Web アプリのトラフィックを Application Gateway サブネットを送信元とするトラフィックに制限します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0.74 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、App Service アプリ、Cosmos DB、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az network vnet create`](/cli/azure/network/vnet#az-network-vnet-create) | 仮想ネットワークを作成します。 |
| [`az network public-ip create`](/cli/azure/network/public-ip#az-network-public-ip-create) | パブリック IP アドレスを作成します。 |
| [`az network public-ip show`](/cli/azure/network/public-ip#az-network-public-ip-show) | パブリック IP アドレスの詳細を表示します。 |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | App Service Web アプリを作成します。 |
| [`az webapp show`](/cli/azure/webapp#az-webapp-show) | App Service Web アプリの詳細を表示します。 |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction#az-webapp-config-access-restriction-add) | App Service Web アプリにアクセス制限を追加します。 |
| [`az network application-gateway create`](/cli/azure/network/application-gateway#az-network-application-gateway-create) | アプリケーション ゲートウェイを作成します。 |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings#az-network-application-gateway-http-settings-update) | Application Gateway の HTTP 設定を更新します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
