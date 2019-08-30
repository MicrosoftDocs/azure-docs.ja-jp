---
title: Azure Data Factory における継続的インテグレーションとデリバリー | Microsoft Docs
description: 継続的インテグレーションとデリバリーを使用して Data Factory パイプラインをある環境 (開発、テスト、運用) から別の環境に移動する方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: craigg
ms.openlocfilehash: c090d9a864bfb5218836627a5579cd3089387af8
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013883"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Azure Data Factory における継続的インテグレーションと継続的デリバリー (CI/CD)

## <a name="overview"></a>概要

継続的インテグレーションは、コードベースに対して行われた変更を、できるだけ早く自動的にテストするプラクティスです。 継続的デリバリーは、継続的インテグレーションの間に発生したテストに続けて、変更をステージングまたは実稼働システムにプッシュします。

Azure Data Factory では、継続的インテグレーションと継続的デリバリーとは、Data Factory パイプラインをある環境 (開発、テスト、運用) から別の環境に移動することを意味します。 継続的インテグレーションと継続的デリバリーを行うために、Data Factory UX 統合と Azure Resource Manager テンプレートを使用できます。 Data Factory UX では、 **[ARM テンプレート]** ドロップダウンから Resource Manager テンプレートを生成できます。 **[ARM テンプレートのエクスポート]** を選択すると、ポータルで、データ ファクトリ用の Resource Manager テンプレートと、すべての接続文字列とその他のパラメーターを含む構成ファイルが生成されます。 次に、環境 (開発、テスト、運用) ごとに 1 つの構成ファイルを作成する必要があります。 すべての環境で使用されるメインの Resource Manager テンプレート ファイルは変更しません。

この機能の概要とデモンストレーションについては、以下の 9 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>継続的インテグレーションのライフサイクル

Azure Repos Git で構成された Azure Data Factory での継続的インテグレーションと継続的デリバリーのライフサイクルのサンプル概要を以下に示します。 Git リポジトリを構成する方法の詳細については、[Azure Data Factory でのソース管理](source-control.md)に関する記事を参照してください。

1.  開発データ ファクトリは Azure Repos Git で作成および構成されます。ここでは、すべての開発者に、パイプラインやデータセットなどの Data Factory リソースを作成するアクセス許可があります。

1.  開発者は、各自の機能ブランチに変更を加える過程で、各自の最新の変更を使用して各自のパイプライン実行をデバッグします。 パイプライン実行をデバッグする方法の詳細については、「[Azure Data Factory での反復開発とデバッグ](iterative-development-debugging.md)」を参照してください。

1.  開発者は、変更の結果に満足したら、各自の機能ブランチからマスターまたはコラボレーション ブランチへの pull request を作成して、同僚が変更をレビューできるようにします。

1.  pull request が承認され、変更がマスター ブランチにマージされたら、開発ファクトリに発行できます。

1.  チームは、変更をテスト ファクトリに、次に運用ファクトリにデプロイする準備ができたら、マスター ブランチから Resource Manager テンプレートをエクスポートします。

1.  エクスポートされた Resource Manager テンプレートは、異なるパラメーター ファイルを使用してテスト ファクトリと運用ファクトリにデプロイされます。

## <a name="create-a-resource-manager-template-for-each-environment"></a>各環境用の Resource Manager テンプレートを作成する

**[ARM テンプレート]** ドロップダウンから、 **[ARM テンプレートのエクスポート]** を選択して、データ ファクトリ用の Resource Manager テンプレートを開発環境にエクスポートします。

![](media/continuous-integration-deployment/continuous-integration-image1.png)

テストおよび運用データ ファクトリで、 **[Import ARM template]\(ARM テンプレートのインポート\)** を選択します。 このアクションによって Azure Portal に移動して、エクスポートされたテンプレートをインポートできます。 **[Build your own template in the editor]\(エディターで独自のテンプレートを作成する\)** を選択して、Resource Manager テンプレート エディターを開きます。

![](media/continuous-integration-deployment/continuous-integration-image3.png) 

**[ファイルの読み込み]** をクリックし、生成された Resource Manager テンプレートを選択します。

![](media/continuous-integration-deployment/continuous-integration-image4.png)

設定ウィンドウで、リンクされたサービスの資格情報などの構成値を入力します。 完了したら、 **[購入]** をクリックして Resource Manager テンプレートをデプロイします。

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Connection strings

