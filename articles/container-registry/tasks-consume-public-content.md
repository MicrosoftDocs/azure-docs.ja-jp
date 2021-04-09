---
title: パブリック レジストリ コンテンツを管理するタスク ワークフロー
description: 自動化された Azure Container Registry タスク ワークフローを作成して、プライベート Azure コンテナー レジストリ内のパブリック イメージ コンテンツを追跡、管理、および使用します。
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349284"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Azure Container Registry タスクを使用してパブリック コンテンツを使用および保守する方法

この記事では、パブリック コンテンツの使用と保守を管理するために役立つ Azure Container Registry のサンプル ワークフローを示します。

1. 依存するパブリック イメージのローカル コピーをインポートします。
1. セキュリティ スキャンと機能テストを通じてパブリック イメージを検証します。
1. 内部使用のためにイメージをプライベート レジストリにレベルを上げます。
1. パブリック コンテンツに依存するアプリケーションのベース イメージの更新をトリガーします。
1. [Azure Container Registry タスク](container-registry-tasks-overview.md)を使用して、このワークフローを自動化します。

このワークフローは次の図のようにまとめられます。

![パブリック コンテンツ ワークフローの使用](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

ゲート インポート ワークフローは、外部で管理されている成果物 (たとえば、[Docker Hub][docker-hub]、[GCR][gcr]、[Quay][quay]、[GitHub Container Registry][ghcr]、[Microsoft Container Registry][mcr]、他の [Azure コンテナー レジストリ][acr]などのパブリック レジストリをソースとするイメージ) に対する組織の依存を管理するために役立ちます。 

パブリック コンテンツへの依存によってもたらされるリスクの背景と、Azure Container Registry を使用してリスクを軽減する方法については、[パブリック コンテンツを使用する OCI に関するブログ投稿][oci-consuming-public-content]と [Azure Container Registry を使用したパブリック コンテンツの管理](buffer-gate-public-content.md)に関するページを参照してください。

Azure Cloud Shell または Azure CLI のローカル インストールを使用し、このチュートリアルの作業を完了できます。 Azure CLI バージョン 2.10 以降が推奨されます。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-cli]に関するページを参照してください。

## <a name="scenario-overview"></a>シナリオの概要

![ワークフロー コンポーネントをインポートする](./media/tasks-consume-public-content/consuming-public-content-objects.png)

このチュートリアルでは、次の設定を行います。

1. 以下を表す 3 つの **コンテナー レジストリ**。
   * ベース イメージの変更をサポートするためのシミュレート対象の [Docker Hub][docker-hub] (`publicregistry`)
   * プライベート イメージを共有するためのチーム レジストリ (`contoso`)
   * インポートされたパブリック コンテンツの会社またはチームの共有レジストリ (`baseartifacts`)
1. 各レジストリ内の **ACR タスク**。 これらのタスクで以下を実行します。  
   1. シミュレート対象のパブリック `node` イメージをビルドします
   1. 会社またはチームの共有レジストリに `node` イメージをインポートして検証します
   1. `hello-world` イメージをビルドしてデプロイします
1. 次の構成を含む **ACR タスク定義**。
1. キー コンテナーへのポインターである **レジストリ資格情報** のコレクション
1. キー コンテナーへのポインターである `acr-task.yaml` 内で使用可能な **シークレット** のコレクション
1. `acr-task.yaml` 内で使用される **構成値** のコレクション
1. すべてのシークレットをセキュリティで保護する **Azure キー コンテナー**
1. `hello-world` ビルド アプリケーションをホストする **Azure コンテナー インスタンス**

## <a name="prerequisites"></a>前提条件

次の手順では、チュートリアルで作成および使用されるリソースの値を構成します。

### <a name="set-environment-variables"></a>環境変数の設定

環境に固有の変数を構成します。 永続的なコンテンツを含むリソースを独自のリソース グループに配置して、誤削除を最小限に抑えるためのベスト プラクティスに従います。 ただし、必要に応じて、これらを 1 つのリソース グループに配置することができます。

この記事の例は、bash シェル形式で示してあります。

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Git リポジトリとトークン

環境をシミュレートするには、次の各 Git リポジトリを自分で管理できるリポジトリにフォークします。 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

次に、フォークされたリポジトリの次の変数を更新します。

git の URL の末尾に追加された `:main` は、既定のリポジトリ ブランチを表します。

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

