---
title: ACR タスクからのクロスレジストリ認証
description: Azure リソースのマネージド ID を使用して、別のプライベート Azure コンテナー レジストリにアクセスできるように、Azure Container Registry タスク (ACR タスク) を構成します
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 789d2c141f8b7c3f2eb8daa31d99090e3d028a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98915830"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>ACR タスクでの Azure マネージド ID を使用したクロスレジストリ認証 

[ACR タスク](container-registry-tasks-overview.md)で、[Azure リソースのマネージド ID を有効にする](container-registry-tasks-authentication-managed-identity.md)ことができます。 その ID をタスクで使用すれば、資格情報を渡したり管理したりしなくても、他の Azure リソースにアクセスすることができます。 

この記事では、タスクの実行に使用されるレジストリとは異なるレジストリからイメージをプルするためのタスクでマネージド ID を有効にする方法について説明します。

Azure リソースを作成するために、この記事では Azure CLI バージョン 2.0.68 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

## <a name="scenario-overview"></a>シナリオの概要

この例のタスクでは、別の Azure コンテナー レジストリから基本イメージをプルし、アプリケーション イメージをビルドしてプッシュします。 基本イメージをプルするために、マネージド ID を使用してタスクを構成し、それに適切なアクセス許可を割り当てます。 

この例では、ユーザー割り当てのマネージド ID またはシステム割り当てのマネージド ID を使用する手順について説明します。 どちらの ID を選ぶかは、所属する組織のニーズによって異なります。

実際のシナリオでは、組織は、アプリケーションをビルドするために、すべての開発チームが使用する一連の基本イメージを保持する場合があります。 これらの基本イメージは企業レジストリに保存され、各開発チームはプル権限のみ所有します。 

## <a name="prerequisites"></a>前提条件

この記事では、次の 2 つの Azure コンテナー レジストリが必要です。

* 最初のレジストリは、ACR タスクを作成して実行するために使います。 この記事では、このレジストリの名前を *myregistry* にします。 
* 2 番目のレジストリでは、タスクでイメージをビルドするときに使用される基本イメージをホストします。 この記事では、2 番目のレジストリの名前を *mybaseregistry* にします。 

後のステップで、独自のレジストリ名に置き換えてください。

必要な Azure コンテナー レジストリがまだない場合は、「[クイックスタート: Azure CLI を使用したプライベート コンテナー レジストリの作成](container-registry-get-started-azure-cli.md)」を参照してください。 まだレジストリにイメージをプッシュする必要はありません。

## <a name="prepare-base-registry"></a>基本レジストリの準備

デモンストレーション目的で、1 回限りの操作として、[az acr import][az-acr-import] を実行して、Docker Hub から基本レジストリにパブリック Node.js イメージをインポートします。 実際には、組織内の別のチームまたはプロセスが基本レジストリのイメージを保持することがあります。

```azurecli
az acr import --name mybaseregistry \
  --source docker.io/library/node:15-alpine \
  --image baseimages/node:15-alpine 
```

## <a name="define-task-steps-in-yaml-file"></a>タスクのステップを YAML ファイルで定義する

この例の[マルチステップ タスク](container-registry-tasks-multi-step.md)のステップは [YAML ファイル](container-registry-tasks-reference-yaml.md)で定義されています。 ローカル作業ディレクトリに `helloworldtask.yaml` という名前のファイルを作成し､次の内容を貼り付けます。 基本レジストリのサーバー名を使用してビルド ステップで `REGISTRY_NAME` の値を更新します。

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git#main -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

ビルド ステップでは、[Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) リポジトリ内の `Dockerfile-app` ファイルを使用してイメージをビルドします。 `--build-arg` で基本レジストリを参照して基本イメージをプルします。 ビルドが成功すると、タスクを実行するために使用されるレジストリにイメージがプッシュされます。

## <a name="option-1-create-task-with-user-assigned-identity"></a>オプション 1: ユーザー割り当て ID を使用してタスクを作成する

