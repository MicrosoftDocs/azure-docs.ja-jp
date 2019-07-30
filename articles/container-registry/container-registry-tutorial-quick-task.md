---
title: チュートリアル - クラウドでコンテナー イメージをビルドする - Azure Container Registry タスク
description: このチュートリアルでは、Azure Container Registry Task (ACR Task) 使用して Azure で Docker コンテナー イメージをビルドして、Azure Container Instances にデプロイする方法を説明します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: a9e84210427612143bffe33efe4a5da5364b7a22
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310453"
---
# <a name="tutorial-build-and-deploy-container-images-in-the-cloud-with-azure-container-registry-tasks"></a>チュートリアル:Azure Container Registry タスクを使用して、クラウドでコンテナー イメージをビルドしてデプロイする

**ACR Task** は、Azure Container Registry 内の機能スイートで、Azure での合理的かつ効率的な Docker コンテナー イメージ ビルドを実現します。 この記事では、ACR Task の*クイック タスク*機能を使用する方法を説明します。

「内部ループ」開発サイクルは、コードの記述、ビルド、およびソース管理にコミットする前のアプリケーションのテストの反復的なプロセスです。 クイック タスクは「内部ループ」をクラウドに拡張し、ビルド成功の検証と、正常にビルドされたイメージのコンテナー レジストリへの自動プッシュを提供します。 イメージは、お使いのレジストリの近くのクラウドにネイティブにビルドされるため、デプロイが高速化されます。

Dockerfile に関する専門知識をすべて、ACR Task に直接転送できます。 ACR Task を使用してクラウドにビルドするために Dockerfile を変更する必要はありません。必要なのはコマンドの実行だけです。 

このチュートリアルでは、シリーズの第 1 部で次のことを行います。

> [!div class="checklist"]
> * サンプル アプリケーションのソース コードを取得する
> * Azure でコンテナー イメージをビルドする
> * コンテナーを Azure Container Instances にデプロイする

以降のチュートリアルでは、コードのコミット時と基本イメージ更新時に自動化されたコンテナー イメージ ビルド用に ACR Task を使用する方法を説明します。 ACR タスクでは、[複数ステップ タスク](container-registry-tasks-multi-step.md)を実行することもできます。その場合、YAML ファイルを使用して、複数のコンテナーをビルド、プッシュ、および (必要に応じて) テストする手順を定義します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルで使用する場合は、Azure CLI のバージョン **2.0.46** 以降がインストールされていて、[az login][az-login] を使用してログインしている必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="github-account"></a>GitHub アカウント

まだお持ちでない場合は、 https://github.com でアカウントを作成します。 このチュートリアル シリーズでは、GitHub リポジトリを使用して ACR Task での自動化されたイメージ ビルドを示します。

### <a name="fork-sample-repository"></a>サンプル リポジトリのフォーク

次に、GitHub UI を使用して、サンプル リポジトリを GitHub アカウントにフォークします。 このチュートリアルでは、ソースからのコンテナー イメージをリポジトリにビルドし、次のチュートリアルでは、リポジトリのフォークにコミットをプッシュして自動化されたタスクを開始します。

以下でリポジトリをフォーク: https://github.com/Azure-Samples/acr-build-helloworld-node

![GitHub の [フォーク] ボタン (マーク済み) のスクリーンショット][quick-build-01-fork]

### <a name="clone-your-fork"></a>フォークの複製

リポジトリをフォークしたら、フォークを複製して、ローカル クローンを含むディレクトリを入力します。

`git` を使用してリポジトリを複製し、 **\<your-github-username\>** を GitHub のユーザー名に置き換えます。

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

ソース コードを含むディレクトリを入力します。

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Bash シェル

このチュートリアルの一連のコマンドは、Bash シェル用にフォーマットされています。 PowerShell、コマンド プロンプト、または他のシェルを使用したい場合は、必要に応じて行継続と環境変数の調整が必要になる場合があります。

## <a name="build-in-azure-with-acr-tasks"></a>ACR Task を使用した Azure でのビルド

これで、コンピューターにソース コードを取得したので、次の手順に従ってコンテナー レジストリを作成して、ACR Task を使用してコンテナー イメージをビルドします。

サンプル コマンドの実行を簡単にするために、このシリーズのチュートリアルではシェル環境変数を使用します。 次のコマンドを実行して、`ACR_NAME` 変数を設定します。 **\<registry-name\>** を新しいコンテナー レジストリの一意の名前に置き換えます。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 このチュートリアルで作成する他のリソースはこの名前に基づくため、この最初の変数のみ変更する必要があります。

