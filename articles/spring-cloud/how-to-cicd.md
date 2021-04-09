---
title: Azure Spring Cloud の CI/CD
description: Azure Spring Cloud の CI/CD
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 331ef39facb9f7cf8f069f2a238be325f53de2d0
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877610"
---
# <a name="cicd-for-azure-spring-cloud"></a>Azure Spring Cloud の CI/CD

継続的インテグレーションと継続的デリバリーのツールを使用すると、最小限の労力とリスクで、既存のアプリケーションにすばやく更新プログラムをデプロイできます。 Azure DevOps は、これらの主要なジョブを整理および制御するのに役立ちます。 現在、Azure Spring Cloud では特定の Azure DevOps プラグインは提供されていません。  ただし、[Azure CLI タスク](/azure/devops/pipelines/tasks/deploy/azure-cli)を使用して、Spring Cloud アプリケーションと DevOps を統合できます。

この記事では、Azure Spring Cloud で Azure CLI タスクを使用して Azure DevOps と統合する方法について説明します。

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager サービス接続を作成する

Azure DevOps プロジェクトへの Azure Resource Manager サービス接続を作成する方法については、[こちらの記事](/azure/devops/pipelines/library/connect-to-azure)を参照してください。 Azure Spring Cloud サービス インスタンスに使用しているものと同じサブスクリプションを必ず選択してください。

## <a name="azure-cli-task-templates"></a>Azure CLI タスクのテンプレート
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>成果物をデプロイする

一連の `tasks` を使用して、プロジェクトをビルドしてデプロイできます。 このスニペットでは、変数、アプリケーションをビルドする .NET Core タスク、および  *.zip* ファイルをデプロイする Azure CLI タスクを定義しています。

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --assign-endpoint
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>成果物をデプロイする

一連の `tasks` を使用して、プロジェクトをビルドしてデプロイできます。 このスニペットでは、最初にアプリケーションをビルドする Maven タスクが定義され、次に Azure Spring Cloud Azure CLI 拡張機能を使用して JAR ファイルをデプロイする 2 つ目のタスクが定義されています。

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>ソースからデプロイする

独立したビルド ステップを使用せず、Azure に直接デプロイすることができます。

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
::: zone-end

## <a name="next-steps"></a>次の手順

* [クイック スタート: 初めての Azure Spring Cloud アプリケーションをデプロイする](spring-cloud-quickstart.md)