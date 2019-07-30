---
title: Azure Container Registry タスクでマネージド ID を使用する
description: Azure リソースのマネージド ID を割り当てることで、他のプライベート コンテナー レジストリなどの Azure リソースに対するアクセスを Azure Container Registry タスクに提供します。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5b60727472a06aaac8ccd3dce8609461e8972311
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148039"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>ACR タスクで Azure マネージド ID を使用する 

コードで資格情報を提供したり管理したりする必要なしに、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使って、ACR タスクから Azure コンテナー レジストリや他の Azure リソースへの認証を行います。 たとえば、マネージド ID を使って、タスク内のステップとして、コンテナー イメージを別のレジストリからプルまたは別のレジストリにプッシュします。

この記事では、マネージド ID および次の操作を行う方法について詳細に説明します。

> [!div class="checklist"]
> * ACR タスクでシステム割り当て ID またはユーザー割り当て ID を有効にする
> * ID に、他の Azure コンテナー レジストリなどの Azure リソースへのアクセスを許可する
> * マネージド ID を使ってタスクからリソースにアクセスする 

Azure リソースを作成するために、この記事では Azure CLI バージョン 2.0.66 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

## <a name="why-use-a-managed-identity"></a>マネージド ID を使用する理由

Azure リソースのマネージド ID は、Azure サービスに Azure Active Directory (Azure AD) で自動的に管理される ID を提供します。 マネージド ID を使って、[特定の Azure リソース](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) (ACR タスクを含む) を構成できます。 次に、その ID を使用して、コードまたはスクリプトで資格情報を渡すことなく他の Azure リソースにアクセスします。

マネージド ID には、次の 2 種類があります。

* *ユーザー割り当て ID*。これは複数のリソースに割り当て、必要なだけ永続化させることができます。 ユーザー割り当て ID は、現在プレビューの段階です。

* "*システム管理 ID*" は、ACR タスクなどの特定のリソースに固有の ID であり、そのリソースの有効期間だけ持続されます。

マネージド ID を使用して Azure リソースを設定した後、他のセキュリティ プリンシパルと同様に、その ID に別のリソースへの必要なアクセス権を付与します。 たとえば、マネージド ID に、Azure 内のプライベート コンテナー レジストリに対するプル、プッシュとプル、またはその他のアクセス許可を持つロールを割り当てます。 (レジストリ ロールの完全な一覧については、「[Azure Container Registry のロールとアクセス許可](container-registry-roles.md)」を参照してください。)ID には 1 つ以上のリソースへのアクセス権を付与できます。

## <a name="create-container-registries"></a>コンテナー レジストリを作成する

このチュートリアルでは、3 つのコンテナー レジストリが必要です。

* 最初のレジストリは、ACR タスクを作成して実行するために使います。 この記事では、このソース レジストリの名前を *myregistry* にします。 
* 2 番目と 3 番目のレジストリは、最初のタスクの例でビルドするイメージをプッシュするためのターゲット レジストリです。 この記事では、ターゲット レジストリの名前を *customregistry1* および *customregistry2* にします。

後のステップで、独自のレジストリ名に置き換えてください。

必要な Azure コンテナー レジストリがまだない場合は、「[クイックスタート: Azure CLI を使用したプライベート コンテナー レジストリの作成](container-registry-get-started-azure-cli.md)」を参照してください。 まだレジストリにイメージをプッシュする必要はありません。

## <a name="example-task-with-a-system-assigned-identity"></a>例:システム割り当て ID を使用するタスク

この例では、システム割り当て ID を使って[マルチステップ タスク](container-registry-tasks-multi-step.md)を作成する方法を示します。 タスクでイメージをビルドした後、イメージをプッシュするために ID を使って 2 つのターゲット レジストリで認証を行います。

