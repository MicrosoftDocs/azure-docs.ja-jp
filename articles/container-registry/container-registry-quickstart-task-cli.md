---
title: クイック スタート - Azure Container Registry 内でのコンテナー イメージのビルドと実行
description: Azure Container Registry を使用して、クラウド内でコンテナー イメージをオンデマンドでビルドして実行するタスクを迅速に実行します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: e5e02d8194f9164a03bb27d932df45d91486c518
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310627"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>クイック スタート:Azure Container Registry タスクを使用したコンテナー イメージのビルドと実行

このクイック スタートでは、Azure Container Registry タスク コマンドを使用して、Docker コンテナー イメージの迅速なビルド、プッシュ、実行を Azure 内でネイティブに行い、「社内ループ」開発サイクルをクラウドにオフロードする方法を示します。 [ACR タスク][container-registry-tasks-overview]は、コンテナー ライフサイクル全体でコンテナー イメージを管理および変更するのに役立つ Azure Container Registry 内の機能のスイートです。 

このクイック スタートの後に、ACR タスクのより高度な機能について説明します。 ACR タスクでは、コードのコミットに基づくイメージのビルドまたは基本イメージの更新を自動化したり、他のシナリオ間で複数のコンテナーを並列にテストしたりすることができます。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][azure-account] を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell または Azure CLI のローカル インストールを使用して、このクイック スタートを完了できます。 これをローカルで使用する場合は、バージョン 2.0.58 以降をお勧めします。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

コンテナー レジストリがまだない場合は、最初に [az group create][az-group-create] コマンドを使用してリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

[az acr create][az-acr-create] コマンドを使用してコンテナー レジストリを作成します。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 次の例では、*myContainerRegistry008* を使用しています。 これを一意の値に更新します。

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry008 --sku Basic
```

この例では、Azure Container Registry について学習している開発者にとって、コストが最適なオプションである *Basic* レジストリを作成します。 利用可能なサービス レベルの詳細については、[コンテナー レジストリの SKU][container-registry-skus] に関するページを参照してください。

## <a name="build-an-image-from-a-dockerfile"></a>Dockerfile からのイメージのビルド

ここでは、Azure Container Registry を使用してイメージをビルドします。 まず、作業ディレクトリを作成してから、次のコンテンツを含む *Dockerfile* という名前の Dockerfile を作成します。 これは、Linux コンテナー イメージをビルドする単純な例ですが、独自の標準的な Dockerfile を作成して他のプラットフォーム用のイメージをビルドできます。

```bash
echo FROM hello-world > Dockerfile
```

[az acr build][az-acr-build] コマンドを実行してイメージをビルドします。 正常にビルドされると、イメージがレジストリにプッシュされます。 次の例では、`sample/hello-world:v1` イメージをプッシュします。 コマンドの最後にある `.` では、Dockerfile の位置を設定します。この場合は現在のディレクトリです。

```azurecli-interactive
az acr build --image sample/hello-world:v1 --registry myContainerRegistry008 --file Dockerfile . 
```

正常なビルドとプッシュからの出力は次のようになります。

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM hello-world
latest: Pulling from library/hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>イメージの実行

ここでは、ビルドしてレジストリにプッシュしたイメージを迅速に実行します。 コンテナー開発ワークフローでは、これはイメージをデプロイする前の検証手順になる可能性があります。

ローカルの作業ディレクトリに、次の 1 ステップのコンテンツを含む *quickrun.yaml* ファイルを作成します。 *\<acrLoginServer\>* はレジストリのログイン サーバー名に置き換えてください。 ログイン サーバー名は、 *\<registry-name\>.azurecr.io* (すべて小文字) という形式です。たとえば、*mycontainerregistry008.azurecr.io* などです。 この例では、前のセクションで `sample/hello-world:v1` イメージをビルドしてプッシュしたことを想定します。

```yml
steps:
  - cmd: <acrLoginServer>/sample/hello-world:v1
```

この例の `cmd` ステップでは既定の構成のコンテナーを実行しますが、`cmd` では追加の `docker run`パラメーターまたはその他の `docker` コマンドもサポートされます。

次のコマンドを使用してコンテナーを実行します。

```azurecli-interactive
az acr run --registry myContainerRegistry008 --file quickrun.yaml .
```

次のような出力になります。

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[az group delete][az-group-delete] コマンドを使用して、リソース グループ、コンテナー レジストリ、そこに格納されているコンテナー イメージを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、ACR タスクの機能を使用して、Docker コンテナー イメージの迅速なビルド、プッシュ、実行を Azure 内でネイティブに行いました。 ACR タスクを使用してイメージのビルドと更新を自動化する方法を学習するには、Azure Container Registry のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Registry のチュートリアル][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
