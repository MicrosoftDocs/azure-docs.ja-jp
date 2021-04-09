---
title: Azure Pipelines を使用した HPC ソリューションのビルドとデプロイ
description: Azure Batch で実行する HPC アプリケーションのビルド/リリース パイプラインをデプロイする方法について説明します。
author: chrisreddington
ms.author: chredd
ms.date: 03/04/2021
ms.topic: how-to
ms.openlocfilehash: 7170044af58a508ff5a43751cc376f8b8d498444
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435547"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Azure Pipelines を使用する HPC ソリューションの構築とデプロイ

Azure DevOps によって提供されるツールを、ハイ パフォーマンス コンピューティング (HPC) ソリューションの自動構築とテストに利用できます。 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) には、ソフトウェアの構築、デプロイ、テスト、および監視を行うための最新の継続的インテグレーション (CI) と継続的配置 (CD) の各プロセスが各種用意されています。 これらのプロセスによってソフトウェアの提供が早まるため、サポート インフラストラクチャや操作ではなくコードに集中することができます。

この記事では、Azure Batch にデプロイされた HPC ソリューションに対して、[Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) を使用して CI/CD プロセスを設定する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、[Azure DevOps 組織](/azure/devops/organizations/accounts/create-organization)が必要です。 また、[Azure DevOps でプロジェクトを作成する](/azure/devops/organizations/projects/create-project)必要もあります。

開始する前に、[ソース管理](/azure/devops/user-guide/source-control)と [Azure Resource Manager テンプレートの構文](../azure-resource-manager/templates/template-syntax.md)の基本を理解しておくと役立ちます。

## <a name="create-an-azure-pipeline"></a>Azure パイプラインを作成する

この例では、Azure Batch インフラストラクチャをデプロイしてアプリケーション パッケージをリリースするためのビルドとリリースのパイプラインを作成します。 コードがローカルで開発される場合、一般的なデプロイ フローは次のようになります。

![パイプラインのデプロイ フローを示す図。](media/batch-ci-cd/DeploymentFlow.png)

このサンプルでは、いくつかの Azure Resource Manager テンプレートと既存のバイナリを使用します。 これらのサンプルをリポジトリにコピーして、Azure DevOps にプッシュできます。

### <a name="understand-the-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートについて理解する

この例では、いくつかの Azure Resource Manager テンプレートを使用してソリューションをデプロイします。 特定の機能を実装するために、3 つの機能テンプレート (ユニットまたはモジュールに似ている) を使用します。 次に、エンド ツー エンドのソリューション テンプレート (deployment.json) を使用して、基になるそれらの機能テンプレートをデプロイします。 この[リンク済みテンプレート構造](../azure-resource-manager/templates/deployment-tutorial-linked-template.md)を使用すると、各機能テンプレートを個別にテストし、ソリューション間で再利用できます。

![Azure Resource Manager テンプレートを使用したリンク済みテンプレート構造を示す図。](media/batch-ci-cd/ARMTemplateHierarchy.png)

このテンプレートにより、アプリケーションを Batch アカウントにデプロイするために必要な Azure ストレージアカウントが定義されます。 詳細については、[Microsoft.Storage リソースの種類の Resource Manager テンプレート リファレンス ガイド](/azure/templates/microsoft.storage/allversions)に関するページを参照してください。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

