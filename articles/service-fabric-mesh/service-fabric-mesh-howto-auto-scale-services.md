---
title: Azure Service Fabric Mesh で実行されているアプリを自動スケーリングする
description: Service Fabric Mesh アプリケーションのサービスに対して自動スケーリング ポリシーを構成する方法について説明します。
author: georgewallace
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: a707e3601bb24b2d5c2aa9402edff4a2e8803033
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625906"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Service Fabric Mesh アプリケーションに対する自動スケーリング ポリシーを作成する

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)に関する記事を参照してください。

Service Fabric Mesh にアプリケーションをデプロイすることの主な利点の 1 つは、サービスを簡単にスケールイン/スケールアウトできることです。これはサービスに対する負荷量の変化に対処するためや可用性を上げるために使用します。 手動でサービスをスケールインまたはスケールアウトしたり、自動スケーリング ポリシーを設定したりできます。

[自動スケーリング](service-fabric-mesh-scalability.md#autoscaling-service-instances)を使用すると、サービス インスタンスの数を動的に増減できます (水平スケーリング)。 自動スケーリングは弾力性に優れ、CPU またはメモリの使用率に基づいてサービス インスタンスをプロビジョニングしたり、削除したりできます。

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>自動スケーリング ポリシーを作成するためのオプション、トリガー、メカニズム
自動スケーリング ポリシーは、スケーリングするサービスごとに定義します。 ポリシーは、YAML サービス リソース ファイルまたは JSON デプロイ テンプレートで定義します。 各スケーリング ポリシーは、トリガーとスケーリング メカニズムの 2 つの部分で構成されます。

トリガーでは、どのようなときに自動スケーリング ポリシーが呼び出されるかを定義します。  トリガーの種類 (平均負荷) と監視するメトリック (CPU またはメモリ) を指定します。  上限と下限の負荷のしきい値をパーセントとして指定します。 スケーリング間隔では、現在デプロイされているすべてのサービス インスタンスで、指定した使用率 (平均 CPU 負荷など) をチェックする頻度 (秒単位) を定義します。  監視対象のメトリックが下限しきい値を下回った場合または上限しきい値を上回った場合は、メカニズムがトリガーされます。  

スケーリング メカニズムでは、ポリシーがトリガーされたときにスケーリング操作を実行する方法を定義します。  メカニズムの種類 (レプリカの追加/削除)、最小と最大のレプリカ数 (整数) を指定します。  サービス レプリカの数は、最小数より低く、または最大数より高くはスケーリングされません。  また、スケーリングの増分を整数で指定します。これは、1 回のスケーリング操作で追加または削除されるレプリカの数です。  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>JSON テンプレートで自動スケーリング ポリシーを定義する

次の例では、JSON デプロイ テンプレートでの自動スケーリング ポリシーを示します。  自動スケーリング ポリシーは、スケーリング対象のサービスのプロパティで宣言します。  この例では、CPU の平均負荷トリガーを定義しています。  デプロイされているすべてのインスタンスの平均 CPU 負荷が 0.2 (20%) を下回るか 0.8 (80%) を上回ると、メカニズムがトリガーされます。  CPU の負荷は、60 秒ごとにチェックされます。  スケーリング メカニズムは、ポリシーがトリガーされたらインスタンスを追加または削除するように定義されています。  サービス インスタンスは、増分 1 で追加または削除されます。  また、最小インスタンス数は 1 に、最大インスタンス数は 40 に定義されています。

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>service.yaml リソース ファイルで自動スケーリング ポリシーを定義する
次の例では、サービス リソース (YAML) ファイルでの自動スケーリング ポリシーを示します。  自動スケーリング ポリシーは、スケーリング対象のサービスのプロパティとして宣言します。  この例では、CPU の平均負荷トリガーを定義しています。  デプロイされているすべてのインスタンスの平均 CPU 負荷が 0.2 (20%) を下回るか 0.8 (80%) を上回ると、メカニズムがトリガーされます。  CPU の負荷は、60 秒ごとにチェックされます。  スケーリング メカニズムは、ポリシーがトリガーされたらインスタンスを追加または削除するように定義されています。  サービス インスタンスは、増分 1 で追加または削除されます。  また、最小インスタンス数は 1 に、最大インスタンス数は 40 に定義されています。

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>次のステップ
[サービスを手動でスケーリングする](service-fabric-mesh-tutorial-template-scale-services.md)方法を学習する
