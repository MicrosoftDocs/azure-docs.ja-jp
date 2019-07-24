---
title: チュートリアル - Azure Container Instances 用にコンテナー レジストリを準備する
description: Azure Container Instances チュートリアル 2/3 - Azure コンテナー レジストリの準備とイメージのプッシュ
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: c1a4313f9a8174b9ea6e6cff694b9a0a9cf395d1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538155"
---
# <a name="tutorial-deploy-an-azure-container-registry-and-push-a-container-image"></a>チュートリアル:Azure コンテナー レジストリをデプロイし、コンテナー イメージをプッシュする

これは 3 つのパートで構成されるチュートリアルの 2 番目のタスクです。 チュートリアルの[パート 1](container-instances-tutorial-prepare-app.md) では、Node.js Web アプリケーションの Docker コンテナー イメージを作成しました。 このチュートリアルでは、このイメージを Azure Container Registry にプッシュします。 コンテナー イメージを作成していない場合は、[チュートリアル 1 - コンテナー イメージの作成](container-instances-tutorial-prepare-app.md)に関するページに戻ってください。

Azure Container Registry は、Azure におけるプライベート Docker レジストリです。 このチュートリアルでは、ご利用のサブスクリプションに Azure Container Registry インスタンスを作成した後、事前に作成しておいたコンテナー イメージをそこにプッシュします。 シリーズの第 2 部であるこの記事では、次の内容を学習します。

> [!div class="checklist"]
> * Azure Container Registry インスタンスの作成
> * Azure Container Registry のコンテナー イメージのタグ付け
> * レジストリへのイメージのアップロード

このシリーズの最後となる次の記事では、コンテナーをプライベート レジストリから Azure Container Instances にデプロイします。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="create-azure-container-registry"></a>Azure Container Registry の作成

コンテナー レジストリを作成する前に、そのデプロイ先となる*リソース グループ*が必要です。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

[az group create][az-group-create] コマンドでリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。

```azurecli
az group create --name myResourceGroup --location eastus
```

リソース グループを作成したら、[az acr create][az-acr-create] コマンドで Azure Container Registry を作成します。 コンテナー レジストリ名は、Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 `<acrName>` を、レジストリの一意の名前に置き換えます。

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

ここに示したのは、*mycontainerregistry082* という名前の新しい Azure Container Registry の出力例からの抜粋です。

```console
$ az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
...
{
  "adminUserEnabled": true,
  "creationDate": "2018-03-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

このチュートリアルの残りの部分では、この手順で選択したコンテナー レジストリ名のプレースホルダーとして `<acrName>` を使用します。

## <a name="log-in-to-container-registry"></a>コンテナー レジストへのにログイン

イメージをプッシュする前に、Azure Container Registry インスタンスにログインする必要があります。 [az acr login][az-acr-login] コマンドを使用して、操作を完了します。 コンテナー レジストリの作成時に選んだ一意名を指定する必要があります。

```azurecli
az acr login --name <acrName>
```

このコマンドは、完了すると `Login Succeeded` を返します。

```console
$ az acr login --name mycontainerregistry082
Login Succeeded
```

## <a name="tag-container-image"></a>コンテナー イメージのタグ付け

Azure Container Registry などのプライベート レジストリにコンテナー イメージをプッシュするには、まず、レジストリのログイン サーバーのフル ネームでイメージにタグ付けする必要があります。

まず、Azure Container Registry のログイン サーバーのフル ネームを取得します。 次の [az acr show][az-acr-show] コマンドを実行します。`<acrName>` の部分は、先ほど作成したレジストリの名前に置き換えてください。

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

たとえば、ご利用のレジストリに *mycontainerregistry082* という名前を付けた場合は、次のようになります。

```console
$ az acr show --name mycontainerregistry082 --query loginServer --output table
Result
------------------------
mycontainerregistry082.azurecr.io
```

次に、[docker images][docker-images] コマンドでローカル イメージを一覧表示します。

```bash
docker images
```

[前のチュートリアル](container-instances-tutorial-prepare-app.md)で作成した *aci-tutorial-app* イメージが、お使いのマシン上に存在する他のあらゆるイメージと共に表示されます。

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

コンテナー レジストリの loginServer で *aci-tutorial-app* イメージにタグを付けます。 また、イメージのバージョン番号を示す `:v1` タグをイメージ名の末尾に追加します。 `<acrLoginServer>` は、先ほど実行した [az acr show][az-acr-show] コマンドの結果に置き換えてください。

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

`docker images` を実行してタグ付け操作を確認します。

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry へのイメージのプッシュ

プライベート レジストリの完全なログイン サーバー名で *aci-tutorial-app* イメージにタグ付けしたら、[docker push][docker-push] コマンドを使ってレジストリにプッシュすることができます。 `<acrLoginServer>` は、先行する手順で取得したログイン サーバーのフル ネームに置き換えてください。

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

インターネット接続に応じて、`push` 操作には数秒から数分かかります。出力は次のようになります。

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Azure Container Registry のイメージの一覧表示

先ほどプッシュしたイメージが確かに Azure Container Registry に存在することを確認し、[az acr repository list][az-acr-repository-list] コマンドを使って、レジストリ内のイメージを一覧表示します。 `<acrName>` を、コンテナー レジストリの名前に置き換えます。

```azurecli
az acr repository list --name <acrName> --output table
```

例: 

```console
$ az acr repository list --name mycontainerregistry082 --output table
Result
----------------
aci-tutorial-app
```

特定のイメージの*タグ*を表示するには、[az acr repository show-tags][az-acr-repository-show-tags] コマンドを使用します。

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

次のような出力が表示されます。

```console
$ az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Container Registry を Azure Container Instances で使用できるように準備し、コンテナー イメージをレジストリにプッシュしました。 次の手順を完了しました。

> [!div class="checklist"]
> * Azure Container Registry インスタンスのデプロイ
> * Azure Container Registry のコンテナー イメージのタグ付け
> * Azure Container Registry へのイメージのアップロード

次のチュートリアルでは、Azure Container Instances を使用してコンテナーを Azure にデプロイする方法について学習します。

> [!div class="nextstepaction"]
> [Azure Container Instances へのコンテナーのデプロイ](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