次のテンプレートにより、[Azure Batch アカウント](accounts.md)が定義されます。 Batch アカウントは、[プール](nodes-and-pools.md#pools)全体で多数のアプリケーションを実行するためのプラットフォームとして機能します。 詳細については、[Microsoft.Batch リソースの種類の Resource Manager テンプレート リファレンス ガイド](/azure/templates/microsoft.batch/allversions)に関するページを参照してください。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

次のテンプレートにより、Batch アカウントに Batch プールが作成されます。 詳細については、[Microsoft.Batch リソースの種類の Resource Manager テンプレート リファレンス ガイド](/azure/templates/microsoft.batch/allversions)に関するページを参照してください。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

最後のテンプレートはオーケストレーターとして機能し、基になる 3 つの機能テンプレートをデプロイします。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resource Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resource Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

### <a name="understand-the-hpc-solution"></a>HPC ソリューションについて理解する

前述のように、このサンプルでは、いくつかの Azure Resource Manager テンプレートと既存のバイナリを使用します。 これらのサンプルをリポジトリにコピーして、Azure DevOps にプッシュできます。

このソリューションでは、アプリケーション パッケージとして ffmpeg を使用します。 まだない場合は、[ffmpeg パッケージをダウンロード](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08)できます。

![リポジトリ構造のスクリーンショット。](media/batch-ci-cd/git-repository.jpg)

このリポジトリには 4 つの主要なセクションがあります。

- **arm-templates** フォルダー。Azure Resource Manager テンプレートが含まれます。
- **hpc-application** フォルダー。Windows 64 ビット バージョンの [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) が含まれます。
- **pipelines** フォルダー。ビルド パイプライン プロセスを定義する YAML ファイルが含まれます。
- オプション: **client-application** フォルダー。[ffmpeg による Azure Batch .NET ファイル処理](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial)サンプルのコピーです。 このアプリケーションは、この記事では必要ありません。


> [!NOTE]
> これはコードベースの構造の一例です。 このアプローチを使用しているのは、アプリケーション、インフラストラクチャ、およびパイプライン コードが同じリポジトリに格納されることを示すためです。

ソース コードが設定されたところで、最初の構築を開始できます。

## <a name="continuous-integration"></a>継続的インテグレーション

[Azure Pipelines](/azure/devops/pipelines/get-started/) は Azure DevOps Services に含まれ、アプリケーションのビルド、テスト、デプロイの各パイプラインの実装に役立ちます。

通常、パイプラインのこの段階では、コードを検証してソフトウェアの該当する部分を構築するために、テストを実行します。 テストの回数や種類、および実行するその他のタスクは、ビルドとリリースの多様な戦略によって異なります。

## <a name="prepare-the-hpc-application"></a>HPC アプリケーションを準備する

このセクションでは、**hpc-application** フォルダーを操作します。 このフォルダーには、Azure Batch アカウント内で実行されるソフトウェア (ffmpeg) が含まれています。

1. Azure DevOps 組織の Azure Pipelines の [ビルド] セクションに移動します。 **新しいパイプライン** を作成します。

    ![[新しいパイプライン] 画面のスクリーンショット。](media/batch-ci-cd/new-build-pipeline.jpg)

1. ビルド パイプラインを作成するには、次の 2 つの方法があります。

    a. [ビジュアル デザイナーを使用する](/azure/devops/pipelines/get-started-designer)。 そのためには、 **[新しいパイプライン]** ページで [ビジュアル デザイナーを使用する] を選択します。

    b. [YAML ビルドを使用する](/azure/devops/pipelines/get-started-yaml)。 新しい YAMLパイプラインを作成するには、 **[新しいパイプライン]** ページで [Azure Repos] または [GitHub] オプションをクリックします。 または、[Visual Designer] を選択してから YAML テンプレートを使用することで、下の例をソース管理に格納して、既存の YAML ファイルを参照できます。

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. 必要に応じてビルドが構成されたら、 **[保存してキューに登録]** を選択します。 継続的インテグレーションを有効にしている場合 ( **[トリガー]** セクション)、リポジトリへの新しいコミットが行われ、ビルドに設定された条件を満たすと、ビルドが自動的にトリガーされます。

    ![既存のビルド パイプラインのスクリーンショット。](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Azure DevOps のビルドの進行状況のライブ更新を表示するには、Azure Pipelines の **[ビルド]** セクションに移動します。 ビルド定義から適切なビルドを選択します。

    ![Azure DevOps におけるビルドからのライブ出力のスクリーンショット。](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> HPC ソリューションを実行するためにクライアント アプリケーションを使用する場合は、そのアプリケーションのために別のビルド定義を作成する必要があります。 [Azure Pipelines](/azure/devops/pipelines/get-started/index) のドキュメントにいくつもの攻略ガイドがあります。

## <a name="continuous-deployment"></a>継続的なデプロイ

また、Azure Pipelines を使用して、アプリケーションおよび基になるインフラストラクチャをデプロイします。 [リリース パイプライン](/azure/devops/pipelines/release)により、継続的配置が有効になり、リリース プロセスが自動化されます。

### <a name="deploy-your-application-and-underlying-infrastructure"></a>アプリケーションおよび基になるインフラストラクチャをデプロイする

インフラストラクチャのデプロイには多くの手順が関係します。 このソリューションは[リンク済みテンプレート](../azure-resource-manager/templates/linked-templates.md)を使用しているため、それらのテンプレートにパブリック エンドポイント (HTTP または HTTPS) からアクセスできる必要があります。 このためには、GitHub 上のリポジトリ、Azure Blob Storage アカウント、または別の保存場所を使用できます。 アップロードされたテンプレート成果物は、プライベート モードで保持されるが、なんらかの形式の Shared Access Signature (SAS) トークンを使用してアクセスできるため、安全性が保たれます。

次の例では、Azure Storage BLOB のテンプレートを含むインフラストラクチャをデプロイする方法を説明します。

1. **新しいリリース定義** を作成し、空の定義を選択します。 新しく作成された環境の名前を、お使いのパイプラインに関連するものに変更します。

    ![初期リリース パイプラインのスクリーンショット。](media/batch-ci-cd/Release-0.jpg)

1. ビルド パイプラインに対する依存関係を作成して、HPC アプリケーションの出力を取得します。

    > [!NOTE]
    > **[ソース エイリアス]** を書き留めておきます。これは、リリース定義内にタスクが作成されるときに必要になります。

    ![適切なビルド パイプライン内の HPCApplicationPackage に対する成果物リンクを示すスクリーンショット。](media/batch-ci-cd/Release-1.jpg)

1. ここでは、別の成果物 Azure Repo に対するリンクを作成します。 これは、リポジトリに格納されている Resource Manager テンプレートにアクセスするために必要です。 Resource Manager テンプレートのコンパイルは必要ないため、ビルド パイプラインを通じてプッシュする必要はありません。

    > [!NOTE]
    > ここでも、 **[ソース エイリアス]** を書き留めておきます。これは後で必要になります。

    ![Azure Repos への成果物リンクを示すスクリーンショット。](media/batch-ci-cd/Release-2.jpg)

1. **[変数]** セクションに移動します。 複数のタスクに同じ情報を再入力せずに済むように、パイプラインに多くの変数を作成することをお勧めします。 この例では、次の変数を使用します。

   - **applicationStorageAccountName**: HPC アプリケーション バイナリを保持するストレージ アカウントの名前
   - **batchAccountApplicationName**: Batch アカウント内のアプリケーションの名前
   - **batchAccountName**: Batch アカウントの名前
   - **batchAccountPoolName**:処理を実行する VM のプールの名前
   - **batchApplicationId**: Batch アプリケーションの一意の ID
   - **batchApplicationVersion**: Batch アプリケーション (つまり ffmpeg バイナリ) のセマンティック バージョン
   - **location**: Azure リソースをデプロイする場所
   - **resourceGroupName**: 作成するリソース グループ (リソースがデプロイされる) の名前
   - **storageAccountName**: リンクされた Resource Manager テンプレートを保持するストレージ アカウントの名前

   ![Azure Pipelines リリースに設定された変数を示すスクリーンショット。](media/batch-ci-cd/Release-4.jpg)

1. [開発] 環境のタスクに移動します。 以下のスナップショットでは 6 つのタスクを確認できます。 これらのタスクで行われるのは、圧縮された ffmpeg ファイルのダウンロード、入れ子になった Resource Manager テンプレートをホストするストレージ アカウントのデプロイ、Batch アカウントと必要な依存関係のデプロイ、Azure Batch アカウントでのアプリケーションの作成、およびアプリケーション パッケージの Azure Batch アカウントへのアップロードです。

    ![HPC アプリケーションを Azure Batch にリリースするために使用されるタスクを示すスクリーンショット。](media/batch-ci-cd/Release-3.jpg)

1. **[Download Pipeline Artifact (Preview)]\(パイプライン成果物のダウンロード(プレビュー)\)** タスクを追加し、次のプロパティを設定します。
    - **[表示名]:** ApplicationPackage をエージェントにダウンロード
    - **[ダウンロードする成果物の名前]:** hpc-application
    - **[ダウンロード先のパス]** : $(System.DefaultWorkingDirectory)

1. Azure Resource Manager テンプレートを格納するためのストレージ アカウントを作成します。 ソリューションの既存のストレージ アカウントを使用することもできますが、この自己完結型サンプルとコンテンツの分離をサポートするために、専用ストレージ アカウントを作成します。

    **[Azure リソース グループの配置]** タスクを追加して、次のプロパティを設定します。
    - **[表示名]:** Resource Manager テンプレートのストレージ アカウントをデプロイします。
    - **[Azure サブスクリプション]:** 適切な Azure サブスクリプションを選択します。
    - **アクション**:リソース グループを作成または更新します。
    - **[リソース グループ]** : $(resourceGroupName)
    - **[場所]** : $(location)
    - **[テンプレート]** : $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    - **[テンプレート パラメーターのオーバーライド]** : -accountName $(storageAccountName)

1. Azure Pipelines を使用して、ソース管理からストレージ アカウントに成果物をアップロードします。 この Azure Pipelines タスクの一環として、ストレージ アカウント コンテナーの URI と SAS トークンを Azure Pipelines の変数に出力でき、このエージェント フェーズ全体で再利用できます。

    **[Azure ファイル コピー]** タスクを追加し、次のプロパティを設定します。
    - **[ソース]:** $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    - **[Azure 接続の種類]** :Azure Resource Manager
    - **[Azure サブスクリプション]:** 適切な Azure サブスクリプションを選択します。
    - **[送信先の種類]** :Azure BLOB
    - **[RM ストレージ アカウント]** : $(storageAccountName)
    - **[コンテナー名]** : templates
    - **[ストレージ コンテナーの URI]** : templateContainerUri
    - **[ストレージ コンテナーの SAS トークン]** : templateContainerSasToken

1. オーケストレーター テンプレートをデプロイします。 このテンプレートには、ストレージ アカウント コンテナーの URI と SAS トークンのパラメーターが含まれています。 Resource Manager テンプレートで必要な変数は、リリース定義の変数セクションに保持されるか、別の Azure Pipelines タスク (Azure Blob コピー タスクの一部など) から設定されます。

    **[Azure リソース グループの配置]** タスクを追加して、次のプロパティを設定します。
    - **[表示名]:** Azure Batch のデプロイ
    - **[Azure サブスクリプション]:** 適切な Azure サブスクリプションを選択します。
    - **アクション**:リソース グループを作成または更新します。
    - **[リソース グループ]** : $(resourceGroupName)
    - **[場所]** : $(location)
    - **[テンプレート]** : $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    - **[テンプレート パラメーターのオーバーライド]** : `-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)`

   一般的には、Azure Key Vault タスクを使用します。 Azure サブスクリプションに接続されているサービス プリンシパルに適切なアクセス ポリシーが設定されている場合は、それによって Azure Key Vault からシークレットをダウンロードして、パイプラインで変数として使用できます。 シークレットの名前として関連する値が設定されます。 たとえば、リリース定義で sshPassword のシークレットを $(sshPassword) と呼ぶことができます。

1. 次の手順では Azure CLI を呼び出します。 1 つ目は、Azure Batch にアプリケーションを作成し、関連するパッケージをアップロードするために使用されます。

    **[Azure CLI]** タスクを追加し、次のプロパティを設定します。
    - **[表示名]:** Azure Batch アカウントでアプリケーションを作成します
    - **[Azure サブスクリプション]:** 適切な Azure サブスクリプションを選択します。
    - **[スクリプトの場所]** :インライン スクリプト
    - **[インライン スクリプト]** : `az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)`

1. 2 つ目の手順は、関連するパッケージ (この場合は ffmpeg ファイル) をアプリケーションにアップロードするために使用されます。

    **[Azure CLI]** タスクを追加し、次のプロパティを設定します。
    - **[表示名]:** Azure Batch アカウントにパッケージをアップロードします。
    - **[Azure サブスクリプション]:** 適切な Azure サブスクリプションを選択します。
    - **[スクリプトの場所]** :インライン スクリプト
    - **[インライン スクリプト]** : `az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip`

    > [!NOTE]
    > アプリケーション パッケージのバージョン番号は変数に設定されます。 これにより、以前のバージョンのパッケージを上書きして、Azure Batch にプッシュされるパッケージのバージョン番号を手動で制御できるようになります。

1. **[リリース] > [新しいリリースの作成]** を選択して、新しいリリースを作成します。 トリガーされたら、新しいリリースのリンクを選択して状態を表示します。

1. 環境の下にある **[ログ]** ボタンを選択して、エージェントからのライブ出力を表示します。

    ![リリースの状態を示すスクリーンショット。](media/batch-ci-cd/Release-5.jpg)

## <a name="test-the-environment"></a>環境をテストする

環境を設定したら、次のテストが正常に完了することを確認します。

PowerShell コマンド プロンプトから Azure CLI を使用して新しい Azure Batch アカウントに接続します。

- Azure アカウントに `az login` でサインインし、指示に従って認証します。
- ここで、`az batch account login -g <resourceGroup> -n <batchAccount>` のように Batch アカウントを認証します。

#### <a name="list-the-available-applications"></a>使用可能なアプリケーションを一覧表示する

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>プールが有効なことを確認する

```azurecli
az batch pool list
```

このコマンドの出力で `currentDedicatedNodes` の値に注意してください。 この値は次のテストで調整されます。

#### <a name="resize-the-pool"></a>プール サイズを変更する

プールのサイズを変更して、ジョブとタスク テストのコンピューティング ノードを用意します。pool list コマンドを使用して、サイズ変更が完了してノードが使用可能になるまで最新の状態を確認します。

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>次のステップ

単純なアプリケーションを介して Batch アカウントを操作する方法について詳しくは、これらのチュートリアルをご覧ください。

- [Python API を使用して Azure Batch で並列ワークロードを実行する](tutorial-parallel-python.md)
- [.NET API を使用して Azure Batch で並列ワークロードを実行する](tutorial-parallel-dotnet.md)
