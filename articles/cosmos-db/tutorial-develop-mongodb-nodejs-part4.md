---
title: Azure Cosmos DB の MongoDB 用 API を使用して Angular アプリを作成する - Cosmos アカウントを作成する
titleSuffix: Azure Cosmos DB
description: Angular と Node で MongoDB に使われる API をそのまま使用して、Azure Cosmos DB を対象とした MongoDB アプリを作成するチュートリアル シリーズのパート 4 です。
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: 8320204f75e583dae0449f83e7c38f6638371c2a
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035123"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Azure Cosmos DB の MongoDB 用 API を使用して Angular アプリを作成する - Cosmos アカウントを作成する

複数のパートから成るこのチュートリアルでは、Express と Angular を使用して Node.js で新しいアプリを作成した後、[Cosmos DB の MongoDB 用 API を使用して構成された Cosmos アカウント](mongodb-introduction.md)にそれを接続する方法を紹介します。

このチュートリアルのパート 4 では、[パート 3](tutorial-develop-mongodb-nodejs-part3.md) の内容をベースとして、次のタスクについて取り上げます。

> [!div class="checklist"]
> * Azure CLI を使用して Azure リソース グループを作成する
> * Azure CLI を使用して Cosmos アカウントを作成する

## <a name="video-walkthrough"></a>ビデオ チュートリアル

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>前提条件

本チュートリアルのこのパートに取り組む前に、[パート 3](tutorial-develop-mongodb-nodejs-part3.md) の手順を済ませておいてください。 

このチュートリアル セクションでは、インターネット ブラウザーから Azure Cloud Shell を使用するか、ローカルにインストールされた [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) を使用できます。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> このチュートリアルでは、アプリケーションを作成する手順を段階的に説明しています。 完成したプロジェクトをダウンロードしたい場合は、GitHub の [angular-cosmosdb リポジトリ](https://github.com/Azure-Samples/angular-cosmosdb)から完全なアプリケーションを取得できます。

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する

Azure Cosmos DB アカウントは、[`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create) コマンドで作成します。

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* `<cosmosdb-name>` には、自分が使用している一意の Azure Cosmos DB アカウント名を使ってください。アカウント名は、Azure に存在するすべての Azure Cosmos DB アカウント名で一意であることが必要です。
* `--kind MongoDB` という設定によって、Azure Cosmos DB が MongoDB クライアント接続を受け入れるようになります。

コマンドが完了するまでに 1 〜 2 分かかる場合があります。 完了すると、ターミナル ウィンドウに新しいデータベースに関する情報が表示されます。 

Azure Cosmos DB アカウントの作成後、次の手順を実行します。
1. 新しいブラウザー ウィンドウを開いて [https://portal.azure.com](https://portal.azure.com) に移動します。
1. 左側のバーにある Azure Cosmos DB のロゴ ( ![Azure Portal の Azure Cosmos DB アイコン](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) ) をクリックして、自分が所有しているすべての Azure Cosmos DB を表示します。
1. 先ほど作成した Azure Cosmos DB アカウントをクリックして **[概要]** タブを選択し、下へスクロールして、データベースが置かれている場所のマップを表示します。 

    ![Azure Portal の新しい Azure Cosmos DB アカウント](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. 左側のナビゲーションで下へスクロールし、**[データをグローバルにレプリケートする]** タブをクリックして表示されるマップで、レプリケート先として利用できるさまざまな地域を確認できます。 たとえば [オーストラリア南東部] または [オーストラリア東部] をクリックすれば、オーストラリアにデータをレプリケートすることができます。 グローバル レプリケーションの詳細については、「[Azure Cosmos DB を使用してデータをグローバルに分散させる方法](distribute-data-globally.md)」を参照してください。 差し当たり、以前のインスタンスを単に保持しておき、実際に必要が生じたときに、前述の方法でレプリケート先を選ぶことにしましょう。

    ![Azure Portal の新しい Azure Cosmos DB アカウント](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>次の手順

本チュートリアルのこのパートでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure CLI を使用して Azure リソース グループを作成しました。
> * Azure CLI を使用して Azure Cosmos DB アカウントを作成しました。

今度は、Mongoose を使用して Azure Cosmos DB をアプリに接続します。次のチュートリアル パートに進んでください。

> [!div class="nextstepaction"]
> [Mongoose を使って Azure Cosmos DB に接続する](tutorial-develop-mongodb-nodejs-part5.md)
