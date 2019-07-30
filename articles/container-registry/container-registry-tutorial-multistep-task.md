---
title: チュートリアル - マルチステップ コンテナー タスク - Azure Container Registry タスク
description: このチュートリアルでは、ソース コードを Git リポジトリにコミットしたらクラウドでコンテナー イメージをビルド、実行、およびプッシュするマルチステップ ワークフローを自動的にトリガーするように Azure Container Registry タスクを構成する方法について説明します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: c78c2c8279972108aee12b9b386175d0f27b7fee
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310408"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>チュートリアル:ソース コードをコミットしたらクラウドでマルチステップ コンテナー ワークフローを実行する

[クイック タスク](container-registry-tutorial-quick-task.md)に加え、ACR タスクでは、ソース コードを Git リポジトリにコミットすると自動的にトリガーできる、マルチステップでマルチコンテナー ベースのワークフローをサポートしています。 

このチュートリアルでは、ソース コードをコミットすると 1 つ以上のコンテナー イメージをビルド、実行、およびレジストリにプッシュするマルチステップ タスクを、サンプル YAML ファイルを使用して定義する方法について説明します。 コードのコミットで 1 つのイメージ ビルドのみを自動化するタスクを作成するには、「[チュートリアル: ソース コードのコミット時にクラウドでコンテナー イメージ ビルドを自動化する](container-registry-tutorial-build-task.md)」を参照してください。 ACR タスクの概要については、「[ACR タスクを使用して OS とフレームワークの修正プログラムの適用を自動化する](container-registry-tasks-overview.md)」を参照してください。

このチュートリアルの内容:

> [!div class="checklist"]
> * YAML ファイルを使用してマルチステップ タスクを定義する
> * タスクを作成します。
> * 必要に応じてタスクに資格情報を追加して、別のレジストリへのアクセスを有効にする
> * タスクのテスト
> * タスクの状態の表示
> * コードのコミットによるタスクのトリガー

