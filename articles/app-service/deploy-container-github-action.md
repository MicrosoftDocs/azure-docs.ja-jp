---
title: GitHub アクションからのカスタム コンテナー CI/CD
description: GitHub アクションを使用してカスタム Linux コンテナーを CI/CD パイプラインから App Service にデプロイする方法について説明します。
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 6af23aba28ce3cda9982878ed08ec515aa25633a
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962606"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>GitHub Actions を使用した App Service へのカスタム コンテナーのデプロイ

[GitHub アクション](https://help.github.com/en/articles/about-github-actions)を使用すると、自動化されたソフトウェア開発ライフサイクル ワークフローを柔軟に構築できます。 [コンテナー向けの Azure App Service Action](https://github.com/Azure/webapps-container-deploy) により、GitHub Actions を使用して、ワークフローを自動化し、カスタム コンテナー [App Service ](overview.md) をデプロイできます。

> [!IMPORTANT]
> GitHub Actions は現在ベータ版です。 まず、ご自分の GitHub アカウントを使用し、[新規登録し、プレビューに参加](https://github.com/features/actions)する必要があります。
> 

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。

Azure App Service のコンテナー ワークフロー ファイルには、次の 3 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**認証** | 1.サービス プリンシパルを定義します。 <br /> 2.GitHub シークレットを作成します。 |
|**ビルド** | 1.環境を設定します。 <br /> 2.コンテナー イメージをビルドします。 |
|**デプロイする** | 1.コンテナー イメージをデプロイする |

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

[Azure CLI](/cli/azure/) の [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用すると、[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を作成できます。 このコマンドは、Azure portal の [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[使ってみる]** ボタンを選択して実行できます。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

上記の例で、プレースホルダーを実際のサブスクリプション ID とリソース グループ名に置き換えます。 これにより、以下のようなご自分の App Service アプリにアクセスするためのロールの割り当て資格情報を含む JSON オブジェクトが出力されます。 この JSON オブジェクトを後のためにコピーします。

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
> 常に最小限のアクセス権を付与することをお勧めします。 上記の Az CLI コマンドでスコープを制限して、特定の App Service アプリと、コンテナーイメージがプッシュされる Azure Container Registry に制限することができます。

## <a name="configure-the-github-secret"></a>GitHub シークレットの構成

[GitHub](https://github.com/) で、自分のリポジトリを参照し、 **[設定] > [シークレット] > [新しいシークレットの追加]** を選択します。

「[サービス プリンシパルの作成](#create-a-service-principal)」からの JSON 出力の内容を秘密の変数の値として貼り付けます。 シークレットに `AZURE_CREDENTIALS` などの名前を付けます。

後でワークフロー ファイルを構成する場合は、Azure ログイン アクションの入力 `creds` にシークレットを使用します。 次に例を示します。

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

同様に、コンテナー レジストリの資格情報に次の追加シークレットを定義し、Docker のログイン アクションで設定します。

- REGISTRY_USERNAME
- REGISTRY_PASSWORD

## <a name="build-the-container-image"></a>コンテナー イメージのビルド

次の例は、Docker イメージをビルドするワークフローの一部を示しています。

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>App Service コンテナーへのデプロイ

App Service のカスタム コンテナーにイメージをデプロイするには、`azure/webapps-container-deploy@v1` アクションを使用します。 このアクションには、次の 5 つのパラメーターがあります。

| **パラメーター**  | **説明**  |
|---------|---------|
| **app-name** | (必須) App Service アプリの名前 | 
| **slot-name** | (オプション) 運用スロット以外の既存のスロットを入力します。 |
| **images** | (必須) 完全修飾コンテナー イメージ名を指定します。 たとえば、' myregistry.azurecr.io/nginx:latest ' や ' python:3.7.2-alpine/' などです。 複数コンテナー アプリの場合は、複数のコンテナー イメージ名を指定できます (複数行で区切ります)。 |
| **configuration-file** | (オプション) Docker 構成ファイルのパス。 完全修飾パスか、既定の作業ディレクトリを基準とした相対パスのいずれかです。 複数コンテナー アプリの場合は必須です。 |
| **container-command** | (オプション) スタートアップ コマンドを入力します。 たとえば、 dotnet run or dotnet filename.dll |

Node.js アプリをビルドし、App Service 内のカスタム コンテナーにデプロイするサンプル ワークフローを次に示します。 `creds` 入力で、前に作成した `AZURE_CREDENTIALS` シークレットを参照する方法に注意してください。

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>次のステップ

GitHub には、一連のアクションが別々のリポジトリにあります。それぞれには、CI/CD に GitHub を使用し、ご自身のアプリを Azure にデプロイするときに役立つドキュメントとサンプルが含まれています。

- [Azure にデプロイするためのアクション ワークフロー](https://github.com/Azure/actions-workflow-samples)

- [Azure ログイン](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [コンテナー用の Azure Web App](https://github.com/Azure/webapps-container-deploy)

- [Docker でのログイン/ログアウト](https://github.com/Azure/docker-login)

- [ワークフローをトリガーするイベント](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s のデプロイ](https://github.com/Azure/k8s-deploy)

- [スターター ワークフロー](https://github.com/actions/starter-workflows)
