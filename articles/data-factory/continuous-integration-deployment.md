---
title: Azure Data Factory における継続的インテグレーションとデリバリー
description: 継続的インテグレーションとデリバリーを使用して Data Factory パイプラインをある環境 (開発、テスト、運用) から別の環境に移動する方法について説明します。
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: aa2c5801e61fb73219934c5d38e894520c41ab26
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784035"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure Data Factory における継続的インテグレーションとデリバリー

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概要

継続的インテグレーションは、コードベースに対して行われた変更を、できるだけ早く自動的にテストするプラクティスです。  継続的デリバリーは、継続的インテグレーションの間に発生したテストに続けて、変更をステージングまたは実稼働システムにプッシュします。

Azure Data Factory では、継続的インテグレーションと継続的デリバリー (CI/CD) とは、Data Factory パイプラインをある環境 (開発、テスト、運用) から別の環境に移動することを意味します。 Azure Data Factory では [Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)を活用し、さまざまな ADF エンティティ (パイプライン、データセット、データ フローなど) の構成を保存します。 データ ファクトリを別の環境に昇格させる手法が 2 つ提案されています。

-    Data Factory と [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) の統合を利用した自動化されたデプロイ
-    Data Factory UX と Azure Resource Manager の統合を利用した Resource Manager テンプレートの手動アップロード。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD のライフサイクル

Azure Repos Git で構成された Azure Data Factory での CI/CD のライフサイクルのサンプル概要を以下に示します。 Git リポジトリを構成する方法の詳細については、「[Azure Data Factory のソース管理](source-control.md)」を参照してください。

1.  Azure Repos Git で開発データ ファクトリが作成され、構成されます。 すべての開発者には、パイプラインやデータセットなどの Data Factory リソースを作成するためのアクセス許可が必要です。

