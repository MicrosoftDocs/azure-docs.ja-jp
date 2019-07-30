---
title: Azure Pipelines と Resource Manager テンプレートを使用した CI/CD
description: Visual Studio で Azure リソース グループのデプロイ プロジェクトを使用して Resource Manager テンプレートをデプロイし、Azure Pipelines での継続的インテグレーションを設定する方法を説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: article
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: b70b38904c0373c53c3731dd0442511116d9c4de
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191226"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Azure Pipelines を使用した Resource Manager テンプレートの統合

Visual Studio には、テンプレートを作成し、それを Azure サブスクリプションにデプロイするための Azure リソース グループ プロジェクトが用意されています。 このプロジェクトは、継続的インテグレーションと継続的デプロイ (CI/CD) のために Azure Pipelines と統合できます。

Azure Pipelines を使用してテンプレートをデプロイする方法は 2 つあります。

* **Azure PowerShell スクリプトを実行するタスクを追加する**。 このオプションでは、Visual Studio プロジェクトに含まれているのと同じスクリプト (Deploy-AzureResourceGroup.ps1) を使用するため、開発ライフ サイクル全体で一貫性が保たれるという利点があります。 このスクリプトでは、プロジェクトの成果物が、Resource Manager からアクセスできるストレージ アカウントにステージされます。 成果物とはプロジェクト内の項目のことで、リンクされたテンプレート、スクリプト、アプリケーション バイナリなどが該当します。 その後、スクリプトによってテンプレートがデプロイされます。

* **コピー タスクとデプロイ タスクを追加する**。 このオプションでは、プロジェクト スクリプトに代わる便利な方法が提供されます。 パイプラインに 2 つのタスクを構成します。 一方のタスクで成果物をステージし、もう一方のタスクでテンプレートをデプロイします。

このトピックでは、両方の方法を説明します。

## <a name="prepare-your-project"></a>プロジェクトを準備する

この記事は、Visual Studio プロジェクトと Azure DevOps 組織でパイプラインを作成する準備ができていることを前提としています。 準備ができていることを確認する手順は次のとおりです。

* Azure DevOps 組織があること。 ない場合は、[無料で作成](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)してください。 チームに Azure DevOps 組織が既にある場合は、使用する Azure DevOps プロジェクトの管理者であることを確認します。

* Azure サブスクリプションへの[サービス接続](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)が構成済みであること。 パイプライン内のタスクは、サービス プリンシパルの ID で実行されます。 接続を作成する手順については、「[DevOps プロジェクトを作成する](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project)」を参照してください。

* **Azure リソース グループ** スタート テンプレートから作成された Visual Studio プロジェクトがあること。 この種類のプロジェクトを作成する方法の詳細については、「[Visual Studio での Azure リソース グループの作成とデプロイ](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)」を参照してください。

* Visual Studio プロジェクトが [Azure DevOps プロジェクトに接続されている](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)こと。

## <a name="create-pipeline"></a>パイプラインの作成

