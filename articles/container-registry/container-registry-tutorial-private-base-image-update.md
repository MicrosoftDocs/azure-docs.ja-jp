---
title: チュートリアル - プライベートの基本イメージを更新してイメージ ビルドをトリガーする
description: このチュートリアルでは、別のプライベート Azure コンテナー レジストリの基本イメージが更新されたときにクラウドでコンテナー イメージ ビルドを自動的にトリガーするよう、Azure Container Registry タスクを構成する方法を説明します。
ms.topic: tutorial
ms.date: 11/20/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: ce06e792fd5a4582e77d18313052ea91a38121a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920209"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>チュートリアル:別のプライベート コンテナー レジストリで基本イメージが更新されたときのコンテナー イメージ ビルドを自動化する 

[ACR タスク](container-registry-tasks-overview.md)では、いずれかの基本イメージ内で OS またはアプリケーション フレームワークにパッチを適用したときなど、コンテナーの[基本イメージが更新](container-registry-tasks-base-images.md)されたときのイメージ ビルドの自動化をサポートしています。 

このチュートリアルでは、コンテナーの基本イメージが別の Azure コンテナー レジストリにプッシュされたときにクラウドでビルドをトリガーする ACR タスクの作成方法について説明します。 基本イメージが[同じ Azure コンテナー レジストリ](container-registry-tutorial-base-image-update.md)にプッシュされたときにイメージのビルドをトリガーする ACR タスクを作成するチュートリアルもお試しください。

このチュートリアルの内容:

> [!div class="checklist"]
> * 基本レジストリの基本イメージをビルドする
> * 基本イメージを追跡するアプリケーション ビルド タスクを別のレジストリに作成する 
> * 基本イメージを更新してアプリケーション イメージ タスクをトリガーする
> * トリガーされたタスクを表示する
> * 更新されたアプリケーション イメージを確認する

## <a name="prerequisites"></a>前提条件

### <a name="complete-the-previous-tutorials"></a>前のチュートリアルを完了しておく

このチュートリアルでは、既に環境を構成し、シリーズの最初の 2 つのチュートリアルの以下の手順を完了していることを前提としています。

* Azure Container Registry の作成
* サンプル リポジトリのフォーク
* サンプル リポジトリの複製
* GitHub 個人用アクセス トークンの作成

まだ完了していない場合は、続行する前に次のチュートリアルを完了してください。

[Azure Container Registry Tasks を使用してクラウド内のコンテナー イメージをビルドする](container-registry-tutorial-quick-task.md)

[Azure Container Registry Tasks を使用してコンテナー イメージ ビルドを自動化する](container-registry-tutorial-build-task.md)

前のチュートリアルで作成したコンテナー レジストリに加えて、基本イメージを格納するためのレジストリを作成する必要があります。 必要な場合は、元のレジストリとは別の場所にもう 1 つレジストリを作成してください。

### <a name="configure-the-environment"></a>環境の構成

次のシェル環境変数に、環境に適した値を設定します。 この手順は必須ではありませんが、このチュートリアルの複数行の Azure CLI コマンドの実行が少し簡単になります。 これらの環境変数を設定しない場合は、それぞれの値を、サンプル コマンド内の現れたところで手動で置き換える必要があります。