1.  開発者は変更を行う目的で[機能ブランチを作成](source-control.md#creating-feature-branches)します。 一番新しい変更を利用し、パイプライン実行をデバッグします。 パイプライン実行をデバッグする方法の詳細については、「[Azure Data Factory での反復開発とデバッグ](iterative-development-debugging.md)」を参照してください。

1.  開発者は、変更の結果に問題がなければ、各自の機能ブランチからメインまたはコラボレーション ブランチへのプル要求を作成して、同僚が変更をレビューできるようにします。

1.  プル要求が承認され、変更がメイン ブランチにマージされたら、変更が開発ファクトリに発行されます。

1.  チームは、変更をテストまたは UAT (ユーザー受け入れテスト) ファクトリにデプロイする準備ができたら、Azure Pipelines リリースに進み、望ましいバージョンの開発ファクトリを UAT にデプロイします。 このデプロイは Azure Pipelines タスクの一環として行われ、Resource Manager テンプレート パラメーターを使用し、適切な構成を適用します。

1.  テスト ファクトリで変更の妥当性が確認されたら、パイプライン リリースの次のタスクを利用し、運用環境のファクトリにデプロイします。

> [!NOTE]
> 開発ファクトリのみ、Git リポジトリに関連付けられます。 テスト ファクトリと運用環境のファクトリには Git リポジトリを関連付けません。更新には必ず Azure DevOps パイプラインか Resource Management テンプレートを利用してください。

下の図は、このライフサイクルのさまざまなステップを示しています。

![Azure Pipelines を使用した継続的インテグレーションの図](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Azure Pipelines リリースを使用して継続的インテグレーションを自動化する

以下は、Azure Pipelines リリースを設定し、複数の環境へのデータ ファクトリのデプロイを自動化するためのガイドです。

### <a name="requirements"></a>必要条件

-   [Azure Resource Manager サービス エンドポイント](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)を使用して Visual Studio Team Foundation Server または Azure Repos にリンクされた Azure サブスクリプション。

-   Azure Repos Git 統合で構成されたデータ ファクトリ。

-   各環境用のシークレットを格納する [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

### <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines リリースをセットアップする

1.  [Azure DevOps](https://dev.azure.com/) で、データ ファクトリで構成したプロジェクトを開きます。

1.  ページの左側で、 **[パイプライン]** を選択して **[リリース]** を選択します。

    ![パイプラインの選択、リリース](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  **[新しいパイプライン]** を選択するか、既存のパイプラインがある場合は **[新規]** 、 **[新しいリリース パイプライン]** の順に選択します。

1.  **[空のジョブ]** テンプレートを選択します。

    ![空のジョブの選択](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  **[ステージ名]** ボックスに、環境の名前を入力します。

1.  **[成果物の追加]** を選択し、開発データ ファクトリで構成した Git リポジトリを選択します。 **既定のブランチ** のリポジトリの [発行ブランチ](source-control.md#configure-publishing-settings)を選択します。 既定では、この発行ブランチは `adf_publish` です。 **[既定のバージョン]** では **[既定のブランチからの最新バージョン]** を選択します。

    ![アーティファクトの追加](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager デプロイ タスクを追加します。

    a.  ステージ ビューで、 **[ステージ タスクを表示します]** を選択します。

    ![ステージ ビュー](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  新しいタスクを作成します。 **[ARM テンプレートのデプロイ]** を探して **[追加]** を選択します。

    c.  デプロイ タスクでは、ターゲットのデータ ファクトリのサブスクリプション、リソース グループ、および場所を選択します。 必要であれば資格情報を指定します。

    d.  **[Action]\(アクション\)** 一覧で、 **[Create or update resource group]\(リソース グループの作成または更新\)** を選択します。

    e.  **[テンプレート]** ボックスの横にある省略記号ボタン ( **[…]** ) を選択します。 構成されている Git リポジトリの発行ブランチで生成される Azure Resource Manager テンプレートを参照します。 ファイル `ARMTemplateForFactory.json` は adf_publish ブランチの <FactoryName> フォルダーで探します。

    f.  **[…]** を選択します ( **[Template parameters]\(テンプレート パラメーター\)** ボックスの横にあります)。これにより、パラメーター ファイルを選択します。 ファイル `ARMTemplateParametersForFactory.json` は adf_publish ブランチの <FactoryName> フォルダーで探します。

    g.  **[…]** を選択します ( **[テンプレート パラメーターのオーバーライド]** ボックスの横にあります)。ターゲットのデータ ファクトリに望ましいパラメーター値を入力します。 Azure Key Vault から取得した資格情報の場合は、シークレットの名前を二重引用符で囲んで入力します。 たとえば、シークレットの名前が cred1 の場合は、この値に「 **"$ (cred1)"** 」と入力します。

    h. **[配置モード]** で **[Incremental]\(増分\)** を選択します。

    > [!WARNING]
    > 完全なデプロイ モードでは、リソース グループに存在していても、新しい Resource Manager テンプレート内で指定されていないリソースは、**削除** されます。 詳細については、「[Azure Resource Manager のデプロイ モード](../azure-resource-manager/templates/deployment-modes.md)」を参照してください。

    ![データ ファクトリの運用環境デプロイ](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  リリース パイプラインを保存します。

1. リリースをトリガーするには、 **[Create release]\(リリースの作成\)** を選択します。 リリースの作成を自動化するには、[Azure DevOps のリリース トリガー](/azure/devops/pipelines/release/triggers)に関するページを参照してください。

   ![[Create release]\(リリースの作成\) の選択](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> CI/CD のシナリオでは、異なる環境内の統合ランタイム (IR) の種類は同じである必要があります。 たとえば、開発環境にセルフホステッド IR がある場合、テストや運用などの他の環境でも、IR の種類はセルフホステッドである必要があります。 同様に、複数のステージ間で統合ランタイムを共有している場合は、開発、テスト、運用など、すべての環境で、統合ランタイムをリンクされたセルフホステッドとして構成する必要があります。

### <a name="get-secrets-from-azure-key-vault"></a>Azure Key Vault からシークレットを取得する

Azure Resource Manager テンプレートに渡すシークレットがある場合は、Azure Pipelines リリースで Azure Key Vault を使うことをお勧めします。

シークレットを処理する方法は 2 つあります。

1.  シークレットをパラメーター ファイルに追加します。 詳しくは、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)」をご覧ください。

    発行ブランチにアップロードされたパラメーター ファイルのコピーを作成します。 次の形式を使用して、Key Vault から取得するパラメーターの値を設定します。

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    このメソッドを使用すると、シークレットはキー コンテナーから自動的にプルされます。

    パラメーター ファイルも発行ブランチ内に存在する必要があります。

1. 前のセクションで説明されている Azure Resource Manager デプロイ タスクの前に、[Azure Key Vault タスク](/azure/devops/pipelines/tasks/deploy/azure-key-vault)を追加します。

    1.  **[タスク]** タブで、新しいタスクを作成します。 **Azure Key Vault** を検索して追加します。

    1.  Key Vault タスクで、キー コンテナーを作成したサブスクリプションを選択します。 必要に応じて資格情報を入力し、キー コンテナーを選択します。

    ![Key Vault タスクの追加](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines エージェントにアクセス許可を与える

適切なアクセス許可が設定されていない場合、Azure Key Vault タスクがアクセス拒否エラーで失敗することがあります。 リリースのログをダウンロードし、Azure Pipelines エージェントにアクセス許可を与えるコマンドを含む .ps1 ファイルを探します。 コマンドは直接実行できます。 または、ファイルからプリンシパル ID をコピーし、Azure portal でアクセス ポリシーを手動で追加できます。 必要な最低限のアクセス許可は、`Get` と `List` です。

### <a name="updating-active-triggers"></a>アクティブなトリガーを更新する

アクティブなトリガーを更新しようとした場合、デプロイは失敗します。 アクティブなトリガーを更新するには、手動でそれらを停止し、デプロイ後に再起動する必要があります。 これは、Azure Powershell タスクを使用して実行できます。

1.  リリースの **[タスク]** タブで、 **[Azure PowerShell]** タスクを追加します。 タスク バージョン 4.* を選択します。 

1.  お使いのファクトリがあるサブスクリプションを選択します。

1.  スクリプトの種類として **[スクリプト ファイル パス]** を選択します。 これにより、リポジトリへの PowerShell スクリプトの保存が求められます。 次の PowerShell スクリプトを使用すると、トリガーを停止できます。

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

(`Start-AzDataFactoryV2Trigger` 関数を使用して) 同様の手順を実行することで、デプロイ後にトリガーを再起動できます。

データ ファクトリ チームは、この記事の最後にある[サンプルのデプロイ前スクリプトとデプロイ後スクリプト](#script)を提供しています。 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>各環境用の Resource Manager テンプレートを手動でプロモートする

1. **[ARM テンプレート]** 一覧から、 **[ARM テンプレートのエクスポート]** を選択して、データ ファクトリ用の Resource Manager テンプレートを開発環境にエクスポートします。

   ![Resource Manager テンプレートのエクスポート](media/continuous-integration-deployment/continuous-integration-image1.png)

1. テストおよび運用データ ファクトリで、 **[Import ARM template]\(ARM テンプレートのインポート\)** を選択します。 このアクションによって Azure Portal に移動して、エクスポートされたテンプレートをインポートできます。 **[Build your own template in the editor]\(エディターで独自のテンプレートを作成する\)** を選択して、Resource Manager テンプレート エディターを開きます。

   ![独自のテンプレートの作成](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. **[ファイルの読み込み]** を選択し、生成された Resource Manager テンプレートを選択します。 これは、手順 1 でエクスポートした .zip ファイルに格納されている **arm_template.json** ファイルです。

   ![テンプレートの編集](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. 設定セクションで、リンクされたサービスの資格情報などの構成値を入力します。 完了したら、 **[購入]** を選択して Resource Manager テンプレートをデプロイします。

   ![設定セクション](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Resource Manager テンプレートでカスタム パラメーターを使用する

開発ファクトリに Git リポジトリが関連付けられている場合、テンプレートの公開やエクスポートによって生成された Resource Manager テンプレートの既定の Resource Manager テンプレート パラメーターをオーバーライドすることができます。 既定の Resource Manager パラメーター構成をオーバーライドする必要が生じる例としては、以下のシナリオが考えられます。

* 自動化された CI/CD を使用していて、Resource Manager のデプロイ中にいくつかのプロパティを変更したいが、プロパティが既定でパラメーター化されていない。
* ファクトリが非常に大きく、既定の Resource Manager テンプレートが許容されるパラメーターの上限 (256) よりも多いために無効である。

    カスタム パラメーターの 256 の上限に対処するには、次の 3 つのオプションがあります。    
  
    * カスタム パラメーター ファイルを使用してパラメーター化を必要としない (既定値を保持できる) プロパティを削除して、パラメーターの数を減らします。
    * データフローのロジックをリファクタリングして、パラメーターを減らします。たとえば、パイプライン パラメーターの値がすべて同じである場合は、代わりにグローバル パラメーターを使用できます。
    * 1 つのデータ ファクトリを複数のデータ フローに分割します。

既定の Resource Manager パラメーター構成をオーバーライドするには、 **[管理]** ハブに移動し、[ソース管理] セクションで **[ARM テンプレート]** を選択します。 **[ARM パラメーター構成]** セクションで、[パラメーター構成の編集] の **[編集]** アイコンをクリックして、Resource Manager パラメーター構成コード エディターを開きます。

![カスタム パラメーターの管理](media/author-management-hub/management-hub-custom-parameters.png)

> [!NOTE]
> **[ARM パラメーター構成]** は、"Git モード" でのみ有効になります。 現在のところ、"ライブ モード" や "Data Factory" モードでは無効になります。

カスタムの Resource Manager パラメーター テンプレート構成を作成すると、Git ブランチのルート フォルダーに **arm-template-parameters-definition.json** という名前のファイルが作成されます。 正確なファイル名を使用する必要があります。

![カスタム パラメーター ファイル](media/continuous-integration-deployment/custom-parameters.png)

コラボレーション ブランチから発行すると、Data Factory では、このファイルを読み取り、その構成を利用してパラメーター化するプロパティが生成されます。 ファイルが見つからない場合は、既定のテンプレートが使用されます。

Resource Manager テンプレートをエクスポートすると、Data Factory により、コラボレーション ブランチでなく、現在作業中のどのブランチからでもこのファイルを読み取られます。 プライベート ブランチからファイルを作成または編集し、UI の **[ARM テンプレートのエクスポート]** を選択して変更内容をテストすることができます。 その後、このファイルをコラボレーション ブランチ内にマージできます。

> [!NOTE]
> カスタムの Resource Manager パラメーター構成では、ARM テンプレート パラメーターの制限である 256 が変更されることはありません。 これにより、パラメーター化されたプロパティの数を選択して減らすことができます。

### <a name="custom-parameter-syntax"></a>カスタムのパラメーター構文

カスタム パラメーター ファイル **arm-template-parameters-definition.json** を作成する際のいくつかのガイドラインを次に示します。 ファイルは、(トリガー、パイプライン、リンクされたサービス、データセット、統合ランタイム、データ フロー) エンティティの種類ごとのセクションで構成されています。

* 関連するエンティティ型の下にプロパティ パスを入力します。
* プロパティ名を `*` に設定すると、その下 (再帰的にではなく、最初のレベルまでのみ) にあるすべてのプロパティをパラメーター化することを指示します。 また、この構成に例外を指定することもできます。
* プロパティの値を文字列として設定すると、プロパティをパラメーター化することを指示します。 「`<action>:<name>:<stype>`」の形式を使用します。
   *  `<action>` には、次のいずれかの文字を指定できます。
      * `=` は、パラメーターの既定値として現在の値を保持することを意味します。
      * `-` は、パラメーターの既定値を保持しないことを意味します。
      * `|` は、接続文字列またはキーに対する Azure Key Vault からのシークレットの特殊なケースです。
   * `<name>` は、パラメーターの名前です。 空白の場合は、プロパティの名前になります。 値が `-` 文字で始まる場合、名前は短縮されます。 たとえば、`AzureStorage1_properties_typeProperties_connectionString` は `AzureStorage1_connectionString` に短縮されます。
   * `<stype>` は、パラメーターの型です。 `<stype>` が空白の場合、既定の型は `string` です。 サポートされる値は `string`、`securestring`、`int`、`bool`、`object`、`secureobject`、および `array` です。
* 定義ファイルに配列を指定すると、テンプレート内の一致するプロパティが配列であることを指示します。 Data Factory は、配列の統合ランタイム オブジェクトに指定された定義を使用することで、配列内のすべてのオブジェクトを反復処理します。 2 番目のオブジェクトである文字列は、各反復処理のパラメーターの名前として使用されるプロパティ名です。
* 定義をリソース インスタンスに固有にすることはできません。 定義はその型のすべてのリソースに適用されます。
* 既定では、Key Vault シークレットなどのセキュリティで保護されたすべての文字列と、接続文字列、キー、トークンなどのセキュリティで保護された文字列がパラメーター化されます。
 
### <a name="sample-parameterization-template"></a>サンプルのパラメーター化テンプレート

次に示すのは、Resource Manager パラメーター構成の例です。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
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
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
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
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
ここでは、前のテンプレートがどのように構築されるかについて、リソースの種類別に説明します。

#### <a name="pipelines"></a>パイプライン
    
* パス `activities/typeProperties/waitTimeInSeconds` 内のすべてのプロパティがパラメーター化されます。 `waitTimeInSeconds` という名前のコードレベルのプロパティを持つパイプライン内のアクティビティ (たとえば、`Wait` アクティビティ) はすべて、既定の名前を使用して数値としてパラメーター化されます。 ただし、Resource Manager テンプレートにその既定値はありません。 これは Resource Manager デプロイ時に必須の入力になります。
* 同様に、`headers` というプロパティ (たとえば、`Web` アクティビティ内の) は、型 `object` (JObject) でパラメーター化されます。 これは、ソース ファクトリの値と同じ値である既定値を保持します。

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

> [!NOTE]
> **Azure のアラートとマトリックス** は、パイプライン用に構成されている場合、ARM デプロイのパラメーターとして現在サポートされていません。 新しい環境にアラートとマトリックスを再適用するには、 [Data Factory の監視、アラート、およびマトリックス](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-metrics)に関するページに従ってください。
> 

### <a name="default-parameterization-template"></a>既定のパラメーター化テンプレート

次に、現在の既定のパラメーター化テンプレートを示します。 いくつかのパラメーターのみを追加する必要がある場合は、このテンプレートを直接編集することをお勧めします。既存のパラメーター化構造が失われないためです。

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>例: 既存の Azure Databricks 対話型クラスター ID のパラメーター化

次の例は、既定のパラメーター化テンプレートに 1 つの値を追加する方法を示しています。 Databricks のリンクされたサービスの既存の Azure Databricks インタラクティブ クラスター ID をパラメーター ファイルに追加するだけです。 このファイルは、`Microsoft.DataFactory/factories/linkedServices` のプロパティ フィールドの下に `existingClusterId` が追加されていることを除き、前のファイルと同じであることに注意してください。

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>リンクされた Resource Manager テンプレート

データ ファクトリの CI/CD を設定済みの場合、ファクトリが大規模になるにつれて Azure Resource Manager テンプレートの制限を超える可能性があります。 たとえば、1 つの制限として、Resource Manager テンプレート内のリソースの最大数があります。 ファクトリ用の完全な Resource Manager テンプレートを生成しながら、大規模なファクトリに対応するために、Data Factory では、リンクされた Resource Manager テンプレートが生成されるようになりました。 この機能を使用すると、ファクトリのペイロード全体が複数のファイルに分割されるので、制限によって制約されることはありません。

Git が構成されている場合は、リンクされたテンプレートが生成され、完全な Resource Manager テンプレートと共に、 adf_publish ブランチの linkedTemplates という名前の新しいフォルダーに保存されます。

![リンクされた Azure Resource Manager テンプレートのフォルダー](media/continuous-integration-deployment/linked-resource-manager-templates.png)

通常、リンクされた Resource Manager テンプレートは、マスター テンプレートと、マスターにリンクされた一連の子テンプレートで構成されます。 親テンプレートは ArmTemplate_master.json という名前になり、子テンプレートには、ArmTemplate_0. json、ArmTemplate_1.json といったパターンで名前が付けられます。 

完全な Resource Manager テンプレートではなく、リンクされたテンプレートを使用するには、ArmTemplateForFactory.json (完全な Resource Manager テンプレート) ではなく ArmTemplate_master.json をポイントするように CI/CD タスクを更新します。 Resource Manager では、リンクされたテンプレートをストレージ アカウントにアップロードして、デプロイ時に Azure によってアクセスできるようにする必要もあります。 詳細については、[リンクされた Resource Manager テンプレートの VSTS を使用したデプロイ](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts)に関する記事を参照してください。

Data Factory のスクリプトは、必ず CI/CD パイプラインのデプロイ タスクの前後に追加してください。

Git が構成されていない場合は、 **[ARM テンプレート]** 一覧の **[ARM テンプレートのエクスポート]** を使用して、リンクされたテンプレートにアクセスできます。

## <a name="hotfix-production-environment"></a>運用環境への修正プログラムの適用

ファクトリを運用環境にデプロイし、すぐに修正が必要なバグがあることが判明したが現在のコラボレーション ブランチをデプロイできない場合、修正プログラムのデプロイが必要なことがあります。 この方法は、クイック修正エンジニアリングまたは QFE と呼ばれています。

1.    Azure DevOps で、運用環境にデプロイされたリリースに移動します。 デプロイされた最後のコミットを見つけます。

2.    コミット メッセージから、コラボレーション ブランチのコミット ID を取得します。

3.    そのコミットから新しい修正プログラム ブランチを作成します。

4.    Azure Data Factory UX に移動し、修正プログラム ブランチに切り替えます。

5.    Azure Data Factory UX を使用して、バグを修正します。 変更をテストします。

6.    修正が検証されたら、 **[ARM テンプレートのエクスポート]** を選択して、修正プログラムの Resource Manager テンプレートを取得します。

7.    このビルドを adf_publish ブランチに手動でチェックインします。

8.    adf_publish のチェックインに基づいて自動的にトリガーするようにリリース パイプラインを構成している場合、新しいリリースは自動的に開始します。 それ以外の場合は、手動でリリースをキューに配置します。

9.    テストと運用ファクトリに修正プログラム リリースをデプロイします。 このリリースには、以前の運用ペイロードに加えて、手順 5 で行った修正が含まれています。

10.   修正プログラムでの変更を開発ブランチに追加して、今後のリリースに同じバグが含まれないようにします。

環境に修正プログラムを適用する方法については、次の詳細なビデオ チュートリアルの動画をご覧ください。 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="exposure-control-and-feature-flags"></a>露出調整と機能フラグ

チームで作業しているときに、変更をマージする場合があるかもしれませんが、PROD や QA などより高度な環境では実行しないかもしれません。 このようなシナリオに対応するため、ADF チームでは、[機能フラグを使用する DevOps 構想](/azure/devops/migrate/phase-features-with-feature-flags)をお勧めしています。 ADF では、[グローバル パラメーター](author-global-parameters.md)と [If Condition アクティビティ](control-flow-if-condition-activity.md)を組み合わせて、これらの環境フラグに基づいてロジックのセットを非表示にすることができます。

機能フラグを設定する方法については、次のビデオ チュートリアルをご覧ください。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IxdW]

## <a name="best-practices-for-cicd"></a>CI/CD のベスト プラクティス

データ ファクトリとの Git 統合を使用していて、変更を開発からテストを経て運用環境に移行する CI/CD パイプラインがある場合は、次のベスト プラクティスをお勧めします。

-   **Git 統合**。 Git 統合で開発データ ファクトリのみを構成します。 テスト環境と運用環境への変更は CI/CD を介してデプロイされるので、Git 統合は必要ありません。

-   **デプロイ前とデプロイ後のスクリプト**。 CI/CD の Resource Manager のデプロイ手順の前に、トリガーの停止と再起動やクリーンアップの実行など、特定のタスクを完了する必要があります。 デプロイ タスクの前後に PowerShell スクリプトを使用することをお勧めします。 詳細については、「[アクティブなトリガーを更新する](#updating-active-triggers)」を参照してください。 データ ファクトリ チームは、このページの終わりに使用する[スクリプトを提供](#script)しています。

-   **統合ランタイムと共有**。 統合ランタイムは頻繁には変更されず、CI/CD のすべてのステージで類似しています。 そのため、Data Factory では、CI/CD のすべてのステージで統合ランタイムの名前と種類を同じにすることが求められます。 すべてのステージで統合ランタイムを共有する場合は、共有の統合ランタイムを含めるためだけに三項ファクトリを使用することを検討してください。 この共有ファクトリは、すべての環境で、リンクされた統合ランタイムの種類として使用できます。

-   **マネージド プライベート エンドポイント デプロイ**。 プライベート エンドポイントがファクトリに既に存在している場合、同じ名前と変更されたプロパティを持つプライベート エンドポイントを含む ARM テンプレートをデプロイしようとすると、デプロイが失敗します。 つまり、既にファクトリに存在するものと同じプロパティを持つ限り、プライベート エンド ポイントを正常にデプロイできます。 環境によってプロパティが異なる場合は、そのプロパティをパラメーター化し、デプロイ時にそれぞれの値を指定することでオーバーライドできます。

-   **Key Vault**。 リンクされているサービスを使用するとき、その接続情報が Azure Key Vault に格納されている場合、キー コンテナーを環境別に保持することが推奨されます。 キー コンテナーごとに個別のアクセス許可レベルを構成することもできます。 たとえば、運用環境のシークレットへのアクセス許可をチーム メンバーに持たせたくない場合があります。 このアプローチに従う場合は、すべてのステージで同じシークレット名を保持することをお勧めします。 同じシークレット名を保持する場合、CI/CD 環境全体で各接続文字列をパラメーター化する必要はありません。変わるのは別個のパラメーターであるキー コンテナーの名前だけだからです。

-  **リソースの名前付け** ARM テンプレートの制約により、リソースの名前にスペースが含まれていると、デプロイで問題が発生する可能性があります。 Azure Data Factory チームは、リソースではスペースの代わりに "_" または "-" 文字を使用することをお勧めします。 たとえば、"Pipeline 1" ではなく "Pipeline_1" という名前を使用します。

## <a name="unsupported-features"></a>サポートされていない機能

- 設計上、Data Factory では、コミットのチェリーピックやリソースの選択的発行は許可されません。 発行には、データ ファクトリで加えられたすべての変更が含まれます

    - データ ファクトリのエンティティは相互に依存しています。 たとえば、トリガーはパイプラインに依存し、パイプラインはデータセットや他のパイプラインに依存しています。 リソースのサブセットを選択的に発行すると、予期しない動作やエラーにつながる可能性があります。
    - まれに、選択的な発行が必要になった場合は、修正プログラムの使用を検討してください。 詳細については、「[運用環境への修正プログラムの適用](#hotfix-production-environment)」を参照してください。

- Azure Data Factory チームでは、データ ファクトリ内の個々のエンティティ (パイプライン、データセットなど) に Azure RBAC 制御を割り当てることを推奨していません。 たとえば、開発者がパイプラインまたはデータセットにアクセスできる場合、データ ファクトリ内のすべてのパイプラインまたはデータセットにアクセスできるはずです。 データ ファクトリ内に多数の Azure RBAC ロールを実装する必要があると思う場合は、2 つ目のデータ ファクトリをデプロイすることを検討してください。

-   非公開のブランチから発行することはできません。

-   現在、Bitbucket でプロジェクトをホストすることはできません。

-   現在、アラートとマトリックスをパラメーターとしてエクスポートおよびインポートすることはできません。 

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a> サンプルのデプロイ前とデプロイ後のスクリプト

次のサンプル スクリプトは、トリガーをデプロイ前に停止し、デプロイ後に再起動するために使用できます。 このスクリプトには、削除済みのリソースを完全に削除するコードも含まれています。 Azure DevOps の git リポジトリにスクリプトを保存し、バージョン 4.* で Azure PowerShell タスクを介して参照します。

デプロイ前スクリプトを実行する場合は、 **[スクリプトの引数]** フィールドで、次のパラメーターのバリエーションを指定する必要があります。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


デプロイ後スクリプトを実行する場合は、 **[スクリプトの引数]** フィールドで、次のパラメーターのバリエーションを指定する必要があります。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell タスク](media/continuous-integration-deployment/continuous-integration-image11.png)

こちらがデプロイ前/後に使用できるスクリプトです。 削除されたリソースとリソース参照を示しています。

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```
