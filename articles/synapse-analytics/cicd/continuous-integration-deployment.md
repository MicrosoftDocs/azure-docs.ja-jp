---
title: Synapse ワークスペースの継続的インテグレーションとデリバリー
description: 継続的インテグレーションとデリバリーを使用してワークスペース内の変更をある環境 (開発、テスト、運用) から別の環境にデプロイする方法について説明します。
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: de3738573bb9bb6f045a45d290c74ba9e6902a5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561959"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Azure Synapse ワークスペースの継続的インテグレーションとデリバリー

## <a name="overview"></a>概要

継続的インテグレーション (CI) は、チーム メンバーが変更をバージョン コントロールにコミットするたびに、コードのビルドとテストを自動化するプロセスです。 継続的配置 (CD) は、複数のテスト環境またはステージング環境から運用環境へのビルド、テスト、構成、およびデプロイを行うプロセスです。

Azure Synapse ワークスペースでは、継続的インテグレーションとデリバリー (CI/CD) を使用して、すべてのエンティティをある環境 (開発、テスト、運用) から別の環境に移動します。 ワークスペースを別のワークスペースに昇格させるには、次の 2 つの部分があります。[Resource Manager テンプレート](../../azure-resource-manager/templates/overview.md)を使用して、ワークスペース リソース (プールとワークスペース) を作成または更新します。Azure DevOps の Synapse CI/CD ツールを使用して、アーティファクト (SQL スクリプト、ノートブック、Spark ジョブの定義、パイプライン、データセット、データフローなど) を移行します。 

この記事では、Azure リリース パイプラインを使用して、複数の環境への Synapse ワークスペースのデプロイを自動化する方法について説明します。

## <a name="prerequisites"></a>[前提条件]

-   開発に使用されているワークスペースは、Studio の Git リポジトリを使用して構成されています。[Synapse Studio でのソース管理](source-control.md)に関するページを参照してください。
-   リリース パイプラインを実行するための Azure DevOps プロジェクトが準備されています。

## <a name="set-up-a-release-pipelines"></a>リリース パイプラインの設定

