---
title: Resource Manager テンプレートで Azure Deployment Manager を使用する | Microsoft Docs
description: Resource Manager テンプレートで Azure Deployment Manager を使用して、Azure リソースを配備します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/31/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: def218564511b65809e776f97d0962b72f72adc2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898135"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>チュートリアル:Azure Deployment Manager で正常性チェックを使用する (パブリック プレビュー)

[Azure Deployment Manager](./deployment-manager-overview.md) で正常性チェックを統合する方法について説明します。 このチュートリアルは、「[Resource Manager テンプレートで Azure Deployment Manager を使用する](./deployment-manager-tutorial.md)」チュートリアルに基づいています。 このチュートリアルに進む前に、そのチュートリアルを完了する必要があります。

「[Resource Manager テンプレートで Azure Deployment Manager を使用する](./deployment-manager-tutorial.md)」で用いるロールアウト テンプレートでは、待機手順を使用しました。 このチュートリアルでは、待機手順を正常性チェックの手順に置き換えます。

> [!IMPORTANT]
> お客様のサブスクリプションが Azure の新機能をテストするようにカナリアに対してマークされている場合は、カナリア リージョンへのデプロイには Azure Deployment Manager しか使用できません。 

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * 正常性チェック サービス シミュレーターの作成
> * ロールアウト テンプレートの変更
> * トポロジのデプロイ
> * 異常状態でのロールアウトのデプロイ
> * ロールアウトのデプロイの検証
> * 正常状態でのロールアウトのデプロイ
> * ロールアウトのデプロイの検証
> * リソースのクリーンアップ

その他のリソース:

- [Azure Deployment Manager REST API リファレンス](https://docs.microsoft.com/rest/api/deploymentmanager/)。
- [Azure Deployment Manager サンプル](https://github.com/Azure-Samples/adm-quickstart)。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* 「[Resource Manager テンプレートで Azure Deployment Manager を使用する](./deployment-manager-tutorial.md)」を完了します。
* このチュートリアルで使用される[テンプレートと成果物](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip)をダウンロードします。

## <a name="create-a-health-check-service-simulator"></a>正常性チェック サービス シミュレーターの作成

運用環境では、通常 1 つまたは複数の監視プロバイダーを使用します。 Microsoft は、正常性統合を可能な限り簡単にするために、サービス正常性監視のトップ企業と連携して、正常性チェックをデプロイと統合するための簡単なコピー/貼り付けソリューションを提供しています。 これらの企業の一覧については、[正常性監視プロバイダー](./deployment-manager-health-check.md#health-monitoring-providers)に関するページを参照してください。 このチュートリアルでは、正常性監視サービスをシミュレーションするための [Azure 関数](/azure/azure-functions/)を作成します。 この関数は、状態コードを受け取り、同じコードを返します。 Azure Deployment Manager テンプレートは、その状態コードを使用して、デプロイを進める方法を決定します。

次の 2 つのファイルは、Azure 関数をデプロイするために使用します。 チュートリアルを進めるためにこれらのファイルをダウンロードする必要はありません。

* Resource Manager テンプレートは、[https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json) にあります。 Azure 関数を作成するには、このテンプレートをデプロイします。
* Azure 関数のソース コードの ZIP ファイルは、[https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip](https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip) にあります。 この ZIP は、Resource Manager テンプレートによって呼び出されます。

Azure 関数をデプロイするには、 **[試してみる]** を選択し、Azure Cloud Shell を開いて、シェル ウィンドウに次のスクリプトを貼り付けます。  コードを貼り付けるには、シェル ウィンドウを右クリックして、 **[貼り付け]** を選択します。

> [!IMPORTANT]
> PowerShell スクリプトの **projectName** は、このチュートリアルでデプロイされる Azure サービスの名前を生成するために使用します。 Azure サービスが異なると、名前に関する要件も異なります。 デプロイを確実に成功させるには、小文字と数字のみが含まれた 12 文字未満の名前を選びます。
> プロジェクト名のコピーを保存します。 このチュートリアルを通じて、同じ projectName を使用します。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

Azure 関数を検証およびテストする:

1. [Azure Portal](https://portal.azure.com)を開きます。
1. リソース グループを開きます。  既定の名前は、**rg** が付加されたプロジェクト名です。
1. リソース グループからアプリ サービスを選択します。  アプリ サービスの既定の名前は、**webapp** が付加されたプロジェクト名です。
1. **[Functions]** を展開して **[HttpTrigger1]** を選択します。

    ![Azure Deployment Manager の正常性チェックの Azure 関数](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. **[&lt;/> 関数の URL の取得]** を選択します。
1. **[コピー]** を選択して、URL をクリップボードにコピーします。  URL は以下のようになります。

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    URL の `{healthStatus}` を状態コードに置き換えます。 このチュートリアルでは、 **unhealthy** を使用して異常シナリオをテストし、**healthy** または **warning** のいずれかを使用して正常シナリオをテストします。 異常状態の URL と正常状態の URL の 2 つの URL を作成します。 次に例を示します。

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    このチュートリアルを完了するには、両方の URL が必要になります。

1. 正常性監視シミュレーターをテストするには、最後の手順で作成した URL を開きます。  異常な状態の結果は、以下のようになります。

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>ロールアウト テンプレートの変更

このセクションの目的は、ロールアウト テンプレートに正常性チェックの手順を含める方法を説明することです。 このチュートリアルを完了するために、お客様独自の CreateADMRollout.json ファイルを作成する必要はありません。 変更後のロールアウト テンプレートは、後続のセクションで使用されるストレージ アカウントで共有されます。

1. **CreateADMRollout.json** を開きます。 この JSON ファイルは、ダウンロードの一部です。  「[前提条件](#prerequisites)」を参照してください。
1. 2 つのパラメーターを追加します。

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. 待機手順のリソース定義を正常性チェック手順のリソース定義に置き換えます。

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    この定義に基づいて、正常性状態が *healthy* または *warning* のいずれかの場合は、ロールアウトが進行します。

1. ロールアウトの定義の **dependsON** を更新して、新しく定義した正常性チェック手順を含めます。

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. **stepGroups** を更新して、正常性チェック手順を含めます。 **healthCheckStep** は、**stepGroup2** の **postDeploymentSteps** 内で呼び出されます。 **stepGroup3** と **stepGroup4** は、正常性状態が *healthy* または *warning* のいずれかの場合のみデプロイされます。

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

    **stepGroup3** セクションの変更前と変更後を比較した場合、このセクションは **stepGroup2** に依存するようになっています。  これは、**stepGroup3** と後続の手順のグループが正常性監視の結果に依存する場合に必要です。

    次のスクリーンショットは、変更された領域と、正常性チェック手順がどのように使用されているかを示しています。

    ![Azure Deployment Manager の正常性チェック テンプレート](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>トポロジのデプロイ

チュートリアルを簡単にするために、トポロジ テンプレートと成果物を以下の場所で共有し、お客様独自のコピーを準備する必要がないようにします。 お客様独自のものを使用する場合は、「[チュートリアル:Resource Manager テンプレートで Azure Deployment Manager を使用する](./deployment-manager-tutorial.md)」の手順に従います。

* トポロジ テンプレート: \\[https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json](https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json)
* 成果物ストア: \\[https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore](https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore)

トポロジをデプロイするには、 **[試してみる]** を選択し、Cloud Shell を開いて PowerShell スクリプトを貼り付けます。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMServiceTopology.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation
```

サービス トポロジと下線付きのリソースが、Azure portal を使用することで正常に作成されていることを確認します。

![Azure Deployment Manager チュートリアルの配備済みサービス トポロジ リソース](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

リソースを参照するには、 **[非表示型の表示]** を選択する必要があります。

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>異常状態でのロールアウトのデプロイ

チュートリアルを簡単にするために、変更したロールアウト テンプレートを以下の場所で共有し、お客様独自のコピーを準備する必要がないようにします。 お客様独自のものを使用する場合は、「[チュートリアル:Resource Manager テンプレートで Azure Deployment Manager を使用する](./deployment-manager-tutorial.md)」の手順に従います。

* トポロジ テンプレート: \\[https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json](https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json)
* 成果物ストア: \\[https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore](https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore)

[正常性チェック サービス シミュレーターの作成](#create-a-health-check-service-simulator)に関するページで作成した異常状態 URL を使用します。 **managedIdentityID** については、[ユーザー割り当てマネージド ID の作成](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity)に関するページを参照してください。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$managedIdentityID = Read-Host -Prompt "Enter a user-assigned managed identity"
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation `
    -managedIdentityID $managedIdentityID `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` は非同期呼び出しです。 成功メッセージは、デプロイが正常に開始されたことだけを意味します。 デプロイを検証するには、`Get-AZDeploymentManagerRollout` を使用します。  次の手順を確認してください。

次の PowerShell スクリプトを使用して、ロールアウトの進行状況を確認する:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

次の出力例は、異常状態が原因でデプロイが失敗したことを示しています。

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

ロールアウトが完了すると、米国西部用に作成された追加のリソース グループが 1 つ表示されます。

## <a name="deploy-the-rollout-with-the-healthy-status"></a>異常状態でのロールアウトのデプロイ

このセクションを繰り返して、正常状態の URL でロールアウトを再デプロイします。  ロールアウトが完了すると、米国東部用に作成されたリソース グループがもう 1 つ表示されます。

## <a name="verify-the-deployment"></a>デプロイを検証する

1. [Azure Portal](https://portal.azure.com)を開きます。
2. ロールアウトの配備によって作成された新しいリソース グループの下で、新たに作成された Web アプリケーションを参照します。
3. Web ブラウザーで、その Web アプリケーションを開きます。 index.html ファイルの場所とバージョンを確認します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルタリング]** フィールドを使用して、このチュートリアルで作成したリソース グループを絞り込みます。 次の 3-4 になります。

    * **&lt;namePrefix>rg**: Deployment Manager リソースが収納されます。
    * **&lt;namePrefix>ServiceWUSrg**: ServiceWUS によって定義されたリソースが収納されます。
    * **&lt;namePrefix>ServiceEUSrg**: ServiceEUS によって定義されたリソースが収納されます。
    * ユーザー定義マネージド ID 用のリソース グループです。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。
5. このチュートリアルで作成した他のリソース グループを削除するには、最後の 2 つの手順を繰り返します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Deployment Manager の正常性チェック機能を使用する方法について学習しました。 詳細は、「[Azure Resource Manager のドキュメント](/azure/azure-resource-manager/)」を参照してください。
