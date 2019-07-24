---
title: Azure CLI サンプル スクリプト - Azure Container Registry で ASP.NET Core アプリを作成する | Microsoft Docs
description: Azure CLI のサンプル スクリプト - Azure Container Registry から App Service 上の Docker コンテナー内に ASP.NET Core Web アプリを作成する
services: appservice
documentationcenter: appservice
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/13/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c05572d4962eca042a807be3eec94462d0830e1b
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653375"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-in-app-service-from-azure-container-registry"></a>Azure Container Registry から App Service 上の Docker コンテナー内に ASP.NET Core Web アプリを作成する

このサンプル スクリプトでは、リソース グループ、Linux App Service プラン、およびアプリを作成します。 次に、Azure Container Registry から Docker コンテナーを使用して ASP.NET Core アプリケーションをデプロイします。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Azure CLI バージョン 2.0.52 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh "Linux Azure Container Registry")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、App Service アプリ、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | App Service アプリを作成します。 |
| [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) | App Service アプリ用の Docker コンテナーを設定します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