```azurecli
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>基本イメージ更新シナリオ

このチュートリアルでは、基本イメージの更新シナリオについて説明します。 このシナリオは、共通のプライベート コンテナー レジストリで基本イメージを管理しつつ、他のレジストリでアプリケーション イメージを作成する開発ワークフローを反映したものです。 チームで使用する一般的なオペレーティング システムとフレームワーク、一般的なサービス コンポーネントは、基本イメージで指定することができます。

たとえば開発者は、独自のレジストリでアプリケーション イメージを開発しつつ、共通の基本レジストリで管理された一連の基本イメージにアクセスすることができます。 基本レジストリの場所は、別のリージョンのほか、geo レプリケーションされた場所でもかまいません。

[コード サンプル][code-sample]には 2 つの Dockerfile が含まれます。これは、アプリケーション イメージと、アプリケーション イメージが基本として指定するイメージです。 以下のセクションでは、基本イメージの新しいバージョンが異なる Azure コンテナー レジストリにプッシュされたときに、アプリケーション イメージのビルドを自動的にトリガーする ACR タスクを作成します。

* [Dockerfile-app][dockerfile-app]:基になる Node.js バージョンを表示する静的な Web ページをレンダリングする小さな Node.js Web アプリケーションです。 バージョン文字列がシミュレートされ、基本イメージで定義されている環境変数 `NODE_VERSION` の内容が表示されます。

* [Dockerfile-base][dockerfile-base]:`Dockerfile-app` によってそのベースとして指定されるイメージ。 これ自体が [Node][base-node] イメージに基づき、`NODE_VERSION` 環境変数を含みます。

以降のセクションでは、タスクを作成し、基本イメージ Docker ファイルで値 `NODE_VERSION` を更新してから、ACR Tasks を使用して基本イメージをビルドします。 ACR タスクによって新しい基本イメージがレジストリにプッシュされると、アプリケーション イメージのビルドが自動的にトリガーされます。 必要に応じて、アプリケーション コンテナー イメージをローカルで実行して、ビルドされたイメージの別のバージョンの文字列を表示します。

このチュートリアルでは、ACR タスクにより、Dockerfile で指定されたアプリケーション コンテナー イメージをビルドしてプッシュします。 ACR タスクでは、[複数ステップ タスク](container-registry-tasks-multi-step.md)を実行することもできます。その場合、YAML ファイルを使用して、複数のコンテナーをビルド、プッシュ、および (必要に応じて) テストする手順を定義します。

## <a name="build-the-base-image"></a>基本イメージをビルドする

ACR タスクの "*クイック タスク*" で、[az acr build][az-acr-build] を使用して基本イメージをビルドすることから開始します。 シリーズの[最初のチュートリアル](container-registry-tutorial-quick-task.md)で説明したように、このプロセスではイメージがビルドされるだけでなく、ビルドが成功した場合にイメージがコンテナー レジストリにプッシュされます。 この例では、イメージが基本イメージ レジストリにプッシュされます。

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>プライベートの基本イメージを追跡するタスクを作成する

次に、[az acr task create][az-acr-task-create] を使用して、アプリケーション イメージ レジストリにタスクを作成すると共に、[マネージド ID](container-registry-tasks-authentication-managed-identity.md) を有効にします。 マネージド ID は、後続の手順で、基本イメージ レジストリに対してタスクの認証を行う目的で使用します。 

この例では、システム割り当て ID を使用しますが、特定のシナリオ向けにユーザー割り当てマネージド ID を作成して有効にしてもかまいません。 詳細については、「[ACR タスクでの Azure マネージド ID を使用したクロスレジストリ認証](container-registry-tasks-cross-registry-authentication.md)」を参照してください。

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample2 \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT \
    --arg REGISTRY_NAME=$BASE_ACR.azurecr.io \
    --assign-identity
```

このタスクは、[前のチュートリアル](container-registry-tutorial-build-task.md)で作成したタスクに似ています。 これは、`--context` によって指定されたリポジトリにコミットがプッシュされたらイメージのビルドをトリガーするよう ACR Tasks に指示します。 前のチュートリアルでイメージのビルドに使用された Dockerfile にはパブリックの基本イメージ (`FROM node:15-alpine`) が指定されていますが、このタスクの Dockerfile である [Dockerfile-app][dockerfile-app] には基本イメージ レジストリ内の基本イメージを指定します。

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

この構成により、このチュートリアルの後半で、基本イメージ内のフレームワークの修正プログラムを簡単にシミュレートできます。

## <a name="give-identity-pull-permissions-to-base-registry"></a>基本レジストリに対するプル アクセス許可を ID に付与する

基本イメージ レジストリからイメージをプルするためのアクセス許可をタスクのマネージド ID に付与するために、まず [az acr task show][az-acr-task-show] を実行して、その ID のサービス プリンシパル ID を取得します。 次に、[az acr show][az-acr-show] を実行して、基本レジストリのリソース ID を取得します。