1. まだパイプラインを追加していない場合は、新しいパイプラインを作成する必要があります。 Azure DevOps 組織から、 **[パイプライン]** 、 **[新しいパイプライン]** を選択します。

   ![新しいパイプラインの追加](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. コードの格納場所を指定します。 次の図では、 **[Azure Repos Git]** が選択されています。

   ![コードのソースの選択](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. そのソースから、プロジェクトのコードが含まれているリポジトリを選択します。

   ![リポジトリの選択](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. 作成するパイプラインの種類を選択します。 **[スタート パイプライン]** を選択できます。

   ![パイプラインの選択](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Azure PowerShell タスクまたはファイルのコピーとデプロイのタスクのいずれかを追加する準備ができました。

## <a name="azure-powershell-task"></a>Azure PowerShell タスク

このセクションでは、プロジェクト内の PowerShell スクリプトを実行する 1 つのタスクを使用して、継続的デプロイを構成する方法を示します。 次の YAML ファイルでは、[Azure PowerShell タスク](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)が作成されます。

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' 
    azurePowerShellVersion: LatestVersion
```

タスクを `AzurePowerShell@3` に設定した場合、パイプラインでは、AzureRM モジュールのコマンドを使用して接続が認証されます。 既定では、Visual Studio プロジェクトの PowerShell スクリプトでは、AzureRM モジュールが使用されます。 [Az モジュール](/powershell/azure/new-azureps-module-az)が使用されるようにスクリプトを更新した場合は、タスクを `AzurePowerShell@4` に設定します。

```yaml
steps:
- task: AzurePowerShell@4
```

`azureSubscription` には、作成したサービス接続の名前を指定します。

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

`scriptPath` には、パイプライン ファイルからスクリプトへの相対パスを指定します。 パスはリポジトリで確認できます。

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

成果物のステージが不要な場合は、デプロイに使用するリソース グループの場所と名前のみを渡します。 まだリソース グループがない場合は、Visual Studio スクリプトによって作成されます。

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

既存のストレージ アカウントに成果物をステージする必要がある場合は、以下を使用します。

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

タスクを作成する方法を説明したので、次はパイプラインを編集する手順を見ていきましょう。

1. パイプラインを開き、YAML の内容に置き換えます。

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. **[保存]** を選択します。

   ![パイプラインの保存](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. コミットのメッセージを指定し、直接**マスター**にコミットします。

1. **[保存]** を選択すると、ビルド パイプラインが自動的に実行されます。 ビルド パイプラインの概要に戻り、状態を監視します。

   ![結果の表示](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

現在実行中のパイプラインを選択し、タスクについての詳細を確認できます。 完了すると、各ステップの結果が表示されます。

## <a name="copy-and-deploy-tasks"></a>コピーとデプロイのタスク

このセクションでは、成果物のステージとテンプレートのデプロイを行う 2 つのタスクを使用して、継続的デプロイを構成する方法を示します。 

次の YAML は、[Azure ファイル コピー タスク](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)を示しています。

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

お使いの環境に合わせて、このタスクの複数の部分を変更します。 `SourcePath` は、パイプライン ファイルを基準とした成果物の場所を示します。 この例では、ファイルはプロジェクト名であった `AzureResourceGroup1` という名前のフォルダーにあります。

```yaml
SourcePath: '<path-to-artifacts>'
```

`azureSubscription` には、作成したサービス接続の名前を指定します。

```yaml
azureSubscription: '<your-connection-name>'
```

ストレージとコンテナーの名前には、成果物の格納に使用するストレージ アカウントとコンテナーの名前を指定します。 ストレージ アカウントは存在している必要があります。

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

次の YAML は、[Azure リソース グループのデプロイ タスク](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops)を示しています。

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

お使いの環境に合わせて、このタスクの複数の部分を変更します。 `azureSubscription` には、作成したサービス接続の名前を指定します。

```yaml
azureSubscription: '<your-connection-name>'
```

`resourceGroupName` と `location` には、デプロイ先のリソース グループの名前と場所を指定します。 リソース グループがない場合は、タスクによって作成されます。

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

デプロイ タスクは、`WebSite.json` という名前のテンプレートと WebSite.parameters.json という名前のパラメーター ファイルにリンクしています。 ご自分のテンプレートとパラメーター ファイルの名前を使用します。

タスクを作成する方法を説明したので、次はパイプラインを編集する手順を見ていきましょう。

1. パイプラインを開き、YAML の内容に置き換えます。

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
   - task: AzureResourceGroupDeployment@2
     displayName: 'Deploy template'
     inputs:
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. **[保存]** を選択します。

1. コミットのメッセージを指定し、直接**マスター**にコミットします。

1. **[保存]** を選択すると、ビルド パイプラインが自動的に実行されます。 ビルド パイプラインの概要に戻り、状態を監視します。

   ![結果の表示](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

現在実行中のパイプラインを選択し、タスクについての詳細を確認できます。 完了すると、各ステップの結果が表示されます。

## <a name="next-steps"></a>次の手順

Resource Manager テンプレートで Azure Pipelines を使用するステップ バイ ステップのプロセスについては、「[チュートリアル:Azure Pipelines を使用した Azure Resource Manager テンプレートの継続的インテグレーション](resource-manager-tutorial-use-azure-pipelines.md)」を参照してください。