このタスク例のステップは、`testtask.yaml` という名前の [YAML ファイル](container-registry-tasks-reference-yaml.md)で定義されています。 そのファイルは、[acr-tasks](https://github.com/Azure-Samples/acr-tasks) サンプル リポジトリの multipleRegistries ディレクトリにあります。 そのファイルをここに再掲します。

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>システム割り当て ID を使用してタスクを作成する

次の [az acr task create][az-acr-task-create] コマンドを実行して、*multiple-reg* タスクを作成します。 タスクのコンテキストはサンプル リポジトリの multipleRegistries フォルダーであり、コマンドではリポジトリの `testtask.yaml` ファイルが参照されています。 `--assign-identity` パラメーター (追加の値なし) では、タスクに対するシステム割り当て ID が作成されます。 このタスクは手動でトリガーする必要があるように設定されますが、コミットがリポジトリにプッシュされたとき、またはプル要求が行われたときに実行されるように、設定できます。 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

コマンドの出力の `identity` セクションでは、タスクで `SystemAssigned` 型の ID が設定されたことが示されています。 `principalId` は、ID のサービス プリンシパル ID です。

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

後のコマンドで使用するため、[az acr task show][az-acr-task-show] コマンドを使って `principalId` を変数に格納します。

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>2 つのターゲット コンテナー レジストリへのプッシュ アクセス許可を ID に付与する

このセクションでは、システム割り当て ID に、*customregistry1* および *customregistry2* という名前の 2 つのターゲット レジストリにプッシュするアクセス許可を付与します。

まず、[az acr show][az-acr-show] コマンドを使って各レジストリのリソース ID を取得し、変数に格納します。

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

[az role assignment create][az-role-assignment-create] コマンドを使って、各レジストリに対する `acrpush` ロールを ID に割り当てます。 このロールには、コンテナー レジストリのイメージをプルおよびプッシュするためのアクセス許可があります。

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>ターゲット レジストリの資格情報をタスクに追加する

次に、両方のターゲット レジストリで認証を行うことができるように、[az acr task credential add][az-acr-task-credential-add] コマンドを使って、ID の資格情報をタスクに追加します。

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>タスクを手動で実行する

[az acr task run][az-acr-task-run] コマンドを使って、タスクを手動でトリガーします。 `--set` パラメーターを使って、タスク変数 `REGISTRY1` および `REGISTRY2` に対する値として 2 つのターゲット レジストリのログイン サーバー名を渡します。

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

出力は次のようになります。

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>例:ユーザー割り当て ID を使用するタスク

この例では、Azure キー コンテナーからシークレットを読み取るアクセス許可を持つユーザー割り当て ID を作成します。 この ID を、シークレットを読み取り、イメージをビルドし、Azure CLI にサインインしてイメージ タグを読み取るマルチステップ タスクに割り当てます。

### <a name="create-a-key-vault-and-store-a-secret"></a>キー コンテナーを作成してシークレットを格納する

まず、必要な場合は、次の [az group create][az-group-create] コマンドを使って、*myResourceGroup* という名前のリソース グループを *eastus* の場所に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

キー コンテナーを作成するには、[az keyvault create][az-keyvault-create] コマンドを使います。 必ず一意のキー コンテナー名を指定してください。 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

[az keyvault secret set][az-keyvault-secret-set] コマンドを使って、サンプルのシークレット例をキー コンテナーに格納します。

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

たとえば、プライベート イメージをプルできるようにプライベート Docker レジストリで認証を行うために資格情報を格納することが必要になる場合があります。

### <a name="create-an-identity"></a>ID の作成

[az identity create][az-identity-create] コマンドを使って、サブスクリプションに *myACRTasksId* という名前の ID を作成します。 前にコンテナー レジストリまたはキー コンテナーを作成するために使ったものと同じリソース グループ、または別のリソース グループを使用できます。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

後の手順で ID を構成するために、[az identity show][az-identity-show] コマンドを使用して、ID のリソース ID とサービス プリンシパル ID を変数に格納します。

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>シークレットを読み取るためのキー コンテナーへのアクセスを ID に許可する

次の [az keyvault set-policy][az-keyvault-set-policy] コマンドを実行して、キー コンテナーに対するアクセス ポリシーを設定します。 次の例では、ユーザー割り当て ID にキー コンテナーからのシークレットの取得を許可します。 このアクセスは、後でマルチステップ タスクを正常に実行するために必要です。

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>レジストリのリソース グループに対する閲覧者アクセスを ID に許可する

次の [az role assignment create][az-role-assignment-create] コマンドを実行して、ID に閲覧者ロールを割り当てます。ここでは、ソース レジストリが含まれるリソース グループに対するものです。 このロールは、後でマルチステップ タスクを正常に実行するために必要です。

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>ユーザー割り当て ID を使用してタスクを作成する

次に、[マルチステップ タスク](container-registry-tasks-multi-step.md)を作成し、それにユーザー割り当て ID を割り当てます。 この例のタスクでは、`managed-identities.yaml` という名前の [YAML ファイル](container-registry-tasks-reference-yaml.md)をローカル作業ディレクトリに作成し、次の内容を貼り付けます。 ファイル内のキー コンテナーの名前を、実際のキー コンテナーの名前に置き換えてください

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

このタスクでは次のことが行われます。

* キー コンテナー内のシークレットにアクセスできることを確認します。 このステップは、デモンストレーション用です。 実際のシナリオでは、プライベート Docker Hub リポジトリにアクセスするために資格情報を取得するタスク ステップが必要になる場合があります。
* `mywebsite` イメージをビルドし、ソース レジストリにプッシュします。
* Azure CLI にログインし、ソース レジストリ内の `my-website` イメージ タグの一覧を表示します。

*msitask* という名前のタスクを作成し、前に作成したユーザー割り当て ID のリソース ID をそれに渡します。 この例のタスクは、ローカル作業ディレクトリに保存した `managed-identities.yaml` ファイルから作成されるため、手動でトリガーする必要があります。

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

コマンドの出力の `identity` セクションでは、タスクで `UserAssigned` 型の ID が設定されたことが示されています。 `principalId` は、ID のサービス プリンシパル ID です。

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>タスクを手動で実行する

[az acr task run][az-acr-task-run] コマンドを使って、タスクを手動でトリガーします。 タスクにソース レジストリの名前を渡すには、`--set` パラメーターを使います。

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

出力では、シークレットが解決されたこと、イメージが正常にビルドされてプッシュされたこと、タスクが ID で Azure CLI にログインしてソース レジストリからイメージ タグを読み取ったことが示されています。

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>次の手順

この記事では、Azure Container Registry タスクでのマネージド ID の使用および次の操作を行う方法について学習しました。

> [!div class="checklist"]
> * ACR タスクでシステム割り当て ID またはユーザー割り当て ID を有効にする
> * ID に、他の Azure コンテナー レジストリなどの Azure リソースへのアクセスを許可する
> * マネージド ID を使ってタスクからリソースにアクセスする  

* 詳細については、「[Azure リソースの管理 ID について](/azure/active-directory/managed-identities-azure-resources/)」を参照してください。

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
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