ACR タスクによって Git Webhook を複製して確立するには、[GitHub アクセス トークン (PAT)][git-token] が必要です。 プライベート リポジトリに対して必要なアクセス許可を使用してトークンを作成する手順については、[GitHub アクセス トークンの作成](container-registry-tutorial-build-task.md#create-a-github-personal-access-token)に関するページを参照してください。 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Docker Hub の資格情報  
Docker Hub からイメージをプルするときにスロットリングと ID 要求を回避するには、[Docker Hub トークン][docker-hub-tokens]を作成します。 次に、次の環境変数を設定します。

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>レジストリを作成する

Azure CLI コマンドを使用して、3 つの Premium レベル コンテナー レジストリをそれぞれ別のリソース グループに作成します。

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>キー コンテナーを作成し、シークレットを設定する

キー コンテナーを作成します。

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

キー コンテナーで Docker Hub のユーザー名とトークンを設定します。

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

キー コンテナーで Git PAT を設定して確認します。

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Azure コンテナー インスタンスのリソース グループを作成します

このリソース グループは、後のタスクで `hello-world` イメージをデプロイするときに使用されます。

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>パブリック `node` ベース イメージを作成する

Docker Hub で `node` イメージをシミュレートするには、[ACR タスク][acr-task]を作成して、パブリック イメージをビルドし、維持します。 この設定により、`node` イメージ メンテナンス ツールによる変更をシミュレートすることができます。

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

Docker のスロットリングを回避するには、タスクに [Docker Hub の資格情報][docker-hub-tokens]を追加します。 [acr task authentication][acr-task-credentials] コマンドを使用して、Docker Hub を含む任意のレジストリに Docker の資格情報を渡すことができます。

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

キー コンテナーから値を読み取るアクセス権をタスクに付与します。

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

[タスクをトリガーする][acr-task-triggers]には、Git コミット、ベース イメージの更新、タイマー、または手動実行を使用します。 

タスクを手動で実行して、`node` イメージを生成します。

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

シミュレート対象のパブリック レジストリ内のイメージを列挙します。

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>`hello-world` イメージを作成する

シミュレート対象のパブリック `node` イメージに基づいて、`hello-world` イメージをビルドします。

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>シミュレート対象のパブリック レジストリへのプル アクセス用のトークンを作成する

スコープを `pull` に指定して、シミュレート対象のパブリック レジストリへの[アクセス トークン][acr-tokens]を作成します。 次に、それをキー コンテナーに設定します。

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Azure Container Instances によるプル アクセス用のトークンを作成する

プルするスコープが指定された `hello-world` イメージをホストするレジストリへの[アクセス トークン][acr-tokens]を作成します。 次に、それをキー コンテナーに設定します。

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>`hello-world` イメージをビルドおよび維持するタスクを作成する

次のコマンドを使用して、`hello-world` リポジトリ内の `acr-tasks.yaml` の定義からタスクを作成します。 このタスク ステップで、`hello-world` イメージをビルドしてから、Azure Container Instances にデプロイします。 この Azure Container Instances 用リソース グループは、前のセクションで作成されました。 `image:tag` に違いが 1 つあるだけのタスク内で `az container create` を呼び出すと、このチュートリアル全体で同じインスタンスにデプロイされます。

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

シミュレート対象のパブリック レジストリのタスクに資格情報を追加します。

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

キー コンテナーから値を読み取るアクセス権をタスクに付与します。

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

リソース グループにアクセス権を付与することによって、Azure Container Instances を作成および管理するアクセス権をタスクに付与します。

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

タスクを作成して構成したら、タスクを実行して `hello-world` イメージをビルドおよびデプロイします。

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

作成したら、`hello-world` イメージをホストしているコンテナーの IP アドレスを取得します。

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

ブラウザーでその IP アドレスにアクセスし、実行中のアプリケーションを確認します。

## <a name="update-the-base-image-with-a-questionable-change"></a>"疑わしい" 変更を使用してベース イメージを更新する

このセクションでは、環境内で問題の原因となっている可能性のあるベース イメージに対して変更をシミュレートします。

1. フォークされた `base-image-node` リポジトリ内の `Dockerfile` を開きます。
1. `BACKGROUND_COLOR` を `Orange` に変更して、変更をシミュレートします。

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

変更をコミットし、ACR タスクによって自動的にビルドが開始されることを監視します。

タスクによって次の実行が開始されることを監視します。

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

最終的には、`Commit` の TRIGGER に基づいて STATUS `Succeeded` が表示されます。

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

**Ctrl + C** キーを押して監視コマンドを終了し、最新の実行のログを表示します。

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

`node` イメージが完了すると、ACR タスクの `watch` によって自動的に `hello-world` イメージのビルドが開始されます。

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

最終的には、`Image Update` の TRIGGER に基づいて STATUS `Succeeded` が表示されます。

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

**Ctrl + C** キーを押して監視コマンドを終了し、最新の実行のログを表示します。

```azurecli-interactive
az acr task logs -r $REGISTRY
```

完了したら、更新された `hello-world` イメージをホストしているサイトの IP アドレスを取得します。

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

ブラウザーでそのサイトにアクセスします。背景はオレンジ色 (疑わしい) のはずです。

### <a name="checking-in"></a>チェックイン

ここまでに、Git コミットに基づいて自動的にビルドされ、ベース `node` イメージに変更される `hello-world` イメージを作成しました。 この例では、タスクによって、Azure Container Registry のベース イメージに対してビルドされますが、サポートされている任意のレジストリを使用できます。

`node` イメージが更新されると、ベース イメージの更新によって、タスクの実行が自動的に再トリガーされます。 ここで見られるように、すべての更新が必要なわけではありません。

## <a name="gated-imports-of-public-content"></a>パブリック コンテンツのゲート インポート

アップストリームの変更によって重要なワークロードが中断しないように、セキュリティ スキャンと機能テストを追加することができます。

このセクションでは、以下を行う ACR タスクを作成します。

* テスト イメージをビルドする
* テスト イメージに対して機能テスト スクリプト `./test.sh` を実行する
* イメージのテストに成功したら、パブリック イメージを **baseimages** レジストリにインポートする

### <a name="add-automation-testing"></a>自動化テストを追加する

アップストリーム コンテンツをゲートするために、自動テストが実装されています。 この例では、`$BACKGROUND_COLOR` を確認する `test.sh` が用意されています。 テストに失敗した場合、`1` の `EXIT_CODE` が返され、それによって ACR タスク ステップが失敗し、タスクの実行が終了します。 テストは、結果のログ記録など、あらゆる形式のツールで拡張できます。 ゲートは、以下で再現しているように、スクリプト内の成功または失敗の応答によって管理されます。

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>タスクの YAML 

`import-baseimage-node` リポジトリ内の `acr-task.yaml` を確認します。これにより、次の手順が実行されます。

1. 次の Dockerfile を使用してテスト ベース イメージをビルドします。
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. 完了したら、`./test.sh` を実行するコンテナーを実行してイメージを検証します
1. 正常に完了した場合にのみ、`when: ['validate-base-image']` を使用してゲートされるインポート手順を実行します

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>ベース イメージをインポートしてテストするタスクを作成する

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

シミュレート対象のパブリック レジストリのタスクに資格情報を追加します。

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

キー コンテナーから値を読み取るアクセス権をタスクに付与します。

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

インポート タスクを実行します。

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> `./test.sh: Permission denied` が原因でタスクが失敗した場合は、スクリプトに実行アクセス許可があることを確認し、Git リポジトリにコミットし直します。
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>`hello-world` イメージを更新してゲートされた `node` イメージからビルドする

[アクセス トークン][acr-tokens]を作成して、`node` リポジトリから `read` にスコープされたベース成果物のレジストリにアクセスします。 次に、キー コンテナーに設定します。

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

ベース成果物のレジストリの **hello-world** タスクに資格情報を追加します。

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

タスクを更新して、`BASE_ARTIFACTS` レジストリを使用するように `REGISTRY_FROM_URL` を変更する

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

**hello-world** タスクを実行して、ベース イメージの依存関係を変更します。

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>"有効な" 変更を使用してベース イメージを更新する

1. `base-image-node` リポジトリ内の `Dockerfile` を開きます。
1. `BACKGROUND_COLOR` を `Green` に変更して有効な変更をシミュレートします。

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

変更をコミットし、更新のシーケンスを監視します。

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

実行したら、**Ctrl + C** キーを押し、ログを監視します。

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

完了したら、**base-image-import** タスクを監視します。

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

実行したら、**Ctrl + C** キーを押し、ログを監視します。

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

完了したら、**hello-world** タスクを監視します。

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

実行したら、**Ctrl + C** キーを押し、ログを監視します。

```azurecli-interactive
az acr task logs -r $REGISTRY
```

完了したら、更新された `hello-world` イメージをホストしているサイトの IP アドレスを取得します。

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

ブラウザーでそのサイトにアクセスします。背景は緑色 (有効) のはずです。

### <a name="view-the-gated-workflow"></a>ゲート ワークフローを表示する

赤色の背景色で、前のセクションの手順をもう一度実行します。

1. `base-image-node` リポジトリ内の `Dockerfile` を開きます
1. `BACKGROUND_COLOR` を `Red` に変更して無効な変更をシミュレートします。

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

変更をコミットし、更新のシーケンスを監視します。

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

実行したら、**Ctrl + C** キーを押し、ログを監視します。

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

完了したら、**base-image-import** タスクを監視します。

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

実行したら、**Ctrl + C** キーを押し、ログを監視します。

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

この時点で、**base-import-node** タスクの検証失敗が表示され、`hello-world` の更新を発行するシーケンスが停止されるはずです。 出力は次のようになります。

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>`hello-world` に更新を発行する

`hello-world` イメージへの変更は、最新の検証済み `node` イメージを使用して続行されます。

ゲート検証に合格したベース `node` イメージに対して追加の変更があると、`hello-world` イメージへのベース イメージの更新がトリガーされます。

## <a name="cleaning-up"></a>クリーンアップ

この記事で使用したリソースが不要になったら、削除してください。

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>次のステップ

この記事の内容。 ACR タスクを使用して自動ゲート ワークフローを作成し、更新されたベース イメージを環境に導入しました。 Azure Container Registry でイメージを管理するには、関連情報を参照してください。


* [コンテナー イメージのタグ付けとバージョン管理に関する推奨事項](container-registry-image-tag-version.md)
* [Azure コンテナー レジストリのコンテナー イメージをロックする](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io