---
title: チュートリアル - コンテナー イメージ ビルドを自動化する - Azure Container Registry タスク
description: このチュートリアルでは、Git リポジトリにソース コードをコミットしたときにクラウドでコンテナー イメージ ビルドを自動的にトリガーするように Azure Container Registry タスクを構成する方法を説明します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/04/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: ea3f4f295da747b3a53956c0888797a5f8607d6e
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310487"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>チュートリアル: ソース コードのコミット時にクラウドでコンテナー イメージ ビルドを自動化する

[クイック タスク](container-registry-tutorial-quick-task.md)に加えて、ACR タスクは、ソース コードを Git リポジトリにコミットしたときのクラウド内での自動 Docker コンテナー イメージ ビルドをサポートしています。

このチュートリアルでは、ソース コードを Git リポジトリにコミットしたときに、ACR タスクによって、Dockerfile で指定されている単一のコンテナー イメージをビルドしてプッシュします。 YAML ファイルを使用して、コードのコミットで複数のコンテナーをビルド、プッシュ、および (必要に応じて) テストする手順を定義する[マルチステップ タスク](container-registry-tasks-multi-step.md)を作成するには、「[チュートリアル:ソース コードをコミットしたらクラウドでマルチステップ コンテナー ワークフローを実行する](container-registry-tutorial-multistep-task.md)」を参照してください。 ACR タスクの概要については、「[ACR タスクを使用して OS とフレームワークの修正プログラムの適用を自動化する](container-registry-tasks-overview.md)」を参照してください

このチュートリアルの内容:

> [!div class="checklist"]
> * タスクを作成します。
> * タスクのテスト
> * タスクの状態の表示
> * コードのコミットによるタスクのトリガー

このチュートリアルは、[前のチュートリアル](container-registry-tutorial-quick-task.md)の手順が既に完了していることを前提としています。 完了していない場合は、先に進む前に、前のチュートリアルの「[前提条件](container-registry-tutorial-quick-task.md#prerequisites)」セクションの手順を行ってください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルで使用する場合は、Azure CLI のバージョン **2.0.46** 以降がインストールされていて、[az login][az-login] を使用してログインしている必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-the-build-task"></a>ビルド タスクを作成する

ACR タスクがコミットの状態を読み取ってリポジトリに webhook を作成できるようにするために必要な手順が済んだので、リポジトリへのコミットでコンテナー イメージのビルドをトリガーするタスクを作成できます。

最初に、次のシェル環境変数に、環境に適した値を設定します。 この手順は必須ではありませんが、このチュートリアルの複数行の Azure CLI コマンドの実行が少し簡単になります。 これらの環境変数を設定しない場合は、それぞれの値を、サンプル コマンド内の現れたところで手動で置き換える必要があります。

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

次に、以下の [az acr task create][az-acr-task-create] コマンドを実行して、タスクを作成します。

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> 以前、プレビュー期間中に `az acr build-task` コマンドを使用してタスクを作成した場合、それらのタスクは [az acr task][az-acr-task] コマンドを使用して再作成する必要があります。

このタスクでは、`--context` で指定されているリポジトリの *master* ブランチにコードがコミットされたら常に、ACR タスクがそのブランチのコードからコンテナー イメージをビルドすることが指定されています。 `--file` によって指定されているリポジトリ ルートの Dockerfile を使用して、イメージがビルドされます。 `--image` 引数では、イメージのタグのバージョン部分に対する `{{.Run.ID}}` のパラメーター化された値が指定されており、ビルドされたイメージが特定のビルドに関連付けられ、一意にタグ付けされることを保証します。

[az acr task create][az-acr-task-create] コマンドが成功した場合、出力は次のようになります。

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2018-09-14T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-build-task"></a>ビルド タスクをテストする

ビルドを定義するタスクが完成しました。 ビルド パイプラインをテストするには、[az acr task run][az-acr-task-run] コマンドを実行することで、ビルドを手動でトリガーします。

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

既定では、`az acr task run` コマンドを実行すると、ログ出力がコンソールにストリーミングされます。

```console
$ az acr task run --registry $ACR_NAME --name taskhelloworld

2018/09/17 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2018/09/17 22:51:00 Setting up Docker configuration...
2018/09/17 22:51:02 Successfully set up Docker configuration
2018/09/17 22:51:02 Logging in to registry: myregistry.azurecr.io
2018/09/17 22:51:03 Successfully logged in
2018/09/17 22:51:03 Executing step: build
2018/09/17 22:51:03 Obtaining source code and scanning for dependencies...
2018/09/17 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 5f574fcf5816
Step 3/5 : RUN cd /src && npm install
 ---> Running in b1bca3b5f4fc
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.078s
Removing intermediate container b1bca3b5f4fc
 ---> 44457db20dac
Step 4/5 : EXPOSE 80
 ---> Running in 9e6f63ec612f
Removing intermediate container 9e6f63ec612f
 ---> 74c3e8ea0d98
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:11 Executing step: push
2018/09/17 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2018/09/17 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2018/09/17 22:51:21 Populating digests for step id: build...
2018/09/17 22:51:22 Successfully populated digests for step id: build
2018/09/17 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: da2 was successful after 27s
```

## <a name="trigger-a-build-with-a-commit"></a>コミットでビルドをトリガーする

手動実行によるタスクのテストが済んだので、次に、ソース コードを変更して自動的にトリガーします。

最初に、[リポジトリ][sample-repo]のローカルなクローンが含まれるディレクトリにいることを確実にします。

```azurecli-interactive
cd acr-build-helloworld-node
```

次に、以下のコマンドを実行することで、新しいファイルを作成してコミットし、GitHub 上のリポジトリのフォークにプッシュします。

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

`git push` コマンドを実行するときに、GitHub の資格情報の入力を求められることがあります。 GitHub のユーザー名を指定し、前にパスワードに対して作成した個人用アクセス トークン (PAT) を入力します。

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

リポジトリにコミットをプッシュすると、ACR タスクによって作成された webhook が発生して、Azure Container Registry でのビルドを開始します。 現在実行中のタスクのログを表示し、ビルドの進行状況を確認および監視します。

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

次のような出力が表示され、現在実行中の (または最後に実行された) タスクが示されます。

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: da4

[...]

Run ID: da4 was successful after 38s
```

## <a name="list-builds"></a>ビルドの一覧を表示する

ユーザーのレジストリで ACR タスクが完了したタスク実行の一覧を表示するには、[az acr task list-runs][az-acr-task-list-runs] コマンドを実行します。

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

次のようなコマンドの出力が表示されます。 ACR タスクが実行した実行が表示され、最新のタスクの [TRIGGER] 列には "Git Commit" と表示されます。

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、タスクを使って、Git リポジトリにソース コードをコミットすると Azure でコンテナー イメージのビルドが自動的にトリガーされるようにする方法を説明しました。 次のチュートリアルに進んで、コンテナー イメージの基本イメージが更新されたらビルドをトリガーするタスクを作成する方法を学習してください。

> [!div class="nextstepaction"]
> [基本イメージ更新時のビルドを自動化する](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-login]: /cli/azure/reference-index#az-login



