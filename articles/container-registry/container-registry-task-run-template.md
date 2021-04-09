---
title: テンプレートを使用したクイック タスクの実行
description: Azure Resource Manager テンプレートを使用して、イメージをビルドする ACR タスクの実行をキューに登録します
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 6e8023c088ac328c2b6e95fccd0230c4d40325c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916067"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Resource Manager テンプレートを使用して ACR タスクを実行する

[ACR タスク](container-registry-tasks-overview.md)は、コンテナー ライフサイクル全体でコンテナー イメージを管理および変更するのに役立つ Azure Container Registry 内の機能のスイートです。 

この記事では、クイック タスクの実行 ([az acr build][az-acr-build] コマンドを使用して手動で作成できるものと同様) をキューに登録する Azure Resource Manager テンプレートの例を示します。

タスクの実行をキューに登録する Resource Manager テンプレートは、自動化のシナリオに役立ち、`az acr build` の機能を拡張します。 次に例を示します。

* テンプレートを使用して、コンテナー レジストリを作成して直ちにタスクの実行をキューに登録し、コンテナー イメージをビルドしてプッシュする
* クイック タスクの実行で使用できる追加のリソース (Azure リソースのマネージド ID など) を作成または有効にする

## <a name="limitations"></a>制限事項

* タスクの実行用の[ソースの場所](container-registry-tasks-overview.md#context-locations)として、GitHub リポジトリなどのリモート コンテキストを指定する必要があります。 ローカル ソース コンテキストを使用することはできません。
* マネージド ID を使用したタスクの実行では、"*ユーザー割り当て*" のマネージド ID のみが許可されます。

## <a name="prerequisites"></a>前提条件

* **GitHub アカウント** - まだお持ちでない場合は、 https://github.com でアカウントを作成します。 
* **フォーク サンプル リポジトリ** - ここに示すタスクの例については、GitHub UI を使用して、次のサンプル リポジトリをご自身の GitHub アカウントにフォークします: https://github.com/Azure-Samples/acr-build-helloworld-node 。 このリポジトリには、小規模なコンテナー イメージをビルドするためのサンプルの Dockerfile とソース コードが含まれています。

## <a name="example-create-registry-and-queue-task-run"></a>例:レジストリを作成してタスクの実行をキューに登録する

この例では、[サンプル テンプレート](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild)を使用して、コンテナー レジストリを作成し、イメージをビルドしてプッシュするタスクの実行をキューに登録します。 

### <a name="template-parameters"></a>Template parameters

この例では、以下のテンプレート パラメーターに値を指定します。

|パラメーター  |値  |
|---------|---------|
|registryName     |作成するレジストリの一意の名前         |
|repository     |ビルド タスクのターゲット リポジトリ        |
|taskRunName     |タスクの実行の名前 (イメージ タグを指定します) |
|sourceLocation     |ビルド タスクのリモート コンテキスト (たとえば、 https://github.com/Azure-Samples/acr-build-helloworld-node )。 リポジトリのルートにある Dockerfile で小規模な Node.js Web アプリのコンテナー イメージをビルドします。 必要に応じて、リポジトリのフォークをビルド コンテキストとして使用します。         |

### <a name="deploy-the-template"></a>テンプレートのデプロイ

[az deployment group create][az-deployment-group-create] コマンドを使用してテンプレートをデプロイします。 この例では、*helloworld-node:testrun* イメージをビルドし、*mycontainerregistry* という名前のレジストリにプッシュします。

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
 ```

上記のコマンドでは、コマンド ラインでパラメーターを渡しています。 必要に応じて、[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)内で渡します。

### <a name="verify-deployment"></a>デプロイの確認

デプロイが正常に完了したら、[az acr repository show-tags][az-acr-repository-show-tags] を実行して、イメージがビルドされたことを確認します。

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

出力:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>実行ログの表示

タスクの実行に関する詳細を確認するには、実行ログを表示します。

まず、[az acr task list-runs][az-acr-task-list-runs] を使用して実行 ID を取得します。
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

出力は次のようになります。

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

[az acr task logs][az-acr-task-logs] を実行して、その実行 ID (この場合は *ca1*) のタスク実行ログを表示します。

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

出力にタスク実行ログが表示されます。

Azure portal でタスク実行ログを確認することもできます。 

1. お使いのコンテナー レジストリに移動します。
2. **[サービス]** で、 **[タスク]**  >  **[実行]** を選択します。
3. 実行 ID を選択します。この例では *ca1* です。 

ポータルにタスク実行ログが表示されます。

## <a name="example-task-run-with-managed-identity"></a>例:マネージド ID を使用したタスクの実行

[サンプル テンプレート](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity)を使用して、ユーザー割り当てのマネージド ID を有効にするタスクの実行をキューに登録します。 そのタスクの実行時に、別の Azure コンテナー レジストリからイメージをプルするために ID が認証されます。 

このシナリオは、「[ACR タスクでの Azure マネージド ID を使用したクロスレジストリ認証](container-registry-tasks-cross-registry-authentication.md)」に似ています。 たとえば、組織では、複数の開発チームによってアクセスされる基本イメージを使用して、集中管理されたレジストリを維持する場合があります。

### <a name="prepare-base-registry"></a>基本レジストリの準備

デモンストレーションの目的のために、基本レジストリとして別個のコンテナー レジストリを作成し、Docker Hub からプルされた Node.js 基本イメージをプッシュします。

1. 基本イメージを格納するために 2 つ目のコンテナー レジストリ (たとえば *mybaseregistry*) を作成します。
1. Docker Hub から `node:9-alpine` イメージをプルし、基本レジストリのタグを付け、基本レジストリにプッシュします。

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>新しい Dockerfile の作成

基本レジストリから基本イメージをプルする Dockerfile を作成します。 GitHub リポジトリのローカル フォーク (たとえば、`https://github.com/myGitHubID/acr-build-helloworld-node.git`) で次の手順を行います。

1. GitHub UI で、 **[Create new file]\(新しいファイルの作成\)** を選択します。
1. そのファイルに *Dockerfile-test* という名前を付け、次の内容を貼り付けます。 *mybaseregistry* は、実際のレジストリ名に置き換えます。
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. **[Commit new file]\(新しいファイルのコミット\)** を選択します。

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>基本レジストリに対するプル アクセス許可を ID に付与する

基本レジストリ *mybaseregistry* からプルするためのアクセス許可をマネージド ID に付与します。

[az acr show][az-acr-show] コマンドを使用して基本レジストリのリソース ID を取得し、変数に格納します。

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

[az role assignment create][az-role-assignment-create] コマンドを使用して、基本レジストリに対する Acrpull ロールを ID に割り当てます。 このロールには、レジストリからイメージをプルするためのアクセス許可のみがあります。

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Template parameters

この例では、以下のテンプレート パラメーターに値を指定します。

|パラメーター  |値  |
|---------|---------|
|registryName     |イメージをビルドするレジストリの名前  |
|repository     |ビルド タスクのターゲット リポジトリ        |
|taskRunName     |タスクの実行の名前 (イメージ タグを指定します) |
|userAssignedIdentity |このタスクで有効にするユーザー割り当て ID のリソース ID|
|customRegistryIdentity | カスタム レジストリでの認証に使用される、タスクで有効にするユーザー割り当て ID のクライアント ID |
|customRegistry |タスクでアクセスするカスタム レジストリのログイン サーバー名 (たとえば *mybaseregistry.azurecr.io*)|
|sourceLocation     |ビルド タスクのリモート コンテキスト (たとえば *https://github.com/\<your-GitHub-ID\> /acr-build-helloworld-node*)。 |
|dockerFilePath | イメージのビルドに使用する、リモート コンテキストの Dockerfile へのパス |

### <a name="deploy-the-template"></a>テンプレートのデプロイ

[az deployment group create][az-deployment-group-create] コマンドを使用してテンプレートをデプロイします。 この例では、*helloworld-node:testrun* イメージをビルドし、*mycontainerregistry* という名前のレジストリにプッシュします。 基本イメージは *mybaseregistry.azurecr.io* からプルされます。

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git#main \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

上記のコマンドでは、コマンド ラインでパラメーターを渡しています。 必要に応じて、[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)内で渡します。

### <a name="verify-deployment"></a>デプロイの確認

デプロイが正常に完了したら、[az acr repository show-tags][az-acr-repository-show-tags] を実行して、イメージがビルドされたことを確認します。

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

出力:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>実行ログの表示

実行ログを表示するには、[前のセクション](#view-run-log)の手順を参照してください。

## <a name="next-steps"></a>次のステップ

 * その他のテンプレート例については、[ACR GitHub リポジトリ](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment)を参照してください。
 * テンプレートのプロパティの詳細については、[タスクの実行](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns)と[タスク](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)のテンプレートのリファレンスを参照してください。


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-logs]: /cli/azure/acr/task#az-acr-task-logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
