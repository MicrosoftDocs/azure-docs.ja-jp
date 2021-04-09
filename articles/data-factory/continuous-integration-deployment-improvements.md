---
title: 継続的インテグレーションおよびデリバリーの自動発行
description: 継続的インテグレーションおよびデリバリーの自動発行の方法について説明します。
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: b2c48fcc11feaec3efc0acab283609181b92a3dc
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780465"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>継続的インテグレーションおよびデリバリーの自動発行

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概要

継続的インテグレーションは、コードベースに対して行われた変更を自動的にテストするプラクティスです。 できるだけ早く、継続的デリバリーで、継続的インテグレーションの間に発生したテストに続けて、変更をステージングまたは実稼働システムにプッシュします。

Azure Data Factory では、継続的インテグレーションと継続的デリバリー (CI/CD) とは、Data Factory パイプラインをある環境 (開発、テスト、運用など) から別の環境に移動することを意味します。 Data Factory では、[Azure Resource Manager テンプレート (ARM テンプレート)](../azure-resource-manager/templates/overview.md) を使用して、パイプライン、データ セット、データ フローなどのさまざまな DataFactory エンティティの構成を格納します。

データ ファクトリを別の環境に昇格させる手法が 2 つ提案されています。

- Data Factory と [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) の統合を使用した自動デプロイ。
- Data Factory のユーザー エクスペリエンスと Azure Resource Manager の統合を使用した、ARM テンプレートの手動アップロード。

詳細については、「[Azure Data Factory における継続的インテグレーションとデリバリー](continuous-integration-deployment.md)」を参照してください。

この記事では、継続的なデプロイの機能強化と CI/CD の自動発行機能に焦点を当てます。

## <a name="continuous-deployment-improvements"></a>継続的なデプロイの機能強化

自動発行機能では、Data Factory のユーザー エクスペリエンスの **[すべて検証]** および **[Export ARM template]\(ARM テンプレートのエクスポート\)** 機能を利用して、一般公開された npm パッケージ [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) 経由でロジックを使用できるようにします。 このため、Data Factory UI にアクセスして手動でボタンを選択しなくても、プログラムを使用してこれらのアクションをトリガーできます。 この機能により、CI/CD パイプラインに真の継続的インテグレーションのエクスペリエンスが提供されます。

### <a name="current-cicd-flow"></a>現在の CI/CD フロー

1. 各ユーザーは、プライベート ブランチに変更を加えます。
1. マスターへのプッシュは許可されていません。 ユーザーは、プル要求を作成して変更を加える必要があります。
1. ユーザーは、Data Factory UI を読み込み、 **[発行]** をクリックして Data Factory に変更をデプロイし、発行ブランチに ARM テンプレートを生成する必要があります。
1. DevOps リリース パイプラインは、新しいリリースを作成し、新しい変更が発行ブランチにプッシュされるたびに ARM テンプレートをデプロイするように構成されています。

