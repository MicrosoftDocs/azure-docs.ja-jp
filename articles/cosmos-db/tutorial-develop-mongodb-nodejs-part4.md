---
title: "Azure を対象とした MongoDB、Angular、Node のチュートリアル - パート 4 | Microsoft Docs"
description: "Angular と Node で MongoDB に使われる API をそのまま使用して、Azure Cosmos DB を対象とした MongoDB アプリを作成するチュートリアル シリーズのパート 4 です。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: 6fd88c58c0067deab08d502be4bd624c297b5fde
ms.contentlocale: ja-jp
ms.lasthandoff: 09/06/2017

---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-4-create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Angular と Azure Cosmos DB を使って MongoDB アプリを作成する - パート 4: Azure CLI を使って Azure Cosmos DB アカウントを作成する

複数のパートから成るこのチュートリアルでは、Node.js で Express、Angular、Azure Cosmos DB データベースを使って記述された新しい [MongoDB API](mongodb-introduction.md) アプリの作成方法を紹介します。

このチュートリアルのパート 4 では、[パート 3](tutorial-develop-mongodb-nodejs-part3.md) の内容をベースとして、次のタスクについて取り上げます。

> [!div class="checklist"]
> * Azure CLI を使用して Azure リソース グループを作成する
> * Azure CLI を使用して Azure Cosmos DB アカウントを作成する

## <a name="video-walkthrough"></a>ビデオ チュートリアル

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>前提条件

本チュートリアルのこのパートに取り組む前に、[パート 3](tutorial-develop-mongodb-nodejs-part3.md) の手順を済ませておいてください。 

このチュートリアル セクションでは、インターネット ブラウザーから Azure Cloud Shell を使用するか、ローカルにインストールされた [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) を使用できます。 Azure CLI をローカルで使う場合は、Azure CLI バージョン 2.0 以降が実行されていることを確認してください。 コマンド プロンプトから `az --version` を実行して、ご使用のバージョンを確認します。 

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> このチュートリアルでは、アプリケーションを作成する手順を段階的に説明しています。 完成したプロジェクトをダウンロードしたい場合は、GitHub の [angular-cosmosdb リポジトリ](https://github.com/Azure-Samples/angular-cosmosdb)から完全なアプリケーションを取得できます。

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する

Azure Cosmos DB アカウントは、[`az cosmosdb create`](/cli/azure/cosmosdb#create) コマンドで作成します。

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

## <a name="next-steps"></a>次のステップ

本チュートリアルのこのパートでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure CLI を使用して Azure リソース グループを作成しました。
> * Azure CLI を使用して Azure Cosmos DB アカウントを作成しました。

今度は、Mongoose を使用して Azure Cosmos DB をアプリに接続します。次のチュートリアル パートに進んでください。

> [!div class="nextstepaction"]
> [Mongoose を使って Azure Cosmos DB に接続する](tutorial-develop-mongodb-nodejs-part5.md)

