---
title: Windows Azure Diagnostics を使用したパフォーマンスの監視
description: Windows Azure Diagnostics を使用して、Azure Service Fabric クラスターのパフォーマンス カウンターを収集します。
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: c2114f5392da788bb440589e69e704a148731e02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "86258616"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Windows Azure Diagnostics 拡張機能を使用したパフォーマンスの監視

このドキュメントでは、Windows Azure Diagnostics (WAD) 拡張機能を使用して Windows クラスター用のパフォーマンス カウンターの収集を設定するために必要な手順について説明します。 Linux クラスターの場合は、[Log Analytics エージェント](service-fabric-diagnostics-oms-agent.md)を設定して、ノードのパフォーマンス カウンターを収集します。 

 > [!NOTE]
> これらの手順が機能するように、WAD 拡張機能をクラスターにデプロイする必要があります。 セットアップが目的でなければ、[Windows Azure Diagnostics を使用したイベントの集計と収集](service-fabric-diagnostics-event-aggregation-wad.md)をご覧ください。  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>WadCfg を介してパフォーマンス カウンターを収集する

WAD を介してパフォーマンス カウンターを収集するには、クラスターの Resource Manager テンプレートで構成を適切に変更する必要があります。 次の手順に従って、収集するパフォーマンス カウンターをテンプレートに追加し、Resource Manager リソースのアップグレードを実行します。

1. クラスターのテンプレートで WAD の構成を検索します (`WadCfg` を検索)。 収集するパフォーマンス カウンターは、`DiagnosticMonitorConfiguration` の下に追加します。

2. `DiagnosticMonitorConfiguration` に次のセクションを追加することにより、パフォーマンス カウンターを収集するように構成を設定します。 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` は、収集されたカウンターの値が Azure ストレージ テーブルと構成された任意のシンクに転送される頻度を定義します。 

3. 収集するパフォーマンス カウンターを、前の手順で宣言した `PerformanceCounterConfiguration` に追加します。 収集する各カウンターは、`counterSpecifier`、`sampleRate`、`unit`、`annotation`、および関連する任意の `sinks` を使用して定義されます。

"*合計プロセッサ時間*" (CPU が処理操作に使用された時間の合計) のカウンターと、Service Fabric カスタム パフォーマンス カウンターの 1 つである "*Service Fabric Actor Method Invocations per Second*" (1 秒あたりの Service Fabric アクターメソッドの呼び出し数) がある構成の例を示します。 Service Fabric カスタム パフォーマンス カウンターの完全な一覧については、[信頼性の高いアクター パフォーマンス カウンター](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters)と[信頼性の高いサービス パフォーマンス カウンター](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters)の記事を参照してください。

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 カウンターのサンプル レートは、ニーズに応じて変更できます。 形式は `PT<time><unit>` です。カウンターを 1 秒ごとに収集する場合は、`"sampleRate": "PT15S"` と設定します。

 ARM テンプレートの変数を使って、パフォーマンス カウンターの配列を収集することもできます。これは、プロセスごとにパフォーマンス カウンターを収集するときに役に立つことがあります。 次の例では、プロセッサ時間とガベージ コレクター時間をプロセスごとに収集した後、ノード自体で 2 つのパフォーマンス カウンターを収集しています。すべて、変数を使用して行っています。 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

1. 収集が必要なパフォーマンス カウンターを適切に追加したら、クラスター リソースをアップグレードして、これらの変更を実行中のクラスターに反映させる必要があります。 変更した `template.json` を保存して、PowerShell を開きます。 `New-AzResourceGroupDeployment` を使用すると、クラスターをアップグレードできます。 呼び出しは、リソース グループ、更新したテンプレート ファイル、パラメーター ファイルの名前を必要とし、更新したリソースに適切な変更を加えるよう求めます。 自分のアカウントでサインインし、適切なサブスクリプションがあれば、次のコマンドを使用してアップグレードを実行します。

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. アップグレードのロールアウトが完了したら (初めてのデプロイかどうか、およびリソース グループのサイズに応じて、15 ～ 45 分かかります)、WAD はパフォーマンス カウンターを収集し、クラスターに関連付けられているストレージ アカウント内の WADPerformanceCountersTable という名前のテーブルにそれらを送信します。 [Resource Manager テンプレートに AI シンクを追加する](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)ことにより、Application Insights のパフォーマンス カウンターを確認します。

## <a name="next-steps"></a>次のステップ
* クラスターのその他のパフォーマンス カウンターを収集します。 収集が必要なカウンターの一覧については、「[パフォーマンス メトリック](service-fabric-diagnostics-event-generation-perf.md)」を参照してください。
* [Windows VM と Azure Resource Manager テンプレートで監視と診断を利用](../virtual-machines/extensions/diagnostics-template.md)して、`WadCfg` にさらに変更を加えます (診断データを送信する追加のストレージ アカウントの構成など)。
* [WadCfg ビルダー](https://azure.github.io/azure-diagnostics-tools/config-builder/)にアクセスして最初からテンプレートを構築し、構文が正しいことを確認します (https://azure.github.io/azure-diagnostics-tools/config-builder/) で最初からテンプレートを構築し、構文が正しいことを確認します)。