1.  [Azure DevOps](https://dev.azure.com/) で、リリース用に作成されたプロジェクトを開きます。

1.  ページの左側で、 **[パイプライン]** を選択して **[リリース]** を選択します。

    ![パイプラインの選択、リリース](media/create-release-1.png)

1.  **[新しいパイプライン]** を選択するか、既存のパイプラインがある場合は **[新規]** 、 **[新しいリリース パイプライン]** の順に選択します。

1.  **[空のジョブ]** テンプレートを選択します。

    ![空のジョブの選択](media/create-release-select-empty.png)

1.  **[ステージ名]** ボックスに、環境の名前を入力します。

1.  **[成果物の追加]** を選択し、開発 Synapse Studio で構成した Git リポジトリを選択します。 プールとワークスペースの ARM テンプレートを管理するために使用した Git リポジトリを選択します。 ソースとして GitHub を使用する場合は、GitHub アカウントとプル リポジトリのサービス接続を作成する必要があります。 [サービス接続](/azure/devops/pipelines/library/service-endpoints)の詳細情報 

    ![発行ブランチの追加](media/release-creation-github.png)

1.  リソース更新用の ARM テンプレートのブランチを選択します。 **[既定のバージョン]** では **[既定のブランチからの最新バージョン]** を選択します。

    ![ARM テンプレートの追加](media/release-creation-arm-branch.png)

1.  **既定のブランチ** のリポジトリの [発行ブランチ](source-control.md#configure-publishing-settings)を選択します。 既定では、この発行ブランチは `workspace_publish` です。 **[既定のバージョン]** では **[既定のブランチからの最新バージョン]** を選択します。

    ![アーティファクトの追加](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>ARM リソースの作成と更新のステージ タスクを設定する 

リソース (ワークスペース、プールなど) を作成または更新するための Azure Resource Manager デプロイ タスクを追加します。

1. ステージ ビューで、 **[ステージ タスクを表示します]** を選択します。

    ![ステージ ビュー](media/release-creation-stage-view.png)

1. 新しいタスクを作成します。 **[ARM テンプレートのデプロイ]** を探して **[追加]** を選択します。

1. デプロイ タスクでは、ターゲットのワークスペースのサブスクリプション、リソース グループ、および場所を選択します。 必要であれば資格情報を指定します。

1. **[Action]\(アクション\)** 一覧で、 **[Create or update resource group]\(リソース グループの作成または更新\)** を選択します。

1. **[テンプレート]** ボックスの横にある省略記号ボタン ( **[…]** ) を選択します。 ターゲットのワークスペースの Azure Resource Manager テンプレートを参照します

1. **[…]** を選択します ( **[Template parameters]\(テンプレート パラメーター\)** ボックスの横にあります)。これにより、パラメーター ファイルを選択します。

1. **[…]** を選択します ( **[テンプレート パラメーターのオーバーライド]** ボックスの横にあります)。ターゲットのワークスペースに望ましいパラメーター値を入力します。 

1. **[配置モード]** で **[Incremental]\(増分\)** を選択します。
    
    ![ワークスペースとプールのデプロイ](media/pools-resource-deploy.png)

1. (省略可能) ワークスペースの付与と更新のロール割り当てのために **Azure PowerShell** を追加します。 リリース パイプラインを使用して Synapse ワークスペースを作成する場合は、パイプラインのサービス プリンシパルが既定のワークスペース管理者として追加されます。PowerShell を実行して、他のアカウントにワークスペースへのアクセスを許可することができます。 
    
    ![アクセス許可の付与](media/release-creation-grant-permission.png)

 > [!WARNING]
> 完全なデプロイ モードでは、リソース グループに存在していても、新しい Resource Manager テンプレート内で指定されていないリソースは、**削除** されます。 詳細については、「[Azure Resource Manager のデプロイ モード](../../azure-resource-manager/templates/deployment-modes.md)」を参照してください。

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>アーティファクトのデプロイのステージ タスクを設定する 

[Synapse ワークスペースのデプロイ](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy)拡張機能を使用して、データセット、SQL スクリプト、ノートブック、Spark ジョブ定義、データフロー、パイプライン、リンクされたサービス、資格情報、IR (Integration Runtime) などの他のアイテムを Synapse ワークスペースにデプロイします。  

1. この拡張機能を **Azure DevOps マーケットプレース** で検索して入手します (https://marketplace.visualstudio.com/azuredevops) 

     ![拡張機能の入手](media/get-extension-from-market.png)

1. 拡張機能をインストールする組織を選択します。 

     ![拡張機能のインストール](media/install-extension.png)

1. Azure DevOps パイプラインのサービス プリンシパルにサブスクリプションのアクセス許可が付与され、ターゲットのワークスペースのワークスペース管理者としても割り当てられていることを確認します。 

1. 新しいタスクを作成します。 **[Synapse ワークスペースのデプロイ]** を探して **[追加]** を選択します。

     ![拡張機能の追加](media/add-extension-task.png)

1.  タスクで、 **[…]** を選択します ( **[テンプレート]** ボックスの横にあります)。これにより、テンプレート ファイルを選択します。

1. **[…]** を選択します ( **[Template parameters]\(テンプレート パラメーター\)** ボックスの横にあります)。これにより、パラメーター ファイルを選択します。

1. ターゲット ワークスペースの接続、リソース グループ、および名前を選択します。 

1. **[…]** を選択します ( **[テンプレート パラメーターのオーバーライド]** ボックスの横にあります)。ターゲットのワークスペースに望ましいパラメーター値を入力します。 

    ![Synapse ワークスペースのデプロイ](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> CI/CD のシナリオでは、異なる環境内の統合ランタイム (IR) の種類は同じである必要があります。 たとえば、開発環境にセルフホステッド IR がある場合、テストや運用などの他の環境でも、IR の種類はセルフホステッドである必要があります。 同様に、複数のステージ間で統合ランタイムを共有している場合は、開発、テスト、運用など、すべての環境で、統合ランタイムをリンクされたセルフホステッドとして構成する必要があります。

## <a name="create-release-for-deployment"></a>デプロイのリリースを作成する 

すべての変更を保存したら、 **[リリースの作成]** を選択してリリースを手動で作成できます。 リリースの作成を自動化するには、[Azure DevOps のリリース トリガー](/azure/devops/pipelines/release/triggers)に関するページを参照してください。

   ![[Create release]\(リリースの作成\) の選択](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>ワークスペース テンプレートのカスタム パラメーターを使用する 

自動化された CI/CD を使用していて、デプロイ中にいくつかのプロパティを変更したいものの、プロパティが既定でパラメーター化されていないことがあります。 この場合は、既定のパラメーター テンプレートをオーバーライドできます。

既定のパラメーター テンプレートをオーバーライドするには、git コラボレーション ブランチのルート フォルダーに **template-parameters-definition.json** という名前のファイルをカスタム パラメーター テンプレートとして作成する必要があります。 正確なファイル名を使用する必要があります。 コラボレーション ブランチから発行すると、Synapse ワークスペースでは、このファイルを読み取り、その構成を利用してパラメーターが生成されます。 ファイルが見つからない場合は、既定のパラメーター テンプレートが使用されます。

### <a name="custom-parameter-syntax"></a>カスタムのパラメーター構文

カスタム パラメーター ファイルを作成するためのガイドラインを次に示します。

* 関連するエンティティ型の下にプロパティ パスを入力します。
* プロパティ名を `*` に設定すると、その下 (再帰的にではなく、最初のレベルまでのみ) にあるすべてのプロパティをパラメーター化することを指示します。 また、この構成に例外を指定することもできます。
* プロパティの値を文字列として設定すると、プロパティをパラメーター化することを指示します。 「`<action>:<name>:<stype>`」の形式を使用します。
   *  `<action>` には、次のいずれかの文字を指定できます。
      * `=` は、パラメーターの既定値として現在の値を保持することを意味します。
      * `-` は、パラメーターの既定値を保持しないことを意味します。
      * `|` は、接続文字列またはキーに対する Azure Key Vault からのシークレットの特殊なケースです。
   * `<name>` は、パラメーターの名前です。 空白の場合は、プロパティの名前になります。 値が `-` 文字で始まる場合、名前は短縮されます。 たとえば、`AzureStorage1_properties_typeProperties_connectionString` は `AzureStorage1_connectionString` に短縮されます。
   * `<stype>` は、パラメーターの型です。 `<stype>` が空白の場合、既定の型は `string` です。 サポートされる値は `string`、`securestring`、`int`、`bool`、`object`、`secureobject`、および `array` です。
* ファイルに配列を指定すると、テンプレート内の一致するプロパティが配列であることを指示します。 Synapse では、指定された定義を使用して、配列内のすべてのオブジェクトを反復処理します。 2 番目のオブジェクトである文字列は、各反復処理のパラメーターの名前として使用されるプロパティ名です。
* 定義をリソース インスタンスに固有にすることはできません。 定義はその型のすべてのリソースに適用されます。
* 既定では、Key Vault シークレットなどのセキュリティで保護されたすべての文字列と、接続文字列、キー、トークンなどのセキュリティで保護された文字列がパラメーター化されます。

### <a name="parameter-template-definition-samples"></a>パラメーター テンプレート定義のサンプル 

ここに、パラメーター テンプレート定義の例を示します。

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
ここでは、前のテンプレートがどのように構築されるかについて、リソースの種類別に説明します。

#### <a name="notebooks"></a>ノートブック 

* パス `properties/bigDataPool/referenceName` 内のすべてのプロパティは、既定値を使用してパラメーター化されます。 各ノートブック ファイルに対して、アタッチされた Spark プールをパラメーター化できます。 

#### <a name="sql-scripts"></a>SQL スクリプト 

* パス `properties/content/currentConnection` 内のプロパティ (poolName および databaseName) は、テンプレートに既定値を指定せずに文字列としてパラメーター化されます。 

#### <a name="pipelines"></a>パイプライン

* パス `activities/typeProperties/waitTimeInSeconds` 内のすべてのプロパティがパラメーター化されます。 `waitTimeInSeconds` という名前のコードレベルのプロパティを持つパイプライン内のアクティビティ (たとえば、`Wait` アクティビティ) はすべて、既定の名前を使用して数値としてパラメーター化されます。 ただし、Resource Manager テンプレートにその既定値はありません。 これは Resource Manager デプロイ時に必須の入力になります。
* 同様に、 (たとえば `Web` アクティビティ内の) `headers` というプロパティ は、型 `object` (Object) でパラメーター化されます。 これは、ソース ファクトリの値と同じ値である既定値を保持します。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* パス `typeProperties` の下にあるすべてのプロパティは、それぞれの既定値を使用してパラメーター化されます。 たとえば、`IntegrationRuntimes` 型のプロパティの下には `computeProperties` と `ssisProperties` という 2 つのプロパティがあります。 両方のプロパティの型は、それぞれの既定の値と型 (Object) で作成されます。

#### <a name="triggers"></a>トリガー

* `typeProperties` の下では、2 つのプロパティがパラメーター化されます。 1 つ目は `maxConcurrency` で、既定値を持つように指定されていて、型は `string` です。 既定のパラメーター名 `<entityName>_properties_typeProperties_maxConcurrency` を持ちます。
* `recurrence` プロパティもパラメーター化されます。 その下にあるそのレベルのすべてのプロパティは、既定の値とパラメーター名を持つ文字列としてパラメーター化されるよう指定されます。 例外は、型 `int` としてパラメーター化される `interval` プロパティです。 パラメーター名の末尾には `<entityName>_properties_typeProperties_recurrence_triggerSuffix` が付きます。 同様に、`freq` プロパティは文字列であり、文字列としてパラメーター化されます。 ただし、`freq` プロパティは既定値なしでパラメーター化されます。 名前は短縮され、サフィックスが付けられます。 たとえば、「 `<entityName>_freq` 」のように入力します。

#### <a name="linkedservices"></a>LinkedServices

* リンクされたサービスは一意です。 リンクされたサービスとデータセットにはさまざまな型があるため、型固有のカスタマイズを行うことができます。 この例では、型 `AzureDataLakeStore` のすべてのリンクされたサービスに対して、特定のテンプレートが適用されます。 他のすべての場合 (`*` を使用)、別のテンプレートが適用されます。
* `connectionString` プロパティは、`securestring` 値としてパラメーター化されます。 既定値はありません。 これには、末尾に `connectionString` が付く、短縮されたパラメーター名が付けられます。
* プロパティ `secretAccessKey` は (たとえば、Amazon S3 のリンクされたサービスでは) `AzureKeyVaultSecret` になることがあります。 Azure Key Vault シークレットとして自動的にパラメーター化され、構成済みのキー コンテナーからフェッチされます。 キー コンテナー自体もパラメーター化できます。

#### <a name="datasets"></a>データセット

* データセットに型固有のカスタマイズを使用できますが、\* レベルの構成を明示的に指定しなくても構成を指定できます。 前の例では、`typeProperties` の下にあるすべてのデータセット プロパティがパラメーター化されます。


## <a name="best-practices-for-cicd"></a>CI/CD のベスト プラクティス

Synapse ワークスペースとの Git 統合を使用していて、変更を開発からテストを経て運用環境に移行する CI/CD パイプラインがある場合は、次のベスト プラクティスをお勧めします。

-   **Git 統合**。 Git 統合で開発 Synapse ワークスペースのみを構成します。 テスト ワークスペースと運用ワークスペースへの変更は CI/CD を介してデプロイされるので、Git 統合は必要ありません。
-   **アーティファクトの移行前にプールを準備します**。 SQL スクリプトまたはノートブックが開発ワークスペース内のプールにアタッチされている場合は、別の環境にも同じ名前のプールが必要です。 
-   **コードとしてのインフラストラクチャ (IaC)** 。 記述型モデルでのインフラストラクチャ (ネットワーク、仮想マシン、ロード バランサー、接続トポロジ) の管理であり、DevOps チームがソース コードに使用するものと同じバージョン管理を使用します。 
-   **その他**。 [ADF アーティファクトのベスト プラクティス](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)に関するページを参照してください

## <a name="troubleshooting-artifacts-deployment"></a>アーティファクト デプロイのトラブルシューティング 

### <a name="use-the-synapse-workspace-deployment-task"></a>Synapse ワークスペース デプロイ タスクを使用する

Synapse には、ARM リソースではない複数のアーティファクトがあります。 これは、Azure Data Factory とは異なります。 Synapse アーティファクトをデプロイするために、ARM テンプレート デプロイ タスクを使用しても正しく機能しません。
 
### <a name="unexpected-token-error-in-release"></a>リリースにおける予期しないトークン エラー

エスケープ処理されていないパラメーター値がパラメーター ファイルに含まれる場合、リリース パイプラインでファイルを解析できず、"予期しないトークン" エラーが生成されます。 パラメーターをオーバーライドすること、または Azure KeyVault を使用してパラメーター値を取得することをお勧めします。 対処法としてダブル エスケープ文字を使用することもできます。
