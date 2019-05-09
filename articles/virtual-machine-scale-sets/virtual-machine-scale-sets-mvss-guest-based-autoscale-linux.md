---
title: Azure の自動スケールで Linux スケール セット テンプレートのゲスト メトリックを使用する | Microsoft Docs
description: Linux 仮想マシン スケール セット テンプレートのゲスト メトリックを使用して自動スケールを行う方法について説明します。
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: manayar
ms.openlocfilehash: deddcc8623803f9d003f3fafcef5252ebd34b813
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438338"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Linux スケール セット テンプレートのゲスト メトリックを使用した自動スケール

VM とスケール セットから収集される Azure のメトリックには、ホスト VM から収集されるメトリックと、ゲスト VM から収集されるメトリックの 2 種類があります。 一般には、標準的な CPU、ディスク、ネットワーク メトリックを使用している場合、ホスト メトリックはおそらく適合します。 ただし、メトリックの選択の幅を大きくする必要がある場合は、おそらくゲスト メトリックの方が適切です。 2 つの違いを見てみましょう。

ホスト メトリックの方が単純で確実です。 ホスト VM によってメトリックが収集されるので、特別な設定は必要ありません。ゲスト メトリックの場合は、ゲスト VM に [Windows 版の Azure Diagnostics の拡張機能](../virtual-machines/windows/extensions-diagnostics-template.md)か、[Linux 版の Azure Diagnostics の拡張機能](../virtual-machines/linux/diagnostic-extension.md)をインストールする必要があります。 一般に、ホスト メトリックではなくゲスト メトリックを使用する理由の 1 つとして、ゲスト メトリックではホスト メトリックよりも多くのメトリックが提供される点が挙げられます。 たとえば、メモリ消費に関するメトリックは、ゲスト メトリックでしか利用できません。 サポートされているホスト メトリックについては[こちら](../azure-monitor/platform/metrics-supported.md)を、よく使用されるゲスト メトリックについては[こちら](../azure-monitor/platform/autoscale-common-metrics.md)をご覧ください。 この記事では、Linux スケール セットのゲストのメトリックに基づいた自動スケール規則を使用するよう、[実行可能な最小のスケール セットのテンプレート](./virtual-machine-scale-sets-mvss-start.md)を編集する方法を説明します。

## <a name="change-the-template-definition"></a>テンプレートの定義を変更する

実行可能な最小のスケール セット テンプレートは[こちら](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json)で確認できます。また、ゲストベースの自動スケールを使用して Linux スケール セットをデプロイするためのテンプレートは[こちら](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json)で確認できます。 このテンプレートを作成する際に使用する diff (`git diff minimum-viable-scale-set existing-vnet`) を項目ごとに確認しましょう。

まず、`storageAccountName` と `storageAccountSasToken` のパラメーターを追加します。 この診断エージェントが、メトリック データをこのストレージ アカウントの[テーブル](../cosmos-db/table-storage-how-to-use-dotnet.md)に格納します。 Linux 診断エージェント バージョン 3.0 の時点で、ストレージ アクセス キーの使用はサポートされなくなりました。 代わりに、[SAS トークン](../storage/common/storage-dotnet-shared-access-signature-part-1.md)を使用します。

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

次に、診断の拡張機能を含めるようにスケール セット `extensionProfile` を変更します。 この構成では、メトリックの保存に使用するストレージ アカウントと SAS トークンに加え、メトリックを収集するスケール セットのリソース ID を指定します。 メトリックを集計する頻度 (ここでは 1 分ごと) と、追跡するメトリック (ここではメモリ使用率) を指定します。 この構成に関する詳細情報と、メモリ使用率以外のメトリックについては、[このドキュメント](../virtual-machines/linux/diagnostic-extension.md)を参照してください。

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

最後に、`autoscaleSettings` リソースを追加して、これらのメトリックに基づいて自動スケールを構成します。 このリソースには、自動スケールの前にスケール セットが存在することを確認できるよう、スケール セットを参照する `dependsOn` 句が含まれています。 自動スケールに別のメトリックを選択した場合は、診断の拡張機能の構成の `counterSpecifier` を、自動スケールの構成の `metricName` として使用します。 自動スケールの構成の詳細については、「[自動スケールのベスト プラクティス](..//azure-monitor/platform/autoscale-best-practices.md)」と [Azure Monitor REST API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn931928.aspx)を参照してください。

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





## <a name="next-steps"></a>次の手順

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
