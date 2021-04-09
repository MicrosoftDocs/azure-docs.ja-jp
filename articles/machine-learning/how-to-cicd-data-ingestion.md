---
title: データ インジェスト パイプラインの DevOps
titleSuffix: Azure Machine Learning
description: DevOps プラクティスを適用し、Azure Data Factory と Azure Databricks を使用してデータを準備するためのデータ インジェスト パイプラインを構築する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: e8a8b952d917db3a7eefd2e0371d41287c5be944
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612475"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>データ インジェスト パイプラインの DevOps

ほとんどのシナリオで、データ インジェスト ソリューションは、すべてのアクティビティを調整するスクリプト、サービス呼び出し、およびパイプラインを構成したものです。 この記事では、機械学習モデルのトレーニング用にデータを準備する一般的なデータ インジェスト パイプラインの開発ライフサイクルに DevOps プラクティスを適用する方法について説明します。 このパイプラインの構築には、次の Azure サービスを使用しています。

* __Azure Data Factory__: 生データを読み取り、データ準備を調整します。
* __Azure Databricks__:データを変換する Python ノートブックを実行します。
* __Azure Pipelines__:継続的インテグレーションと開発プロセスを自動化します。

## <a name="data-ingestion-pipeline-workflow"></a>データ インジェスト パイプライン ワークフロー

データ インジェスト パイプラインでは、次のワークフローが実装されます。

1. 生データが Azure Data Factory (ADF) パイプラインに読み込まれます。
1. ADF パイプラインが Azure Databricks クラスターにデータを送信し、Python ノートブックが実行されてデータが変換されます。
1. データが BLOB コンテナーに格納され、それを使用して Azure Machine Learning でモデルがトレーニングされます。

