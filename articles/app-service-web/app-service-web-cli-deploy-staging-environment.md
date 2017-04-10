---
title: "Azure CLI のサンプル スクリプト - ステージング環境への Web アプリのデプロイ | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - ステージング環境への Web アプリのデプロイ"
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
ms.openlocfilehash: 6e24be1f9525b976864d63dc433e853f399a545d
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-to-a-staging-environment"></a>ステージング環境への Web アプリのデプロイ

このサンプル スクリプトでは、Azure CLI 2.0 を使用して次の操作を行います。 

* 西ヨーロッパ Azure リージョンの Azure App Service で Web アプリを作成する。
* App Service プランを STANDARD レベル (デプロイ スロットでの最低レベル) にアップグレードする。
* "staging" という名前のデプロイ スロットを設定する。
* GitHub からステージング スロットに Web アプリ コードをデプロイします。
* 検証のためのブラウザーで、デプロイしたステージング スロットを開く。
* ステージング スロットを運用環境にスワップする。

## <a name="prerequisites"></a>前提条件

* `az login` を実行して Azure にログインします。
* 自分の GitHub リポジトリに Web アプリ コードを配置します。

> [!NOTE]
> 自分のものではないパブリックの GitHub リポジトリを使用する場合、App Service ではその GitHub リポジトリからコードがデプロイされますが、継続的なデプロイに必要な SSH キーと webhook を設定することはできません。
>
>

## <a name="create-vm-sample"></a>VM サンプルの作成

[!code-azurecli[main](../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "ステージング環境への Web アプリのデプロイ")]

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
| [az appservice plan update](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#update) | App Service プランを更新して、価格レベルのスケールを実行します。 |
| [az appservice web deployment slot create](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#create) | デプロイ スロットを作成します。 |
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | Web アプリを Git または Mercurial リポジトリと関連付けます。 |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | ブラウザーで Web アプリを開きます。 |
| [az appservice web deployment slot swap](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#swap) | 指定したデプロイ スロットを運用環境にスワップします。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/en-us/cli/azure/overview)のページをご覧ください。

その他の Azure App Service Web Apps 用 CLI サンプル スクリプトは、[Azure CLI サンプル]()のページにあります。

