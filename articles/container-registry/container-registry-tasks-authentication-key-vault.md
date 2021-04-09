---
title: ACR タスクからの外部認証
description: Azure リソースのマネージド ID を使用して、Azure キー コンテナーに格納されている Docker Hub 資格情報を読み取るように、Azure Container Registry タスク (ACR タスク) を構成します。
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 0bc43f958a14016146160a06372af0b36a9fff75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86058131"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Azure マネージド ID を使用した、ACR タスクでの外部認証 

[ACR タスク](container-registry-tasks-overview.md)で、[Azure リソースのマネージド ID を有効にする](container-registry-tasks-authentication-managed-identity.md)ことができます。 その ID をタスクで使用すれば、資格情報を渡したり管理したりしなくても、他の Azure リソースにアクセスすることができます。 

この記事では、Azure キー コンテナーに格納されたシークレットにアクセスするタスクでマネージド ID を有効にする方法を説明します。 

Azure リソースを作成するために、この記事では Azure CLI バージョン 2.0.68 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

## <a name="scenario-overview"></a>シナリオの概要

この例のタスクでは、Azure キー コンテナーに格納されている Docker Hub の資格情報を読み取ります。 この資格情報は、Docker Hub のプライベート リポジトリに対する書き込み (プッシュ) アクセス許可を持つ Docker Hub アカウント用です。 資格情報を読み取るために、マネージド ID を使用してタスクを構成し、それに適切なアクセス許可を割り当てます。 この ID を関連付けるタスクでは、イメージを作成し、Docker Hub にサインインして、そのイメージをプライベート リポジトリにプッシュします。 

この例では、ユーザー割り当てのマネージド ID またはシステム割り当てのマネージド ID を使用する手順について説明します。 どちらの ID を選ぶかは、所属する組織のニーズによって異なります。

実際のシナリオでは、企業がビルド プロセスの一環として、Docker Hub のプライベート リポジトリにイメージを発行することが考えられます。 

## <a name="prerequisites"></a>前提条件

タスクの実行場所となる Azure コンテナー レジストリが必要です。 この記事では、このレジストリの名前を *myregistry* にします。 後のステップで、独自のレジストリ名に置き換えてください。

まだ Azure コンテナー レジストリがない場合は、「[クイック スタート: Azure CLI を使用したプライベート コンテナー レジストリの作成](container-registry-get-started-azure-cli.md)」を参照してください。 まだレジストリにイメージをプッシュする必要はありません。

また、Docker Hub のプライベート リポジトリと、そのリポジトリへの書き込みアクセス許可のある Docker Hub アカウントが必要となります。 この例では、このリポジトリの名前を *hubuser/hubrepo* にします。 

## <a name="create-a-key-vault-and-store-secrets"></a>キー コンテナーを作成してシークレットを格納する

まず、必要な場合は、次の [az group create][az-group-create] コマンドを使って、*myResourceGroup* という名前のリソース グループを *eastus* の場所に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

キー コンテナーを作成するには、[az keyvault create][az-keyvault-create] コマンドを使います。 必ず一意のキー コンテナー名を指定してください。 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

[az keyvault secret set][az-keyvault-secret-set] コマンドを使って、必要な Docker Hub の資格情報をキー コンテナーに格納します。 これらのコマンドで、値が環境変数に渡されます。

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

実際のシナリオでは、シークレットを別個のプロセスで設定、管理するケースが多いでしょう。

## <a name="define-task-steps-in-yaml-file"></a>タスクのステップを YAML ファイルで定義する

このタスク例のステップは、[YAML ファイル](container-registry-tasks-reference-yaml.md)で定義されています。 ローカル作業ディレクトリに `dockerhubtask.yaml` という名前のファイルを作成し、次の内容を貼り付けます。 ファイル内のキー コンテナーの名前を、実際のキー コンテナーの名前に置き換えてください。

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

このタスク ステップにより、次の作業が行われます。

* Docker Hub に対して認証を行うためのシークレットの資格情報を管理します。
* シークレットを `docker login` コマンドに渡して Docker Hub に対して認証を行います。
* [Azure-Samples/acr-tasks](https://github.com/Azure-Samples/acr-tasks.git) リポジトリ内のサンプル Dockerfile を使用してイメージを作成します。
* プライベート Docker Hub リポジトリにイメージをプッシュします。


## <a name="option-1-create-task-with-user-assigned-identity"></a>オプション 1: ユーザー割り当て ID を使用してタスクを作成する

このセクションの手順では、タスクを作成してユーザー割り当て ID を有効にします。 システム割り当て ID を有効にする場合は、「[オプション 2: システム割り当て ID を使用してタスクを作成する](#option-2-create-task-with-system-assigned-identity)」を参照してください。 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>タスクを作成する

以下の [az acr task create][az-acr-task-create] コマンドを実行して、*dockerhubtask* タスクを作成します。 このタスクはソース コードのコンテキストなしで実行され、このコマンドは作業ディレクトリ内のファイル `dockerhubtask.yaml` を参照します。 ユーザー割り当て ID のリソース ID を `--assign-identity` パラメーターで渡します。 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>ID にキー コンテナーへのアクセスを許可する

次の [az keyvault set-policy][az-keyvault-set-policy] コマンドを実行して、キー コンテナーに対するアクセス ポリシーを設定します。 次の例では、ID にキー コンテナーからのシークレットの読み取りを許可します。 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

「[タスクを手動で実行する](#manually-run-the-task)」に進んでください。

## <a name="option-2-create-task-with-system-assigned-identity"></a>オプション 2:システム割り当て ID を使用してタスクを作成する

このセクションの手順では、タスクを作成してシステム割り当て ID を有効にします。 ユーザー割り当て ID を有効にする場合は、「[オプション 1: ユーザー割り当て ID を使用してタスクを作成する](#option-1-create-task-with-user-assigned-identity)」を参照してください。 

### <a name="create-task"></a>タスクを作成する

以下の [az acr task create][az-acr-task-create] コマンドを実行して、*dockerhubtask* タスクを作成します。 このタスクはソース コードのコンテキストなしで実行され、このコマンドは作業ディレクトリ内のファイル `dockerhubtask.yaml` を参照します。 `--assign-identity` パラメーターを値なしで指定すると、システム割り当て ID がタスクで有効になります。  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>ID にキー コンテナーへのアクセスを許可する

次の [az keyvault set-policy][az-keyvault-set-policy] コマンドを実行して、キー コンテナーに対するアクセス ポリシーを設定します。 次の例では、ID にキー コンテナーからのシークレットの読み取りを許可します。 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>タスクを手動で実行する

マネージド ID を有効にしたタスクが正常に実行されていることを確認するには、[az acr task run][az-acr-task-run] コマンドを使用してタスクを手動でトリガーします。 `--set` パラメーターを使用して、プライベート リポジトリの名前をタスクに渡します。 この例では、プレースホルダー リポジトリの名前を *hubuser/hubrepo* にします。

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

タスクが正常に実行されたときは、出力結果を見ると、Docker Hub に対する認証が成功したこと、またイメージが正常に作成されてプライベート リポジトリにプッシュされたことがわかります。

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

イメージがプッシュされたことを確認するには、プライベート Docker Hub リポジトリでタグ (この例では `cf24`) をチェックします。

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
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