このセクションの手順では、タスクを作成してユーザー割り当て ID を有効にします。 システム割り当て ID を有効にする場合は、「[オプション 2: システム割り当て ID を使用してタスクを作成する](#option-2-create-task-with-system-assigned-identity)」を参照してください。 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>タスクを作成する

次の [az acr task create][az-acr-task-create] コマンドを実行して、タスク *helloworldtask* を作成します。 このタスクはソース コードのコンテキストなしで実行され、このコマンドは作業ディレクトリ内のファイル `helloworldtask.yaml` を参照します。 ユーザー割り当て ID のリソース ID を `--assign-identity` パラメーターで渡します。 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>基本レジストリに対するプル アクセス許可を ID に付与する

このセクションでは、基本レジストリ *mybaseregistry* からプルするためのアクセス許可をマネージド ID に付与します。

[az acr show][az-acr-show] コマンドを使用して基本レジストリのリソース ID を取得し、変数に格納します。

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

[az role assignment create][az-role-assignment-create] コマンドを使用して、基本レジストリに対する `acrpull` ロールを ID に割り当てます。 このロールには、レジストリからイメージをプルするためのアクセス許可のみがあります。

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

「[ターゲット レジストリの資格情報をタスクに追加する](#add-target-registry-credentials-to-task)」に進んでください。

## <a name="option-2-create-task-with-system-assigned-identity"></a>オプション 2:システム割り当て ID を使用してタスクを作成する

このセクションの手順では、タスクを作成してシステム割り当て ID を有効にします。 ユーザー割り当て ID を有効にする場合は、「[オプション 1: ユーザー割り当て ID を使用してタスクを作成する](#option-1-create-task-with-user-assigned-identity)」を参照してください。 

### <a name="create-task"></a>タスクを作成する

次の [az acr task create][az-acr-task-create] コマンドを実行して、タスク *helloworldtask* を作成します。 このタスクはソース コードのコンテキストなしで実行され、このコマンドは作業ディレクトリ内のファイル `helloworldtask.yaml` を参照します。 `--assign-identity` パラメーターを値なしで指定すると、システム割り当て ID がタスクで有効になります。 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>基本レジストリに対するプル アクセス許可を ID に付与する

このセクションでは、基本レジストリ *mybaseregistry* からプルするためのアクセス許可をマネージド ID に付与します。

[az acr show][az-acr-show] コマンドを使用して基本レジストリのリソース ID を取得し、変数に格納します。

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

[az role assignment create][az-role-assignment-create] コマンドを使用して、基本レジストリに対する `acrpull` ロールを ID に割り当てます。 このロールには、レジストリからイメージをプルするためのアクセス許可のみがあります。

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>ターゲット レジストリの資格情報をタスクに追加する

ここで、[az acr task credential add][az-acr-task-credential-add] コマンドを使用して、基本レジストリで ID の資格情報を使用して認証するためのタスクを有効にします。 タスクで有効にしたマネージド ID の種類に対応するコマンドを実行します。 ユーザー割り当て ID を有効にした場合は、ID のクライアント ID を指定して `--use-identity` を渡します。 システム割り当て ID を有効にした場合は、`--use-identity [system]` を渡します。

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>タスクを手動で実行する

マネージド ID を有効にしたタスクが正常に実行されていることを確認するには、[az acr task run][az-acr-task-run] コマンドを使用してタスクを手動でトリガーします。 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

タスクが正常に実行されている場合、出力は次のようになります。

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
15-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 15-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

[az acr repository show-tags][az-acr-repository-show-tags] コマンドを実行して、イメージがビルドされ、*myregistry* に正常にプッシュされたことを確認します。

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

出力例:

```console
cf10
```

## <a name="next-steps"></a>次のステップ

* [ACR タスクにおけるマネージド ID の有効化](container-registry-tasks-authentication-managed-identity.md)について学習する。
* [ACR タスクの YAML 参照](container-registry-tasks-reference-yaml.md)に関するページを参照する

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
