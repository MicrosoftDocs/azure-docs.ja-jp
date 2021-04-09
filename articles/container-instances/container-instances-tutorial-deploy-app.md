---
title: チュートリアル - コンテナー インスタンスにコンテナー アプリをデプロイする
description: Azure Container Instances チュートリアル 3/3 - Azure Container Instances にコンテナー アプリケーションをデプロイする
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 2ea3d285f00d38df84587d9a7c15242fff38453b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87500574"
---
# <a name="tutorial-deploy-a-container-application-to-azure-container-instances"></a>チュートリアル: Azure Container Instances にコンテナー アプリケーションをデプロイする

これは、3 部構成のシリーズの最後のチュートリアルです。 シリーズではこれまで、[コンテナー イメージを作成](container-instances-tutorial-prepare-app.md)して、[Azure Container Registry にプッシュ](container-instances-tutorial-prepare-acr.md)しました。 この記事で Azure Container Instances にコンテナーをデプロイして、このシリーズは完了です。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Azure Container Registry から Azure Container Instances へのコンテナーのデプロイ
> * ブラウザーでの実行中のアプリケーションの表示
> * コンテナーのログの表示

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Azure CLI を使用して、コンテナーをデプロイする

このセクションでは、Azure CLI を使用してイメージをデプロイします。このイメージは、[最初のチュートリアル](container-instances-tutorial-prepare-app.md)で構築し、[2 つ目のチュートリアル](container-instances-tutorial-prepare-acr.md)で Azure Container Registry にプッシュしたものです。 続行する前に、これらのチュートリアルを完了していることを確認してください。

### <a name="get-registry-credentials"></a>レジストリ資格情報を取得する

[2 つ目のチュートリアル](container-instances-tutorial-prepare-acr.md)で作成したようなプライベート Azure コンテナー レジストリでホストされるイメージをデプロイする場合は、レジストリにアクセスするための資格情報を指定する必要があります。 

レジストリへの "*プル*" アクセス許可を持った Azure Active Directory のサービス プリンシパルを作成して構成することが、多くのシナリオにおいてベスト プラクティスとなります。 必要なアクセス許可を持つサービス プリンシパルを作成するためのサンプル スクリプトについては、「[Azure Container Instances から Azure Container Registry の認証を受ける](../container-registry/container-registry-auth-aci.md)」を参照してください。 "*サービス プリンシパル ID*" と "*サービス プリンシパル パスワード*" を書き留めておいてください。 これらの資格情報は、コンテナーをデプロイするときにレジストリにアクセスするために使用します。

また、コンテナー レジストリ ログイン サーバーの完全な名前が必要となります (`<acrName>` は実際のレジストリ名に置き換えてください)。

```azurecli
az acr show --name <acrName> --query loginServer
```

### <a name="deploy-container"></a>コンテナーをデプロイする

次に、[az container create][az-container-create] コマンドを使用して、コンテナーをデプロイします。 `<acrLoginServer>` は、前のコマンドから取得した値に置き換えてください。 `<service-principal-ID>` と `<service-principal-password>` は、レジストリへのアクセス用に作成したサービス プリンシパルの ID とパスワードに置き換えます。 `<aciDnsLabel>` は、適切な DNS 名に置き換えてください。

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <service-principal-ID> --registry-password <service-principal-password> --dns-name-label <aciDnsLabel> --ports 80
```

数秒以内に、Azure から最初の応答を受信します。 `--dns-name-label` の値は、コンテナー インスタンスを作成する Azure リージョン内で一意である必要があります。 コマンドを実行したときに **DNS 名ラベル** のエラー メッセージが表示された場合は、前述のコマンドの値を変更してください。

### <a name="verify-deployment-progress"></a>デプロイの進行状況を確認する

デプロイの状態を表示するには、[az container show][az-container-show] を使用します。

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

状態が *Pending* から *Running* に変わるまで、[az container show][az-container-show] コマンドを繰り返します。これには 1 分もかかりません。 コンテナーが *Running* になったら、次の手順に進みます。

## <a name="view-the-application-and-container-logs"></a>アプリケーションとコンテナー ログを表示する

デプロイが成功すると、[az container show][az-container-show] コマンドでコンテナーの完全修飾ドメイン名 (FQDN) が表示されます。

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

次に例を示します。
```output
"aci-demo.eastus.azurecontainer.io"
```

実行中のアプリケーションを表示するには、お使いのブラウザーで表示された DNS 名に移動します。

![ブラウザーでの Hello World アプリ][aci-app-browser]

コンテナーのログ出力を表示することもできます。

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

出力例:

```output
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアル シリーズで作成したリソースのいずれかが不要になった場合は、[az group delete][az-group-delete] コマンドを実行して、リソース グループとそこに含まれているすべてのリソースを削除できます。 このコマンドは、作成したコンテナー レジストリだけでなく、実行中のコンテナーと関連するすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Container Instances インスタンスにコンテナーをデプロイするプロセスを完了しました。 次の手順を完了しました。

> [!div class="checklist"]
> * Azure CLI を使用した Azure Container Registry からのコンテナーのデプロイ
> * ブラウザーでのアプリケーションの表示
> * コンテナー ログの表示

基本的な知識を習得した後は、コンテナー グループの仕組みなど、Azure Container Instances の詳細について学習してください。

> [!div class="nextstepaction"]
> [Azure Container Instances のコンテナー グループ](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
