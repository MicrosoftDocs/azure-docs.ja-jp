---
title: GitHub アクションからのカスタム コンテナー CI/CD
description: GitHub アクションを使用してカスタム Linux コンテナーを CI/CD パイプラインから App Service にデプロイする方法について説明します。
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: 1fe09970bcb9b9432b9b6f22de04bb24f1e84fa8
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761767"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>GitHub Actions を使用した App Service へのカスタム コンテナーのデプロイ

[GitHub Actions](https://docs.github.com/en/actions) を使用すると、自動化されたソフトウェア開発ワークフローを柔軟に構築できます。 [Azure Web Deploy アクション](https://github.com/Azure/webapps-deploy)を使用すれば、カスタム コンテナーを [App Service ](overview.md) にデプロイするワークフローを、GitHub Actions を使用して自動化できます。

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローに含まれるさまざまな手順とパラメーターが含まれています。

Azure App Service のコンテナー ワークフロー ファイルには、次の 3 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**認証** | 1.サービス プリンシパルまたは発行プロファイルを取得します。 <br /> 2.GitHub シークレットを作成します。 |
|**ビルド** | 1.環境を作成します。 <br /> 2.コンテナー イメージをビルドします。 |
|**デプロイする** | 1.コンテナー イメージをデプロイする |

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- GitHub アカウント。 ない場合は、[無料](https://github.com/join)でサインアップしてください。 Azure App Service にデプロイするには、GitHub リポジトリにコードが必要です。 
- コンテナー用の作業コンテナー レジストリと Azure App Service アプリ。 この例では、Azure Container Registry を使用します。 コンテナー用の Azure App Service への完全デプロイを確実に完了してください。 通常の Web アプリと異なり、コンテナー用の Web アプリには既定のランディング ページがありません。 実際の例を得るには、コンテナーを発行します。
    - [コンテナー化された Node.js アプリケーションを Docker を使って作成し、コンテナー イメージをレジストリにプッシュした後、イメージを Azure App Service にデプロイする方法をご確認ください](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>デプロイ資格情報を生成する

GitHub Actions 用の Azure App Services での認証で推奨される方法は、発行プロファイルを使用することです。 サービス プリンシパルを使用して認証することもできますが、そのプロセスにはさらに多くの手順が必要です。 

発行プロファイル資格情報またはサービス プリンシパルを [GitHub シークレット](https://docs.github.com/en/actions/reference/encrypted-secrets)として保存して、Azure で認証します。 ワークフロー内のシークレットにアクセスします。 

# <a name="publish-profile"></a>[発行プロファイル](#tab/publish-profile)

発行プロファイルは、アプリレベルの資格情報です。 発行プロファイルを GitHub シークレットとして設定します。 

1. Azure portal で、お使いのアプリ サービスに移動します。 

1. **[概要]** ページで、 **[発行プロファイルの取得]** オプションを選択します。

    > [!NOTE]
    > 2020 年 10 月の時点で、Linux Web アプリでは、**ファイルをダウンロードする前に**、アプリ設定 `WEBSITE_WEBDEPLOY_USE_SCM` を `true` に設定する必要があります。 この要件は、今後削除される予定です。 一般的な Web アプリの設定を構成する方法については、「[Azure portal で App Service アプリを構成する](./configure-common.md)」を参照してください。  

1. ダウンロードしたファイルを保存します。 このファイルの内容を使用して、GitHub シークレットを作成します。

# <a name="service-principal"></a>[サービス プリンシパル](#tab/service-principal)

[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)は、[Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを使用して作成できます。 このコマンドは、Azure portal で [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[使ってみる]** ボタンを選択して実行します。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

この例で、プレースホルダーをお使いのサブスクリプション ID、リソース グループ名、アプリ名に置き換えます。 これにより、この App Service アプリにアクセスするためのロールの割り当て資格情報を含む JSON オブジェクトが出力されます。 この JSON オブジェクトを後のためにコピーします。

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 常に最小限のアクセス権を付与することをお勧めします。 前の例の範囲は、リソース グループ全体ではなく、特定の App Service アプリに限定されます。

---
## <a name="configure-the-github-secret-for-authentication"></a>認証用の GitHub シークレットを構成する

# <a name="publish-profile"></a>[発行プロファイル](#tab/publish-profile)

[GitHub](https://github.com/) で自分のリポジトリを参照し、 **[設定]、[シークレット]、[Add a new secret]\(新しいシークレットの追加\)** の順に選択します。

[アプリ レベルの資格情報](#generate-deployment-credentials)を使用するには、ダウンロードした発行プロファイルのファイルの内容をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_WEBAPP_PUBLISH_PROFILE` という名前を付けます。

GitHub ワークフローを構成するときに、Azure Web アプリをデプロイするアクションの中で `AZURE_WEBAPP_PUBLISH_PROFILE` を使用します。 次に例を示します。
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[サービス プリンシパル](#tab/service-principal)

[GitHub](https://github.com/) で自分のリポジトリを参照し、 **[設定]、[シークレット]、[Add a new secret]\(新しいシークレットの追加\)** の順に選択します。

[ユーザー レベルの資格情報](#generate-deployment-credentials)を使用するには、Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` などの名前を付けます。

後でワークフロー ファイルを構成する場合は、Azure ログイン アクションの入力 `creds` にシークレットを使用します。 次に例を示します。

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>レジストリ用の GitHub シークレットを構成する

Docker ログイン アクションで使用するシークレットを定義します。 このドキュメントの例では、コンテナー レジストリ用に Azure Container Registry を使用しています。 

1. Azure portal または Docker でお使いのコンテナーにアクセスし、ユーザー名とパスワードをコピーします。 Azure Container Registry のユーザー名とパスワードは、Azure portal の、お使いのレジストリの **[設定]**  >  **[アクセス キー]** で確認できます。 

2. レジストリ ユーザー名のために `REGISTRY_USERNAME` という名前の新しいシークレットを定義します。 

3. レジストリ パスワードのために `REGISTRY_PASSWORD` という名前の新しいシークレットを定義します。 

## <a name="build-the-container-image"></a>コンテナー イメージのビルド

次の例は、Node.js Docker イメージをビルドするワークフローの一部を示しています。 [Docker Login](https://github.com/azure/docker-login) を使用してプライベート コンテナー レジストリにログインします。 この例では Azure Container Registry を使用しますが、他のレジストリでも同じアクションを実行できます。 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

また、[Docker Login](https://github.com/azure/docker-login) を使用して複数のコンテナー レジストリに同時にログインすることもできます。 この例には、docker.io を使用した認証用の 2 つの新しい GitHub シークレットが含まれています。 この例は、レジストリのルート レベルに Dockerfile があることを前提としています。 

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>App Service コンテナーへのデプロイ

App Service のカスタム コンテナーにイメージをデプロイするには、`azure/webapps-deploy@v2` アクションを使用します。 このアクションには 7 つのパラメーターがあります。

| **パラメーター**  | **説明**  |
|---------|---------|
| **app-name** | (必須) App Service アプリの名前 | 
| **publish-profile** | (オプション) Web アプリ (Windows および Linux) および Web アプリ コンテナー (Linux) に適用されます。 複数コンテナー シナリオはサポートされていません。 Web 配置のシークレットでプロファイル (\*.publishsettings) ファイルの内容を発行します | 
| **slot-name** | (オプション) 運用スロット以外の既存のスロットを入力します。 |
| **package** | (オプション) Web アプリのみに適用されます。パッケージまたはフォルダーへのパス。 デプロイする \*.zip、\*.war、\*.jar またはフォルダー |
| **images** | (必須) Web アプリ コンテナーのみに適用されます。完全修飾コンテナー イメージ名を指定します。 たとえば、' myregistry.azurecr.io/nginx:latest ' や ' python:3.7.2-alpine/' などです。 複数コンテナー アプリの場合は、複数のコンテナー イメージ名を指定できます (複数行で区切ります)。 |
| **configuration-file** | (オプション) Web アプリ コンテナーのみに適用されます。Docker-Compose ファイルのパス。 完全修飾パスか、既定の作業ディレクトリを基準とした相対パスのいずれかです。 複数コンテナー アプリの場合は必須です。 |
| **スタートアップ コマンド** | (オプション) スタートアップ コマンドを入力します。 たとえば、 dotnet run or dotnet filename.dll |

# <a name="publish-profile"></a>[発行プロファイル](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[サービス プリンシパル](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

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
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>次のステップ

GitHub には、一連のアクションが別々のリポジトリにあります。それぞれには、CI/CD に GitHub を使用し、ご自身のアプリを Azure にデプロイするときに役立つドキュメントとサンプルが含まれています。

- [Azure にデプロイするためのアクション ワークフロー](https://github.com/Azure/actions-workflow-samples)

- [Azure ログイン](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker でのログイン/ログアウト](https://github.com/Azure/docker-login)

- [ワークフローをトリガーするイベント](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s のデプロイ](https://github.com/Azure/k8s-deploy)

- [スターター ワークフロー](https://github.com/actions/starter-workflows)