```azurecli-interactive
ACR_NAME=<registry-name>
```

コンテナー レジストリ環境変数は入力済みなので、チュートリアルの残りのコマンドをコピーして貼り付けられるようになりました。値を編集する必要はありません。 次のコマンドを実行して、リソース グループとコンテナー レジストリを作成します。

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

レジストリができたので、ACR Task を使用して、サンプル コードからコンテナー イメージをビルドします。 [az acr build][az-acr-build] コマンドを実行して、*クイック タスク*を実行します。

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
```

[az acr build][az-acr-build] コマンドの出力は、次のようになります。 ソース コード ("コンテキスト") の Azure へのアップロードと、クラウドで ACR Task によって実行された `docker build` 操作の詳細を表示できます。 ACR Task は `docker build` を使用してイメージをビルドするため、Dockerfile を変更することなく ACR Task の使用をすぐに開始できます。

```console
$ az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
Packing source code into tar file to upload...
Sending build context (4.813 KiB) to ACR...
Queued a build with build ID: da1
Waiting for build agent...
2018/08/22 18:31:42 Using acb_vol_01185991-be5f-42f0-9403-a36bb997ff35 as the home volume
2018/08/22 18:31:42 Setting up Docker configuration...
2018/08/22 18:31:43 Successfully set up Docker configuration
2018/08/22 18:31:43 Logging in to registry: myregistry.azurecr.io
2018/08/22 18:31:55 Successfully logged in
Sending build context to Docker daemon   21.5kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 88087d7e709a
Step 3/5 : RUN cd /src && npm install
 ---> Running in e80e1263ce9a
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.1s
Removing intermediate container e80e1263ce9a
 ---> 26aac291c02e
Step 4/5 : EXPOSE 80
 ---> Running in 318fb4c124ac
Removing intermediate container 318fb4c124ac
 ---> 113e157d0d5a
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in fe7027a11787
Removing intermediate container fe7027a11787
 ---> 20a27b90eb29
Successfully built 20a27b90eb29
Successfully tagged myregistry.azurecr.io/helloacrtasks:v1
2018/08/22 18:32:11 Pushing image: myregistry.azurecr.io/helloacrtasks:v1, attempt 1
The push refers to repository [myregistry.azurecr.io/helloacrtasks]
6428a18b7034: Preparing
c44b9827df52: Preparing
172ed8ca5e43: Preparing
8c9992f4e5dd: Preparing
8dfad2055603: Preparing
c44b9827df52: Pushed
172ed8ca5e43: Pushed
8dfad2055603: Pushed
6428a18b7034: Pushed
8c9992f4e5dd: Pushed
v1: digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1 size: 1366
2018/08/22 18:32:43 Successfully pushed image: myregistry.azurecr.io/helloacrtasks:v1
2018/08/22 18:32:43 Step ID acb_step_0 marked as successful (elapsed time in seconds: 15.648945)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloacrtasks
    tag: v1
    digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git: {}