接続文字列の構成方法については、各コネクタの記事を参照してください。 たとえば、Azure SQL Database の場合は、「[Azure Data Factory を使用した Azure SQL Database との間でのデータのコピー](connector-azure-sql-database.md)」をご覧ください。 接続文字列を検証するために、Data Factory UX でリソースのコード ビューを開くことができます。 コード ビューでは、接続文字列のパスワードまたはアカウント キーの部分は削除されます。 コード ビューを開くには、次のスクリーンショットで強調表示されているアイコンを選択します。

![コード ビューを開いて接続文字列を確認する](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Azure Pipelines リリースを使用して継続的インテグレーションを自動化する

次に示すのは、Azure Pipelines リリースを設定し、複数の環境へのデータ ファクトリのデプロイを自動化するためのガイドです。

![Azure Pipelines を使用した継続的インテグレーションの図](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>必要条件

-    [Azure Resource Manager サービス エンドポイント](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)を使用して Team Foundation Server または Azure Repos にリンクされた Azure サブスクリプション。

-   Azure Repos Git 統合が構成された Data Factory。

-   各環境用のシークレットを格納する  [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

### <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines リリースをセットアップする

1.  [Azure DevOps ユーザー エクスペリエンス](https://dev.azure.com/)で、Data Factory で構成したプロジェクトを開きます。

1.  ページの左側で、 **[パイプライン]** をクリックして **[リリース]** を選択します。

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  **[新しいパイプライン]** を選択するか、既存のパイプラインがある場合は **[New]\(新規作成\)** 、 **[新しいリリース パイプライン]** の順に選択します。

1.  **[空のジョブ]** テンプレートを選択します。

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  **[ステージ名]** フィールドに、環境の名前を入力します。

1.  **[成果物の追加]** を選択し、Data Factory で構成したものと同じリポジトリを選択します。 最新の既定のバージョンを使用する既定のブランチとして `adf_publish`を選択します。

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager デプロイ タスクを追加します。

    a.  ステージ ビューで、 **[View stage tasks]\(ステージ タスクの表示\)** リンクをクリックします。

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  新しいタスクを作成します。 **[Azure Resource Group Deployment]\(Azure リソース グループの配置\)** を探して **[追加]** をクリックします。

    c.  デプロイ タスクでは、サブスクリプション、リソース グループ、およびターゲットの Data Factory の場所を選択し、必要に応じて資格情報を指定します。

    d.  アクション ドロップダウンで、 **[Create or update resource group]\(リソース グループの作成または更新\)** を選択します。

    e.  **[…]** を選択します ( **[テンプレート]** フィールドにあります)。 「[各環境用の Resource Manager テンプレートを作成する](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment)」の **ARM テンプレートのインポート**の手順を通じて作成した Azure Resource Manager テンプレートを参照します。 `adf_publish` ブランチの `<FactoryName>` フォルダーでこのファイルを探します。

    f.  **[…]** を選択します **[テンプレート パラメーター]** フィールドでこれを行って、 パラメーター ファイルを選択します。 コピーを作成したか既定のファイル *ARMTemplateParametersForFactory.json* を使用しているかに応じて、適切なファイルを選択します。

    g.  **[…]** を選択します ( **[テンプレート パラメーターのオーバーライド]** フィールドの横にあります)。ターゲットの Data Factory の情報を入力します。 キー コンテナーから取得した資格情報の場合は、シークレット名を二重引用符で囲んで入力します。 たとえば、シークレットの名前が `cred1` の場合、その値として `"$(cred1)"` と入力します。

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. **[増分]** デプロイ モードを選択します。

    > [!WARNING]
    > **[完全]** デプロイ モードを選択すると、Resource Manager テンプレートに定義されていないターゲット リソース グループ内のすべてのリソースを含む既存のリソースが削除される可能性があります。

1.  リリース パイプラインを保存します。

1. リリースをトリガーするには、 **[Create release]\(リリースの作成\)** を選択します。

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Azure Key Vault からシークレットを取得する

Azure Resource Manager テンプレートに渡すシークレットがある場合は、Azure Pipelines リリースで Azure Key Vault を使うことをお勧めします。

シークレットを処理する方法は 2 つあります。

1.  シークレットをパラメーター ファイルに追加します。 詳しくは、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/resource-manager-keyvault-parameter.md)」をご覧ください。

    -   発行ブランチにアップロードされたパラメーター ファイルのコピーを作成し、キー コンテナーから取得するパラメーターの値を次の形式で設定します。

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

    -   このメソッドを使用すると、シークレットはキー コンテナーから自動的にプルされます。

    -   パラメーター ファイルも発行ブランチ内に存在する必要があります。

1.  前のセクションで説明されている Azure Resource Manager デプロイ タスクの前に、[Azure Key Vault タスク](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)を追加します。

    -   **[タスク]** タブを選択し、新しいタスクを作成した後、**Azure Key Vault** を探して追加します。

    -   Key Vault タスクでは、キー コンテナーを作成したサブスクリプションを選択し、必要に応じて資格情報を指定した後、そのキー コンテナーを選択します。

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines エージェントにアクセス許可を与える

適切なアクセス許可が存在しない場合、Azure Key Vault タスクがアクセス拒否エラーで失敗することがあります。 リリースのログをダウンロードし、Azure Pipelines エージェントへのアクセス許可を与えるコマンドがある `.ps1` ファイルを探します。 コマンドは直接実行するか、ファイルからプリンシパル ID をコピーし、Azure Portal でアクセス ポリシーを手動で追加できます 必要な最低限のアクセス許可は、**Get** と **List** です。

### <a name="update-active-triggers"></a>アクティブなトリガーを更新する

アクティブなトリガーを更新しようとした場合、デプロイは失敗します。 アクティブなトリガーを更新するには、手動でそれらを停止し、デプロイ後に起動する必要があります。 これは、Azure Powershell タスクを使用して実行できます。

1.  リリースの [タスク] タブで、 **[Azure PowerShell]** タスクを追加します。

1.  接続の種類として **[Azure Resource Manager]** を選択し、サブスクリプションを選択します。

1.  スクリプトの種類として **[インライン スクリプト]** を選択し、コードを入力します。 次の例は、トリガーを停止します。

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

(`Start-AzDataFactoryV2Trigger` 関数を使用して) 同様の手順に従うことで、デプロイ後にトリガーを再起動できます。

> [!IMPORTANT]
> 継続的インテグレーションと継続的デプロイ シナリオでは、Integration Runtime の種類は異なる環境間で同じである必要があります。 たとえば、開発環境に "*セルフホステッド*" Integration Runtime (IR) がある場合、テストや運用などの他の環境環境でも、IR の種類は "*セルフホステッド*" である必要があります。 同様に、複数のステージ間で統合ランタイムを共有している場合は、開発、テスト、運用のすべての環境で、統合ランタイムを "*リンクされたセルフホステッド*" として構成する必要があります。

#### <a name="sample-prepostdeployment-script"></a>サンプルのデプロイ前/デプロイ後スクリプト

デプロイ前にトリガーを停止し、デプロイ後に再起動するサンプル スクリプトを次に示します。 このスクリプトには、削除済みのリソースを完全に削除するコードも含まれています。 最新バージョンの Azure PowerShell をインストールするには、「[PowerShellGet を使用した Windows への Azure PowerShell のインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)」を参照してください。

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
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

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Resource Manager テンプレートでカスタム パラメーターを使用する

GIT モードの場合は、Resource Manager テンプレートの既定のプロパティをオーバーライドして、テンプレート内のパラメーター化されたプロパティと、ハードコーディングされているプロパティで設定することができます。 既定のパラメーター化のテンプレートは、以下のシナリオでオーバーライドすることもできます。

* 自動化された CI/CD を使用していて、Resource Manager のデプロイ中にいくつかのプロパティを変更したいが、プロパティが既定でパラメーター化されていない。
* ファクトリが非常に大きく、既定の Resource Manager テンプレートが許容されるパラメーターの上限 (256) よりも多いために無効である。

このような条件下で既定のパラメーター化テンプレートをオーバーライドするには、リポジトリのルート フォルダーに  *arm-template-parameters-definition.json*  という名前のファイルを作成します。 名前は完全に一致する必要があります。 Data Factory は、コラボレーション ブランチからだけでなく、現在 Azure Data Factory ポータルにあるどのブランチからもこのファイルの読み取りを試みます。 プライベート ブランチからファイルを作成または編集し、UI の **[ARM テンプレートのエクスポート]** を使用して変更内容をテストすることができます。 その後、このファイルをコラボレーション ブランチ内にマージできます。 ファイルが見つからない場合は、既定のテンプレートが使用されます。


### <a name="syntax-of-a-custom-parameters-file"></a>カスタム パラメーター ファイルの構文

カスタム パラメーター ファイルの作成時に使用するいくつかのガイドラインを次に示します。 ファイルは、(トリガー、パイプライン、リンクされたサービス、データセット、統合ランタイムなどの) エンティティの種類ごとのセクションで構成されています。
* 関連するエンティティ型の下にプロパティ パスを入力します。
* プロパティ名を "\*" に設定する場合は、その下 (最初のレベルに至るまで、再帰的にではない) にあるすべてのプロパティをパラメーター化することを指示します。 また、これに例外を指定することもできます。
* プロパティの値を文字列として設定した場合、プロパティをパラメーター化することを指示します。  `<action>:<name>:<stype>` の形式を使用します。
   *  `<action>`  は次のいずれかの文字になります。
      * `=`  は、パラメーターの既定値として現在の値を保持することを意味します。
      * `-`  は、パラメーターの既定値を保持しないことを意味します。
      * `|`  は、接続文字列またはキーに対する Azure Key Vault からのシークレットの特殊なケースです。
   * `<name>`  は、パラメーターの名前です。 空白の場合は、プロパティの名前になります。 値が `-` 文字で始まる場合、名前は短縮されます。 たとえば、`AzureStorage1_properties_typeProperties_connectionString` は `AzureStorage1_connectionString` に短縮されます。
   * `<stype>`  は、パラメーターの型です。  `<stype>`  が空白の場合、既定の型は `string` です。 サポートされる値は `string`、`bool`、`number`、`object`、`securestring` です。
* 定義ファイルに配列を指定した場合、テンプレート内の一致するプロパティが配列であることを指示します。 Data Factory は、配列の Integration Runtime オブジェクトに指定された定義を使用することで、配列内のすべてのオブジェクトを反復処理します。 2 番目のオブジェクトである文字列は、各反復処理のパラメーターの名前として使用されるプロパティ名です。
* リソース インスタンス固有の定義を含めることはできません。 定義はその型のすべてのリソースに適用されます。
* 既定では、Key Vault シークレットなどのセキュリティで保護されたすべての文字列と、接続文字列、キー、トークンなどのセキュリティで保護された文字列がパラメーター化されます。
 
### <a name="sample-parameterization-template"></a>サンプルのパラメーター化テンプレート

パラメーター化テンプレートの内容例を次に示します。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
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
                    "interval": "=:triggerSuffix:number",
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
以下、上記のテンプレートがどのように構築されるかについて、リソースの種類別に説明します。

#### <a name="pipelines"></a>パイプライン
    
* パス activities/typeProperties/waitTimeInSeconds 内のすべてのプロパティがパラメーター化されます。 `waitTimeInSeconds` という名前のコードレベルのプロパティを持つパイプライン内のアクティビティ (たとえば、`Wait` アクティビティ) はすべて、既定の名前を使用して数値としてパラメーター化されます。 ただし、Resource Manager テンプレートにその既定値はありません。 これは Resource Manager デプロイ時に必須の入力になります。
* 同様に、`headers` というプロパティ (たとえば、`Web` アクティビティ内の) は、型 `object` (JObject) でパラメーター化されます。 これは、ソース ファクトリと同じ値である既定値を保持します。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* パス `typeProperties` の下にあるすべてのプロパティは、それぞれの既定値を使用してパラメーター化されます。 たとえば、**IntegrationRuntimes** 型のプロパティの下には `computeProperties` と `ssisProperties` という 2 つのプロパティがあります。 両方のプロパティの型は、それぞれの既定の値と型 (Object) で作成されます。

#### <a name="triggers"></a>トリガー

* `typeProperties` の下では、2 つのプロパティがパラメーター化されます。 1 つ目は `maxConcurrency` で、既定値を持つように指定されていて、型は `string` です。 既定のパラメーター名前 `<entityName>_properties_typeProperties_maxConcurrency` を持ちます。
* `recurrence` プロパティもパラメーター化されます。 その下にあるそのレベルのすべてのプロパティは、既定の値とパラメーター名を持つ文字列としてパラメーター化されるよう指定されます。 例外は `interval` プロパティで、数値型として、`<entityName>_properties_typeProperties_recurrence_triggerSuffix` のサフィックスが付いたパラメーターでパラメーター化されます。 同様に、`freq` プロパティは文字列であり、文字列としてパラメーター化されます。 ただし、`freq` プロパティは既定値なしでパラメーター化されます。 名前は短縮され、サフィックスが付けられます。 たとえば、「 `<entityName>_freq` 」のように入力します。

#### <a name="linkedservices"></a>LinkedServices

* リンクされたサービスは一意です。 リンクされたサービスとデータセットにはさまざまな型があるため、型固有のカスタマイズを行うことができます。 この例では、型 `AzureDataLakeStore` のすべてのリンクされたサービスには特定のテンプレートが適用され、それ以外には (\* を使用して) 別のテンプレートが適用されます。
* `connectionString` プロパティは値 `securestring` としてパラメーター化され、既定値はなく、`connectionString` のサフィックスが付いた短縮されたパラメーター名を持つことになります。
* プロパティ `secretAccessKey` は (たとえば、リンクされたサービス `AmazonS3` において) たまたま `AzureKeyVaultSecret` になります。 Azure Key Vault シークレットとして自動的にパラメーター化され、構成済みのキー コンテナーからフェッチされます。 キー コンテナー自体もパラメーター化できます。

#### <a name="datasets"></a>データセット

* データセットに型固有のカスタマイズを使用できますが、\* レベルの構成を明示的に指定しなくても構成を指定できます。 上の例では、`typeProperties` の下にあるすべてのデータセット プロパティがパラメーター化されます。

### <a name="default-parameterization-template"></a>既定のパラメーター化テンプレート

次に、現在の既定のパラメーター化テンプレートを示します。 1 つまたは複数のパラメーターを追加するだけであれば、これを直接編集すると、既存のパラメーター化構造が失われないので便利な場合があります。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
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
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
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

既定のパラメーター化テンプレートに 1 つの値を追加する方法の例を次に示します。 Databricks のリンクされたサービスの既存の Databricks インタラクティブ クラスター ID をパラメーター ファイルに追加したいだけです。 次のファイルは、`Microsoft.DataFactory/factories/linkedServices` の properties フィールドに `existingClusterId` が含まれること以外は、上記のファイルと同じであることに注意してください。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
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

データ ファクトリの継続的インテグレーションと継続的デプロイ (CI/CD) を設定済みの場合、ファクトリが大規模になるにつれて Azure Resource Manager テンプレートの制限に達する可能性があります。 制限の例には、Resource Manager テンプレート内のリソースの最大数があります。 大規模なファクトリに対応するために、Data Factory では、ファクトリ用の完全な Resource Manager テンプレートの生成と共に、リンクされた Resource Manager テンプレートが生成されるようになりました。 この機能を使用すると、ファクトリのペイロード全体が複数のファイルに分割されるので、制限に達することはありません。

Git が構成されている場合は、リンクされたテンプレートが生成され、完全な Resource Manager テンプレートと共に、`adf_publish` ブランチの `linkedTemplates` という名前の新しいフォルダーに保存されます。

![リンクされた Azure Resource Manager テンプレートのフォルダー](media/continuous-integration-deployment/linked-resource-manager-templates.png)

通常、リンクされた Resource Manager テンプレートには、マスター テンプレートと、マスターにリンクされた一連の子テンプレートがあります。 親テンプレートは `ArmTemplate_master.json` と呼ばれ、子テンプレートには、`ArmTemplate_0.json``ArmTemplate_1.json`… というパターンで名前が付けられます。 完全な Resource Manager テンプレートではなくリンクされたテンプレートを使用するには、`ArmTemplateForFactory.json` (完全な Resource Manager テンプレート) ではなく `ArmTemplate_master.json` をポイントするように CI/CD タスクを更新します。 Resource Manager では、リンクされたテンプレートをストレージ アカウントにアップロードして、デプロイ時に Azure によってアクセスできるようにする必要もあります。 詳細については、「[Deploying Linked ARM Templates with VSTS (リンクされた ARM テンプレートの VSTS を使用したデプロイ)](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)」を参照してください。

Data Factory のスクリプトは、必ず CI/CD パイプラインのデプロイ タスクの前後に追加してください。

Git が構成されていない場合は、 **[ARM テンプレートのエクスポート]** によって、リンクされたテンプレートにアクセスできます。

## <a name="hot-fix-production-branch"></a>運用ブランチへの修正プログラムの適用

ファクトリを運用環境にデプロイし、すぐに修正が必要なバグがあることが判明したが現在のコラボレーション ブランチをデプロイできない場合、修正プログラムのデプロイが必要なことがあります。

1.  Azure DevOps で、運用環境にデプロイされたリリースに移動し、デプロイされた最後のコミットを見つけます。

2.  コミット メッセージから、コラボレーション ブランチのコミット ID を取得します。

3.  そのコミットから新しい修正プログラム ブランチを作成します。

4.  Azure Data Factory UX に移動し、このブランチに切り替えます。

5.  Azure Data Factory UX を使用して、バグを修正します。 変更をテストします。

6.  修正が検証されたら、 **[Export ARM template]\(ARM テンプレートのエクスポート\)** をクリックして、修正プログラムの Resource Manager テンプレートを取得します。

7.  このビルドを adf_publish ブランチに手動でチェックインします。

8.  adf_publish のチェックインに基づいて自動的にトリガーするようにリリース パイプラインを構成している場合、新しいリリースは自動的に開始します。 それ以外の場合は、手動でリリースをキューに配置します。

9.  テスト ファクトリと運用ファクトリに修正プログラム リリースをデプロイします。 このリリースには、以前の運用ペイロードに加えて、手順 5 で行った修正が含まれています。

10. 修正プログラムでの変更を開発ブランチに追加して、今後のリリースで同じバグが発生しないようにします。

## <a name="best-practices-for-cicd"></a>CI/CD のベスト プラクティス

データ ファクトリとの Git 統合を使用していて、変更を開発環境からテスト環境を経て運用環境に移動する CI/CD パイプラインがある場合は、次のベスト プラクティスお勧めします。

-   **Git 統合**。 Git 統合で構成する必要があるのは開発環境のデータ ファクトリのみです。 テスト環境と運用環境への変更は CI/CD を介してデプロイされるので、Git 統合は必要ありません。

-   **データ ファクトリの CI/CD スクリプト**。 CI/CD の Resource Manager デプロイ手順の前に、トリガーの停止/開始やクリーンアップなどの特定のタスクが必要です。 デプロイの前後に PowerShell スクリプトを使用することをお勧めします。 詳細については、「[アクティブなトリガーを更新する](#update-active-triggers)」を参照してください。 

-   **統合ランタイムと共有**。 統合ランタイムは頻繁には変更されず、CI/CD のすべてのステージで類似しています。 そのため、Data Factory では、CI/CD のすべてのステージで統合ランタイムの名前と種類を同じにすることが期待されています。 すべてのステージで統合ランタイムを共有するつもりの場合は、共有の統合ランタイムを含めるためだけに三項ファクトリを使用することを検討してください。 この共有ファクトリは、すべての環境で、リンクされた統合ランタイムの種類として使用できます。

-   **Key Vault**。 Azure Key Vault ベースのリンクされたサービスを使用する場合は、異なる環境用に個別のキー コンテナーを保持することで、さらに有効に活用できます。 個別にアクセス許可レベルを構成することもできます。 たとえば、運用環境のシークレットへのアクセス許可をチーム メンバーに持たせたくない場合があります。 このアプローチに従う場合は、すべてのステージで同じシークレット名を保持することをお勧めします。 同じ名前にすると、CI/CD 環境を通して Resource Manager テンプレートを変更する必要がなくなります。なぜなら、変わる項目はキー コンテナーの名前だけであり、これは Resource Manager テンプレートのパラメーターの 1 つです。

## <a name="unsupported-features"></a>サポートされていない機能

-   個々のリソースを発行することはできません。 データ ファクトリ エンティティは相互に依存しており、依存関係の変化を追跡することは困難で、予期しない動作につながる可能性があります。 たとえば、トリガーはパイプラインに依存し、パイプラインはデータセットや他のパイプラインに依存します。 変更セット全体のサブセットのみを発行できるとした場合、特定の予期しないエラーが発生する可能性があります。

-   非公開のブランチから発行することはできません。

-   Bitbucket でプロジェクトをホストすることはできません。
