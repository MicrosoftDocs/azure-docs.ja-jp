---
title: "自動スケールと仮想マシン スケール セット | Microsoft Docs"
description: "診断機能と自動スケール リソースを使用してスケール セット内の仮想マシンを自動的にスケールする方法を説明します。"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: adegeo
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 3f1fdddcf382026e25ac6bca0b835e560152be9f
ms.openlocfilehash: 98b302c26867d2fb2ade296e6983306266fa6f63
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-use-automatic-scaling-and-virtual-machine-scale-sets"></a>自動スケールと仮想マシン スケール セットの使用方法
スケール セット内の仮想マシンの自動スケールとは、パフォーマンス要件を満たすために、必要に応じてセット内の仮想マシンを作成または削除することを意味します。 作業の量が多くなると、アプリケーションで効率よく作業を実行できるようにするために、追加リソースが必要になる場合があります。

自動スケールは、管理オーバーヘッドを軽減するための自動化されたプロセスです。 オーバーヘッドを減らすことで、システム パフォーマンスを継続的に監視する必要がなくなるだけでなく、リソースの管理方法を決める必要性もなくなります。 スケールはエラスティック プロセスです。 負荷が増加したときにはより多くのリソースを追加できます。一方、需要が減ったときには、リソースを削除してコストを軽減しながらも、適切なパフォーマンスを維持できます。