Run ID: da1 was successful after 1m9.970148252s
```

出力の最後のほうに、ACR Task によって、イメージの検出された依存関係が表示されます。 これにより、ACR Task は基本イメージの更新時 (OS またはフレームワークの修正プログラムによる基本イメージの更新時) にイメージ ビルドを自動化できます。 基本イメージの更新プログラムの ACR Task のサポートについては、このチュートリアル シリーズで後ほど説明します。

## <a name="deploy-to-azure-container-instances"></a>Azure Container Instances へのデプロイ

ACR Task によって、既定でビルド イメージがレジストリに自動的に正常にプッシュされるため、レジストリからすぐにイメージをデプロイできます。

このセクションでは、Azure Key Vault とサービス プリンシパルを作成し、サービス プリンシパルの資格情報を使用して Azure Container Instances (ACI) にコンテナーをデプロイします。

### <a name="configure-registry-authentication"></a>レジストリの認証を構成する

すべての運用環境のシナリオでは、[サービス プリンシパル][service-principal-auth]を使用して、Azure コンテナー レジストリにアクセスする必要があります。 サービス プリンシパルを使用すると、コンテナー イメージへのロールベースのアクセス制御を提供できます。 たとえば、レジストリに対するプルのみのアクセス権を持つサービス プリンシパルを設定できます。

#### <a name="create-a-key-vault"></a>Key Vault を作成します

[Azure キー コンテナー](/azure/key-vault/)にコンテナーがない場合、次のコマンドを使用して Azure CLI で 1 つ作成します。

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-a-service-principal-and-store-credentials"></a>サービス プリンシパルを作成し、資格情報を格納する

今度は、サービス プリンシパルを作成し、その資格情報をキー コンテナーに格納する必要があります。

[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] command to create the service principal, and [az keyvault secret set][az-keyvault-secret-set]コマンドを使用してサービス プリンシパルを作成し、サービス プリンシパルの**パスワード**を資格情報コンテナーに格納します。

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

上記のコマンドの `--role` 引数により、*acrpull* ロールを持つサービス プリンシパルが構成されます。これにより、レジストリに対するプルのみのアクセス権が付与されます。 プッシュ アクセス権とプル アクセス権の両方を付与するには、`--role` 引数を *acrpush* に変更します。

次にサービス プリンシパルの *appId* を資格情報コンテナーに格納します。appId は、認証のために Azure Container Registry に渡す**ユーザー名**です。

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Azure キー コンテナーを作成してに 2 つのシークレットを格納します。

* `$ACR_NAME-pull-usr`:サービス プリンシパル ID。コンテナー レジストリの**ユーザー名**として使用します。
* `$ACR_NAME-pull-pwd`:サービス プリンシパルのパスワード。コンテナー レジストリの**パスワード**として使用します。

これらのシークレットは、アプリケーションおよびサービスがレジストリからイメージをプルしたときの名前で参照できます。

### <a name="deploy-a-container-with-azure-cli"></a>Azure CLI でコンテナーをデプロイする

これでサービス プリンシパルの資格情報が Azure Key Vault シークレットに格納されたので、格納した資格情報をアプリケーションとサービスで使用してプライベート レジストリにアクセスできます。

次の [az container create][az-container-create] コマンドを実行して、コンテナー インスタンスをデプロイします。 このコマンドは、Azure Key Vault に格納されたサービス プリンシパルの資格情報を使用して、コンテナー レジストリに対する認証を行います。

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-tasks \
    --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-tasks-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

前のコマンドが、コンテナーの DNS 名のラベルにコンテナーのレジストリの名前を追加するため、`--dns-name-label` の値は Azure 内で一意でなければなりません。 コマンドの出力には、次のようにコンテナーの完全修飾ドメイン名 (FQDN) が表示されます。

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-tasks \
>     --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-tasks-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
----------------------------------------------
acr-tasks-myregistry.eastus.azurecontainer.io
```

コンテナーの FQDN を書き留めます。これは次のセクションで使用します。

### <a name="verify-the-deployment"></a>デプロイを検証する

コンテナーの起動プロセスを監視するには、[az container attach][az-container-attach] コマンドを使用します。

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-tasks
```

`az container attach` 出力では、イメージを取得して開始するときに最初にコンテナーの状態が表示され、次にローカル コンソールの STDOUT と STDERR がコンテナーにバインドされます。

```console
$ az container attach --resource-group $RES_GROUP --name acr-tasks
Container 'acr-tasks' is in state 'Running'...
(count: 1) (last timestamp: 2018-08-22 18:39:10+00:00) pulling image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2018-08-22 18:39:15+00:00) Successfully pulled image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2018-08-22 18:39:17+00:00) Created container
(count: 1) (last timestamp: 2018-08-22 18:39:17+00:00) Started container

Start streaming logs:
Server running at http://localhost:80
```

`Server running at http://localhost:80` が表示されたら、ブラウザーでコンテナーの FQDN に移動して、実行中のアプリケーションを表示します。 `az container create`前のセクションで実行されたコマンドの出力に FQDN が表示されている必要があります。

![ブラウザーに表示されたサンプル アプリケーションのスクリーンショット][quick-build-02-browser]

コンテナーからコンソールをデタッチするには、`Control+C` を押します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[az container delete][az-container-delete] コマンドを使用してコンテナー インスタンスを停止します。

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-tasks
```

このチュートリアルで作成した "*すべての*" リソース (コンテナー レジストリ、キー コンテナー、サービス プリンシパルなど) を削除するには、次のコマンドを実行します。 ただし、これらのリソースはシリーズの[次のチュートリアル](container-registry-tutorial-build-task.md)で使用するため、次のチュートリアルに進んでいる場合は削除しないことをお勧めします。

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>次の手順

これで、クイックタスクで内部ループをテストしたので、ソース コードを Git リポジトリにコミットしたときにコンテナー イメージ ビルドをトリガーするように**ビルド タスク**を構成します。

> [!div class="nextstepaction"]
> [タスクによる自動ビルドのトリガー](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-login]: /cli/azure/reference-index#az-login
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
