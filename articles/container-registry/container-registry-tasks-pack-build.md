---
title: Cloud Native Buildpack でイメージをビルドする
description: az acr pack build コマンドを使用して、アプリからコンテナー イメージをビルドし、Dockerfile を使用せずに Azure Container Registry にプッシュします。
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 264fc7314c78088ebfefb9ddb8edbe38fa16581a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92736622"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Cloud Native Buildpacks を使用して、アプリからイメージをビルドしてプッシュする

Azure CLI の `az acr pack build` コマンドは、[Buildpacks](https://buildpacks.io/) の [`pack`](https://github.com/buildpack/pack) CLI ツールを使用してアプリをビルドし、そのイメージを Azure コンテナー レジストリにプッシュします。 この機能を使用すると、Dockerfile を定義しなくても、Node.js、Java、およびその他の言語のアプリケーション ソース コードからコンテナー イメージをすばやく作成するというオプションが得られます。

Azure Cloud Shell または Azure CLI のローカル インストールを使って、この記事の例を実行できます。 それをローカルで使う場合は、バージョン 2.0.70 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="use-the-build-command"></a>ビルド コマンドを使用する

Cloud Native Buildpacks を使用してコンテナー イメージをビルドおよびプッシュするには、[az acr pack build][az-acr-pack-build] コマンドを実行します。 [az acr build][az-acr-build] コマンドでは Dockerfile ソースと関連コードからイメージをビルドしてプッシュするのに対して、`az acr pack build` ではアプリケーション ソース ツリーを直接指定します。

`az acr pack build` を実行するときは、少なくとも以下を指定します。

* コマンドを実行する Azure コンテナー レジストリ
* イメージ名と生成されるイメージのタグ
* ローカル ディレクトリ、GitHub リポジトリ、リモートの tarball など、ACR タスクで[サポートされるコンテキストの場所](container-registry-tasks-overview.md#context-locations)のいずれか。
* 実際のアプリケーションに適した Buildpack ビルダー イメージの名前。 Azure Container Registry では、ビルドを高速化するために、`cloudfoundry/cnb:0.0.34-cflinuxfs3` などのビルダー イメージがキャッシュされます。  

`az acr pack build` では、[Run 変数](container-registry-tasks-reference-yaml.md#run-variables)や、ストリーミングされ、後で取得できるように保存される[タスク実行ログ](container-registry-tasks-logs.md)など、ACR タスク コマンドの他の機能もサポートしています。

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>例: Cloud Foundry ビルダーを使用して Node.js イメージをビルドする

次の例では、`cloudfoundry/cnb:0.0.34-cflinuxfs3` ビルダーを使用して、[Azure Samples/nodejs-docs-hello world リポジトリ](https://github.com/Azure-Samples/nodejs-docs-hello-world)の Node.js アプリからコンテナー イメージをビルドします。 このビルダーは Azure Container Registry によってキャッシュされているため、`--pull` パラメーターは必要ありません。

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

この例では、`1.0` タグを使用して `node-app` イメージをビルドし、これを *myregistry* コンテナー レジストリにプッシュします。 この例では、ターゲット レジストリ名はイメージ名の前に明示的に指定します。 指定しない場合、レジストリのログイン サーバー名が自動的にイメージ名の前に付加されます。

コマンドの出力には、イメージのビルドおよびプッシュの進行状況が表示されます。 

イメージが正常にビルドされたら、Docker を使用してこれを実行できます (インストールされている場合)。 まず、次のようにレジストリにサインインします。

```azurecli
az acr login --name myregistry
```

次のように、イメージを実行します。

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

任意のブラウザーで `localhost:1337` を参照し、サンプル Web アプリを確認します。 `[Ctrl]+[C]` キーを押して、コンテナーを停止します。

## <a name="example-build-java-image-with-heroku-builder"></a>例: Heroku ビルダーを使用して Java イメージをビルドする

次の例では、`heroku/buildpacks:18` ビルダーを使用して、[buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) リポジトリの Java アプリからコンテナー イメージをビルドします。 `--pull` パラメーターでは、コマンドによって最新のビルダー イメージをプルする必要があることを指定します。 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

この例では、コマンドの実行 ID でタグ付けされた `java-app` イメージをビルドし、これを *myregistry* コンテナー レジストリにプッシュします。

コマンドの出力には、イメージのビルドおよびプッシュの進行状況が表示されます。 

イメージが正常にビルドされたら、Docker を使用してこれを実行できます (インストールされている場合)。 まず、次のようにレジストリにサインインします。

```azurecli
az acr login --name myregistry
```

*runid* をイメージ タグで置き換えて、イメージを実行します。

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

任意のブラウザーで `localhost:8080` を参照し、サンプル Web アプリを確認します。 `[Ctrl]+[C]` キーを押して、コンテナーを停止します。


## <a name="next-steps"></a>次のステップ

`az acr pack build` を使用してコンテナー イメージをビルドし、プッシュした後は、任意のイメージを任意のターゲットにデプロイできます。 Azure のデプロイ オプションには、[App Service](../app-service/tutorial-custom-container.md) や [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md) などでの実行が含まれます。

ACR タスクの機能の詳細については、「[ACR タスクでコンテナー イメージのビルドとメンテナンスを自動化する](container-registry-tasks-overview.md)」を参照してください。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
