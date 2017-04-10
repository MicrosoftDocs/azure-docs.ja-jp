---
title: "Azure CLI のサンプル スクリプト - ローカル Git リポジトリからの Web アプリのデプロイ | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - ローカル Git リポジトリからの Web アプリのデプロイ"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 343f7509e43c2305e6fa7d5e5cb9e6772e1a7e01
ms.openlocfilehash: 7c970e6d4447fed96d91143a502ec823eb60df62
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-from-a-local-git-repository"></a>ローカル Git リポジトリから Web アプリをデプロイする

このサンプル スクリプトでは、Azure CLI 2.0 を使用して次の操作を行います。 

* 西ヨーロッパ Azure リージョンの Azure App Service で Web アプリを作成する。
* ローカル Git リポジトリから Web アプリのコードをデプロイする。
* デプロイした Web アプリをブラウザーで表示する。

## <a name="prerequisites"></a>前提条件

* `az login` を実行して Azure にログインします。
* Web アプリのコードをローカル Git リポジトリにコミットします。

## <a name="create-vm-sample"></a>VM サンプルの作成

[!code-azurecli[main](../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh "ローカル Git リポジトリからの Web アプリのデプロイ")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name $webappname
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) | App Service プランを作成します。 |
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | Web アプリを作成します。 |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) | ローカル Git リポジトリのソース管理構成を作成します。 |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | ブラウザーで Web アプリを開きます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/en-us/cli/azure/overview)のページをご覧ください。

その他の Azure App Service Web Apps 用 CLI サンプル スクリプトは、[Azure CLI サンプル]()のページにあります。
