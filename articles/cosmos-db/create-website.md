---
title: テンプレートを使用した Web アプリのデプロイ - Azure Cosmos DB
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos アカウント、Azure App Service Web Apps、サンプル Web アプリケーションをデプロイする方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 55d58a6c4724bd01325db029ed75d77ccc96d0f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333581"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して、Azure Cosmos DB と Azure App Service および GitHub の Web アプリをデプロイする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

このチュートリアルでは、最初の実行で Azure Cosmos DB に接続する Web アプリケーションを "ノー タッチで" デプロイする方法について説明します。Azure Cosmos DB の接続情報を切り取って `appsettings.json` に貼り付けたり、Azure portal で Azure App Services のアプリケーション設定に貼り付ける必要はありません。 これらの操作はすべて、Azure Resource Manager テンプレートを使用して、1 回の操作で行われます。 この例では、[Web アプリのチュートリアル](sql-api-dotnet-application.md)の [Azure Cosmos DB ToDo サンプル](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)をデプロイします。

Resource Manager テンプレートは非常に柔軟であり、Azure の任意のサービスにわたる複雑なデプロイを構成できます。 これには、GitHub からのアプリケーションのデプロイや、Azure portal での Azure App Service のアプリケーション設定への接続情報の挿入などの高度なタスクが含まれます。 このチュートリアルでは、1 つの Resource Manager テンプレートを使用して次の操作を行う方法について説明します。

* Azure Cosmos アカウントをデプロイする。
* Azure App Service ホスティング プランをデプロイする。
* Azure App Service をデプロイする。
* Azure Cosmos アカウントのエンドポイントとキーを、Azure portal の App Service アプリケーション設定に挿入する。
* GitHub リポジトリから App Service に Web アプリケーションをデプロイする。

結果のデプロイには、Azure Cosmos DB のエンドポイント URL または認証キーを Azure portal から切り取って貼り付ける必要なく Azure Cosmos DB に接続できる、完全に機能する Web アプリケーションが含まれます。

## <a name="prerequisites"></a>前提条件

> [!TIP]
> このチュートリアルは、Azure Resource Manager テンプレートや JSON の使用経験があるユーザーを対象にしているわけではありませんが、参照するテンプレートやデプロイメント オプションに変更を加える場合は、これらの領域に関する知識が必要になります。

## <a name="step-1-deploy-the-template"></a>手順 1:テンプレートのデプロイ

最初に、以下の **[Azure に配置する]** ボタンを選択して Azure portal を開き、カスタム デプロイを作成します。 Azure リソース管理テンプレートは、[Azure クイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)から表示することもできます

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

Azure portal で、デプロイ先のサブスクリプションを選択し、新しいリソース グループを選択または作成します。 次に、以下の値を入力します。

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="テンプレートのデプロイメント UI のスクリーンショット":::

* **[リージョン]** - Resource Manager に必要です。 リソースが配置される場所のパラメーターで使用されているものと同じリージョンを入力します。
* **[アプリケーション名]** - この名前は、このデプロイのすべてのリソースで使用されます。 既存の Azure Cosmos DB および App Service アカウントとの競合を避けるために、必ず一意の名前を選択してください。
* **[場所]** - リソースがデプロイされるリージョン。
* **[App Service Plan Tier]\(App Service プラン レベル\)** - App Service プランの価格レベルです。
* **[App Service Plan Instances]\(App Service プラン インスタンス\)** - App Service プランのワーカーの数。
* **[リポジトリの URL]** - GitHub 上の Web アプリケーションに対するリポジトリ。
* **[ブランチ]** - GitHub リポジトリのブランチ。
* **[データベース名]** - Azure Cosmos データベース名。
* **[コンテナー名]** - Azure Cosmos コンテナー名。

値を入力したら、 **[作成]** ボタンを選択してデプロイを開始します。 この手順は完了するまでに 5 分から 10 分かかります。

> [!TIP]
> このテンプレートでは、テンプレートに入力された Azure App Service アプリ名と Azure Cosmos アカウント名が有効かつ使用可能であることが検証されません。 デプロイメントの送信前に、指定する予定の名前の可用性を確認しておくことを強くお勧めします。


## <a name="step-2-explore-the-resources"></a>手順 2:リソースを調べる

### <a name="view-the-deployed-resources"></a>デプロイされたリソースを表示する

テンプレートによってリソースがデプロイされると、リソース グループ内でそれぞれを確認できるようになります。

:::image type="content" source="./media/create-website/resource-group.png" alt-text="リソース グループ":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Cosmos DB エンドポイントとキーを表示する

