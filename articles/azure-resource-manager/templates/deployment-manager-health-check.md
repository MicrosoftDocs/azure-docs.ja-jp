---
title: 正常性統合ロールアウト - Azure Deployment Manager
description: Azure Deployment Manager で多くのリージョンにわたってサービスをデプロイする方法について説明します すべてのリージョンにロール アウトする前に、デプロイの安定性を確認する安全なデプロイの実施方法について説明します。
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: ae95269dbac3ef1561e19d4b7ea5dd383c1eed73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99536969"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Azure Deployment Manager に正常性統合ロールアウトを導入する (パブリック プレビュー)

[Azure Deployment Manager](./deployment-manager-overview.md) を利用すると、Azure Resource Manager リソースを段階的にロールアウトできます。 リソースはリージョン単位で順番にデプロイされます。 Azure Deployment Manager の統合正常性チェックでは、ロールアウトを監視し、ロールアウトに問題がある場合、自動的に停止できます。問題を解決し、影響を最小限に抑えることができます。 この機能によって、更新中の後戻りによってサービスが利用できなくなることを減らすことができます。

## <a name="health-monitoring-providers"></a>正常性監視プロバイダー

Microsoft は、正常性統合を可能な限り簡単にするために、サービス正常性監視のトップ企業と連携して、正常性チェックをデプロイと統合するための簡単なコピー/貼り付けソリューションを提供しています。 正常性モニターをまだ使用していない場合、こちらのソリューションから始めることをお勧めします。