![現在の CI/CD フローを示す図。](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>手動操作

現在の CI/CD フローでは、ユーザー エクスペリエンスが ARM テンプレートの作成に介在します。 その結果、ユーザーは Data Factory UI にアクセスし、 **[発行]** を手動で選択して ARM テンプレートの生成を開始し、発行ブランチにドロップする必要があります。

### <a name="the-new-cicd-flow"></a>新しい CI/CD フロー

1. 各ユーザーは、プライベート ブランチに変更を加えます。
1. マスターへのプッシュは許可されていません。 ユーザーは、プル要求を作成して変更を加える必要があります。
1. Azure DevOps パイプラインのビルドは、マスターへの新しいコミットが行われるたびにトリガーされます。 検証が成功した場合にはリソースを検証し、成果物として ARM テンプレートを生成します。
1. DevOps リリース パイプラインは、新しいリリースを作成し、新しいビルドが利用可能になるたびに ARM テンプレートをデプロイするように構成されています。

![新しい CI/CD フローを示す図。](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>変更箇所

- これで、DevOps ビルド パイプラインを使用するビルド プロセスが完成しました。
- ビルド パイプラインでは、ADFUtilities NPM パッケージを使用します。これにより、すべてのリソースが検証され、ARM テンプレートが生成されます。 これらのテンプレートは単独にすることも、リンクすることもできます。
- ビルド パイプラインは、Data Factory UI ( **[発行]** ボタン) の代わりに、Data Factory リソースの検証と ARM テンプレートの生成を担当します。
- DevOps リリース定義では、Git アーティファクトではなく、この新しいビルド パイプラインが使用されるようになりました。

> [!NOTE]
> 引き続き既存のメカニズム (`adf_publish` ブランチ) を使用することも、新しいフローを使用することもできます。 両方ともサポートされています。

## <a name="package-overview"></a>パッケージの概要

現在、パッケージで 2 つのコマンドが使用できます。

- Resource Manager テンプレートのエクスポート
- 検証

### <a name="export-arm-template"></a>Resource Manager テンプレートのエクスポート

`npm run start export <rootFolder> <factoryId> [outputFolder]` を実行して、特定のフォルダーのリソースを使用して ARM テンプレートをエクスポートします。 このコマンドでは、ARM テンプレートを生成する前に検証チェックも実行されます。 次に例を示します。

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` は、Data Factory リソースが配置されている場所を表す必須フィールドです。
- `FactoryId` は、Data Factory リソース ID を `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` 形式で表す必須フィールドです。
- `OutputFolder` は、生成された ARM テンプレートを保存するための相対パスを指定する省略可能なパラメーターです。
 
> [!NOTE]
> 生成された ARM テンプレートは、ライブ バージョンのファクトリには発行されません。 デプロイは CI/CD パイプラインを使用して行う必要があります。
 
### <a name="validate"></a>検証

`npm run start validate <rootFolder> <factoryId>` を実行して、特定のフォルダーのすべてのリソースを検証します。 次に例を示します。

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` は、Data Factory リソースが配置されている場所を表す必須フィールドです。
- `FactoryId` は、Data Factory リソース ID を `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` 形式で表す必須フィールドです。

## <a name="create-an-azure-pipeline"></a>Azure パイプラインを作成する

npm パッケージはさまざまな方法で使用できますが、主な利点の 1 つは、[Azure パイプライン](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0)を介して使用されることです。 コラボレーション ブランチへの各マージでは、最初にすべてのコードを検証してから、リリース パイプラインで使用できる[ビルド成果物](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0)に ARM テンプレートをエクスポートするパイプラインをトリガーできます。 現在の CI/CD プロセスとの違いは、*既存の `adf_publish` ブランチではなく、この成果物でリリース パイプラインをポイントする* ことです。

作業を開始するには、次の手順に従います。

1.  Azure DevOps プロジェクトを開き、 **[パイプライン]** に移動します。 **[新しいパイプライン]** を選択します。

    ![[新しいパイプライン] ボタンを示すスクリーンショット。](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  パイプライン YAML スクリプトを保存するリポジトリを選択します。 これは Data Factory リソースの同じリポジトリ内の build フォルダーに保存することをお勧めします。 次の例で示すように、このリポジトリ内に、パッケージ名が含まれている *package.json* ファイルがあることを確認します。

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.3"
        }
    } 
    ```
    
1.  **[スタート パイプライン]** を選択します。 次の例で示すように、YAML ファイルをアップロードまたはマージした場合は、それを直接ポイントして編集することもできます。

    ![スタート パイプラインを示すスクリーンショット。](media/continuous-integration-deployment-improvements/starter-pipeline.png)

    ```yaml
    # Sample YAML file to validate and export an ARM template into a build artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  YAML コードを入力します。 YAML ファイルを出発点として使用することをお勧めします。
1.  保存して実行します。 YAML を使用した場合、main ブランチが更新されるたびにトリガーされます。

## <a name="next-steps"></a>次のステップ

Data Factory での継続的インテグレーションおよびデリバリーの詳細については、以下を参照してください。

- [Azure Data Factory における継続的インテグレーションとデリバリー](continuous-integration-deployment.md)。
