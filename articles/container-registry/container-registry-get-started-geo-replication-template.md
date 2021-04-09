---
title: クイックスタート - geo レプリケーションされたレジストリを作成する - Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して geo レプリケーション Azure コンテナー レジストリを作成する方法を説明します。
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/06/2020
ms.openlocfilehash: 97b556e0329644b973def8333ddb5e70e370b0bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91827033"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して geo レプリケーション コンテナー レジストリを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure Container Registry インスタンスを作成する方法を示します。 このテンプレートは、[geo レプリケーション](container-registry-geo-replication.md) レジストリを設定します。このレジストリでは、レジストリの内容が複数の Azure リージョン間で自動的に同期されます。 geo レプリケーションを使用すると、単一の管理エクスペリエンスを提供しつつ、リージョン デプロイからイメージへのネットワーク的に近接するアクセスができます。 これは、[Premium](container-registry-skus.md) レジストリ サービス レベルの機能です。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/)からのものです。 テンプレートは、レジストリと追加のリージョン レプリカを設定します。

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

このテンプレートでは、次のリソースが定義されています。

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)** : Azure コンテナー レジストリを作成します
* **[Microsoft.ContainerRegistry/registries/replications](/azure/templates/microsoft.containerregistry/registries/replications)** : コンテナー レジストリ レプリカを作成します

その他の Azure Container Registry テンプレートのサンプルについては、[クイックスタート テンプレート ギャラリー](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular)を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

 1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 1. 次の値を選択または入力します。

    * **サブスクリプション**: Azure サブスクリプションを選択します。
    * **リソース グループ**: **[新規作成]** を選択し、リソース グループの一意の名前を入力し、 **[OK]** を選択します。
    * **[リージョン]** : リソース グループの場所を選択します。 例:**米国中部**。
    * **[Acr Name]\(Acr 名\)** : 生成されたレジストリ名を受け入れるか、名前を入力します。 名前はグローバルに一意である必要があります。
    * **[Acr Admin User Enabled]\(ACR 管理者ユーザーの有効\)** : 既定値を受け入れます。
    * **[場所]** : レジストリのホーム レプリカ用に生成された場所を受け入れるか、**米国中部** などの場所を入力します。 
    * **[Acr Sku]\(ACR の SKU\)** : 既定値を受け入れます。
    * **[Acr Replica Location]\(Acr レプリカの場所\)** : リージョンの短い名前を使用して、レジストリ レプリカの場所を入力します。 ホーム レジストリの場所とは違う場所にする必要があります。 例: **westeurope**。

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="テンプレートのプロパティ":::

1. **[Review + Create]\(確認と作成\)** を選択し、使用条件を確認します。 同意する場合は、 **[作成]** を選択します。

1. レジストリが正常に作成されると、次の通知が表示されます。

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="ポータル通知":::

 テンプレートをデプロイするには Azure portal を使用します。 Azure portal だけでなく、Azure PowerShell、Azure CLI、REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-cli.md)」を参照してください。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal またはツール (Azure CLI など) を使用して、コンテナー レジストリのプロパティを確認します。

1. ポータルで Container Registries を検索し、作成したコンテナー レジストリを選択します。

1. **[概要]** ページで、レジストリの **ログイン サーバー** を書き留めます。 Docker を使用してイメージにタグを付けてレジストリにプッシュするときに、この URI を使用します。 情報については、[Docker CLI を使用した最初のイメージのプッシュ](container-registry-get-started-docker-cli.md)に関するページを参照してください。

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="レジストリの概要":::

1. **[レプリケーション]** ページで、ホーム レプリカの場所と、テンプレートを通じて追加されたレプリカの場所を確認します。 必要に応じて、このページでさらにレプリカを追加します。

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="レジストリ レプリケーション":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、リソース グループ、レジストリ、およびレジストリ レプリカを削除します。 これを行うには、Azure portal に移動し、レジストリを含むリソース グループを選択して、 **[リソース グループの削除]** を選択します。

リソース グループの削除

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレートを使用して Azure Container Registry を作成し、別の場所にレジストリ レプリカを構成しました。 Azure Container Registry のチュートリアルに進んで、ACR についての理解を深めましょう。

> [!div class="nextstepaction"]
> [Azure Container Registry のチュートリアル](container-registry-tutorial-prepare-registry.md)

テンプレートの作成手順について説明したチュートリアルについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [チュートリアル:初めての ARM テンプレートを作成してデプロイする](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
