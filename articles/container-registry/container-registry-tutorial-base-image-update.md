---
title: チュートリアル - 基本イメージの更新時にイメージ ビルドをトリガーする
description: このチュートリアルでは、同じレジストリの基本イメージが更新されたときにクラウドでコンテナー イメージ ビルドを自動的にトリガーするように Azure Container Registry タスクを構成する方法を説明します。
ms.topic: tutorial
ms.date: 01/22/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 4797dd1f1fe19b98ab94c4743ad4af3c43ce0627
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "78402859"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>チュートリアル:Azure コンテナー レジストリで基本イメージの更新時にコンテナー イメージ ビルドを自動化する 

ACR タスクでは、いずれかの基本イメージ内で OS またはアプリケーション フレームワークにパッチを適用したときなど、コンテナーの[基本イメージが更新](container-registry-tasks-base-images.md)されたときのコンテナー イメージ ビルドの自動化をサポートしています。 

このチュートリアルでは、コンテナーの基本イメージが同じレジストリにプッシュされたときにクラウドでビルドをトリガーする ACR タスクの作成方法について説明します。 基本イメージが[別の Azure コンテナー レジストリ](container-registry-tutorial-private-base-image-update.md)にプッシュされたときにイメージのビルドをトリガーする ACR タスクを作成するチュートリアルもお試しください。 

このチュートリアルの内容:

> [!div class="checklist"]
> * 基本イメージをビルドする
> * 基本イメージを追跡するアプリケーション イメージを同じレジストリに作成する 
> * 基本イメージを更新してアプリケーション イメージ タスクをトリガーする
> * トリガーされたタスクを表示する
> * 更新されたアプリケーション イメージを確認する

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

ローカルで Azure CLI を使用する場合は、Azure CLI のバージョン **2.0.46** 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="complete-the-previous-tutorials"></a>前のチュートリアルを完了しておく

このチュートリアルでは、シリーズの最初の 2 つのチュートリアルの以下の手順を完了していることを前提としています。

* Azure Container Registry の作成
* サンプル リポジトリのフォーク
* サンプル リポジトリのクローン
* GitHub 個人用アクセス トークンの作成

まだ完了していない場合は、続行する前に次のチュートリアルを完了してください。

[Azure Container Registry Tasks を使用してクラウド内のコンテナー イメージをビルドする](container-registry-tutorial-quick-task.md)