スケール セットに対する自動スケールは、Azure Resource Manager テンプレート、Azure PowerShell、Azure CLI、Azure Portal を使用して設定します。

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>Resource Manager テンプレートを使用したスケール設定
アプリケーションの各リソースを個別にデプロイして管理するのではなく、1 回の連携した操作ですべてのリソースをデプロイするテンプレートを使用します。 このテンプレートでは、アプリケーションのリソースを定義し、さまざまな環境に対応するデプロイ パラメーターを指定します。 テンプレートは、JSON、およびデプロイの値を構築するときの式で構成されます。 詳細については、「 [Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。

テンプレートでは、容量 (capacity 要素) を指定します。

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

容量によって、セット内の仮想マシンの数を指定します。 容量を手動で変更するには、別の値を指定したテンプレートをデプロイします。 容量を変更するためだけにテンプレートをデプロイする場合、新しい容量を指定した SKU 要素のみを含めることができます。

スケール セットの容量を自動的に変更するには、autoscaleSettings リソースと診断拡張機能を組み合わせて使用します。

### <a name="configure-the-azure-diagnostics-extension"></a>Azure 診断拡張機能の構成
自動スケールは、スケール セット内の各仮想マシンでメトリックが正常に収集されている場合にのみ実行できます。 Azure 診断拡張機能は、自動スケール リソースにおけるメトリック収集のニーズを満たす監視機能と診断機能を備えています。 この拡張機能は、Resource Manager テンプレートの一部としてインストールできます。

次の例では、テンプレートで診断拡張機能を構成するために使用される変数を示しています。

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

パラメーターはテンプレートがデプロイされたときに指定されます。 この例では、データが格納されるストレージ アカウントの名前やデータが収集されるスケール セットの名前が指定されています。 また、この Windows Server の例では Thread Count パフォーマンス カウンターのみが収集されます。 Windows または Linux で使用可能なすべてのパフォーマンス カウンターを使用して診断情報を収集できます。こうしたパフォーマンス カウンターは、拡張機能の構成に含めることができます。

次の例では、テンプレートにおける拡張機能の定義を示しています。

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

診断拡張機能を実行すると、指定したストレージ アカウントに置かれたテーブルにデータが収集されます。 収集されたデータは、WADPerformanceCounters テーブルで見ることができます。

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>autoScaleSettings リソースの構成
autoscaleSettings リソースは、スケール セット内の仮想マシン数の増減に関する判断を下すために、診断拡張機能によって収集された情報を利用します。

次の例は、テンプレートにおけるリソースの構成を示しています。

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

この例では、自動スケール アクションを定義するために&2; つの規則を作成しています。 最初の規則でスケールアウト アクションを定義し、2 番目の規則でスケールイン アクションを定義します。 この&2; つの規則では次の値が指定されています。

* **metricName** - この値は、診断拡張機能の wadperfcounter 変数で定義したパフォーマンス カウンターと同じです。 この例では、Thread Count カウンターが使用されます。  
* **metricResourceUri** - この値は、仮想マシン スケール セットのリソース識別子です。 この識別子には、リソース グループの名前、リソースプロバイダーの名前、スケールするスケール セットの名前が含まれます。
* **timeGrain** – この値は、収集するメトリックの粒度です。 前の例では、1 分間隔でデータを収集します。 この値は、timeWindow と組み合わせて使用されます。
* **statistic** – この値は、自動スケール アクションに対応するためのメトリックの集計方法を指定します。 指定できる値は Average、Min、Max です。
* **timeWindow** – この値は、インスタンス データを収集する時間の範囲です。 5 分～ 12 時間の範囲で指定する必要があります。
* **timeAggregation** – この値は、時間経過に沿った、収集されたデータの集計方法を指定します。 既定値は Average です。 指定できる値は Average、Minimum、Maximum、Last、Total、Count です。
* **operator** – この値は、メトリック データとしきい値を比較するときに使用する演算子です。 指定できる値は、Equals、NotEquals、GreaterThan、GreaterThanOrEqual、LessThan、LessThanOrEqual です。
* **threshold** – この値は、スケール アクションをトリガーするポイントとなる値です。 スケールアウト アクションのしきい値と、スケールイン アクションのしきい値の間には、十分な差を持たせてください。 2 つのしきい値を同じ値に設定した場合、セットのサイズが絶え間なく変更されることを避けるために、スケール アクションが実行されなくなります。 たとえば、上の例で両方のしきい値を 600 スレッドに設定した場合、スケール アクションは実行されません。
* **direction** – この値は、しきい値に達したときに実行するアクションを指定します。 指定できる値は Increase または Decrease です。
* **type** – この値は、発生させるアクションの種類です。これは ChangeCount に設定する必要があります。
* **value** – この値は、スケール セットに追加する仮想マシンの数またはスケール セットから削除する仮想マシンの数です。 1 以上の値を設定する必要があります。
* **cooldown** – この値は、前回のスケール アクションから次回のスケール アクションまでの待機時間です。 この値は&1; 分から&1; 週間の範囲で指定する必要があります。

使用するパフォーマンス カウンターによっては、このテンプレート構成の一部の要素を別の方法で使用します。 上の例では、パフォーマンス カウンターを Thread Count に、スケールアウト アクションのしきい値を 650 に、スケールイン アクションのしきい値を 550 に設定しています。 %Processor Time などのカウンターを使用する場合は、スケール アクションを決定する CPU 使用率をしきい値として設定します。

仮想マシンに負荷が発生し、スケール アクションがトリガーされると、テンプレート内の value の値に応じてセットの容量が増やされます。 たとえば、容量が 3、スケール アクション値が 1 に設定されているスケール セットは次のようになります。

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

負荷が発生し、平均スレッド数がしきい値 650 を超えたときには、次のようになります。

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

スケールアウト アクションがトリガーされ、セットの容量が&1; ずつ増加します。

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

さらに、スケール セットに仮想マシンが追加されます。

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

5 分間のクールダウン期間の後で、仮想マシンの平均スレッド数がまだ 600 を超えている場合には、セットに仮想マシンがもう 1 つ追加されます。 平均スレッド数が 550 を下回ったままになると、スケール セットの容量が 1 削減され、セットから 1 つの仮想マシンが削除されます。

## <a name="set-up-scaling-using-azure-powershell"></a>Azure PowerShell を使用したスケール設定
PowerShell を使用して自動スケールを設定する例については、「[Azure Insights の PowerShell クイック スタート サンプル](../monitoring-and-diagnostics/insights-powershell-samples.md)」をご覧ください。

## <a name="set-up-scaling-using-azure-cli"></a>Azure CLI を使用したスケール設定
Azure CLI を使用して自動スケールを設定する例については、「[Azure Insights クロスプラットフォーム CLI のクイック スタート サンプル](../monitoring-and-diagnostics/insights-cli-samples.md)」をご覧ください。

## <a name="set-up-scaling-using-the-azure-portal"></a>Azure Portal を使用したスケール設定
Azure Portal を使用して自動スケーリングを設定する例については、「[Azure Portal を使用して仮想マシン スケール セットを作成する](virtual-machine-scale-sets-portal-create.md)」をご覧ください。

## <a name="investigate-scaling-actions"></a>スケール操作に関する情報
* Azure Portal - 現在、ポータルを使用して入手できる情報の量は限られています。
* Azure リソース エクスプローラー - このツールは、スケール セットの現在の状態を調査するうえで最適なツールです。 次のパスに従うと、作成したスケール セットのインスタンス ビューが表示されます: [ubscriptions]、{該当するサブスクリプション}、[resourceGroups]、{該当するリソース グループ}、[providers]、[Microsoft.Compute]、[virtualMachineScaleSets]、{該当するスケール セット}、[virtualMachines]
* Azure PowerShell - 次のコマンドを使用して一部の情報を得ることができます。
  
        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput
* 通常の仮想マシンに接続するときと同じように Jumpbox 仮想マシンに接続し、スケール セット内の仮想マシンにリモートからアクセスして個々のプロセスを監視することができます。

## <a name="next-steps"></a>次のステップ
* 自動スケールを構成したスケール セットの作成方法の例については、「 [仮想マシン スケール セットでのマシンの自動スケール](virtual-machine-scale-sets-windows-autoscale.md) 」を参照してください。
* Azure Monitor の監視機能の例については、「[Azure Insights の PowerShell クイック スタート サンプル](../monitoring-and-diagnostics/insights-powershell-samples.md)」をご覧ください。
* 通知機能の詳細については、「[Azure Insights で自動スケール操作を使用して電子メールと Webhook アラート通知を送信する](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)」をご覧ください。
* [Azure Monitor で監査ログを使用して電子メールと Webhook アラート通知を送信する](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)方法をご確認ください。
* [高度な自動スケールのシナリオ](virtual-machine-scale-sets-advanced-autoscale.md)を紹介します。


