---
title: Azure Data Factory における継続的インテグレーションとデリバリー | Microsoft Docs
description: 継続的インテグレーションとデリバリーを使用して Data Factory パイプラインをある環境 (開発、テスト、運用) から別の環境に移動する方法について説明します。
services: data-factory
documentationcenter: ''
author: gauravmalhot
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: gamal
manager: craigg
ms.openlocfilehash: 2edd4e28a0dd67be3c06159bce2e968d681b7f70
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905258"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Azure Data Factory における継続的インテグレーションと継続的デリバリー (CI/CD)

継続的インテグレーションは、コードベースに対して行われた変更を、できるだけ早く自動的にテストするプラクティスです。 継続的デリバリーは、継続的インテグレーションの間に発生したテストに続けて、変更をステージングまたは実稼働システムにプッシュします。

Azure Data Factory では、継続的インテグレーションと継続的デリバリーとは、Data Factory パイプラインをある環境 (開発、テスト、実稼働) から別の環境に移動することを意味します。 継続的インテグレーションと継続的デリバリーを行うために、Data Factory UI 統合と Azure Resource Manager テンプレートを使用できます。 **ARM テンプレート** オプションを選択すると、Data Factory UI によって Resource Manager テンプレートを生成できます。 **[ARM テンプレートのエクスポート]** を選択すると、ポータルで、データ ファクトリ用の Resource Manager テンプレートと、すべての接続文字列とその他のパラメーターを含む構成ファイルが生成されます。 次に、環境 (開発、テスト、実稼働) ごとに 1 つの構成ファイルを作成する必要があります。 すべての環境で使用されるメインの Resource Manager テンプレート ファイルは変更しません。

この機能の概要とデモンストレーションについては、以下の 9 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-manager-template-for-each-environment"></a>各環境用の Resource Manager テンプレートを作成する
**[ARM テンプレートのエクスポート]** を選択して、データ ファクトリ用の Resource Manager テンプレートを開発環境にエクスポートします。

![](media/continuous-integration-deployment/continuous-integration-image1.png)

次に、テスト データ ファクトリと実稼働データ ファクトリに移動し、**[ARM テンプレートのインポート]** を選択します。

![](media/continuous-integration-deployment/continuous-integration-image2.png)

このアクションによって Azure Portal に移動して、エクスポートされたテンプレートをインポートできます。 **[Build your own template in the editor]\(エディターで独自のテンプレートをビルド\)**、**[ファイルの読み込み]** の順に選択し、生成された Resource Manager テンプレートを選択します。 設定を入力します。データ ファクトリとパイプライン全体が、実稼働環境にインポートされます。

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

**[ファイルの読み込み]** を選択して、エクスポートされた Resource Manager テンプレートを選択し、すべての構成値 (たとえば、リンクされたサービス) を指定します。

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**[接続文字列]**。 接続文字列の作成に必要な情報は、各コネクタに関する記事で見つかります。 たとえば、Azure SQL Database の場合は、「[Azure Data Factory を使用した Azure SQL Database との間でのデータのコピー](connector-azure-sql-database.md)」をご覧ください。 正しい接続文字列は、Data Factory UI でリソースに対するコード ビューを開いて確認することもできます (リンクされたサービスの場合など)。 ただし、コード ビューでは、接続文字列のパスワードまたはアカウント キーの部分は削除されます。 コード ビューを開くには、次のスクリーンショットで強調表示されているアイコンを選択します。