| ![Azure Deployment Manager の正常性モニター プロバイダー、Azure Monitor](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![Azure Deployment Manager の正常性モニター プロバイダー、Datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![Azure Deployment Manager の正常性モニター プロバイダー、Site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Azure Deployment Manager の正常性モニター プロバイダー、Wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Azure Monitor、クラウド ネイティブ向けの Microsoft のフル スタック可観測性プラットフォーム、監視と分析のハイブリッド。 |Datadog は、最新式のクラウド環境向け監視/分析プラットフォームで業界をリードしています。 「[how Datadog integrates with Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/)」 (Datadog と Azure Deployment Manager の統合のしくみ) を参照してください。|Site24x7 は、プライベート/パブリック クラウド サービス監視のオールインワン型ソリューションです。 「[how Site24x7 integrates with Azure Deployment Manager](https://www.site24x7.com/azure/adm.html)」 (Site24x7 と Azure Deployment Manager の統合のしくみ) を参照してください。| Wavefront は、マルチクラウド アプリケーション環境向けの監視/分析プラットフォームです。 「[how Wavefront integrates with Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/)」 (Wavefront と Azure Deployment Manager の統合のしくみ) を参照してください。|

## <a name="how-service-health-is-determined"></a>サービスの正常性が判断されるしくみ

[正常性監視プロバイダー](#health-monitoring-providers)は、サービスを監視し、サービスの正常性に問題があればそれを警告するメカニズムをいくつか提供しています。 [Azure Monitor](../../azure-monitor/overview.md) もそのようなサービスの 1 つです。 Azure Monitor は、特定のしきい値が超過したときにアラートを作成するために使用できます。 たとえば、新しい更新プログラムをサービスにデプロイすると、メモリと CPU の使用率が予想レベルを超えて急上昇します。 通知を受けたら、是正措置を行うことができます。

サービスのモニターの状態をプログラミングで調べられるように、こうした正常性プロバイダーは通常、REST API を提供します。 REST API は (HTTP 応答コードで決定される) 単純な正常/異常信号と共に戻ってきますが、それが受信する信号に関する詳細情報も含まれることがあります。

Azure Deployment Manager の新しい `healthCheck` 手順を使用すると、正常なサービスを示す HTTP コードを宣言できます。 REST の結果が複雑な場合は、一致した場合に正常な応答を示す正規表現を指定できます。

Azure Deployment Manager 正常性チェックの設定の流れ:

1. ご利用の正常性サービス プロバイダーを介して正常性モニターを作成します。
1. Azure Deployment Manager ロールアウトの一環として 1 つまたは複数の `healthCheck` 手順を作成します。 `healthCheck` 手順に次の情報を入力します。

    1. 正常性モニターの REST API の URI (正常性サービス プロバイダーによって定義されています)。
    1. 認証情報です。 現在のところ、API キー スタイルの認証のみサポートされています。 Azure Monitor に関しては、Azure Deployment Manager ロールアウトに使用されるユーザー割り当てのマネージド ID が Azure Monitor に拡張されるため、認証の種類を `RolloutIdentity` として設定してください。
    1. [HTTP 状態コード](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes)または正常性応答を定義する正規表現。 すべてが一致した場合に応答を正常と見なす正規表現か、一部でも一致すれば正常と見なす正規表現を指定できます。 いずれの方法もサポートされています。

    次の JSON は、Azure Monitor を Azure Deployment Manager と統合する例です。 この例では `RolloutIdentity` を使用し、アラートがない場合にロールアウトが続行される正常性チェックを設定します。 サポートされている唯一の Azure Monitor API:[アラート – すべて取得](/rest/api/monitor/alertsmanagement/alerts/getall)。

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT1M",
          "maxElasticDuration": "PT1M",
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
                    "type": "RolloutIdentity"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "\"value\":\\[\\]"
                    ],
                    "matchQuantifier": "All"
                  }
                }
              }
            ]
          }
        }
      }
    }
    ```

    次の JSON は、その他すべての正常性監視プロバイダーの例です。

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

1. Azure Deployment Manager ロールアウト中、適切なタイミングで `healthCheck` 手順を呼び出します。 次の例では、`stepGroup2` の `postDeploymentSteps` で `healthCheck` 手順が呼び出されます。

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

サンプルを段階的に体験するには、「[チュートリアル: Use health check in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)」 (チュートリアル: Azure Deployment Manager で正常性チェックを使用する) を参照してください。

## <a name="phases-of-a-health-check"></a>正常性チェックのフェーズ

この時点では、Azure Deployment Manager では、サービスの正常性を問い合わせる方法とロールアウト中にそれを行うフェーズが認識されています。 しかしながら、Azure Deployment Manager では、このようなチェックのタイミングを詳細に設定することもできます。 `healthCheck` 手順は連続する 3 つのフェーズで実行されます。そのいずれでも、継続時間を設定できます。

1. Wait

    1. デプロイ操作の完了後、VM は再起動され、新しいデータに基づいて再構成されることがあります。さらには、初めて開始されることもあります。 正常性監視プロバイダーが集計し、有用な情報に変える正常性信号の送信がサービスで開始されるまで時間がかかることがあります。 この騒々しいプロセスの間は、サービスの正常性を確認しても意味がないかもしれません。更新が安定した状態に達していないためです。 実際、リソースが落ち着くとき、サービスは正常な状態とそうではない状態の間で揺れていることがあります。
    1. 待機フェーズ中、サービスの正常性は監視されません。 正常性チェック プロセスを始める前に、デプロイされたリソースにベイクする時間を与えるために利用されます。

1. Elastic

    1. あらゆるケースにおいて、リソースが安定するまでにかかる時間を把握することはできないため、Elastic フェーズでは、リソースが潜在的に不安定なときと安定して正常な状態を維持するためにリソースが必要とされるときの間に柔軟な時間が確保されます。
    1. Elastic フェーズの開始時、Azure Deployment Manager は指定の REST エンドポイントにポーリングを開始し、サービスの正常性を定期的に問い合わせます。 ポーリング間隔は設定可能です。
    1. サービスが正常ではないことを示す信号と共に正常性モニターが戻ってきた場合、そのような信号は無視され、Elastic フェーズが続行され、ポーリングが続行されます。
    1. サービスが正常であることを示す信号が正常性モニターによって戻されると、Elastic フェーズは終了となり、HealthyState フェーズが開始されます。
    1. そのため、Elastic フェーズに指定された継続時間は、正常応答が必須と見なされるまでサービスの正常性のポーリングに利用できる最大時間となります。

1. HealthyState

    1. HealthyState フェーズの間、Elastic フェーズと同じ間隔でサービスの正常性が継続的にポーリングされます。
    1. サービスには、指定されている継続時間の間ずっと、正常性監視プロバイダーからの正常性信号を維持することが求められます。
    1. ある時点で異常応答が検出された場合、Azure Deployment Manager によってロールアウト全体が中止となり、異常サービス信号を含む REST 応答が返されます。
    1. HealthyState の継続時間が終了すると、`healthCheck` は完了となります。デプロイは次の手順に進みます。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Deployment Manager で正常性監視を統合する方法について学習しました。 Deployment Manager でデプロイする方法については、次の記事に進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: Azure Deployment Manager で正常性チェックを使用する](./deployment-manager-tutorial-health-check.md)
