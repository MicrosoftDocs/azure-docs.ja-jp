---
title: Azure DevOps を使用して、Function App コードを継続的に更新する
description: Azure Functions をターゲットとする、Azure DevOps パイプラインを設定する方法について学習します。
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: a3f423a144738fdaa4462606de6ad4a4e34d6775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97563417"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Azure DevOps を使用した継続的デリバリー

[Azure Pipelines](/azure/devops/pipelines/) を使用して、関数を Azure Functions アプリに自動的にデプロイできます。

パイプラインを定義するには、次の 2 つのオプションがあります。

- **YAML ファイル**:YAML ファイルは、パイプラインを記述します。 このファイルには、ビルド ステップ セクションとリリース セクションが含まれている場合があります。 YAML ファイルは、アプリと同じリポジトリにある必要があります。
- **テンプレート**:テンプレートは、アプリをビルドまたはデプロイする既製のタスクです。

## <a name="yaml-based-pipeline"></a>YAML ベースのパイプライン

YAML ベースのパイプラインを作成するには、まずアプリをビルドしてから、アプリをデプロイします。

### <a name="build-your-app"></a>アプリの構築

Azure Pipelines でアプリをビルドする方法は、アプリのプログラミング言語によって異なります。 言語ごとにデプロイ成果物を作成するための固有のビルド ステップがあります。 デプロイ成果物は、Azure でFunction App をデプロイするために使用されます。

# <a name="c"></a>[C\#](#tab/csharp)

次のサンプルを使用して、.NET アプリをビルドする YAML ファイルを作成できます。

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次のサンプルを使用して、JavaScript アプリをビルドする YAML ファイルを作成できます。

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

次のサンプルのいずれかを使用して、特定の Python バージョン用のアプリをビルドする YAML ファイルを作成できます。 Python は、Linux で実行されている Function App でのみサポートされています。

**バージョン 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**バージョン 3.6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次のサンプルを使用して、PowerShell アプリをパッケージ化する YAML ファイルを作成できます。 PowerShell は、Windows Azure Functions でのみサポートされています。

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>アプリをデプロイする

ホストしている OS によっては、YAML ファイルに次の YAML サンプルのいずれかを含める必要があります。

#### <a name="windows-function-app"></a>Windows Function App

Windows Function App をデプロイするには、次のスニペットを使用します。

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux Function App

Linux Function App をデプロイするには、次のスニペットを使用します。

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>テンプレート ベースのパイプライン

Azure DevOps ではテンプレートは、アプリをビルドまたはデプロイするタスクの定義済みのグループです。

### <a name="build-your-app"></a>アプリの構築

Azure Pipelines でアプリをビルドする方法は、アプリのプログラミング言語によって異なります。 言語ごとにデプロイ成果物を作成するための固有のビルド ステップがあります。 デプロイ成果物は、Azure でFunction App を更新するために使用されます。

新しいビルド パイプラインを作成するときに、組み込みのビルド テンプレートを使用するには、 **[従来のエディターを使用する]** を選択して、デザイナー テンプレートを使用してパイプラインを作成します。

![Azure Pipelines の従来のエディターを選択する](media/functions-how-to-azure-devops/classic-editor.png)

コードのソースを構成したら、Azure Functions のビルド テンプレートを検索します。 お使いのアプリの言語に一致するテンプレートを選択します。

![Azure Functions のビルド テンプレートを選択する](media/functions-how-to-azure-devops/build-templates.png)

場合によっては、ビルド成果物に特定のフォルダー構造があります。 **[Prepend root folder name to archive paths]\(アーカイブ パスの前にルート フォルダー名を付加する\)** チェック ボックスをオンにする必要がある場合があります。

![ルート フォルダー名の前に付加するオプション](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript アプリ

お使いの JavaScript アプリに Windows ネイティブ モジュールへの依存関係がある場合は、エージェント プールのバージョンを **Hosted VS2017** に更新する必要があります。

![エージェント プールのバージョンを更新する](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>アプリをデプロイする

新しいリリース パイプラインを作成するときに、Azure Functions のリリース テンプレートを検索します。

![Azure Functions のリリース テンプレートを検索する](media/functions-how-to-azure-devops/release-template.png)

リリース テンプレートでは、デプロイ スロットへのデプロイはサポートされていません。

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Azure CLI を使用してビルド パイプラインを作成する

Azure でビルド パイプラインを作成するには、`az functionapp devops-pipeline create` [コマンド](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)を使用します。 ビルド パイプラインは、リポジトリに加えられたコード変更をビルドしてリリースするために作成されます。 このコマンドにより、ビルドとリリース パイプラインを定義し、それをリポジトリにコミットする新しい YAML ファイルが生成されます。 このコマンドの前提条件は、コードの場所によって異なります。

- コードが GitHub にある場合:

    - サブスクリプションへの **書き込み** アクセス許可が必要です。

    - Azure DevOps のプロジェクト管理者である必要があります。

    - 十分なアクセス許可を持つ GitHub 個人用アクセス トークン (PAT) を作成するためのアクセス許可が必要です。 詳細については、[GitHub PAT アクセス許可の要件](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication)に関するセクションを参照してください。

    - 自動生成された YAML ファイルをコミットできるように、GitHub リポジトリのメイン ブランチにコミットするためのアクセス許可が必要です。

- コードが Azure Repos にある場合:

    - サブスクリプションへの **書き込み** アクセス許可が必要です。

    - Azure DevOps のプロジェクト管理者である必要があります。

## <a name="next-steps"></a>次のステップ

- 「[Azure Functions の概要](functions-overview.md)」を確認してください。
- [Azure DevOps の概要](/azure/devops/pipelines/)を確認してください。