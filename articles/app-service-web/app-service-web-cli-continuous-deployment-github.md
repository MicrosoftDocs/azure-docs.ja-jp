---
title: "Azure CLI のサンプル スクリプト - GitHub からの Web アプリの継続的なデプロイ | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - GitHub からの Web アプリの継続的なデプロイ"
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
ms.sourcegitcommit: d19b07667bc2d34f860be46c299560fe9a788d53
ms.openlocfilehash: 649521d6f3740b099ab41c8ef70f7e4430aa5de9
ms.lasthandoff: 02/27/2017

---

# <a name="continuously-deploy-web-app-from-github"></a>GitHub から Web アプリを継続的にデプロイする

このサンプル スクリプトでは、Azure CLI 2.0 を使用して次の操作を行います。 

* 西ヨーロッパ Azure リージョンの Azure App Service で Web アプリを作成する。 
* GitHub から Web アプリ コードをデプロイする。
* デプロイした Web アプリをブラウザーで表示する。

## <a name="prerequisites"></a>前提条件

* `az login` を実行して Azure にログインします。
* 自分の GitHub リポジトリに Web アプリ コードを配置します。

> [!NOTE]
> 自分のものではないパブリックの GitHub リポジトリを使用する場合、App Service ではその GitHub リポジトリからコードがデプロイされますが、継続的なデプロイに必要な SSH キーと webhook を設定することはできません。
>
>

## <a name="create-vm-sample"></a>VM サンプルの作成

[!code-azurecli[main](../../cli_scripts/app-service/deploy-github/deploy-github.sh "GitHub からの Web アプリの継続的なデプロイ")]

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
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | Web アプリを Git または Mercurial リポジトリと関連付けます。 |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | ブラウザーで Web アプリを開きます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/en-us/cli/azure/overview)のページをご覧ください。

その他の Azure App Service Web Apps 用 CLI サンプル スクリプトは、[Azure CLI サンプル]()のページにあります。

