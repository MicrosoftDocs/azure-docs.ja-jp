---
title: Azure Spring Cloud の CI/CD
description: Azure Spring Cloud の CI/CD
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d31be45f5cf608b7dfd727741de68544574d7f8a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496459"
---
# <a name="cicd-for-azure-spring-cloud"></a>Azure Spring Cloud の CI/CD

継続的インテグレーションと継続的デリバリーのツールを使用すると、開発者は最小限の労力とリスクで、既存のアプリケーションにすばやく更新プログラムをデプロイできます。 Azure DevOps は、これらの主要なジョブを整理および制御するのに役立ちます。 現在、Azure Spring Cloud では特定の Azure DevOps プラグインは提供されていません。  ただし、[Azure CLI タスク](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops)を使用して、Spring Cloud アプリケーションと DevOps を統合できます。 この記事では、Azure Spring Cloud で Azure CLI タスクを使用して Azure DevOps と統合する方法について説明します。

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager サービス接続を作成する

Azure DevOps プロジェクトへの Azure Resource Manager サービス接続を作成する方法については、[こちらの記事](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)を参照してください。 Azure Spring Cloud サービス インスタンスに使用しているものと同じサブスクリプションを必ず選択してください。

## <a name="azure-cli-task-templates"></a>Azure CLI タスクのテンプレート

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
