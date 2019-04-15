---
title: Azure Pipelines を使用する HPC ソリューションの構築とデプロイ - Azure Batch | Microsoft Docs
description: Azure Batch で実行する HPC アプリケーションのビルド/リリース パイプラインをデプロイする方法について説明します。
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
services: batch
ms.openlocfilehash: 1e3b2d42ae390b9eb755e568b82fc1a90e0eae6a
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807345"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Azure Pipelines を使用する HPC ソリューションの構築とデプロイ

Azure DevOps サービスでは、開発チームがカスタム アプリケーションの構築時に使用するさまざまなツールが提供されます。 Azure DevOps によって提供されるツールを、ハイ パフォーマンス コンピューティング ソリューションの自動構築とテストに利用できます。 この記事では、Azure Batch にデプロイされるハイ パフォーマンス コンピューティング ソリューションのために、Azure Pipelines を使用して継続的インテグレーション (CI) と継続的配置 (CD) をどのように設定するかを説明します。

Azure Pipelines では、ソフトウェアの構築、デプロイ、テスト、および監視のためにさまざまな最新 CI/CD プロセスが提供されます。 これらのプロセスによってソフトウェアの提供が早まるため、サポート インフラストラクチャや操作ではなくコードに集中することができます。

## <a name="create-an-azure-pipeline"></a>Azure パイプラインを作成する

この例では、Azure Batch インフラストラクチャをデプロイしてアプリケーション パッケージをリリースするためのビルド パイプラインとリリース パプラインを作成します。 コードがローカルで開発される場合、一般的なデプロイ フローは次のようになります。