![コード ビューを開いて接続文字列を確認する](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>継続的インテグレーションのライフサイクル
Data Factory UI で Azure Repos Git 統合を有効にした後で使用できる継続的インテグレーションと継続的デリバリーのライフサイクル全体を次に示します。

1.  すべての開発者がパイプラインやデータセットなどの Data Factory リソースを作成できる開発データ ファクトリを Azure Repos で設定します。

1.  開発者は、パイプラインなどのリソースを変更できます。 開発者は、変更を行った後、**[デバッグ]** を選択して、最新の変更を行ったパイプラインがどのように実行されるかを確認できます。

1.  開発者は、変更の結果に満足したら、各自のブランチからマスター ブランチ (または、コラボレーション ブランチ) への pull request を作成して、同僚が変更をレビューできるようにします。

1.  変更がマスター ブランチに反映されたら、開発者は、**[発行]** を選択して、開発ファクトリに発行できます。

1.  チームは、変更をテスト ファクトリと実稼働ファクトリにレベル上げする準備ができたら、マスター ブランチから Resource Manager テンプレートをエクスポートできます。マスター ブランチがライブ開発 Data Factory に戻っている場合は、他のブランチからエクスポートできます。

1.  エクスポートされた Resource Manager テンプレートは、異なるパラメーター ファイルを使用してテスト ファクトリと実稼働ファクトリにデプロイできます。

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Azure Pipelines リリースを使用して継続的インテグレーションを自動化する

データ ファクトリの複数の環境へのデプロイを自動化できるように Azure Pipelines リリースを設定する手順を次に示します。

![Azure Pipelines を使用した継続的インテグレーションの図](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>必要条件

-    [*Azure Resource Manager サービス エンドポイント*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)を使用して Team Foundation Server または Azure Repos にリンクされた Azure サブスクリプション。

-   Azure Repos Git 統合が構成されたデータ ファクトリ。

-   シークレットを格納する  [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 。

### <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines リリースをセットアップする

1.  Data Factory で構成したのと同じプロジェクトの Azure Repos ページに移動します。

1.  上部メニューの **[Azure Pipelines]** &gt; **[リリース]** &gt; **[リリース定義の作成]** をクリックします。

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  **[空のプロセス]** テンプレートを選択します。

1.  環境の名前を入力します。

1.  Git 成果物を追加し、Data Factory で構成したのと同じリポジトリを選択します。 最新の既定のバージョンを使用する既定のブランチとして `adf_publish`を選択します。

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager デプロイ タスクを追加します。

    a.  新しいタスクを作成し、**Azure リソース グループのデプロイ**を探して追加します。

    b.  デプロイ タスクでは、サブスクリプション、リソース グループ、およびターゲットの Data Factory の場所を選択し、必要に応じて資格情報を指定します。

    c.  **[リソース グループの作成または更新]** アクションを選択します。

    d.  **[…]** を選択します  (**[テンプレート]** フィールドにあります)。 ポータルでの発行操作によって作成された Resource Manager テンプレートを参照します (*ARMTemplateForFactory.json*)。 `adf_publish` ブランチの `<FactoryName>` フォルダーでこのファイルを探します。

    e.  パラメーター ファイルに対して同じことを行います。 コピーを作成したか既定のファイル *ARMTemplateParametersForFactory.json* を使用しているかに応じて、適切なファイルを選択します。

    f.  **[…]** を選択します  (**[テンプレート パラメーターのオーバーライド]** フィールドの横にあります)。ターゲットの Data Factory の情報を入力します。 キー コンテナー の資格情報については、シークレットと同じ名前を次の形式で使用します。たとえばシークレットの名前が `cred1` の場合は、`"$(cred1)"` と入力します (引用符で囲みます)。

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. **[増分]** デプロイ モードを選択します。

    > [!WARNING]
    > **[完全]** デプロイ モードを選択すると、Resource Manager テンプレートに定義されていないターゲット リソース グループ内のすべてのリソースを含む既存のリソースが削除される可能性があります。

1.  リリース パイプラインを保存します。

1.  このリリース パイプラインから新しいリリースを作成します。

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>省略可能 - Azure Key Vault からシークレットを取得する

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

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines エージェントにアクセス許可を与える
Azure Key Vault タスクは、初回はアクセス拒否エラーで失敗する可能性があります。 リリースのログをダウンロードし、Azure Pipelines エージェントへのアクセス許可を与えるコマンドがある `.ps1` ファイルを探します。 コマンドは直接実行するか、ファイルからプリンシパル ID をコピーし、Azure Portal でアクセス ポリシーを手動で追加できます  (必要な最低限のアクセス許可は、*Get*と*List* です)。

### <a name="update-active-triggers"></a>アクティブなトリガーを更新する
アクティブなトリガーを更新しようとした場合、デプロイは失敗します。 アクティブなトリガーを更新するには、手動でそれらを停止し、デプロイ後に起動する必要があります。 次の例に示すように、この目的のための Azure Powershell タスクを追加できます。

1.  リリースの [タスク] タブで、**[Azure Powershell]** を探します。

1.  接続の種類として **[Azure Resource Manager]** を選択し、サブスクリプションを選択します。

1.  スクリプトの種類として **[インライン スクリプト]** を選択し、コードを入力します。 次の例は、トリガーを停止します。

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

同様の手順と同様のコード (`Start-AzDataFactoryV2Trigger` 関数) を使用して、デプロイ後にトリガーを再起動できます。

> [!IMPORTANT]
> 継続的インテグレーションと継続的デプロイ シナリオでは、Integration Runtime の種類は異なる環境間で同じである必要があります。 たとえば、開発環境に "*セルフホステッド*" Integration Runtime (IR) がある場合、テストや運用などの他の環境環境でも、IR の種類は "*セルフホステッド*" である必要があります。 同様に、複数のステージ間で統合ランタイムを共有している場合は、開発、テスト、運用のすべての環境で、統合ランタイムを "*リンクされたセルフホステッド*" として構成する必要があります。

## <a name="sample-deployment-template"></a>デプロイ テンプレートの例

Azure Pipelines にインポートできるサンプル デプロイ テンプレートを次に示します。

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>トリガーの停止と再起動およびクリーンアップを行うサンプル スクリプト

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

カスタム パラメーター ファイルの作成時に使用するいくつかのガイドラインを次に示します。 ファイルは、エンティティ型 (trigger、pipeline、linkedservice、dataset、integrationruntime など) ごとのセクションで構成されています。
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
 
## <a name="sample-parameterization-template"></a>サンプルのパラメーター化テンプレート

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

### <a name="explanation"></a>説明:

#### <a name="pipelines"></a>パイプライン
    
* パス activities/typeProperties/waitTimeInSeconds 内のすべてのプロパティがパラメーター化されます。 つまり、`waitTimeInSeconds` という名前のコードレベルのプロパティを持つパイプライン内のアクティビティ (たとえば、`Wait` アクティビティ) は、既定の名前を持つ数としてパラメーター化されます。 ただし、Resource Manager テンプレートは既定値を保持しません。 これは Resource Manager デプロイ時に必須の入力になります。
* 同様に、`headers` というプロパティ (たとえば、`Web` アクティビティ内の) は、型 `object` (JObject) でパラメーター化されます。 これは、ソース ファクトリと同じ値である既定値を保持します。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* 唯一のプロパティ、およびすべてのプロパティが、パス `typeProperties` の下で、それぞれ既定値でパラメーター化されます。 たとえば、現時点のスキーマでは、**IntegrationRuntimes** 型のプロパティの下には `computeProperties` と `ssisProperties` という 2 つのプロパティがあります。 両方のプロパティの型は、それぞれの既定の値と型 (Object) で作成されます。

#### <a name="triggers"></a>トリガー

* `typeProperties` の下では、2 つのプロパティがパラメーター化されます。 1 つ目は `maxConcurrency` で、既定値を保持するよう指定されていて、型は `string` です。 既定のパラメーター名前 `<entityName>_properties_typeProperties_maxConcurrency` を持ちます。
* `recurrence` プロパティもパラメーター化されます。 その下にあるそのレベルのすべてのプロパティは、既定の値とパラメーター名を持つ文字列としてパラメーター化されるよう指定されます。 例外は `interval` プロパティで、数値型として、`<entityName>_properties_typeProperties_recurrence_triggerSuffix` のサフィックスが付いたパラメーターでパラメーター化されます。 同様に、`freq` プロパティは文字列であり、文字列としてパラメーター化されます。 ただし、`freq` プロパティは既定値なしでパラメーター化されます。 名前は短縮され、サフィックスが付けられます。 たとえば、「 `<entityName>_freq` 」のように入力します。

#### <a name="linkedservices"></a>LinkedServices

* リンクされたサービスは一意です。 リンクされたサービスとデータセットには複数の型がある可能性があるため、型固有のカスタマイズを行うことができます。 たとえば、型 `AzureDataLakeStore` のすべてのリンクされたサービスには特定のテンプレートが適用され、それ以外には (\* を使用して) 別のテンプレートが適用されることになります。
* 前の例では、`connectionString` プロパティは値 `securestring` としてパラメーター化され、既定値は保持せず、`connectionString` のサフィックスが付いた短縮されたパラメーター名を持つことになります。
* ただし、プロパティ `secretAccessKey` は `AzureKeyVaultSecret` となります (たとえば、リンクされたサービス `AmazonS3`)。 そのため、Azure Key Vault シークレットとして自動的にパラメーター化され、ソース ファクトリ内で構成されたキー コンテナーからフェッチされます。 キー コンテナー自体もパラメーター化できます。

#### <a name="datasets"></a>データセット

* データセットに型固有のカスタマイズを使用できる場合でも、\* レベルの構成を明示的に指定せずに構成を指定できます。 前の例では、`typeProperties` の下にあるすべてのデータセット プロパティがパラメーター化されます。

既定のパラメーター化のテンプレートは変更できますが、これは現在のテンプレートです。 これは、その他のプロパティをパラメーターとして 1 つだけ追加する必要がある場合だけでなく、既存のパラメーター化を失わずに再作成する必要がある場合にも役立ちます。


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

**例**:Databricks インタラクティブ クラスター ID を、(Databricks のリンクされたサービスから) パラメーター ファイルに追加します。

```
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

お使いのデータ ファクトリに対して継続的インテグレーションと継続的デプロイ (CI/CD) を設定している場合、ファクトリが大きくなったときに、Resource Manager テンプレート内のリソースの最大数や最大ペイロードなどの Resource Manager テンプレートの限界に達したことに気付くことがあります。 このようなシナリオ向けに、Data Factory では、ファクトリ用の完全な Resource Manager テンプレートの生成と共に、リンクされた Resource Manager テンプレートも生成されるようになりました。 その結果、ファクトリのペイロード全体を複数のファイルに分解して、前述の制限に達しないようにすることができます。

Git が構成されている場合は、リンクされたテンプレートが生成され、完全な Resource Manager テンプレートと共に、`adf_publish` ブランチの `linkedTemplates` という名前の新しいフォルダーに保存されます。

![リンクされた Azure Resource Manager テンプレートのフォルダー](media/continuous-integration-deployment/linked-resource-manager-templates.png)

通常、リンクされた Resource Manager テンプレートには、マスター テンプレートと、マスターにリンクされた一連の子テンプレートがあります。 親テンプレートは `ArmTemplate_master.json` と呼ばれ、子テンプレートには、`ArmTemplate_0.json``ArmTemplate_1.json`… というパターンで名前が付けられます。 完全な Resource Manager テンプレートからリンクされたテンプレートの使用に移行するには、`ArmTemplateForFactory.json` (つまり、完全な Resource Manager テンプレート) ではなく、`ArmTemplate_master.json` をポイントするように CI/CD タスクを更新します。 Resource Manager では、リンクされたテンプレートをストレージ アカウントにアップロードして、デプロイ時に Azure によってアクセスできるようにする必要もあります。 詳細については、「[Deploying Linked ARM Templates with VSTS (リンクされた ARM テンプレートの VSTS を使用したデプロイ)](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)」を参照してください。

Data Factory のスクリプトは、必ず CI/CD パイプラインのデプロイ タスクの前後に追加してください。

Git が構成されていない場合は、**[ARM テンプレートのエクスポート]** によって、リンクされたテンプレートにアクセスできます。

## <a name="best-practices-for-cicd"></a>CI/CD のベスト プラクティス

データ ファクトリとの Git 統合を使用していて、変更を開発環境からテスト環境を経て運用環境に移動する CI/CD パイプラインがある場合は、次のベスト プラクティスお勧めします。

-   **Git 統合**。 Git 統合で構成する必要があるのは開発環境のデータ ファクトリのみです。 テスト環境と運用環境への変更は CI/CD を介してデプロイされるので、Git 統合を使用する必要はありません。

-   **データ ファクトリの CI/CD スクリプト**。 CI/CD での Resource Manager デプロイ ステップの前に、トリガーの停止や、その他の種類のファクトリ クリーンアップなどの処理を行う必要があります。 これらのことをすべて行う[こちらのスクリプト](#sample-script-to-stop-and-restart-triggers-and-clean-up)を使用することをお勧めします。 デプロイの前と後に 1 回ずつ、適切なフラグを使用してスクリプトを実行します。

-   **統合ランタイムと共有**。 統合ランタイムは、データ ファクトリのインフラストラクチャ コンポーネントの 1 つであり、あまり変更されず、CI/CD のすべてのステージで似ています。 そのため、Data Factory では、CI/CD のすべてのステージで統合ランタイムの名前と種類を同じにすることが期待されています。 すべてのステージで統合ランタイムを共有しようと考えている場合 (たとえば、セルフホステッド統合ランタイム)、共有方法の 1 つは、共有する統合ランタイムを格納するためだけに、三項ファクトリでセルフホステッド IR をホストすることです。 そうすれば、リンクされた IR の種類として開発/テスト/運用でそれを使用できます。

-   **Key Vault**。 推奨される Azure Key Vault ベースのリンクされたサービス使用する場合は、開発/テスト/運用に個別のキー コンテナーを使用することで、さらに有効に活用できます。個別にアクセス許可レベルを構成することもできます。 運用環境のシークレットへのアクセス許可をチーム メンバーに持たせたくない場合があります。 また、すべてのステージでシークレット名を同じにすることもお勧めします。 同じ名前にすると、CI/CD を通して Resource Manager テンプレートを変更する必要がなくなります。なぜなら、変更する必要がある項目はキー コンテナーの名前だけであり、これは Resource Manager テンプレートのパラメーターの 1 つです。

## <a name="unsupported-features"></a>サポートされていない機能

-   データ ファクトリのエンティティは相互に依存しているため、リソースを個別に発行することはできません。 たとえば、トリガーはパイプラインに依存し、パイプラインはデータセットや他のパイプラインに依存します。変化する依存関係を追跡するのは困難です。 発行するリソースを手動で選択できたとしても、変更のセット全体のサブセットしか選択していない可能性があり、発行後に予期しない動作が発生します。

-   非公開のブランチから発行することはできません。

-   Bitbucket でプロジェクトをホストすることはできません。