![データ インジェスト パイプライン ワークフロー](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

## <a name="continuous-integration-and-delivery-overview"></a>継続的インテグレーションとデリバリーの概要

多くのソフトウェア ソリューションと同様に、担当するチーム (たとえば、データ エンジニア) があります。 ここで Azure Data Factory、Azure Databricks、Azure Storage アカウントなどの同じ Azure リソースについて共同で作業し、共有しています。 こうしたリソースをまとめたものが開発環境です。 データ エンジニアは同じソース コード ベースを編集します。

継続的インテグレーションとデリバリー システムにより、ソリューションの構築、テスト、およびデリバリー (配置) のプロセスが自動化されます。 継続的インテグレーション (CI) プロセスでは、次のタスクが実行されます。

* コードをアセンブルする
* コード品質テストを使用してチェックする
* 単体テストを実行する
* テストされたコードや Azure Resource Manager テンプレートなどの成果物を生成する

継続的デリバリー (CD) プロセスでは、ダウンストリーム環境に成果物が配置されます。

![CICD データ インジェストの図](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

この記事では、[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) で CI および CD プロセスを自動化する方法について説明します。

## <a name="source-control-management"></a>ソース コントロール管理

変更を追跡し、チーム メンバー間の共同作業を可能にするには、ソース コード管理が必要になります。
たとえば、Azure DevOps、GitHub、または GitLab リポジトリにコードを格納します。 コラボレーション ワークフローは、ブランチ モデルに基づいています。 たとえば、[GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html) です。

### <a name="python-notebook-source-code"></a>Python ノートブックのソース コード

データ エンジニアは、IDE (たとえば、[Visual Studio Code](https://code.visualstudio.com)) 内のローカルで、または Databricks ワークスペースで、Python ノートブックのソース コードを編集します。 コードの変更が完了すると、ブランチ ポリシーに従ってリポジトリにマージされます。

> [!TIP] 
> `.ipynb` Jupyter Notebook 形式ではなく `.py` ファイルでコードを格納することをお勧めします。 こうすることで、コードが読みやすくなり、CI プロセスでの自動コード品質チェックが可能になります。

### <a name="azure-data-factory-source-code"></a>Azure Data Factory ソース コード

Azure Data Factory パイプラインのソース コードは、Azure Data Factory ワークスペースによって生成された JSON ファイルのコレクションです。 通常、データ エンジニアは、ソース コード ファイルを直接使用するのではなく、Azure Data Factory ワークスペースでビジュアル デザイナーを使用します。 

ソース コード管理リポジトリを使用するようにワークスペースを構成するには、「[Azure Repos Git 統合を使用した作成](../data-factory/source-control.md#author-with-azure-repos-git-integration)」を参照してください。   

## <a name="continuous-integration-ci"></a>継続的インテグレーション (CI)

継続的インテグレーション プロセスの最終的な目標は、ソース コードから共同チームの作業を収集し、ダウンストリーム環境への配置のために準備することです。 ソース コード管理と同様に、このプロセスは Python ノートブックと Azure Data Factory パイプラインとで異なります。 

### <a name="python-notebook-ci"></a>Python ノートブックの CI

Python ノートブックの CI プロセスでは、コラボレーション ブランチ (たとえば、**master**、_develop_) からコードを取得し、次のアクティビティを実行します。
* コードのリンティング
* 単体テスト
* コードを成果物として保存する

次のコード スニペットは、Azure DevOps ***yaml*** パイプラインでこれらの手順を実装する方法を示しています。

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks
```

このパイプラインでは、Python コードの実行に [flake8](https://pypi.org/project/flake8/) を使用しています。 これを使って、ソース コードに定義されている単体テストを実行し、リンティングとテストの結果を発行して、Azure パイプライン実行画面で使用できるようにします。

![単体テストのリンティング](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

リンティングと単体テストが成功すると、パイプラインによってソース コードが成果物のリポジトリにコピーされ、以降の配置手順で使用されます。

### <a name="azure-data-factory-ci"></a>Azure Data Factory の CI

Azure Data Factory パイプラインの CI プロセスは、データ インジェスト パイプラインのボトルネックになります。 継続的インテグレーションはありません。 Azure Data Factory の配置可能な成果物は、Azure Resource Manager テンプレートのコレクションです。 このようなテンプレートを生成する唯一の方法は、Azure Data Factory ワークスペースの ***[発行]*** ボタンをクリックすることです。

1. データ エンジニアは、機能ブランチのソース コードをコラボレーション ブランチ (たとえば、**master** または _develop_) にマージします。 
1. アクセス許可を持つ担当者が ***[発行]*** ボタンをクリックし、コラボレーション ブランチのソース コードから Azure Resource Manager テンプレートを生成します。 
1. ワークスペースによってパイプラインが検証され (リンティングと単体テストの時点と考えます)、Azure Resource Manager テンプレートが生成され (ビルドの時点と考えます)、生成されたテンプレートが同じコード リポジトリ内のテクニカル ブランチ ***adf_publish*** に保存されます (成果物の発行の時点と考えます)。 このブランチは、Azure Data Factory ワークスペースによって自動的に作成されます。 

このプロセスの詳細については、「[Azure Data Factory における継続的インテグレーションとデリバリー](../data-factory/continuous-integration-deployment.md)」を参照してください。

生成される Azure Resource Manager テンプレートが環境に依存しないようにすることが重要です。 つまり、環境によって異なる可能性があるすべての値をパラメーター化します。 Azure Data Factory は高機能なので、このような値の大部分をパラメーターとして公開することができます。 たとえば、次のテンプレートでは、Azure Machine Learning ワークスペースへの接続プロパティがパラメーターとして公開されています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

ただし、既定では Azure Data Factory ワークスペースで処理されないカスタム プロパティを公開することもできます。 この記事のシナリオでは、Azure Data Factory パイプラインから、データを処理する Python ノートブックが呼び出されます。 このノートブックでは、入力データ ファイルの名前をパラメーターとして受け取ります。

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

この名前は、**Dev**、_*_QA_*_、_*_UAT_*_、_*_PROD_*_ の各環境で異なります。 複数のアクティビティがある複雑なパイプラインでは、いくつかのカスタム プロパティを使用できます。 1 つの場所でこれらのすべての値を収集し、パイプライン "_変数_" として定義することをお勧めします。

![スクリーンショットには、PrepareData というノートブックと、ML Execute Pipeline という ML Execute Pipeline が上部に示され、その下には [変数] タブが選択され、新しい変数を名前、種類、および既定値とともに追加するオプションが示されています。](media/how-to-cicd-data-ingestion/adf-variables.png)

パイプライン アクティビティでは、実際に使用するときに、パイプライン変数を参照する場合があります。

![スクリーンショットには、PrepareData と呼ばれるノートブックと、ML Execute Pipeline と呼ばれる ML Execute Pipeline が上部に示され、その下には [設定] タブが選択されています。](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Azure Data Factory ワークスペースの既定では、Azure Resource Manager テンプレート パラメーターとしてパイプライン変数を公開 "***していません***"。 このワークスペースでは、[Default Parameterization](../data-factory/continuous-integration-deployment.md#default-parameterization-template) を使用して、Azure Resource Manager テンプレート パラメーターとして公開するパイプライン プロパティを指定します。 パイプライン変数を一覧に追加するには、[既定のパラメーター化テンプレート](../data-factory/continuous-integration-deployment.md#default-parameterization-template)の `"Microsoft.DataFactory/factories/pipelines"` セクションを次のスニペットで更新し、結果の JSON ファイルをソース フォルダーのルートに配置します。

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

これを行うと、***[発行]*** ボタンがクリックされたときに、Azure Data Factory ワークスペースによってパラメーター一覧に変数が強制的に追加されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

JSON ファイルの値は、パイプライン定義に構成されている既定値です。 Azure Resource Manager テンプレートの配置時に、ターゲット環境の値で上書きされることが想定されています。

## <a name="continuous-delivery-cd"></a>継続的デリバリー (CD)

継続的デリバリー プロセスでは、成果物を受け取り、それを最初のターゲット環境に配置します。 ソリューションが機能することを確認するために、テストを実行します。 成功した場合は、次の環境に進みます。 

CD Azure パイプラインは、環境を表す複数のステージで構成されています。 各ステージには、次の手順を実行する[配置](/azure/devops/pipelines/process/deployment-jobs)と[ジョブ](/azure/devops/pipelines/process/phases?tabs=yaml)が含まれています。

* Python ノートブックを Azure Databricks ワークスペースに配置する
* Azure Data Factory パイプラインを配置する 
* パイプラインを実行する
* データ インジェストの結果を確認する

パイプライン ステージは、[承認](/azure/devops/pipelines/process/approvals?tabs=check-pass)と[ゲート](/azure/devops/pipelines/release/approvals/gates)を使用して構成できます。これにより、環境のチェーンの中で配置プロセスがどのように進化するかをさらに制御できます。

### <a name="deploy-a-python-notebook"></a>Python ノートブックを配置する

次のコード スニペットでは、Python ノートブックを Databricks クラスターにコピーする Azure パイプラインの[配置](/azure/devops/pipelines/process/deployment-jobs)を定義しています。

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

CI によって生成された成果物は自動的に配置エージェントにコピーされ、`$(Pipeline.Workspace)` フォルダーから利用できます。 この場合、配置タスクは、Python ノートブックを含む `di-notebooks` 成果物を参照します。 この[配置](/azure/devops/pipelines/process/deployment-jobs)では、[Databricks Azure DevOps 拡張機能](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)を使用してノートブック ファイルを Databricks ワークスペースにコピーします。

`Deploy_to_QA` ステージには、Azure DevOps プロジェクトで定義されている `devops-ds-qa-vg` 変数グループへの参照が含まれています。 このステージの手順では、この変数グループの変数を参照します (たとえば、`$(DATABRICKS_URL)` と `$(DATABRICKS_TOKEN)`)。 次のステージ (たとえば、`Deploy_to_UAT`) は、独自の UAT スコープの変数グループに定義されているものと同じ変数名で動作するという考え方です。

### <a name="deploy-an-azure-data-factory-pipeline"></a>Azure Data Factory パイプラインを配置する

Azure Data Factory の配置可能な成果物は、Azure Resource Manager テンプレートです。 次のスニペットに示すように、"***Azure リソース グループの配置***" タスクと共に配置されます。

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
データ ファイル名パラメーターの値は、QA ステージ変数グループに定義されている `$(DATA_FILE_NAME)` 変数に由来します。 同様に、***ARMTemplateForFactory.json*** で定義されているすべてのパラメーターはオーバーライドできます。 指定されていない場合は、既定値が使用されます。

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>パイプラインを実行し、データ インジェストの結果を確認する

次の手順では、配置したソリューションが動作していることを確認します。 次のジョブ定義では、[PowerShell スクリプト](https://github.com/microsoft/DataOps/tree/master/adf/utils)を使って Azure Data Factory パイプラインを実行し、Azure Databricks クラスター上で Python ノートブックを実行します。 このノートブックでは、データが正しく取り込まれたかどうかが確認され、`$(bin_FILE_NAME)` という名前を持つ結果のデータ ファイルが検証されます。

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

ジョブの最後のタスクでは、ノートブックの実行結果を確認します。 エラーが返された場合は、パイプラインの実行状態が失敗に設定されます。

## <a name="putting-pieces-together"></a>まとめ

CI/CD Azure パイプライン全体は、次のステージで構成されています。
* CI
* QA への配置
    * Databricks への配置 + ADF への配置
    * 統合テスト

お持ちのターゲット環境数と同数の "***配置** _" ステージ数があります。 各 "*_配置_*" ステージには、並列で実行される 2 つの[配置](/azure/devops/pipelines/process/deployment-jobs)と、配置後にその環境でソリューションをテストするために実行される[ジョブ](/azure/devops/pipelines/process/phases?tabs=yaml)が含まれています。

パイプラインのサンプル実装は、次の ***yaml*** スニペットにまとめられています。

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>次のステップ

* [Azure Data Factory のソース管理](../data-factory/source-control.md)
* [Azure Data Factory における継続的インテグレーションとデリバリー](../data-factory/continuous-integration-deployment.md)
* [DevOps for Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)