このチュートリアルは、[前のチュートリアル](container-registry-tutorial-quick-task.md)の手順が既に完了していることを前提としています。 完了していない場合は、先に進む前に、前のチュートリアルの「[前提条件](container-registry-tutorial-quick-task.md#prerequisites)」セクションの手順を行ってください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルで使用する場合は、Azure CLI のバージョン **2.0.62** 以降がインストールされていて、[az login][az-login] を使用してログインしている必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>マルチステップ タスクの作成

ACR タスクがコミットの状態を読み取ってリポジトリに Webhook を作成できるようにするために必要なステップを完了したので、コンテナー イメージのビルド、実行、およびプッシュをトリガーするマルチステップ タスクを作成します。

### <a name="yaml-file"></a>YAML ファイル

マルチステップ タスクのステップは、[YAML ファイル](container-registry-tasks-reference-yaml.md)に定義します。 このチュートリアルの最初のマルチステップ タスク例は、ファイル `taskmulti.yaml` に定義されています。これは、複製した GitHub リポジトリのルートにあります。

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

このマルチステップ タスクは、次のことを行います。

1. `build` ステップを実行して、作業ディレクトリの Dockerfile からイメージをビルドします。 イメージは、タスクが実行されるレジストリである `Run.Registry` をターゲットとし、固有の ACR タスクの実行 ID でタグ付けされます。 
1. `cmd` ステップを実行して、一時コンテナーでイメージを実行します。 この例は、実行時間の長いコンテナーをバック グラウンドで起動し、コンテナー ID を返して、コンテナーを停止します。 実際のシナリオでは、実行中のコンテナーが正しく実行されることを確認するために、それをテストするステップを含める場合があります。
1. `push` ステップでは、ビルドされたイメージを実行レジストリにプッシュします。

### <a name="task-command"></a>タスクのコマンド

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
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

このタスクは、`--context` によって指定されたリポジトリ内の *master* ブランチにコードがコミットされたらいつでも、ACR タスクがそのブランチのコードからマルチステップ タスクを実行することを指定しています。 リポジトリ ルートからの `--file` で指定された YAML ファイルにこれらのステップが定義されています。 

[az acr task create][az-acr-task-create] コマンドが成功した場合、出力は次のようになります。

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
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
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
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

## <a name="test-the-multi-step-workflow"></a>マルチステップ ワークフローのテスト

複数ステップのタスクをテストするには、[az acr task run][az-acr-task-run] コマンドを実行して手動でそれをトリガーします。

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

既定では、`az acr task run` コマンドを実行すると、ログ出力がコンソールにストリーミングされます。 出力には、それぞれのタスク ステップの実行の進行状況が示されます。 次の出力は、主要な手順を表示するように要約されいます。

```console
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
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

リポジトリにコミットをプッシュすると、ACR タスクによって作成された webhook が発生して、Azure Container Registry でタスクを開始します。 現在実行中のタスクのログを表示し、ビルドの進行状況を確認および監視します。

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

次のような出力が表示され、現在実行中の (または最後に実行された) タスクが示されます。

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>ビルドの一覧を表示する

ユーザーのレジストリで ACR タスクが完了したタスク実行の一覧を表示するには、[az acr task list-runs][az-acr-task-list-runs] コマンドを実行します。

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

次のようなコマンドの出力が表示されます。 ACR タスクが実行した実行が表示され、最新のタスクの [TRIGGER] 列には "Git Commit" と表示されます。

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>マルチレジストリでマルチステップのタスクの作成

既定で ACR タスクには、タスクが実行されるレジストリとの間でイメージをプッシュまたはプルするアクセス許可があります。 実行レジストリに加えて 1 つ以上のレジストリをターゲットとするマルチステップ タスクを実行したい場合があります。 たとえば、あるレジストリでイメージを作成し、実稼働システムがアクセスする 2 番目のレジストリにさまざまなタグと共にイメージを格納する必要がある場合があります。 この例では、そのようなタスクを作成して、もう 1 つのレジストリの資格情報を指定する方法を示します。

2 番目のレジストリがまだない場合は、この例のために作成してください。 レジストリが必要な場合は、[前のチュートリアル](container-registry-tutorial-quick-task.md)または [Azure CLI を使用したコンテナー レジストリの作成に関するクイック スタート](container-registry-get-started-azure-cli.md)を参照してください。

タスクを作成するには、レジストリ ログイン サーバーの名前が必要です。これは、*mycontainerregistrydate.azurecr.io* (すべて小文字) の形式になります。 この例では、2 番目のレジストリを使用して、ビルド日付でタグ付けされたイメージを格納します。

### <a name="yaml-file"></a>YAML ファイル

このチュートリアルの 2 番目のマルチステップ タスク例は、ファイル `taskmulti-multiregistry.yaml` に定義されています。これは、複製した GitHub リポジトリのルートにあります。

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

このマルチステップ タスクは、次のことを行います。

1. 2 つの `build` ステップを実行して、作業ディレクトリの Dockerfile からイメージをビルドします。
    * 1 つ目は、タスクが実行されるレジストリである `Run.Registry` をターゲットとし、ACR タスクの実行 ID でタグ付けされています。 
    * 2 つ目は、タスクを作成するときに設定する (または `az acr task create` に渡される外部の `values.yaml` ファイルを通じて提供する) `regDate` の値で識別されるレジストリをターゲットとします。 このイメージは、実行日でタグ付けされます。
1. `cmd` ステップを実行して、ビルドしたコンテナーの 1 つを実行します。 この例は、実行時間の長いコンテナーをバック グラウンドで起動し、コンテナー ID を返して、コンテナーを停止します。 実際のシナリオでは、正しく実行されることを確認するために、実行中のコンテナーをテストする場合があります。
1. `push` ステップでは、ビルドされたイメージをプッシュします。最初のイメージは実行レジストリに、2 番目のイメージは `regDate` で識別されるレジステリにプッシュします。

### <a name="task-command"></a>タスクのコマンド

前に定義されたシェル環境変数を使用して、次の [az acr task create][az-acr-task-create] コマンドを実行することで、タスクを作成します。 *mycontainerregistrydate* をご使用のレジストリの名前に置き換えます。

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti-image.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>タスクの資格情報の追加

`regDate` の値で識別されるレジストリにイメージをプッシュするには、[az acr task credential add][az-acr-task-credential-add] コマンドを使用して、そのレジストリのログイン資格情報をタスクに追加します。

この例では、*AcrPush* ロールにスコープ指定されたレジストリへのアクセス許可を持つ[サービス プリンシパル](container-registry-auth-service-principal.md)を作成することをお勧めします。 サービス プリンシパルを作成するには、この [Azure CLI スクリプト](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh)を参照してください。

サービス プリンシパルのアプリケーション ID とパスワードを次の `az acr task credential add` コマンドで渡します。

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

CLI は、追加したレジストリ ログイン サーバーの名前を返します。

### <a name="test-the-multi-step-workflow"></a>マルチステップ ワークフローのテスト

前の例と同様に、複数ステップのタスクをテストするには、[az acr task run][az-acr-task-run] コマンドを実行して手動でそれをトリガーします。 Git リポジトリへのコミットでタスクをトリガーするには、「[コミットでビルドをトリガーする](#trigger-a-build-with-a-commit)」セクションを参照してください。

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

既定では、`az acr task run` コマンドを実行すると、ログ出力がコンソールにストリーミングされます。 前と同様に、この出力には、それぞれのタスク ステップの実行の進行状況が示されます。 次の出力は、主要な手順を表示するように要約されいます。

出力:

```console
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Git リポジトリにソース コードをコミットすると自動的にトリガーされるマルチステップ、マルチコンテナーベースのタスクを作成する方法を説明しました。 並列ステップ実行や依存型のステップ実行など、マルチステップ タスクの高度な機能については、「[ACR タスクの参照: YAML](container-registry-tasks-reference-yaml.md)」を参照してください。 次のチュートリアルに進んで、コンテナー イメージの基本イメージが更新されたらビルドをトリガーするタスクを作成する方法を学習してください。

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
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
