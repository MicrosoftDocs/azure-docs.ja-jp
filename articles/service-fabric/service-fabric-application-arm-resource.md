---
title: Azure Resource Manager を使用したデプロイとアップグレード
description: Azure Resource Manager テンプレートを使用して Service Fabric クラスターにアプリケーションとサービスをデプロイする方法を説明します。
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: ed6bc7d96cb3ea0934929e6543c5e637a9f42c1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930839"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>アプリケーションとサービスを Azure Resource Manager のリソースとして管理する

Azure Resource Manager を使用して、Service Fabric クラスターにアプリケーションとサービスを デプロイできます。 これは、クラスターの準備が整ってから PowerShell や CLI を使用してアプリケーションのデプロイと管理を行うのではなく、アプリケーションとサービスを JSON で記述してクラスターと同じ Resource Manager テンプレートでデプロイできることを意味します。 アプリケーションの登録、プロビジョニング、デプロイのプロセスを、すべて 1 ステップで実行できます。

これは、クラスターに必要なセットアップ、ガバナンス、またはクラスター管理アプリケーションをデプロイする方法として推奨されます。 これには、[パッチ オーケストレーション アプリケーション](service-fabric-patch-orchestration-application.md)、ウォッチドッグ、または他のアプリケーションやサービスをデプロイする前にクラスターで実行する必要があるすべてのアプリケーションが含まれます。 

該当する場合は、アプリケーションを Resource Manager のリソースとして管理することで次のメリットが得られます。
* 監査証跡:Resource Manager はすべての操作を監査し、詳細な *アクティビティ ログ* を保存します。このログは、アプリケーションとクラスターに加えられたすべての変更を追跡するのに役立ちます。
* Azure ロールベースのアクセス制御 (Azure RBAC):同じ Resource Manager テンプレートを使用して、クラスターと、そのクラスターにデプロイされたアプリケーションへのアクセスを管理できます。
* Azure Resource Manager (Azure Portal 経由) を使用して、クラスターと重要なアプリケーションのデプロイをすべて 1 か所で管理できます。

次のスニペットは、1 つのテンプレートを使用して管理できるさまざまな種類のリソースを示しています。

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

## <a name="add-a-new-application-to-your-resource-manager-template"></a>Resource Manager テンプレートに新しいアプリケーションを追加する

1. クラスターの Resource Manager テンプレートをデプロイ用に準備します。 この手順の詳細については、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」をご覧ください。
2. クラスターに展開するいくつかのアプリケーションについて検討します。 他のアプリケーションから依存される、常に実行されるアプリケーションがある場合や、 クラスターのガバナンスまたはセットアップのアプリケーションをデプロイする予定の場合、 そのようなアプリケーションは、前述のように Resource Manager テンプレートを使用して管理するのが最適です。 
3. この方法でどのようなアプリケーションをデプロイするかを決定したら、それらのアプリケーションをパッケージ化し、zip 圧縮して、ストレージ共有に配置する必要があります。 共有には、デプロイ時に使用する Azure Resource Manager の REST エンドポイントを通じてアクセスできる必要があります。 詳細については、「[ストレージ アカウントの作成](service-fabric-concept-resource-model.md#create-a-storage-account)」を参照してください。
4. Resource Manager テンプレートのクラスターの宣言の下に、各アプリケーションのプロパティを記述します。 これらのプロパティには、レプリカまたはインスタンスの数と、リソース (他のアプリケーションやサービス) 間の依存関係チェーンが含まれます。 これはアプリケーションやサービスのマニフェストに代わるものではなく、マニフェストに含まれる内容の一部をクラスターの Resource Manager テンプレートの一部として記述します。 次に示すのは、ステートレス サービス *Service1* とステートフル サービス *Service2* を *Application1* の一部としてデプロイするサンプル テンプレートです。

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
   }
   ```

   > [!NOTE] 
   > 個々のテンプレート プロパティに関する使用法および詳細を見つけるには、Service Fabric の [Azure Resource Manager リファレンス](/azure/templates/microsoft.servicefabric/clusters/applicationtypes)を参照してください。

5. デプロイします。 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Service Fabric リソース プロバイダーのアプリケーション リソースを削除する
次のコマンドは、クラスターからプロビジョニング解除されるようにアプリ パッケージをトリガーし、それによって、使用されているディスク領域がクリーンアップされます。
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
Microsoft.ServiceFabric/clusters/application を ARM テンプレートから削除するだけでは、アプリケーションはプロビジョニング解除されません

>[!NOTE]
> 削除が完了すると、SFX または ARM でパッケージ バージョンが表示されなくなります。 実行されているアプリケーションの種類のバージョン リソースを削除することはできません。ARM/SFRP によって妨げられます。 実行中のパッケージをプロビジョニング解除しようとすると、SF ランタイムによって妨げられます。


## <a name="manage-an-existing-application-via-resource-manager"></a>Resource Manager を使用して既存のアプリケーションを管理する

クラスターが既に稼働していて、Resource Manager のリソースとして管理したいアプリケーションのいくつかが既にデプロイされている場合は、それらのアプリケーションを削除してから再デプロイする代わりに、同じAPI の PUT 呼び出しを使用してアプリケーションを Resource Manager のリソースとして認識させることができます。 詳細については、「[Service Fabric アプリケーション リソース モデルとは](./service-fabric-concept-resource-model.md)」を参照してください。

> [!NOTE]
> クラスターのアップグレードで異常なアプリを無視できるようにするには、"upgradeDescription/healthPolicy" セクションで "maxPercentUnhealthyApplications: 100" を指定します。すべての設定について詳しくは、[Service Fabrics REST API クラスター アップグレード ポリシーのドキュメント](/rest/api/servicefabric/sfrp-model-clusterupgradepolicy) をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Service Fabric CLI](service-fabric-cli.md) または [PowerShell](service-fabric-deploy-remove-applications.md) を使用して、他のアプリケーションをクラスターにデプロイします。 
* [Service Fabric クラスターをアップグレードします](service-fabric-cluster-upgrade.md)。