[Azure Container Registry Tasks を使用してコンテナー イメージ ビルドを自動化する](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>環境の構成

次のシェル環境変数に、環境に適した値を設定します。 この手順は必須ではありませんが、このチュートリアルの複数行の Azure CLI コマンドの実行が少し簡単になります。 これらの環境変数を設定しない場合は、それぞれの値を、サンプル コマンド内の現れたところで手動で置き換える必要があります。

[![埋め込みの起動](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell を起動する")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```


### <a name="base-image-update-scenario"></a>基本イメージ更新シナリオ

このチュートリアルでは、基本イメージとアプリケーション イメージが 1 つのレジストリで管理されている状況での基本イメージの更新について説明します。 

[コード サンプル][code-sample]には 2 つの Dockerfile が含まれます。これは、アプリケーション イメージと、アプリケーション イメージが基本として指定するイメージです。 以下のセクションでは、基本イメージの新しいバージョンが同じコンテナー レジストリにプッシュされたときに、アプリケーション イメージのビルドを自動的にトリガーする ACR タスクを作成します。

* [Dockerfile-app][dockerfile-app]:基になる Node.js バージョンを表示する静的な Web ページをレンダリングする小さな Node.js Web アプリケーションです。 バージョン文字列がシミュレートされ、基本イメージで定義されている環境変数 `NODE_VERSION` の内容が表示されます。

* [Dockerfile-base][dockerfile-base]:`Dockerfile-app` によってそのベースとして指定されるイメージ。 これ自体が [Node][base-node] イメージに基づき、`NODE_VERSION` 環境変数を含みます。

以降のセクションでは、タスクを作成し、基本イメージ Docker ファイルで値 `NODE_VERSION` を更新してから、ACR Tasks を使用して基本イメージをビルドします。 ACR タスクによって新しい基本イメージがレジストリにプッシュされると、アプリケーション イメージのビルドが自動的にトリガーされます。 必要に応じて、アプリケーション コンテナー イメージをローカルで実行して、ビルドされたイメージの別のバージョンの文字列を表示します。

このチュートリアルでは、ACR タスクにより、Dockerfile で指定されたアプリケーション コンテナー イメージをビルドしてプッシュします。 ACR タスクでは、[複数ステップ タスク](container-registry-tasks-multi-step.md)を実行することもできます。その場合、YAML ファイルを使用して、複数のコンテナーをビルド、プッシュ、および (必要に応じて) テストする手順を定義します。

## <a name="build-the-base-image"></a>基本イメージをビルドする

ACR タスクの "*クイック タスク*" で、[az acr build][az-acr-build] を使用して基本イメージをビルドすることから開始します。 シリーズの[最初のチュートリアル](container-registry-tutorial-quick-task.md)で説明したように、このプロセスではイメージがビルドされるだけでなく、ビルドが成功した場合にイメージがコンテナー レジストリにプッシュされます。

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>タスクを作成します。

次に、[az acr task create][az-acr-task-create] を使用してタスクを作成します。

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

このタスクは、[前のチュートリアル](container-registry-tutorial-build-task.md)で作成したタスクに似ています。 これは、`--context` によって指定されたリポジトリにコミットがプッシュされたらイメージのビルドをトリガーするよう ACR Tasks に指示します。 前のチュートリアルでイメージのビルドに使用された Dockerfile にはパブリックの基本イメージ (`FROM node:9-alpine`) が指定されていますが、このタスクの Dockerfile である [Dockerfile-app][dockerfile-app] には同じレジストリ内の基本イメージを指定します。

```dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

この構成により、このチュートリアルの後半で、基本イメージ内のフレームワークの修正プログラムを簡単にシミュレートできます。

## <a name="build-the-application-container"></a>アプリケーション コンテナーをビルドする

[az acr task run][az-acr-task-run] を使用してタスクを手動でトリガーし、アプリケーション イメージをビルドします。 基本イメージに対するアプリケーション イメージの依存関係がタスクで追跡されるようにするために、この手順が必要となります。

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

次のオプションの手順を実行する場合は、タスクが完了したら、**実行 ID** ("da6" など) をメモしておきます。

### <a name="optional-run-application-container-locally"></a>省略可能:アプリケーション コンテナーをローカルで実行する

ローカルで作業し (Cloud Shell ではなく)、Docker がインストールされている場合は、コンテナーを実行して、その基本イメージをリビルドする前に Web ブラウザーでアプリケーションのレンダリングを確認します。 Cloud Shell を使用する場合は、このセクションをスキップしてください (Cloud Shell では `az acr login` または `docker run` はサポートされていません)。

最初に、[az acr login][az-acr-login] を使用してコンテナー レジストリに対して認証します。

```azurecli
az acr login --name $ACR_NAME
```

次に、`docker run` を使ってコンテナーをローカルで実行します。 **\<run-id\>** を、前の手順の出力にあった実行 ID ("da6" など) に置き換えます。 この例では、コンテナーに `myapp` という名前を付け、停止したときにコンテナーを削除する `--rm` パラメーターが含まれています。

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

ブラウザーで `http://localhost:8080` に移動します。次のような Node.js バージョン番号が Web ページに表示されます。 後の手順で、バージョン文字列に "a" を追加して、バージョンを増やします。

![ブラウザーに表示されたサンプル アプリケーションのスクリーンショット][base-update-01]

コンテナーを停止して削除するには、次のコマンドを実行します。

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>ビルドを一覧表示する

次に、[az acr task list-runs][az-acr-task-list-runs] コマンドを使用して、ご使用のレジストリで ACR タスクが完了したタスク実行の一覧を表示します。

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

前のチュートリアルを完了している場合 (レジストリを削除していない場合)、次のような出力が表示されます。 タスク実行番号と最新の実行 ID をメモします。これによって、次のセクションで基本イメージを更新した後に出力を比較できます。

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>基本イメージを更新する

ここで、基本イメージでフレームワークのパッチをシミュレートします。 **Dockerfile-base** を編集して、`NODE_VERSION` で定義されたバージョン番号の後に "a" を追加します。

```dockerfile
ENV NODE_VERSION 9.11.2a
```

クイック タスクを実行して、変更された基本イメージをビルドします。 出力の**実行 ID** をメモします。

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

ビルドが完了し、ACR タスクによって新しい基本イメージがレジストリにプッシュされると、アプリケーション イメージのビルドがトリガーされます。 前に作成したタスクがアプリケーション イメージ ビルドをトリガーするのに少し時間がかかる場合があります。これは、新しくビルドされ、プッシュされた基本イメージを検出する必要があるためです。

## <a name="list-updated-build"></a>更新更新されたビルドを一覧表示する

これで基本イメージを更新したので、もう一度タスクの実行を一覧表示して、以前の一覧と比較します。 最初に出力に違いがない場合は、周期的にコマンドを実行して、一覧に表示される新しいタスクの実行を確認します。

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

出力は次のようになります。 最後に実行されたビルドのトリガーは "イメージの更新" であり、これは、タスクが基本イメージのクイック タスクによって開始されたことを示しています。

```output
Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

新しくビルドされたコンテナーを実行する次のオプションの手順を実行し、更新後のバージョン番号を表示する場合は、イメージの更新によってトリガーされたビルドの**実行 ID** の値をメモしておきます (前の出力では "da8")。

### <a name="optional-run-newly-built-image"></a>省略可能:新しくビルドされたイメージを実行する

ローカルで作業しており (Cloud Shell ではなく)、Docker がインストールされている場合は、そのビルドが完了したら新しいアプリケーション イメージを実行します。 `<run-id>` を、前の手順で取得した実行 ID に置き換えます。 Cloud Shell を使用する場合は、このセクションをスキップしてください (Cloud Shell では `docker run` はサポートされていません)。

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

ブラウザーで http://localhost:8081 に移動します。Web ページに更新された Node.js バージョン番号 ("a" が追加された) が表示されます。

![ブラウザーに表示されたサンプル アプリケーションのスクリーンショット][base-update-02]

**基本**イメージを新しいバージョン番号に更新しましたが、最後にビルドされた**アプリケーション** イメージによって新しいバージョンが表示されることに注意してください。 ACR Tasks によって変更が基本イメージに反映され、アプリケーション イメージが自動的にリビルドされました。

コンテナーを停止して削除するには、次のコマンドを実行します。

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、イメージの基本イメージが更新されたときにコンテナー イメージ ビルドを自動的にトリガーするタスクを使用する方法を説明しました。 次のチュートリアルに進んで、定義されたスケジュールでタスクをトリガーする方法を学習してください。

> [!div class="nextstepaction"]
> [スケジュールに基づいてタスクを実行する](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
