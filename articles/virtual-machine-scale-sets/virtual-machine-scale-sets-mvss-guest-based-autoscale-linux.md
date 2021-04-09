---
title: Azure の自動スケールで Linux スケール セット テンプレートのゲスト メトリックを使用する
description: Linux 仮想マシン スケール セット テンプレートのゲスト メトリックを使用して自動スケールを行う方法について説明します。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/26/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 0605780651e1a3c54ae53d13a3f99e1124fa76db
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585005"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Linux スケール セット テンプレートのゲスト メトリックを使用した自動スケール

VM とスケール セットから収集される Azure のメトリックには、ホスト メトリックとゲスト メトリックの 2 つの大きな種類があります。 一般には、標準的な CPU、ディスク、ネットワーク メトリックを使用している場合、ホスト メトリックは適合します。 ただし、メトリックの選択の幅を大きくする必要がある場合は、ゲスト メトリックの方を使用すべきです。

ホスト メトリックの場合は、ホスト VM によってメトリックが収集されるので、特別な設定は必要ありません。ゲスト メトリックの場合は、ゲスト VM に [Windows 版の Azure Diagnostics の拡張機能](../virtual-machines/extensions/diagnostics-template.md)か、[Linux 版の Azure Diagnostics の拡張機能](../virtual-machines/extensions/diagnostics-linux.md)をインストールする必要があります。 一般に、ホスト メトリックではなくゲスト メトリックを使用する理由の 1 つとして、ゲスト メトリックではホスト メトリックよりも多くのメトリックが提供される点が挙げられます。 たとえば、メモリ消費に関するメトリックは、ゲスト メトリックでしか利用できません。 サポートされているホスト メトリックについては[こちら](../azure-monitor/essentials/metrics-supported.md)を、よく使用されるゲスト メトリックについては[こちら](../azure-monitor/autoscale/autoscale-common-metrics.md)をご覧ください。 この記事では、Linux スケール セットのゲストのメトリックに基づいた自動スケール規則を使用するよう、[実行可能な基本のスケール セットのテンプレート](virtual-machine-scale-sets-mvss-start.md)を編集する方法を説明します。

## <a name="change-the-template-definition"></a>テンプレートの定義を変更する

[前回のアーティクル](virtual-machine-scale-sets-mvss-start.md)では、基本的なスケール セット テンプレートを作成しました。 では、以前のテンプレートを使用して、Linux スケール セットをゲスト メトリック ベースの自動スケーリングに展開するテンプレートを作成します。

まず、`storageAccountName` と `storageAccountSasToken` のパラメーターを追加します。 この診断エージェントが、メトリック データをこのストレージ アカウントの[テーブル](../cosmos-db/tutorial-develop-table-dotnet.md)に格納します。 Linux 診断エージェント バージョン 3.0 の時点で、ストレージ アクセス キーの使用はサポートされなくなりました。 代わりに、[SAS トークン](../storage/common/storage-sas-overview.md)を使用します。

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

次に、診断の拡張機能を含めるようにスケール セット `extensionProfile` を変更します。 この構成では、メトリックの保存に使用するストレージ アカウントと SAS トークンに加え、メトリックを収集するスケール セットのリソース ID を指定します。 メトリックを集計する頻度 (ここでは 1 分ごと) と、追跡するメトリック (ここではメモリ使用率) を指定します。 この構成に関する詳細情報と、メモリ使用率以外のメトリックについては、[このドキュメント](../virtual-machines/extensions/diagnostics-linux.md)を参照してください。

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

最後に、`autoscaleSettings` リソースを追加して、これらのメトリックに基づいて自動スケールを構成します。 このリソースには、自動スケールの前にスケール セットが存在することを確認できるよう、スケール セットを参照する `dependsOn` 句が含まれています。 自動スケールに別のメトリックを選択した場合は、診断の拡張機能の構成の `counterSpecifier` を、自動スケールの構成の `metricName` として使用します。 自動スケールの構成の詳細については、「[自動スケールのベスト プラクティス](../azure-monitor/autoscale/autoscale-best-practices.md)」と [Azure Monitor REST API リファレンス ドキュメント](/rest/api/monitor/autoscalesettings)を参照してください。

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>次のステップ

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
