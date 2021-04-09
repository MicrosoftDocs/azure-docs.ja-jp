---
title: GitHub アクションによるコンテナー インスタンスのデプロイ
description: コンテナー イメージを構築し、Azure Container Instances にプッシュおよびデプロイする手順を自動化する GitHub アクションを構成します
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 1409d8fc1430cd9bf67bd735d9826a74979d495b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98762968"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>GitHub アクションを構成してコンテナー インスタンスを作成する

[GitHub Actions](https://docs.github.com/en/actions) は GitHub の一連の機能であり、コードを格納するのと同じ場所でソフトウェア開発ワークフローを自動化したり、 pull request や問題に対して共同作業を行ったりするために使用します。

[[Deploy to Azure Container Instances]\(Azure Container Instances にデプロイ\)](https://github.com/azure/aci-deploy) GitHub アクションを使用して、Azure Container Instances への単一のコンテナーのデプロイを自動化します。 このアクションでは、[az container create][az-container-create] コマンドと同様にコンテナー インスタンスのプロパティを設定できます。

この記事では、以下のアクションを実行するワークフローを GitHub リポジトリで設定する方法について説明します。

* Dockerfile からのイメージのビルド
* Azure コンテナー レジストリにイメージをプッシュする
* Azure コンテナー インスタンスにコンテナー イメージをデプロイする

この記事では、ワークフローを設定する 2 つの方法を示します。

* [GitHub ワークフローを構成する](#configure-github-workflow) - [Deploy to Azure Container Instances] (Azure Container Instances にデプロイ) アクションやその他のアクションを使用して、GitHub リポジトリでワークフローを作成します。  
* [CLI 拡張機能を使用する](#use-deploy-to-azure-extension) - Azure CLI の [[Azure に配置する]](https://github.com/Azure/deploy-to-azure-cli-extension) 拡張機能で `az container app up` コマンドを使用します。 このコマンドは、GitHub ワークフローの作成とデプロイ手順を効率化します。

> [!IMPORTANT]
> Azure Container Instances 用の GitHub アクションは現在、プレビュー段階です。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="prerequisites"></a>前提条件

* **GitHub アカウント** - まだお持ちでない場合は、 https://github.com でアカウントを作成します。
* **Azure CLI** - Azure Cloud Shell または Azure CLI のローカル インストールを使用して、Azure CLI の手順を実行できます。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。
* **Azure コンテナー レジストリ** - ない場合は、[Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)、[Azure portal](../container-registry/container-registry-get-started-portal.md)、またはその他の方法を使用して Basic レベルで Azure コンテナー レジストリを作成します。 デプロイに使用されるリソース グループをメモしておきます。これは、GitHub ワークフローに使用されます。

## <a name="set-up-repo"></a>リポジトリの設定

* この記事の例では、GitHub を使用して次のリポジトリをフォークします: https://github.com/Azure-Samples/acr-build-helloworld-node

  このリポジトリには、小規模な Web アプリのコンテナー イメージを作成するための Dockerfile とソース ファイルが含まれています。

  ![GitHub の [フォーク] ボタン (マーク済み) のスクリーンショット](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* リポジトリに対して Actions が有効になっていることを確認します。 フォークされたリポジトリに移動し、 **[Settings]**  >  **[Actions]\(アクション\)** を選択します。 **[Actions permissions]\(アクションのアクセス許可\)** で、 **[Enable local and third party Actions for this repository]\(このリポジトリに対してローカルおよびサード パーティのアクションを有効にする\)** が選択されていることを確認します。

## <a name="configure-github-workflow"></a>GitHub ワークフローを構成する

### <a name="create-service-principal-for-azure-authentication"></a>Azure 認証のサービス プリンシパルを作成する

GitHub ワークフローでは、Azure CLI に対して認証を行うための Azure 資格情報を指定する必要があります。 次の例では、共同作成者のロールを持ち、コンテナー レジストリのリソース グループをスコープとするサービス プリンシパルを作成します。

まず、リソース グループのリソース ID を取得します。 次の [az group show][az-group-show] コマンドでは、自分のグループの名前に置き換えます。

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

サービス プリンシパルを作成するには、[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] を使用します。

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

出力は次のようになります。

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

JSON 出力は、後の手順で使用されるため保存します。 また、次のセクションでサービス プリンシパルを更新する必要がある `clientId` もメモしておいてください。

### <a name="update-service-principal-for-registry-authentication"></a>レジストリ認証のサービス プリンシパルを更新する

Azure サービス プリンシパルの資格情報を更新して、コンテナー レジストリに対するプッシュとプルのアクセスを許可します。 この手順により、GitHub ワークフローでサービス プリンシパルを使用して、[コンテナー レジストリに対する認証](../container-registry/container-registry-auth-service-principal.md)と、Docker イメージのプッシュおよびプルを実行できます。 

コンテナー レジストリのリソース ID を取得します。 次の [az acr show][az-acr-show] コマンドでは、自分のレジストリの名前に置き換えます。

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

[az role assignment create][az-role-assignment-create] を使用して、レジストリに対するプッシュおよびプル アクセスを付与する AcrPush ロールを割り当てます。 サービス プリンシパルのクライアント ID は自分のものに置き換えてください。

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>資格情報を GitHub リポジトリに保存する

1. GitHub UI で、フォークされたリポジトリに移動し、 **[設定]**  >  **[シークレット]** を選択します。 

1. **[Add a new secret]\(新しいシークレットの追加\)** を選択して、次のシークレットを追加します。

|Secret  |値  |
|---------|---------|
|`AZURE_CREDENTIALS`     | サービス プリンシパルの作成ステップからの JSON 出力全体 |
|`REGISTRY_LOGIN_SERVER`   | レジストリのログイン サーバー名 (すべて小文字)。 例: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  サービス プリンシパルの作成からの JSON 出力からの `clientId`       |
|`REGISTRY_PASSWORD`     |  サービス プリンシパルの作成からの JSON 出力からの `clientSecret` |
| `RESOURCE_GROUP` | サービス プリンシパルのスコープ指定に使用したリソース グループの名前 |

### <a name="create-workflow-file"></a>ワークフロー ファイルを作成する

1. GitHub UI で、 **[Actions]\(アクション\)**  >  **[New workflow]\(新しいワークフロー\)** を選択します。
1. **[Set up a workflow yourself]\(ワークフローを自分でセットアップする\)** を選択します。
1. **[Edit new file]\(新しいファイルの編集\)** で、次の YAML の内容を貼り付けてサンプル コードを上書きします。 既定のファイル名 `main.yml` をそのまま使用するか、選択したファイル名を指定します。
1. **[Start commit]\(コミットの開始\)** を選択し、コミットの短い説明と詳細な説明を必要に応じて入力し、 **[Commit new file]\(新しいファイルのコミット\)** を選択します。

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@main
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>ワークフローを検証する

ワークフロー ファイルをコミットすると、ワークフローがトリガーされます。 ワークフローの進行状況を確認するには、 **[アクション]**  >  **[ワークフロー]** に移動します。 

![ワークフローの進行状況を表示する](./media/container-instances-github-action/github-action-progress.png)

ワークフローの各手順の状態と結果の表示については、「[ワークフロー実行の履歴を表示する](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history)」を参照してください。 ワークフローが完了しない場合は、[ログの表示とエラーの診断](https://docs.github.com/en/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures)に関するページを参照してください。

ワークフローが正常に完了したら、[az container show][az-container-show] コマンドを実行して、*aci-sampleapp* という名前のコンテナー インスタンスの情報を取得します。 リソース グループの名前は自分のものに置き換えてください。 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

出力は次のようになります。

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

インスタンスがプロビジョニングされたら、ブラウザーでコンテナーの FQDN にアクセスして、実行中の Web アプリを表示します。

![実行中の Web アプリのブラウザー表示](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>[Azure に配置する] 拡張機能の使用

または、Azure CLI の [[Azure に配置する] 拡張機能](https://github.com/Azure/deploy-to-azure-cli-extension)を使用してワークフローを構成します。 拡張機能の `az container app up` コマンドは、Azure Container Instances にデプロイするワークフローを設定するための入力パラメーターを受け取ります。 

Azure CLI によって作成されるワークフローは、[GitHub を使用して手動で作成](#configure-github-workflow)できるワークフローに似ています。

### <a name="additional-prerequisite"></a>追加の前提条件

このシナリオの [前提条件](#prerequisites)と [リポジトリ設定](#set-up-repo)に加えて、Azure CLI の **[Azure に配置する] 拡張機能** をインストールする必要があります。

[az extension add][az-extension-add] コマンドを実行して、拡張機能をインストールします。

```azurecli
az extension add \
  --name deploy-to-azure
```

拡張機能の検索、インストール、および管理の詳細については、「[Azure CLI で拡張機能を使用する](/cli/azure/azure-cli-extensions-overview)」を参照してください。

### <a name="run-az-container-app-up"></a>`az container app up` を実行します。

[az container app up][az-container-app-up] コマンドを実行するには、少なくとも以下を指定します。

* Azure コンテナー レジストリの名前。例: *myregistry*
* GitHub リポジトリの URL。例: `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

サンプル コマンド:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>コマンドの進行状況

* プロンプトが表示されたら、GitHub 資格情報を入力するか、*repo* および *user* スコープを持つ [GitHub 個人用アクセス トークン](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) (PAT) を入力して、GitHub アカウントで認証します。 GitHub 資格情報を入力した場合、コマンドによって PAT が作成されます。 追加のプロンプトに従って、ワークフローを構成します。

* このコマンドは、ワークフローのリポジトリ シークレットを作成します。

  * Azure CLI 用のサービス プリンシパル資格情報
  * Azure コンテナー レジストリにアクセスするための資格情報

* コマンドによってワークフロー ファイルがリポジトリにコミットされた後、ワークフローがトリガーされます。 

出力は次のようになります。

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

ワークフローの状態と各手順の結果を GitHub UI で表示する方法については、「[ワークフロー実行の履歴を表示する](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history)」を参照してください。

### <a name="validate-workflow"></a>ワークフローを検証する

ワークフローは、GitHub リポジトリのベース名 (この場合は *acr-build-helloworld-node*) を使用して Azure コンテナー インスタンスをデプロイします。 ワークフローが正常に完了したら、[az container show][az-container-show] コマンドを実行して、*acr-build-helloworld-node* という名前のコンテナー インスタンスの情報を取得します。 リソース グループの名前は自分のものに置き換えてください。 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

出力は次のようになります。

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

インスタンスがプロビジョニングされたら、ブラウザーでコンテナーの FQDN にアクセスして、実行中の Web アプリを表示します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[az container delete][az-container-delete] コマンドを使用してコンテナー インスタンスを停止します。

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

リソース グループとその中のすべてのリソースを削除するには、[az group delete][az-group-delete] コマンドを実行します。

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>次のステップ

開発ワークフローを自動化するためのその他のアクションについて [GitHub マーケットプレース](https://github.com/marketplace?type=actions)で確認します


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