次に、ポータルで Azure Cosmos アカウントを開きます。 次のスクリーンショットは、Azure Cosmos アカウントのエンドポイントとキーを示しています。

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Cosmos キー":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>アプリケーション設定で Azure Cosmos DB キーを表示する

次に、リソース グループ内の Azure App Service に移動します。 [構成] タブをクリックして、App Service の [アプリケーション設定] を表示します。 [アプリケーション設定] には、Cosmos DB に接続するために必要な Cosmos DB アカウントと主キーの値、およびテンプレート デプロイから渡されたデータベース名とコンテナー名が含まれています。

:::image type="content" source="./media/create-website/application-settings.png" alt-text="アプリケーション設定":::

### <a name="view-web-app-in-deployment-center"></a>[デプロイ センター] で Web アプリを表示する

次に、App Service の [デプロイ センター] に移動します。 ここには、テンプレートに渡された GitHub リポジトリを指す [リポジトリ] が表示されます。 また、下の [状態] は [Success(Active)]\(成功 (アクティブ)\) を示しています。これはアプリケーションが正常にデプロイされ、開始されたことを意味します。

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="デプロイ センター":::

### <a name="run-the-web-application"></a>Web アプリケーションの実行

[デプロイ センター] の上部にある **[参照]** をクリックして、Web アプリケーションを開き ます。 Web アプリケーションのホーム画面が表示されます。 **[新規作成]** をクリックし、フィールドにデータを入力して、[保存] をクリックします。 結果の画面には Cosmos DB に保存されたデータが表示されます。

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="ホーム画面":::

## <a name="step-3-how-does-it-work"></a>手順 3:動作のしくみ

これを機能させるには、3 つの要素が必要です。

### <a name="reading-app-settings-at-runtime"></a>実行時のアプリ設定の読み取り

まず、アプリケーションでは、ASP.NET MVC Web アプリケーションの `Startup` クラスで、Cosmos DB エンドポイントとキーを要求する必要があります。 [Cosmos DB To Do サンプル](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)はローカルで実行でき、お客様が appsettings.json に接続情報を入力できます。 しかし、デプロイする場合、このファイルはアプリと共にデプロイされます。 赤で囲まれた以下の行で appsettings.json の設定にアクセスできない場合は、Azure App Service のアプリケーション設定から試行されます。

:::image type="content" source="./media/create-website/startup.png" alt-text="databaseName、containerName、account、key など、いくつかの文字列変数が赤でマークされているメソッドを示すスクリーンショット。":::

### <a name="using-special-azure-resource-management-functions"></a>特殊な Azure リソース管理関数の使用

デプロイされたアプリケーションでこれらの値を使用できるようにするために、Azure Resource Manager テンプレートでは、[reference](../azure-resource-manager/templates/template-functions-resource.md#reference) や [listKeys](../azure-resource-manager/templates/template-functions-resource.md#listkeys) などの特殊な Azure リソース管理関数を使用して、Cosmos DB アカウントからこれらの値を要求できます。これらの関数を使うと、Cosmos DB アカウントから値を取得し、'{section:key}' 形式で、上のアプリケーションで使用されているものと一致するキー名を使用して、アプリケーション設定の値に挿入できます。 たとえば、「 `CosmosDb:Account` 」のように入力します。

:::image type="content" source="./media/create-website/template-keys.png" alt-text="テンプレート キー":::

### <a name="deploying-web-apps-from-github"></a>GitHub からの Web アプリのデプロイ

最後に、GitHub から App Service に Web アプリケーションをデプロイする必要があります。 これは、以下の JSON を使用して行います。 2 点注意しなければならないのは、このリソースの種類と名前です。 `"type": "sourcecontrols"` と `"name": "web"` の両方のプロパティ値はハードコーディングされているため、変更しないでください。

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="GitHub からのデプロイ":::

## <a name="next-steps"></a>次のステップ

お疲れさまでした。 Azure Cosmos DB、Azure App Service、および Cosmos DB への接続に必要な接続情報を自動的に取得するサンプル Web アプリケーションを、すべて 1 回の操作で、機密情報の切り取りと貼り付けを行う必要なくデプロイできました。 このテンプレートを出発点として使用し、変更を加えることで、独自の Web アプリケーションを同じ方法でデプロイできます。

* このサンプル用の Azure Resource Manager テンプレートについては、[Azure クイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)にアクセスしてください
* サンプル アプリのソース コードについては、[GitHub 上の Cosmos DB To Do アプリ](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)にアクセスしてください。
