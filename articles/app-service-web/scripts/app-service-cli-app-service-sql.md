---
title: "Azure CLI のサンプル スクリプト - SQL データベースへの Web アプリの接続 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - SQL データベースへの Web アプリの接続"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f5317827d58e0103e43d0d5e1e68c1ada0c4d7bc
ms.lasthandoff: 03/21/2017

---

# <a name="connect-a-web-app-to-a-sql-database"></a>Web アプリを SQL データベースに接続する

このシナリオでは、Azure SQL データベースと Azure Web アプリの作成方法について説明します。 作成後、アプリの設定で SQL データベースを Web アプリにリンクします。

必要に応じて、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)の手順に従って Azure CLI をインストールし、`az login` を実行して、Azure との接続を作成します。

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows クライアントで実行する方法については、[Windows での Azure CLI の実行](../../virtual-machines/virtual-machines-windows-cli-options.md)に関する記事を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "SQL Database")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Web アプリ、SQL データベース、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service プランを作成します。 App Service プランとは、Azure Web アプリ用のサーバー ファームのようなものです。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | App Service プラン内に Azure Web アプリを作成します。 |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | SQL データベース サーバーを作成します。  |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | SQL データベース サーバーに新しいデータベースを作成します。 |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | Azure Web アプリのアプリケーション設定を作成または更新します。 アプリケーション設定は、アプリの環境変数として公開されます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../app-service-cli-samples.md)のページにあります。
