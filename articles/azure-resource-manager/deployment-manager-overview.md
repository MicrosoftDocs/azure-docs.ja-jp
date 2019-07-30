---
title: リージョン全体で安全なデプロイを実施する - Azure Deployment Manager
description: Azure Deployment Manager で多くのリージョンにわたってサービスをデプロイする方法について説明します すべてのリージョンにロール アウトする前に、デプロイの安定性を確認する安全なデプロイの実施方法について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 6a25444f0207ec5eceb029c5d31d222a31813e22
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066832"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Azure Deployment Manager で安全なデプロイを実施できるようにする (パブリック プレビュー)

多くのリージョンにわたってサービスをデプロイし、各リージョンで期待どおりに実行できるようにするには、Azure Deployment Manager を使用してサービスの段階的ロールアウトを調整します。 Azure のデプロイの場合と同様に、[Resource Manager テンプレート](resource-group-authoring-templates.md)でサービスのリソースを定義します。 テンプレートを作成した後、Deployment Manager を使用して、サービスのトポロジとロールアウトする方法について記述します。

Deployment Manager は、Resource Manager の一機能です。 デプロイ中に機能を拡張します。 複数のリージョンにデプロイする必要のある複雑なサービスが存在する場合は、Deployment Manager を使用します。 サービスのロールアウトを段階的に行えば、すべてのリージョンにサービスがデプロイされる前に潜在的な問題を見つけることができます。 段階的なロールアウトの特別な注意事項が不要な場合は、Resource Manager の標準[デプロイ オプション](resource-group-template-deploy-portal.md)を使用してください。 Deployment Manager は、継続的インテグレーションと継続的デリバリー (CI/CD) のサービスなど、Resource Manager のデプロイをサポートするすべての既存のサードパーティ ツールとシームレスに統合します。

