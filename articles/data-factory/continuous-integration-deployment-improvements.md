---
title: 継続的インテグレーションおよびデリバリーの自動発行
description: 継続的インテグレーションおよびデリバリーの自動発行の方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
manager: weetok
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 7b9e07c6bd2cb52858550fe5c34b2660c4d977eb
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99431116"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>継続的インテグレーションおよびデリバリーの自動発行

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概要

継続的インテグレーションは、コードベースに対して行われた変更を、できるだけ早く自動的にテストするプラクティスです。継続的デリバリーは、継続的インテグレーションの間に発生したテストに続けて、変更をステージングまたは実稼働システムにプッシュします。

Azure Data Factory では、継続的インテグレーションと継続的デリバリー (CI/CD) とは、Data Factory パイプラインをある環境 (開発、テスト、運用) から別の環境に移動することを意味します。 Azure Data Factory では [Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)を活用し、さまざまな ADF エンティティ (パイプライン、データセット、データ フローなど) の構成を保存します。 データ ファクトリを別の環境に昇格させる手法が 2 つ提案されています。

- Data Factory と [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) の統合を利用した自動化されたデプロイ。
- Data Factory UX と Azure Resource Manager の統合を利用した Resource Manager テンプレートの手動アップロード。

詳細については、「[Azure Data Factory における継続的インテグレーションとデリバリー](continuous-integration-deployment.md)」を参照してください。

この記事では、継続的なデプロイの機能強化と CI/CD の自動発行機能に焦点を当てています。

## <a name="continuous-deployment-improvements"></a>継続的なデプロイの機能強化

"自動発行" 機能では、*validate all* を取得し、ADF UX から *Azure Resource Manager (ARM) テンプレート* 機能をエクスポートし、公開されている npm パッケージ [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) でロジックを使用できるようにします。 これにより、ADF UI に移動してボタンをクリックする代わりに、プログラムによってこれらのアクションをトリガーできます。 これにより、CI/CD パイプラインに真の継続的インテグレーションのエクスペリエンスが提供されます。

### <a name="current-cicd-flow"></a>現在の CI/CD フロー

1. 各ユーザーは、プライベート ブランチに変更を加えます。
2. マスターへのプッシュは禁止されています。変更を行うには、ユーザーがマスターへの PR を作成する必要があります。
3. ユーザーは、ADF UI を読み込み、[発行] をクリックして Data Factory に変更をデプロイし、発行ブランチに ARM テンプレートを生成する必要があります。
4. DevOps リリース パイプラインは、新しいリリースを作成し、新しい変更が発行ブランチにプッシュされるたびに ARM テンプレートをデプロイするように構成されています。

![現在の CI/CD フロー](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>手動操作

現在の CI/CD フローでは、UX は ARM テンプレートを作成するための仲介者であるため、ユーザーは ADF UI に移動し、[発行] を手動でクリックして ARM テンプレートの生成を開始し、発行ブランチ (ハッキングの一部) にドロップする必要があります。

### <a name="the-new-cicd-flow"></a>新しい CI/CD フロー

1. 各ユーザーは、プライベート ブランチに変更を加えます。
2. マスターへのプッシュは禁止されています。変更を行うには、ユーザーがマスターへの PR を作成する必要があります。
3. **Azure DevOps パイプラインのビルドは、マスターへの新しいコミットが行われるたびにトリガーされ、検証が成功した場合にはリソースを検証し、成果物として ARM テンプレートを生成します。**
4. DevOps リリース パイプラインは、新しいリリースを作成し、新しいビルドが利用可能になるたびに ARM テンプレートをデプロイするように構成されています。 

![新しい CI/CD フロー](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>変更箇所

- これで、DevOps ビルド パイプラインを使用したビルド プロセスが完成しました。
- ビルド パイプラインでは、ADFUtilities NPM パッケージを使用します。これにより、すべてのリソースが検証され、ARM テンプレート (単一およびとリンクされたテンプレート) が生成されます。
- ビルド パイプラインは、ADF リソースを検証し、ADF UI ([発行] ボタン) の代わりに ARM テンプレートを生成します。
- DevOps リリース定義では、Git アーティファクトではなく、この新しいビルド パイプラインが使用されるようになりました。

> [!NOTE]
> 既存のメカニズム (adf_publish ブランチ) を引き続き使用することも、新しいフローを使用することもできます。 両方ともサポートされています。 

## <a name="package-overview"></a>パッケージの概要

パッケージで現在使用できるコマンドは 2 つあります。
- Resource Manager テンプレートのエクスポート
- 検証

### <a name="export-arm-template"></a>Resource Manager テンプレートのエクスポート

npm run start export <rootFolder> <factoryId> [outputFolder] を実行して、特定のフォルダーのリソースを使用して ARM テンプレートをエクスポートします。 このコマンドでは、ARM テンプレートを生成する前に検証チェックが実行されます。 次に例を示します。

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- RootFolder は、Data Factory リソースが配置されている場所を表す必須フィールドです。
- FactoryId は、Data factory のリソース ID を "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>" の形式で表す必須フィールドです。
- OutputFolder は、生成された ARM テンプレートを保存するための相対パスを指定する省略可能なパラメーターです。
 
> [!NOTE]
> 生成された ARM テンプレートは、`Live` バージョンのファクトリには発行されません。 デプロイは CI/CD パイプラインを使用して行う必要があります。 
 

### <a name="validate"></a>検証

npm run start validate <rootFolder> <factoryId> を実行して、特定のフォルダーのすべてのリソースを検証します。 次に例を示します。
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- RootFolder は、Data Factory リソースが配置されている場所を表す必須フィールドです。
- FactoryId は、Data factory のリソース ID を "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>" の形式で表す必須フィールドです。


## <a name="create-an-azure-pipeline"></a>Azure パイプラインを作成する

npm パッケージはさまざまな方法で使用できますが、主な利点の 1 つは、[Azure パイプライン](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0)を介して使用されることです。 コラボレーション ブランチへの各マージでは、最初にすべてのコードを検証してから、リリース パイプラインで使用できる[ビルド成果物](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0)に ARM テンプレートをエクスポートするパイプラインをトリガーできます。 **現在の CI/CD プロセスとの違いは、既存の `adf_publish` ブランチではなく、この成果物でリリース パイプラインをポイントすることです。**

作業を開始するには、次の手順に従います。

1.  Azure DevOps プロジェクトを開き、"パイプライン" に移動します。 [新しいパイプライン] を選択します。

    ![新しいパイプライン](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  パイプライン YAML スクリプトを保存するリポジトリを選択します。 これは ADF リソースの同じリポジトリ内の *build* フォルダーに保存することをお勧めします。 以下の例で示すように、このリポジトリ内に、パッケージ名が含まれている **package.json** ファイルもあることを確認します。

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.2"
        }
    } 
    ```
    
3.  *[スタート パイプライン]* を選択します。 以下の例で示すように、YAML ファイルをアップロードまたはマージした場合は、それを直接ポイントして編集することもできます。 

    ![スタート パイプライン](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
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
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  YAML コードに入力します。 YAML ファイルを作成し、開始点として使用することをお勧めします。
5.  保存して実行します。 YAML を使用する場合は、"main" ブランチが更新されるたびにトリガーされます。

## <a name="next-steps"></a>次のステップ

Data Factory での継続的インテグレーションおよびデリバリーの詳細については、以下を参照してください。 

- [Azure Data Factory における継続的インテグレーションとデリバリー](continuous-integration-deployment.md)。
