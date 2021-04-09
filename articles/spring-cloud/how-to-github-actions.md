---
title: GitHub Actions を使用する Azure Spring Cloud CI/CD
description: GitHub Actions を使用して Azure Spring Cloud の CI/CD ワークフローを構築する方法
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 8400fcacbfa4c76aceb079b788255e3d3b83ce33
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877451"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>GitHub Actions を使用する Azure Spring Cloud CI/CD

GitHub Actions は、自動化されたソフトウェア開発ライフサイクル ワークフローをサポートします。 Azure Spring Cloud 用 GitHub Actions を使用すると、リポジトリに構築、テスト、パッケージ化、リリース、および Azure へのデプロイを行うワークフローを作成できます。 

## <a name="prerequisites"></a>前提条件
この例には [Azure CLI](/cli/azure/install-azure-cli) が必要です。

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>GitHub リポジトリを設定して認証する
Azure ログイン アクションを承認するには、Azure サービス プリンシパルの資格情報が必要です。 Azure の資格情報を取得するには、ローカル マシン上で次のコマンドを実行します。

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

特定のリソース グループにアクセスするには、範囲を縮小できます。

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

このコマンドからは、次のような JSON オブジェクトが出力されます。

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

この例では、[GitHub 上の steeltoe サンプル](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample)を使用しています。  リポジトリをフォークし、フォークの GitHub リポジトリ ページを開いて、 **[設定]** タブを選択します。 **[シークレット]** メニューを開き、 **[新しいシークレット]** を選択します。

 ![新しいシークレットを追加する](./media/github-actions/actions1.png)

シークレット名を `AZURE_CREDENTIALS` に設定し、その値を「*Set up your GitHub repository and authenticate (GitHub リポジトリを設定して認証する)* 」という見出しの下にある JSON 文字列に設定します。

 ![シークレット データを設定する](./media/github-actions/actions2.png)

[GitHub Actions で Key Vault を使用して Azure Spring を認証する](./spring-cloud-github-actions-key-vault.md)方法の記事で説明されているように、GitHub Actions で Key Vault から Azure のログイン資格情報を取得することもできます。

## <a name="provision-service-instance"></a>サービス インスタンスをプロビジョニングする
Azure Spring Cloud サービス インスタンスをプロビジョニングするには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>ワークフローを構築する
ワークフローは、次のオプションを使用して定義されます。

### <a name="prepare-for-deployment-with-azure-cli"></a>Azure CLI を使用してデプロイを準備する

コマンド `az spring-cloud app create` は現在べき等ではありません。 一度実行した後、同じコマンドを再度実行するとエラーが発生します。 既存の Azure Spring Cloud アプリおよびインスタンスにはこのワークフローをお勧めします。

準備には、次の Azure CLI コマンドを使用します。
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Azure CLI を使用して直接デプロイする

以下の内容の `.github/workflows/main.yml` ファイルをリポジトリに作成します。 `<your resource group name>` と `<your service name>` を正しい値に置き換えます。

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>
    
    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}
          
      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes
      
      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```
::: zone-end

::: zone pivot="programming-language-java"
## <a name="set-up-github-repository-and-authenticate"></a>GitHub リポジトリを設定して認証する
Azure ログイン アクションを承認するには、Azure サービス プリンシパルの資格情報が必要です。 Azure の資格情報を取得するには、ローカル マシン上で次のコマンドを実行します。
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
特定のリソース グループにアクセスするには、範囲を縮小できます。
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
このコマンドからは、次のような JSON オブジェクトが出力されます。
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

この例では、GitHub 上の [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) サンプルを使用します。  このサンプルをフォークし、GitHub リポジトリ ページを開き、 **[Settings]\(設定\)** タブをクリックします。 **[Secrets]\(シークレット\)** メニューを開き、 **[Add a new secret]\(新しいシークレットの追加\)** をクリックします。

 ![新しいシークレットを追加する](./media/github-actions/actions1.png)

シークレット名を `AZURE_CREDENTIALS` に設定し、その値を「*Set up your GitHub repository and authenticate (GitHub リポジトリを設定して認証する)* 」という見出しの下にある JSON 文字列に設定します。

 ![シークレット データを設定する](./media/github-actions/actions2.png)

[GitHub Actions で Key Vault を使用して Azure Spring を認証する](./spring-cloud-github-actions-key-vault.md)方法の記事で説明されているように、GitHub Actions で Key Vault から Azure のログイン資格情報を取得することもできます。

## <a name="provision-service-instance"></a>サービス インスタンスをプロビジョニングする
Azure Spring Cloud サービス インスタンスをプロビジョニングするには、Azure CLI を使用して次のコマンドを実行します。
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>ワークフローを構築する
ワークフローは、次のオプションを使用して定義されます。

### <a name="prepare-for-deployment-with-azure-cli"></a>Azure CLI を使用してデプロイを準備する
コマンド `az spring-cloud app create` は現在べき等ではありません。  既存の Azure Spring Cloud アプリおよびインスタンスにはこのワークフローをお勧めします。

準備には、次の Azure CLI コマンドを使用します。
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Azure CLI を使用して直接デプロイする
リポジトリに `.github/workflow/main.yml` ファイルを作成します。

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Azure CLI アクションを使用してデプロイする
az `run` コマンドには、Azure CLI の最新バージョンが使用されます。 破壊的変更がある場合は、Azure または CLI `action` で特定のバージョンの Azure CLI を使用することもできます。 

> [!Note] 
> このコマンドは新しいコンテナーで実行されるため、`env` は機能しません。また、クロス アクション ファイル アクセスには追加の制限が存在する可能性があります。

リポジトリに .github/workflow/main.yml ファイルを作成します。
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Maven プラグインを使用してデプロイする
もう 1 つの方法は、Jar のデプロイとアプリ設定の更新に [Maven プラグイン](./spring-cloud-quickstart.md)を使用することです。 コマンド `mvn azure-spring-cloud:deploy` はべき等であり、必要に応じて自動的にアプリを作成します。 対応するアプリを事前に作成する必要はありません。

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>ワークフローを実行する

`.github/workflow/main.yml` を GitHub にプッシュすると、GitHub **Actions** が自動的に有効になります。 このアクションは、新しいコミットがプッシュされたときにトリガーされます。 ブラウザーでこのファイルを作成する場合、アクションが既に実行されている必要があります。

アクションが有効になっていることを確認するには、GitHub リポジトリ ページの **[Actions]\(アクション\)** タブをクリックします。

![アクションが有効かどうかを確認する](./media/github-actions/actions3.png)

アクションの実行結果がエラーになる場合、たとえば、Azure の資格情報を設定していなかった場合は、エラーを修正した後にチェックを再実行できます。 GitHub リポジトリ ページの **[Actions]\(アクション\)** をクリックし、特定のワークフロー タスクを選択し、 **[Rerun checks]\(チェックの再実行\)** ボタンをクリックしてチェックを再実行します。

![チェックを再実行する](./media/github-actions/actions4.png)

## <a name="next-steps"></a>次のステップ

* [Spring Cloud GitHub アクションの Key Vault](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory のサービス プリンシパル](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)
* [GitHub Actions for Azure](https://github.com/Azure/actions/)
