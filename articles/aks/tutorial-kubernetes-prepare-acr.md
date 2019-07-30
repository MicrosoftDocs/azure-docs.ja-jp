---
title: Kubernetes on Azure のチュートリアル - コンテナー レジストリを作成する
description: この Azure Kubernetes Service (AKS) チュートリアルでは、Azure Container Registry インスタンスを作成し、サンプルのアプリケーション コンテナー イメージをアップロードします。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 5089326af1d7f6e057667cd916f35de92bf517ef
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614239"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>チュートリアル:Azure Container Registry をデプロイして使用する

Azure Container Registry (ACR) は、コンテナー イメージ用のプライベート レジストリです。 プライベート コンテナー レジストリを使用すると、アプリケーションとカスタム コードを安全にビルドおよびデプロイすることができます。 7 つのパートのうちの 2 番目のこのチュートリアルでは、ACR インスタンスをデプロイして、それにコンテナー イメージをプッシュします。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure Container Registry (ACR) インスタンスを作成する
> * ACR のコンテナー イメージにタグを付ける
> * イメージを ACR にアップロードする
> * レジストリ内のイメージを表示する

追加のチュートリアルでは、この ACR インスタンスが AKS の Kubernetes クラスターと統合され、アプリケーションがイメージからデプロイされます。

## <a name="before-you-begin"></a>開始する前に

[前のチュートリアル][aks-tutorial-prepare-app]では、単純な Azure Voting アプリケーション用のコンテナー イメージを作成しました。 Azure Voting アプリのイメージを作成していない場合、[チュートリアル 1 - コンテナー イメージの作成][aks-tutorial-prepare-app]に関するページに戻ってください。

このチュートリアルでは、Azure CLI バージョン 2.0.53 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する

Azure Container Registry を作成するには、まず、リソース グループが必要です。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create][az-group-create] コマンドでリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。

```azurecli
az group create --name myResourceGroup --location eastus
```

[az acr create][az-acr-create] コマンドを使用して Azure Container Registry インスタンスを作成し、独自のレジストリ名を指定します。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 このチュートリアルの残りの部分では、コンテナー レジストリ名のプレースホルダーとして `<acrName>` を使用します。 独自の一意のレジストリ名を指定します。 *Basic* SKU は、ストレージとスループットのバランスが取れた、開発目的のコスト最適化されたエントリ ポイントです。

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>コンテナー レジストリにログインする

ACR インスタンスを使用するには、まずログインする必要があります。 [az acr login][az-acr-login] コマンドを使用して、前の手順でコンテナー レジストリに設定した一意の名前を指定します。

```azurecli
az acr login --name <acrName>
```

このコマンドは、完了すると *"Login Succeeded (ログインに成功しました)"* というメッセージを返します。

## <a name="tag-a-container-image"></a>コンテナー イメージにタグを付ける

現在のローカル イメージの一覧を表示するには、[docker images][docker-images] コマンドを使用します。

```
$ docker images

REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

ACR で *azure-vote-front* コンテナー イメージを使用するには、イメージにレジストリのログイン サーバー アドレスでタグを付ける必要があります。 このタグは、イメージ レジストリにコンテナー イメージをプッシュするときに、ルーティングするために使用されます。

ログイン サーバー アドレスを取得するには、[az acr list][az-acr-list] コマンドを使用し、*loginServer* に対して次のようにクエリを実行します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

ここで、ローカルの *azure-vote-front* イメージに、コンテナー レジストリの *acrloginServer* アドレスでタグを付けます。 イメージのバージョンを示すには、イメージ名の最後に *:v1* を追加します。

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

タグが適用されたことを確認するには、[docker イメージ][docker-images]を再実行します。 イメージに ACR インスタンスのアドレスとバージョン番号でタグが付けられています。

```
$ docker images

REPOSITORY                                           TAG           IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest        eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry.azurecr.io/azure-vote-front      v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest        a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask         788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>イメージをレジストリにプッシュ

ビルドしてタグ付けしたイメージと共に、*azure-vote-front* イメージを ACR インスタンスにプッシュします。 [docker push][docker-push] を使用して、次のように、イメージ名に独自の *acrLoginServer* アドレスを指定します。

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

ACR へのイメージのプッシュが完了するまでに、数分かかることがあります。

## <a name="list-images-in-registry"></a>レジストリ内のイメージの一覧表示

ご利用の ACR インスタンスにプッシュされたイメージの一覧を返すには、[az acr repository list][az-acr-repository-list] コマンドを使用します。 実際の `<acrName>` を次のように指定します。

```azurecli
az acr repository list --name <acrName> --output table
```

次の出力例では、レジストリ内で利用可能なイメージとして *azure-vote-front* を示しています。

```
Result
----------------
azure-vote-front
```

特定のイメージのタグを表示するには、次のように [az acr repository show-tags][az-acr-repository-show-tags] コマンドを使用します。

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

次の出力例は、前の手順でタグを付けた *v1* イメージを示しています。

```
Result
--------
v1
```

これで、プライベートの Azure Container Registry インスタンスに格納されているコンテナー イメージができました。 このイメージは、次のチュートリアルで、ACR から Kubernetes クラスターにデプロイされます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Container Registry を作成し、AKS クラスターで使用するためのイメージをプッシュしました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Azure Container Registry (ACR) インスタンスを作成する
> * ACR のコンテナー イメージにタグを付ける
> * イメージを ACR にアップロードする
> * レジストリ内のイメージを表示する

Azure に Kubernetes クラスターをデプロイする方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Kubernetes クラスターのデプロイ][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az-acr-login
[az-acr-list]: https://docs.microsoft.com/cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
