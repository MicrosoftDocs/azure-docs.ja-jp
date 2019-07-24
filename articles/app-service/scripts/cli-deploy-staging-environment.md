---
title: Azure CLI サンプル スクリプト - アプリを作成してステージング スロットにコードをデプロイする | Microsoft Docs
description: Azure CLI のサンプル スクリプト - App Service アプリの作成およびステージング スロットへのコードのデプロイ
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 12/11/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 069dbcb5784414e63d093b7e38c399f358f830fb
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653783"
---
# <a name="create-an-app-service-app-and-deploy-code-to-a-staging-environment-using-azure-cli"></a>Azure CLI を使用した App Service アプリの作成とステージング環境へのコードのデプロイ

このサンプル スクリプトでは、App Service 上でアプリを作成し、"staging" という名前のデプロイ スロットを追加した後、サンプル アプリを "staging" スロットにデプロイします。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Create an app and deploy code to a staging environment")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | App Service アプリを作成します。 |
| [`az webapp deployment slot create`](/cli/azure/webapp/deployment/slot?view=azure-cli-latest#az-webapp-deployment-slot-create) | デプロイ スロットを作成します。 |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | App Service アプリを Git または Mercurial のリポジトリに関連付けます。 |
| [`az webapp deployment slot swap`](/cli/azure/webapp/deployment/slot?view=azure-cli-latest#az-webapp-deployment-slot-swap) | 指定したデプロイ スロットを運用環境にスワップします。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