Azure Deployment Manager はプレビュー段階です。 [フィードバック](https://aka.ms/admfeedback)を提供して、機能の改善にご協力ください。

Deployment Manager を使用するには、次の 4 つのファイルを作成する必要があります。

* トポロジ テンプレート
* ロールアウト テンプレート
* トポロジのパラメーター ファイル
* ロールアウトのパラメーター ファイル

トポロジ テンプレートをデプロイしてから、ロールアウト テンプレートをデプロイします。

その他のリソース:

- [Azure Deployment Manager REST API リファレンス](https://docs.microsoft.com/rest/api/deploymentmanager/)
- [チュートリアル:Resource Manager テンプレートで Azure Deployment Manager を使用する](./deployment-manager-tutorial.md)」の手順に従います。
- [チュートリアル:Use health check in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)」 (チュートリアル: Azure Deployment Manager で正常性チェックを使用する) を参照してください。
- [Azure Deployment Manager サンプル](https://github.com/Azure-Samples/adm-quickstart)。

## <a name="identity-and-access"></a>ID とアクセス

Deployment Manager では、[ユーザー割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) がデプロイ操作を実行します。 デプロイの開始前にこの ID を作成します。 この ID には、サービスをデプロイしているサブスクリプションへのアクセス権と、デプロイを完了するための十分な特権が与えられている必要があります。 ロールを介して認められるアクションの詳細については、「[Azure リソースの組み込みロール](../role-based-access-control/built-in-roles.md)」を参照してください。

ID は、ロールアウトと同じ場所に存在する必要があります。

## <a name="topology-template"></a>トポロジ テンプレート

トポロジ テンプレートでは、サービスを構成する Azure リソースとそのデプロイ先を記述します。 次の図は、サービス例のトポロジを示しています。

![サービス トポロジからサービス、サービス ユニットに至る階層](./media/deployment-manager-overview/service-topology.png)

トポロジ テンプレートには、次のリソースが含まれています。

* 成果物ソース - Resource Manager テンプレートおよびパラメーターの格納場所
* サービス トポロジ - 成果物ソースへのポイント
  * サービス - 場所と Azure サブスクリプション ID を指定します
    * サービス ユニット - リソース グループ、デプロイ モード、およびテンプレートとパラメーター ファイルへのパスを指定します

各レベルでの動作については、指定した値を確認すると役立ちます。

![各レベルの値](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>テンプレートの成果物ソース

トポロジ テンプレートで、テンプレート ファイルとパラメーター ファイルを保持する成果物ソースを作成します。 成果物ソースは、デプロイ用のファイルを引き出す方法です。 この記事の後半ではバイナリ用の別の成果物ソースが登場します。

次の例は、成果物ソースの一般的な形式を示します。

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "<artifact-source-name>",
    "location": "<artifact-source-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "<root-folder-for-templates>",
        "authentication": {
            "type": "SAS",
            "properties": {
                "sasUri": "<SAS-URI-for-storage-container>"
            }
        }
    }
}
```

詳細については、[artifactSources テンプレート リファレンス](/azure/templates/Microsoft.DeploymentManager/artifactSources)を参照してください。

### <a name="service-topology"></a>サービス トポロジ

次の例は、サービス トポロジ リソースの一般的な形式を示します。 テンプレートおよびパラメーター ファイルを保持する成果物ソースのリソース ID を提供します。 サービス トポロジにはすべてのサービス リソースが含まれます。 成果物ソースが使用可能であることを確認ために、サービス トポロジが成果物ソースに応じて異なります。

```json
{
    "type": "Microsoft.DeploymentManager/serviceTopologies",
    "name": "<topology-name>",
    "location": "<topology-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "artifactSourceId": "<resource-ID-artifact-source>"
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "resources": [
        {
            "type": "services",
            ...
        }
    ]
}
```

詳細については、[serviceTopologies テンプレート リファレンス](/azure/templates/Microsoft.DeploymentManager/serviceTopologies)を参照してください。

### <a name="services"></a>サービス

次の例は、サービス リソースの一般的な形式を示しています。 各サービスで、サービスのデプロイに使用する場所と Azure サブスクリプション ID を提供します。 複数のリージョンにデプロイするには、リージョンごとにサービスを定義します。 サービスはサービス トポロジに依存します。

```json
{
    "type": "services",
    "name": "<service-name>",
    "location": "<service-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service-topology>"
    ],
    "properties": {
        "targetSubscriptionId": "<subscription-ID>",
        "targetLocation": "<location-of-deployed-service>"
    },
    "resources": [
        {
            "type": "serviceUnits",
            ...
        }
    ]
}
```

詳細については、[services テンプレート リファレンス](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services)を参照してください。

### <a name="service-units"></a>サービス ユニット

次の例は、サービス ユニット リソースの一般的な形式を示します。 各サービス ユニットで、リソース グループ、デプロイに使用する[デプロイ モード](deployment-modes.md)、およびテンプレートおよびパラメーター ファイルへのパスを指定します。 テンプレートとパラメーターの相対パスを指定する場合、完全なパスは成果物ソースのルート フォルダーから構成されます。 テンプレートとパラメーターの絶対パスを指定できますが、リリースを簡単にバージョン管理する機能が失われます。 サービス ユニットはサービスによって異なります。

```json
{
    "type": "serviceUnits",
    "name": "<service-unit-name>",
    "location": "<service-unit-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service>"
    ],
    "tags": {
        "serviceType": "Service West US Web App"
    },
    "properties": {
        "targetResourceGroup": "<resource-group-name>",
        "deploymentMode": "Incremental",
        "artifacts": {
            "templateArtifactSourceRelativePath": "<relative-path-to-template>",
            "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
        }
    }
}
```

各テンプレートには、1 つの手順でデプロイする関連リソースを含める必要があります。 たとえば、サービス ユニットは、サービスのフロント エンドにすべてのリソースをデプロイするテンプレートを持つことができます。

詳細については、[serviceUnits テンプレート リファレンス](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits)を参照してください。

## <a name="rollout-template"></a>ロールアウト テンプレート

ロールアウト テンプレートには、サービスのデプロイ時に行う手順を記述します。 使用するサービス トポロジを指定し、サービス ユニットのデプロイの順序を定義します。 デプロイ用バイナリを格納するための成果物ソースが含まれています。 ロールアウト テンプレートでは、次の階層を定義します。

* 成果物ソース
* 手順
* ロールアウト
  * 手順グループ
    * デプロイ操作

次の図は、ロールアウト テンプレートの階層を示します。

![ロールアウトから手順までの階層](./media/deployment-manager-overview/Rollout.png)

各ロールアウトには多くの手順グループを使用できます。 各手順グループには、サービス トポロジ内のサービス ユニットを指す 1 つのデプロイ操作があります。

### <a name="artifact-source-for-binaries"></a>バイナリの成果物ソース

ロールアウト テンプレートでは、サービスにデプロイする必要があるバイナリの成果物ソースを作成します。 この成果物ソースは、[テンプレートの成果物ソース](#artifact-source-for-templates)に似ていますが、スクリプト、Web ページ、コンパイル済みコード、サービスで必要なその他のファイルが含まれている点が異なります。

### <a name="steps"></a>手順

デプロイ操作の前または後に実行する手順を定義できます。 現在は、`wait` 手順と "healthCheck" 手順だけを利用できます。

待機手順は、続行する前にデプロイを一時停止します。 次のサービス ユニットをデプロイする前に、サービスが期待どおりに実行されていることを確認できます。 次の例は、待機手順の一般的な形式を示します。

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.DeploymentManager/steps",
    "name": "waitStep",
        "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

duration プロパティは、[ISO 8601 標準](https://en.wikipedia.org/wiki/ISO_8601#Durations)を使用します。 前述の例では、1 分間の待機を指定します。

正常性チェックの手順の詳細については、「[Azure Deployment Manager に正常性統合ロールアウトを導入する](./deployment-manager-health-check.md)」と「[Tutorial:Use health check in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)」 (チュートリアル: Azure Deployment Manager で正常性チェックを使用する) を参照してください。

詳細については、[steps テンプレート リファレンス](/azure/templates/Microsoft.DeploymentManager/steps)を参照してください。

### <a name="rollouts"></a>ロールアウト

成果物ソースが使用可能であることを確認するために、ロールアウトが成果物ソースに応じて異なります。 ロールアウトは、デプロイされるサービス ユニットごとの手順グループを定義します。 デプロイの前または後に行うアクションを定義できます。 たとえば、サービス ユニットがデプロイされた後にデプロイが待機することを指定できます。 手順グループの順序を定義できます。

ID オブジェクトは、デプロイ アクションを実行する[ユーザー割り当てマネージド ID](#identity-and-access) を指定します。

次の例は、ロールアウトの一般的な形式を示します。

```json
{
    "type": "Microsoft.DeploymentManager/rollouts",
    "name": "<rollout-name>",
    "location": "<rollout-location>",
    "apiVersion": "2018-09-01-preview",
    "Identity": {
        "type": "userAssigned",
        "identityIds": [
            "<managed-identity-ID>"
        ]
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "properties": {
        "buildVersion": "1.0.0.0",
        "artifactSourceId": "<artifact-source-ID>",
        "targetServiceTopologyId": "<service-topology-ID>",
        "stepGroups": [
            {
                "name": "stepGroup1",
                "dependsOnStepGroups": ["<step-group-name>"],
                "preDeploymentSteps": ["<step-ID>"],
                "deploymentTargetId":
                    "<service-unit-ID>",
                "postDeploymentSteps": ["<step-ID>"]
            },
            ...
        ]
    }
}
```

詳細については、[rollouts テンプレート リファレンス](/azure/templates/Microsoft.DeploymentManager/rollouts)を参照してください。

## <a name="parameter-file"></a>パラメーター ファイル

2 つのパラメーター ファイルを作成します。 一方のパラメーター ファイルはサービス トポロジのデプロイ時に使用され、もう一方はロールアウト デプロイに使用されます。 両方のパラメーター ファイルで同じであることを確認する必要のあるいくつかの値があります。

## <a name="containerroot-variable"></a>containerRoot 変数

バージョン管理されたデプロイでは、成果物へのパスを新しいバージョンごとに変更します。 デプロイを初めて実行するときに、パスは `https://<base-uri-blob-container>/binaries/1.0.0.0` であるとします。 2 回目は、`https://<base-uri-blob-container>/binaries/1.0.0.1` になります。 Deployment Manager は、`$containerRoot` 変数を使用すると、現在のデプロイに関する正しいルート パスの取得が簡略化されます。 この値はバージョンごとに変更され、デプロイ前にはわかりません。

テンプレート用のパラメーター ファイルで `$containerRoot` 変数を使用して、Azure リソースをデプロイします。 デプロイ時に、この変数は、ロールアウトからの実際の値に置き換えられます。

たとえば、ロールアウト中に、バイナリの成果物の成果物ソースを作成します。

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "[variables('rolloutArtifactSource').name]",
    "location": "[parameters('azureResourceLocation')]",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "[parameters('binaryArtifactRoot')]",
        "authentication" :
        {
            "type": "SAS",
            "properties": {
                "sasUri": "[parameters('artifactSourceSASLocation')]"
            }
        }
    }
},
```

`artifactRoot` および `sasUri` プロパティを通知します。 成果物のルートは、`binaries/1.0.0.0` のような値に設定できます。 SAS URI は、アクセス用の SAS トークンを含むストレージ コンテナーの URI です。 Deployment Manager は、`$containerRoot` 変数の値を自動的に構築します。 `<container>/<artifactRoot>` の形式でこれらの値を組み合わせます。

テンプレートおよびパラメーター ファイルは、バージョン管理されたバイナリを取得するための正しいパスを認識している必要があります。 たとえば、Web アプリのファイルをデプロイするには、$containerRoot 変数で次のパラメータ ファイルを作成します。 パスには 2 つの円記号 (`\\`) を使用する必要があります。最初の円記号はエスケープ文字であるためです。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "deployPackageUri": {
            "value": "$containerRoot\\helloWorldWebAppWUS.zip"
        }
    }
}
```

次に、テンプレートでこのパラメータを使用します。

```json
{
    "name": "MSDeploy",
    "type": "extensions",
    "location": "[parameters('location')]",
    "apiVersion": "2015-08-01",
    "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
    ],
    "tags": {
        "displayName": "WebAppMSDeploy"
    },
    "properties": {
        "packageUri": "[parameters('deployPackageURI')]"
    }
}
```

新しいフォルダーを作成し、ロールアウト中にそのルートを渡すことによって、バージョン管理されたデプロイを管理します。 パスは、リソースをデプロイするテンプレートにまで至ります。

## <a name="next-steps"></a>次の手順

この記事では、Deployment Manager について学習しました。 Deployment Manager でデプロイする方法については、次の記事に進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:Resource Manager テンプレートで Azure Deployment Manager を使用する](./deployment-manager-tutorial.md)