![パイプラインのデプロイ フローを示す図](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>セットアップ

この記事の手順を行うには、Azure DevOps 組織とチーム プロジェクトが必要です。

* [Azure DevOps 組織の作成](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Azure DevOps でのプロジェクトの作成](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>環境のソース管理

ソース管理によって、チームがコードベースに加えられた変更を追跡して、コードの以前のバージョンを確認できるようになります。

通常、ソース管理はソフトウェア コードと一緒に検討します。 基になるインフラストラクチャはどうすればよいでしょうか。 これは、Azure Resource Manager テンプレートまたは他のオープンソースの代替手段を使用して、基になるインフラストラクチャを宣言によって定義する Infrastructure as Code につながります。

このサンプルは、多くの Resource Manager テンプレート (JSON ドキュメント) と既存のバイナリに大きく依存します。 これらのサンプルをリポジトリにコピーして、Azure DevOps にプッシュできます。

このサンプルで使用されるコードベース構造は次のとおりです。

* **arm-templates** フォルダー。多数の Azure Resource Manager テンプレートが含まれます。 この記事でテンプレートについて説明します。
* **client-application** フォルダー。[ffmpeg による Azure Batch .NET ファイル処理](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial)サンプルのコピーです。 これはこの記事では必要ありません。
* **hpc-application** フォルダー。Windows 64 ビット バージョンの [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip) です。
* **pipelines** フォルダー。 これには、構築プロセスを示す YAML ファイルが含まれています。 これはこの記事で説明します。

このセクションでは、バージョン管理と Resource Manager テンプレートの設計に習熟していることを前提としています。 これらの概念になじみがない場合は、以下のページで詳細を確認してください。

* [ソース管理の概要](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Azure Resource Manager テンプレートの構造と構文の詳細](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

この例では、複数の Resource Manager テンプレートを利用してソリューションをデプロイします。 これを行うために、特定の機能を実装している機能テンプレート (ユニットまたはモジュールに似ている) をいくつも使用します。 また、これらの基になる機能をまとめる役割を持つエンド ツー エンド テンプレートも使用します。 この方法には次の 2 つの利点があります。

* 基になる機能テンプレートは、個別に単体テストを行うことができます。
* 基になる機能テンプレートは、組織内で標準として定義でき、複数のソリューションで再利用できます。

この例の場合は、3 つのテンプレートをデプロイするエンド ツー エンド ソリューション テンプレート (deployment.json) があります。 基になるテンプレートは、ソリューションの特定の機能をデプロイする機能テンプレートです。

![Azure Resource Manager テンプレートを使用したリンク テンプレート構造の例](media/batch-ci-cd/ARMTemplateHierarchy.png)

最初に説明するテンプレートは Azure Storage Account 用です。 このソリューションでは、アプリケーションを Batch アカウントにデプロイするためにストレージ アカウントが必要です。 ストレージ アカウント用の Resource Manager テンプレートを構築する際には、[Microsoft.Storage リソース タイプの Resource Manager テンプレートのリファレンス ガイド](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions)を参照することをお勧めします。

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

次は、Azure Batch アカウントのテンプレートについて説明します。 Azure Batch アカウントは、プール (マシンのグループ) 全体で多数のアプリケーションを実行するためのプラットフォームとして機能します。 Batch アカウント用の Resource Manager テンプレートを構築する際には、[Microsoft.Batch リソース タイプの Resource Manager テンプレートのリファレンス ガイド](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)を参照することをお勧めします。

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

次のテンプレートは、Azure Batch プール (アプリケーションを処理するバックエンド マシン) を作成するサンプルです。 Batch アカウント プール用の Resource Manager テンプレートを構築する際には、[Microsoft.Batch リソース タイプの Resource Manager テンプレートのリファレンス ガイド](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)を参照することをお勧めします。

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

最後に、オーケストレーターのように機能するテンプレートがあります。 このテンプレートには、機能テンプレートをデプロイする役割があります。

[リンクされた Azure Resource Manager テンプレートの作成](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md)については別の記事でも詳しく説明しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
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

#### <a name="the-hpc-solution"></a>HPC ソリューション

インフラストラクチャとソフトウェアはコードとして定義でき、同じリポジトリに一緒に配置できます。

このソリューションでは、アプリケーション パッケージとして ffmpeg を使用します。 ffmpeg パッケージは[ここで](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)ダウンロードできます。

![Git リポジトリの構造の例](media/batch-ci-cd/git-repository.jpg)

このリポジトリには 4 つの主要なセクションがあります。

* Infrastructure as Code を格納する **arm-templates** フォルダー
* ffmpeg のバイナリを含む **hpc-application** フォルダー
* ビルド パイプラインの定義を含む **pipelines** フォルダー
* **省略可能**: .NET アプリケーションのコードを格納する **client-application** フォルダー。 これはサンプルでは使用しませんが、ユーザー自身のプロジェクトで、クライアント アプリケーションを介して HPC バッチ アプリケーションを実行したい場合があります。

> [!NOTE]
> これはコードベースの構造の一例です。 このアプローチを使用しているのは、アプリケーション、インフラストラクチャ、およびパイプライン コードが同じリポジトリに格納されることを示すためです。

ソース コードが設定されたところで、最初の構築を開始できます。

## <a name="continuous-integration"></a>継続的インテグレーション

[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops) は Azure DevOps Services に含まれ、アプリケーションのビルド、テスト、デプロイの各パイプラインの実装に役立ちます。

通常、パイプラインのこの段階では、コードを検証してソフトウェアの該当する部分を構築するために、テストを実行します。 テストの回数や種類、および実行するその他のタスクは、ビルドとリリースの多様な戦略によって異なります。

## <a name="preparing-the-hpc-application"></a>HPC アプリケーションを準備する

この例では、特に **hpc-application**フォルダーについて説明します。 **hpc-application** フォルダーは、Azure Batch アカウント内から実行される ffmpeg ソフトウェアです。

1. Azure DevOps 組織の Azure Pipelines の [ビルド] セクションに移動します。 **新しいパイプライン**を作成します。

    ![新しいビルド パイプラインを作成する](media/batch-ci-cd/new-build-pipeline.jpg)

1. ビルド パイプラインを作成するには、次の 2 つの方法があります。

    a. [ビジュアル デザイナーを使用します](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav)。 これを使用するには、**[新しいパイプライン]** ページで [ビジュアル デザイナーを使用する] をクリックします。

    b. [YAML ビルドを使用します](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops)。 新しい YAMLパイプラインを作成するには、[新しいパイプライン] ページで [Azure Repos] または [GitHub] オプションをクリックします。 または、[Visual Designer] をクリックしてから YAML テンプレートを使用することで、以下の例をソース管理に格納して、既存の YAML ファイルを参照できます。

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

1. 必要に応じてビルドが構成されたら、**[保存してキューに登録]** を選択します。 継続的インテグレーションを有効にしている場合 (**[トリガー]** セクション)、リポジトリへの新しいコミットが行われ、ビルドに設定された条件を満たすと、ビルドが自動的にトリガーされます。

    ![既存のビルド パイプラインの例](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Azure DevOps のビルドの進行状況のライブ更新を表示するには、Azure Pipelines の **[ビルド]** セクションに移動します。 ビルド定義から適切なビルドを選択します。

    ![ビルドのライブ出力の表示](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> HPC バッチ アプリケーションを実行するためにクライアント アプリケーションを使用する場合は、そのアプリケーションのために別のビルド定義を作成する必要があります。 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) のドキュメントにいくつもの攻略ガイドがあります。

## <a name="continuous-deployment"></a>継続的なデプロイ

また、Azure Pipelines を使用して、アプリケーションと基になるインフラストラクチャをデプロイします。 [リリース パイプライン](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops)は、継続的配置を有効にし、リリース プロセスを自動化するコンポーネントです。

### <a name="deploying-your-application-and-underlying-infrastructure"></a>アプリケーションと基になるインフラストラクチャのデプロイ

インフラストラクチャのデプロイには多くの手順が関係します。 [リンクされたテンプレート](../azure-resource-manager/resource-group-linked-templates.md)を使用しているため、それらのテンプレートにパブリック エンドポイント (HTTP または HTTPS) からアクセスできる必要があります。 このためには、GitHub 上のリポジトリ、Azure Blob Storage アカウント、または別の保存場所を使用できます。 アップロードされたテンプレート成果物は、プライベート モードで保持されるが、なんらかの形式の Shared Access Signature (SAS) トークンを使用してアクセスできるため、安全性が保たれます。 次の例では、Azure Storage BLOB のテンプレートを含むインフラストラクチャをデプロイする方法を説明します。

1. **新しいリリース定義**を作成し、空の定義を選択します。 次に、新しく作成された環境の名前を、パイプラインに関連するものに変更する必要があります。

    ![初期のリリース パイプライン](media/batch-ci-cd/Release-0.jpg)

1. ビルド パイプラインに対する依存関係を作成して、HPC アプリケーションの出力を取得します。

    > [!NOTE]
    > ここでも、**[ソース エイリアス]** に注意してください。これは、リリース定義内にタスクが作成されるときに必要になります。

    ![適切なビルド パイプラインに HPCApplicationPackage に対する成果物リンクを作成する](media/batch-ci-cd/Release-1.jpg)

1. ここでは、別の成果物 Azure Repo に対するリンクを作成します。 これは、リポジトリに格納されている Resource Manager テンプレートにアクセスするために必要です。 Resource Manager テンプレートのコンパイルは必要ないため、ビルド パイプラインを通じてプッシュする必要はありません。

    > [!NOTE]
    > ここでも、**[ソース エイリアス]** に注意してください。これは、リリース定義内にタスクが作成されるときに必要になります。

    ![Azure Repos への成果物リンクを作成する](media/batch-ci-cd/Release-2.jpg)

1. **[変数]** セクションに移動します。 複数のタスクに同じ情報を入力せずにすむように、パイプラインに多くの変数を作成することをお勧めします。 この例で使用される変数とそれらのデプロイへの影響を次に示します。

    * **applicationStorageAccountName**:HPC アプリケーション バイナリを保持するストレージ アカウントの名前
    * **batchAccountApplicationName**:Azure Batch アカウント内のアプリケーションの名前
    * **batchAccountName**:Azure Batch アカウントの名前
    * **batchAccountPoolName**:処理を実行する VM のプールの名前
    * **batchApplicationId**:Azure Batch アプリケーションの一意の ID
    * **batchApplicationVersion**:バッチ アプリケーション (つまり ffmpeg バイナリ) のセマンティック バージョン
    * **location**:Azure リソースをデプロイする場所
    * **resourceGroupName**:作成するリソース グループ (リソースがデプロイされる) の名前
    * **storageAccountName**:リンクされた Resource Manager テンプレートを保持するストレージ アカウントの名前

    ![Azure Pipelines リリースの変数設定の例](media/batch-ci-cd/Release-4.jpg)

1. [開発] 環境のタスクに移動します。 以下のスナップショットでは 6 つのタスクを確認できます。 これらのタスクで行われるのは、圧縮された ffmpeg ファイルのダウンロード、入れ子になった Resource Manager テンプレートをホストするストレージ アカウントのデプロイ、Batch アカウントと必要な依存関係のデプロイ、Azure Batch アカウントでのアプリケーションの作成、およびアプリケーション パッケージの Azure Batch アカウントへのアップロードです。

    ![HPC アプリケーションを Azure Batch にリリースするために使用されるタスクの例](media/batch-ci-cd/Release-3.jpg)

1. **[Download Pipeline Artifact (Preview)]\(パイプライン成果物のダウンロード(プレビュー)\)** タスクを追加し、次のプロパティを設定します。
    * **[表示名]:** ApplicationPackage をエージェントにダウンロード
    * **[ダウンロードする成果物の名前]:** hpc-application
    * **[ダウンロード先のパス]**: $(System.DefaultWorkingDirectory)

1. 成果物を格納するストレージ アカウントを作成します。 ソリューションの既存のストレージ アカウントを使用できますが、自己完結型サンプルとコンテンツの分離のために、成果物 (具体的には Resource Manager テンプレート) のための専用ストレージ アカウントを作成します。

    **[Azure リソース グループの配置]** タスクを追加して、次のプロパティを設定します。
    * **[表示名]:** Resource Manager テンプレートのストレージ アカウントのデプロイ
    * **[Azure サブスクリプション]:** 適切な Azure サブスクリプションを選択します。
    * **アクション**:リソース グループを作成または更新します。
    * **[リソース グループ]**: $(resourceGroupName)
    * **[場所]**: $(location)
    * **[テンプレート]**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **[テンプレート パラメーターのオーバーライド]**: -accountName $(storageAccountName)

1. ソース管理からストレージ アカウントに成果物をアップロードします。 これを実行する Azure パイプライン タスクがあります。 このタスクの中で、ストレージ アカウント コンテナーの URL と SAS トークンを Azure Pipelines の変数に出力できます。 つまり、このエージェント フェーズ全体で再利用できます。

    **[Azure ファイル コピー]** タスクを追加し、次のプロパティを設定します。
    * **[ソース]:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **[Azure 接続の種類]**:Azure Resource Manager
    * **[Azure サブスクリプション]:** 適切な Azure サブスクリプションを選択します。
    * **[送信先の種類]**:Azure BLOB
    * **[RM ストレージ アカウント]**: $(storageAccountName)
    * **[コンテナー名]**: templates
    * **[ストレージ コンテナーの URI]**: templateContainerUri
    * **[ストレージ コンテナーの SAS トークン]**: templateContainerSasToken

1. オーケストレーター テンプレートをデプロイします。 前述のオーケストレーター テンプレートを思い出してください。SAS トークンに加えてストレージ アカウント コンテナー URL のパラメーターがあったことにも注意します。 Resource Manager テンプレートで必要な変数は、リリース定義の変数セクションに保持されるか、別の Azure Pipelines タスク (Azure Blob コピー タスクの一部など) から設定されることに注意してください。

    **[Azure リソース グループの配置]** タスクを追加して、次のプロパティを設定します。
    * **[表示名]:** Azure Batch のデプロイ
    * **[Azure サブスクリプション]:** 適切な Azure サブスクリプションを選択します。
    * **アクション**:リソース グループを作成または更新します。
    * **[リソース グループ]**: $(resourceGroupName)
    * **[場所]**: $(location)
    * **[テンプレート]**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **[テンプレート パラメーターのオーバーライド]**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

一般的には、Azure Key Vault タスクを使用します。 サービス プリンシパル (Azure サブスクリプションへの接続) に適切なアクセス ポリシーが設定されている場合は、サービス プリンシパルが Azure Key Vault からシークレットをダウンロードでき、パイプラインで変数として使用できます。 シークレットの名前として関連する値が設定されます。 たとえば、リリース定義で sshPassword のシークレットを $(sshPassword) と呼ぶことができます。

1. 次の手順では Azure CLI を呼び出します。 1 つ目は、Azure Batch にアプリケーションを作成し、 関連付けられたパッケージをアップロードするために使用されます。

    **[Azure CLI]** タスクを追加し、次のプロパティを設定します。
    * **[表示名]:** Azure Batch アカウントでのアプリケーションの作成
    * **[Azure サブスクリプション]:** 適切な Azure サブスクリプションを選択します。
    * **[スクリプトの場所]**:インライン スクリプト
    * **[インライン スクリプト]**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. 2 つ目の手順は、関連付けられたパッケージをアプリケーションにアップロードするために使用されます。 この例では、ffmpeg ファイルです。

    **[Azure CLI]** タスクを追加し、次のプロパティを設定します。
    * **[表示名]:** パッケージの Azure Batch アカウントへのアップロード
    * **[Azure サブスクリプション]:** 適切な Azure サブスクリプションを選択します。
    * **[スクリプトの場所]**:インライン スクリプト
    * **[インライン スクリプト]**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > アプリケーション パッケージのバージョン番号は変数に設定されます。 これが便利なのは、パッケージの以前のバージョンを上書きすると役立つ場合や、Azure Batch にプッシュするパッケージのバージョン番号を手動で管理したい場合です。

1. **[リリース] > [新しいリリースの作成]** を選択して、新しいリリースを作成します。 トリガーされたら、新しいリリースのリンクを選択して状態を表示します。

1. 環境の下にある **[ログ]** ボタンを選択すると、エージェントからのライブ出力を見ることができます。

    ![リリースの状態を表示する](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>環境のテスト

環境を設定したら、次のテストが正常に完了することを確認します。

PowerShell コマンド プロンプトから Azure CLI を使用して新しい Azure Batch アカウントに接続します。

* Azure アカウントに `az login` でサインインし、指示に従って認証します。
* ここで、`az batch account login -g <resourceGroup> -n <batchAccount>` のように Batch アカウントを認証します。

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

## <a name="next-steps"></a>次の手順

この記事の他に、.NET と Python を使用して ffmpeg を利用する 2 つのチュートリアルがあります。 単純なアプリケーションを介して Batch アカウントを操作する方法について詳しくは、次のチュートリアルをご覧ください。

* [Python API を使用して Azure Batch で並列ワークロードを実行する](tutorial-parallel-python.md)
* [.NET API を使用して Azure Batch で並列ワークロードを実行する](tutorial-parallel-dotnet.md)
