---
title: GitHub アクションを使用して CI/CD を構成する
description: GitHub Actions を使用して CI/CD パイプラインからご自分のコードを Azure App Service にデプロイする方法について説明します。 Build タスクをカスタマイズし、複雑なデプロイを実行します。
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 59eb56dd188edf258c3631cde957c0864454ad76
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762661"
---
# <a name="deploy-to-app-service-using-github-actions"></a>GitHub Actions を使用した App Service へのデプロイ

[GitHub Actions](https://docs.github.com/en/actions/learn-github-actions) を使用して、GitHub からワークフローの自動化と [Azure App Service](overview.md) へのデプロイを実行します。 

## <a name="prerequisites"></a>前提条件 

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub アカウント。 ない場合は、[無料](https://github.com/join)でサインアップしてください。  
- 作業中の Azure App Service アプリ。 
    - .NET:[Azure に ASP.NET Core Web アプリを作成する](quickstart-dotnetcore.md)
    - ASP.NET:[Azure に ASP.NET Framework Web アプリを作成する](quickstart-dotnet-framework.md)
    - JavaScript:[Azure App Service での Node.js Web アプリの作成](quickstart-nodejs.md)  
    - Java:[Azure App Service で Java アプリを作成する](quickstart-java.md)
    - Python: [Azure App Service で Python アプリを作成する](quickstart-python.md)

## <a name="workflow-file-overview"></a>ワークフロー ファイルの概要

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。

このファイルには 3 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**Authentication** | 1.サービス プリンシパルまたは発行プロファイルを定義します。 <br /> 2.GitHub シークレットを作成します。 |
|**ビルド** | 1.環境を設定します。 <br /> 2.Web アプリを作成します。 |
|**Deploy** | 1.Web アプリをデプロイします。 |

## <a name="use-the-deployment-center"></a>デプロイ センターを使用する

App Service デプロイ センターを使用して、GitHub Actions の使用をすぐに開始できます。 これにより、アプリケーション スタックに基づくワークフロー ファイルが自動的に生成され、GitHub リポジトリの適切なディレクトリにコミットされます。

1. Azure portal で自分の Web アプリに移動します。
1. 左側の **[デプロイ センター]** をクリックします。
1. **[継続的デプロイ (CI/CD)]** で、 **[GitHub]** を選択します。
1. 次に、 **[GitHub Actions]** を選択します。
1. ドロップダウンを使用して、自分の GitHub リポジトリ、ブランチ、およびアプリケーション スタックを選択します。
    - 選択したブランチが保護されている場合でも、ワークフロー ファイルの追加を続行できます。 続行する前に、ブランチの保護状態を確認してください。
1. 最後の画面で、選択内容を確認し、リポジトリにコミットされるワークフロー ファイルをプレビューできます。 選択内容が適切であれば、 **[完了]** をクリックします。

これにより、ワークフロー ファイルがリポジトリにコミットされます。 アプリをビルドしてデプロイするワークフローがすぐに開始されます。

## <a name="set-up-a-workflow-manually"></a>ワークフローを手動で設定する

デプロイ センターを使用せずにワークフローをデプロイすることもできます。 これを行うには、最初にデプロイ資格情報を生成する必要があります。 

## <a name="generate-deployment-credentials"></a>デプロイ資格情報を生成する

GitHub Actions 用の Azure App Services での認証で推奨される方法は、発行プロファイルを使用することです。 サービス プリンシパルを使用して認証することもできますが、そのプロセスにはさらに多くの手順が必要です。 

発行プロファイル資格情報またはサービス プリンシパルを [GitHub シークレット](https://docs.github.com/en/actions/reference/encrypted-secrets)として保存して、Azure で認証します。 ワークフロー内のシークレットにアクセスします。 

# <a name="publish-profile"></a>[発行プロファイル](#tab/applevel)

発行プロファイルは、アプリレベルの資格情報です。 発行プロファイルを GitHub シークレットとして設定します。 

1. Azure portal で、お使いのアプリ サービスに移動します。 

1. **[概要]** ページで、 **[発行プロファイルの取得]** オプションを選択します。

1. ダウンロードしたファイルを保存します。 このファイルの内容を使用して、GitHub シークレットを作成します。

> [!NOTE]
> 2020 年 10 月の時点では、Linux Web アプリでは、**発行プロファイルをダウンロードする前に**、アプリ設定 `WEBSITE_WEBDEPLOY_USE_SCM` を `true` に設定する必要があります。 この要件は、今後削除される予定です。

# <a name="service-principal"></a>[サービス プリンシパル](#tab/userlevel)

[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)は、[Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを使用して作成できます。 このコマンドは、Azure portal で [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[試してみる]** ボタンを選択して実行します。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

上記の例で、プレースホルダーをご利用のサブスクリプション ID、リソース グループ名、アプリ名に置き換えます。 これにより、以下のようなご自分の App Service アプリにアクセスするためのロールの割り当て資格情報を含む JSON オブジェクトが出力されます。 この JSON オブジェクトを後のためにコピーします。

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

## <a name="configure-the-github-secret"></a>GitHub シークレットの構成


# <a name="publish-profile"></a>[発行プロファイル](#tab/applevel)

[GitHub](https://github.com/) で自分のリポジトリを参照し、 **Settings > Secrets > Add a new secret\(新しいシークレットの追加\)** の順に選択します。

[アプリ レベルの資格情報](#generate-deployment-credentials)を使用するには、ダウンロードした発行プロファイルのファイルの内容をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_WEBAPP_PUBLISH_PROFILE` という名前を付けます。

GitHub ワークフローを構成するときに、Azure Web アプリをデプロイするアクションの中で `AZURE_WEBAPP_PUBLISH_PROFILE` を使用します。 次に例を示します。
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[サービス プリンシパル](#tab/userlevel)

[GitHub](https://github.com/) で自分のリポジトリを参照し、 **Settings > Secrets > Add a new secret\(新しいシークレットの追加\)** の順に選択します。

[ユーザー レベルの資格情報](#generate-deployment-credentials)を使用するには、Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` と名前を付けます。

後でワークフロー ファイルを構成する場合は、Azure ログイン アクションの入力 `creds` にシークレットを使用します。 次に例を示します。

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>環境をセットアップする

次の設定操作のいずれかを使用し、環境を設定できます。

|**Language**  |**設定操作**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

以下の例は、サポートされているさまざまな言語の環境を設定する方法を示しています。

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>Web アプリを作成します

Web アプリをビルドして Azure App Service にデプロイするプロセスは、言語に応じて変化します。 

以下の例は、サポートされているさまざまな言語で Web アプリをビルドするワークフローの一部です。

すべての言語で、`working-directory` を使用して Web アプリのルート ディレクトリを設定できます。 

**.NET**

環境変数 `AZURE_WEBAPP_PACKAGE_PATH` で、Web アプリ プロジェクトのパスを設定します。 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

NuGet の依存関係を復元し、`run` を使用して msbuild を実行できます。 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Node.js の場合、`working-directory` を設定するか、`pushd` で npm ディレクトリを変更できます。 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>App Service にデプロイする

ご自分のコードを App Service アプリにデプロイするには、`azure/webapps-deploy@v2` アクションを使用します。 このアクションには、次の 4 つのパラメーターがあります。

| **パラメーター**  | **説明**  |
|---------|---------|
| **app-name** | (必須) App Service アプリの名前 | 
| **publish-profile** | (オプション) Web 配置のシークレットでプロファイル ファイルの内容を発行する |
| **package** | (オプション) パッケージまたはフォルダーへのパス。 パスには、*.zip、*.war、*.jar、またはデプロイするフォルダーを含めることができます |
| **slot-name** | (省略可能) 運用[スロット](deploy-staging-slots.md)以外の既存のスロットを入力します |


# <a name="publish-profile"></a>[発行プロファイル](#tab/applevel)

### <a name="net-core"></a>.NET Core

Azure 発行プロファイルを使用する .NET Core アプリをビルドして Azure にデプロイします。 `publish-profile` 入力で、先ほど作成した `AZURE_WEBAPP_PUBLISH_PROFILE` シークレットを参照します。

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

NuGet と認証に `publish-profile` を使用する ASP.NET MVC アプリをビルドしてデプロイします。 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@main  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Azure 発行プロファイルを使用する Java Spring アプリをビルドして Azure にデプロイします。 `publish-profile` 入力で、先ほど作成した `AZURE_WEBAPP_PUBLISH_PROFILE` シークレットを参照します。

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

`jar` ではなく `war` をデプロイするには、`package` の値を変更します。 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

アプリの発行プロファイルを使用する Node.js アプリをビルドして Azure にデプロイします。 `publish-profile` 入力で、先ほど作成した `AZURE_WEBAPP_PUBLISH_PROFILE` シークレットを参照します。

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

アプリの発行プロファイルを使用する Python アプリをビルドして Azure にデプロイします。 `publish-profile` 入力で、前に作成した `AZURE_WEBAPP_PUBLISH_PROFILE` シークレットを参照する方法に注意してください。

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[サービス プリンシパル](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

Azure サービス プリンシパルを使用する .NET Core アプリをビルドして Azure にデプロイします。 `creds` 入力で、前に作成した `AZURE_CREDENTIALS` シークレットを参照する方法に注意してください。


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

Azure サービス プリンシパルを使用する ASP.NET MVC アプリをビルドして Azure にデプロイします。 `creds` 入力で、前に作成した `AZURE_CREDENTIALS` シークレットを参照する方法に注意してください。

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@main
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Azure サービス プリンシパルを使用する Java Spring アプリをビルドして Azure にデプロイします。 `creds` 入力で、前に作成した `AZURE_CREDENTIALS` シークレットを参照する方法に注意してください。

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

Azure サービス プリンシパルを使用する Node.js アプリをビルドして Azure にデプロイします。 `creds` 入力で、前に作成した `AZURE_CREDENTIALS` シークレットを参照する方法に注意してください。

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Azure サービス プリンシパルを使用する Python アプリをビルドして Azure にデプロイします。 `creds` 入力で、前に作成した `AZURE_CREDENTIALS` シークレットを参照する方法に注意してください。

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
```


---

## <a name="next-steps"></a>次のステップ

GitHub には、一連のアクションが別々のリポジトリにあります。それぞれには、CI/CD に GitHub を使用し、ご自身のアプリを Azure にデプロイするときに役立つドキュメントとサンプルが含まれています。

- [Azure にデプロイするためのアクション ワークフロー](https://github.com/Azure/actions-workflow-samples)

- [Azure ログイン](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [コンテナー用の Azure Web App](https://github.com/Azure/webapps-container-deploy)

- [Docker でのログイン/ログアウト](https://github.com/Azure/docker-login)

- [ワークフローをトリガーするイベント](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s のデプロイ](https://github.com/Azure/k8s-deploy)

- [スターター ワークフロー](https://github.com/actions/starter-workflows)