```azurecli
# Get service principal ID of the task
principalID=$(az acr task show --name baseexample2 --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
[az role assignment create][az-role-assignment-create] を実行して、レジストリに対するプル アクセス許可をマネージド ID に割り当てます。

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>ターゲット レジストリの資格情報をタスクに追加する

タスクに資格情報を追加するには、[az acr task credential add][az-acr-task-credential-add] を実行します。 タスクのシステム割り当てマネージド ID が資格情報にアクセスできることを示す `--use-identity [system]` パラメーターを渡してください。

```azurecli
az acr task credential add \
  --name baseexample2 \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>タスクを手動で実行する

[az acr task run][az-acr-task-run] を使用してタスクを手動でトリガーし、アプリケーション イメージをビルドします。 基本イメージに対するアプリケーション イメージの依存関係がタスクで追跡されるようにするために、この手順が必要となります。

```azurecli
az acr task run --registry $ACR_NAME --name baseexample2
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

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-01.png" alt-text="ブラウザーのサンプル アプリケーションのスクリーンショット":::

コンテナーを停止して削除するには、次のコマンドを実行します。

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>ビルドを一覧表示する

次に、[az acr task list-runs][az-acr-task-list-runs] コマンドを使用して、ご使用のレジストリで ACR タスクが完了したタスク実行の一覧を表示します。

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

前のチュートリアルを完了している場合 (レジストリを削除していない場合)、次のような出力が表示されます。 タスク実行番号と最新の実行 ID をメモします。これによって、次のセクションで基本イメージを更新した後に出力を比較できます。

```console
$ az acr task list-runs --registry $ACR_NAME --output table

UN ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

## <a name="update-the-base-image"></a>基本イメージを更新する

ここで、基本イメージでフレームワークのパッチをシミュレートします。 **Dockerfile-base** を編集して、`NODE_VERSION` で定義されたバージョン番号の後に "a" を追加します。

```Dockerfile
ENV NODE_VERSION 15.2.1a
```

クイック タスクを実行して、変更された基本イメージをビルドします。 出力の **実行 ID** をメモします。

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

ビルドが完了し、ACR タスクによって新しい基本イメージがレジストリにプッシュされると、アプリケーション イメージのビルドがトリガーされます。 前に作成したタスクがアプリケーション イメージ ビルドをトリガーするのに少し時間がかかる場合があります。これは、新しくビルドされ、プッシュされた基本イメージを検出する必要があるためです。

## <a name="list-updated-build"></a>更新更新されたビルドを一覧表示する

これで基本イメージを更新したので、もう一度タスクの実行を一覧表示して、以前の一覧と比較します。 最初に出力に違いがない場合は、周期的にコマンドを実行して、一覧に表示される新しいタスクの実行を確認します。

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

出力は次のようになります。 最後に実行されたビルドのトリガーは "イメージの更新" であり、これは、タスクが基本イメージのクイック タスクによって開始されたことを示しています。

```console
$ az acr task list-runs --registry $ACR_NAME --output table

         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca13      baseexample2    linux       Succeeded  Image Update  2020-11-21T00:06:00Z  00:00:43
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

新しくビルドされたコンテナーを実行する次のオプションの手順を実行し、更新後のバージョン番号を表示する場合は、イメージの更新によってトリガーされたビルドの **実行 ID** の値をメモしておきます (前の出力では "ca13")。

### <a name="optional-run-newly-built-image"></a>省略可能:新しくビルドされたイメージを実行する

ローカルで作業しており (Cloud Shell ではなく)、Docker がインストールされている場合は、そのビルドが完了したら新しいアプリケーション イメージを実行します。 `<run-id>` を、前の手順で取得した実行 ID に置き換えます。 Cloud Shell を使用する場合は、このセクションをスキップしてください (Cloud Shell では `docker run` はサポートされていません)。

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

ブラウザーで http://localhost:8081 に移動します。Web ページに更新された Node.js バージョン番号 ("a" が追加された) が表示されます。

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="ブラウザーの更新されたサンプル アプリケーションのスクリーンショット":::

**基本** イメージを新しいバージョン番号に更新しましたが、最後にビルドされた **アプリケーション** イメージによって新しいバージョンが表示されることに注意してください。 ACR Tasks によって変更が基本イメージに反映され、アプリケーション イメージが自動的にリビルドされました。

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
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task]: /cli/azure/acr#az-acr-task
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create

