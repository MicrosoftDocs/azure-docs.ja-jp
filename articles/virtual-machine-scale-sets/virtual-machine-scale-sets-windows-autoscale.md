---
title: "Windows 仮想マシン スケール セットの自動スケール | Microsoft Docs"
description: "Azure PowerShell を利用し、Windows 仮想マシン スケール セットの自動スケールを設定する"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 4fa0cdc2ee71d4e499dfc45bac7b690d728b626a
ms.openlocfilehash: 2649edd4af67e25ce49bf0ced858b7c7e4633290
ms.lasthandoff: 02/09/2017


---
# <a name="automatically-scale-machines-in-a-virtual-machine-scale-set"></a>仮想マシン スケール セットでのマシンの自動スケール
仮想マシン スケール セットを使用すると、まったく同じ仮想マシンを簡単にまとめてデプロイし、管理することができます。 スケール セットは、最高水準のスケーラビリティが要求されるアプリケーションのための、拡張性が高くカスタマイズしやすいコンピューティング レイヤーです。Windows プラットフォーム イメージのほか、Linux プラットフォーム イメージ、カスタム イメージ、拡張機能をサポートしています。 スケール セットの詳細については、「[仮想マシン スケール セットの概要](virtual-machine-scale-sets-overview.md)」を参照してください。

このチュートリアルでは、Windows 仮想マシンのスケール セットを作成し、複数のマシンをまとめて自動的にスケールする方法を紹介しています。 スケール セットを作成し、スケール処理を設定するには、Azure Resource Manager テンプレートを作成し、Azure PowerShell を使ってそれをデプロイする必要があります。 テンプレートの詳細については、「 [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」をご覧ください。 スケール セットの自動スケールに関する詳細については、「 [Automatic scaling and Virtual Machine Scale Sets (自動スケールと仮想マシン スケール セット)](virtual-machine-scale-sets-autoscale-overview.md)」を参照してください。

この記事では、次のリソースと拡張機能をデプロイします。

* Microsoft.Storage/storageAccounts
* Microsoft.Network/virtualNetworks
* Microsoft.Network/publicIPAddresses
* Microsoft.Network/loadBalancers
* Microsoft.Network/networkInterfaces
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.Insights.VMDiagnosticsSettings
* Microsoft.Insights/autoscaleSettings

Resource Manager のリソースの詳細については、「[Azure Resource Manager vs. classic deployment (Azure Resource Manager 対クラシック デプロイ)](../azure-resource-manager/resource-manager-deployment-model.md)」を参照してください。

## <a name="step-1-install-azure-powershell"></a>手順 1: Azure PowerShell をインストールする
最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、Azure にサインインする方法については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」を参照してください。

## <a name="step-2-create-a-resource-group-and-a-storage-account"></a>手順 2: リソース グループとストレージ アカウントを作成する
1. **リソース グループを作成します**。リソースはすべてリソース グループにデプロイする必要があります。 **vmsstestrg1** という名前のリソース グループを作成するには、[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx) を使用します。
2. **ストレージ アカウントを作成します**。このストレージ アカウントがテンプレートの保存先となります。 [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) を使用して、**vmsstestsa** という名前のストレージ アカウントを作成します。

## <a name="step-3-create-the-template"></a>手順 3: テンプレートを作成する
Azure リソース マネージャー テンプレートを使用すると、Azure リソースとそれに関連するデプロイ パラメーターを JSON 形式で記述することによって、それらのリソースをまとめてデプロイし、管理することができます。

1. 任意のエディターで C:\VMSSTemplate.json ファイルを作成し、このテンプレートをサポートする初期 JSON 構造体を追加します。

    ```json
    {
      "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
      },
      "variables": {
      },
      "resources": [
      ]
    }
    ```

2. パラメーターは必須ではありませんが、パラメーターを指定すると、テンプレートをデプロイする際に値を入力することができます。 これらのパラメーターは、テンプレートに追加した parameters を親要素として、その下に追加します。

    ```json   
    "vmName": { "type": "string" },
    "vmSSName": { "type": "string" },
    "instanceCount": { "type": "string" },
    "adminUsername": { "type": "string" },
    "adminPassword": { "type": "securestring" },
    "resourcePrefix": { "type": "string" }
    ```
   
    * スケール セット内のマシンにアクセスするために使用する個別の仮想マシンの名前。
    * テンプレートの保存先となるストレージ アカウントの名前。
    * スケール セットに最初に作成する仮想マシンの数。
    * 仮想マシンの管理者アカウントの名前とパスワード。
    * スケール セットをサポートするために作成されるリソースの名前のプレフィックス。

3. 絶えず値が変化する場合や、複数のパラメーター値を組み合わせて値を作成する必要がある場合は、テンプレートの中で変数を使用して指定できます。 これらの変数は、テンプレートに追加した variables を親要素として、その下に追加します。

    ```json
    "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
    "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
    "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
    "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
    "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
    "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
    "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
    "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
      "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
    ```
   
    * ネットワーク インターフェイスで使用されている DNS 名。

        * 仮想ネットワークとサブネットに使用する IP アドレスの名前とプレフィックス。
        * 仮想ネットワーク、ロード バランサー、ネットワーク インターフェイスの名前と識別子。
        * スケール セット内のマシンに関連付けられているアカウントのストレージ アカウントの名前。
        * 仮想マシンにインストールする診断拡張機能の設定。 診断拡張機能の詳細については、「[Azure リソース マネージャー テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

4. テンプレートに追加した resources を親要素として、その下にストレージ アカウント リソースを追加します。 このテンプレートは、オペレーティング システムのディスクと診断データの保存先として、推奨される&5; つのストレージ アカウントをループを使って作成します。 この一連のアカウントは、スケール セットで最大 100 台の仮想マシンをサポートします。これが現時点での最大数となります。 各ストレージ アカウントには、変数で定義された文字指定子とテンプレートのパラメーターで指定したプレフィックスとを組み合わせた名前が付けられます。

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
      "apiVersion": "2015-06-15",
      "copy": {
        "name": "storageLoop",
        "count": 5
      },
      "location": "[resourceGroup().location]",
      "properties": { "accountType": "Standard_LRS" }
    },
    ```

5. 仮想ネットワーク リソースを追加します。 詳細については、「 [ネットワーク リソース プロバイダー](../virtual-network/resource-groups-networking.md)」を参照してください。

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
        "subnets": [
          {
            "name": "subnet1",
            "properties": { "addressPrefix": "10.0.0.0/24" }
          }
        ]
      }
    },
    ```

6. ロード バランサーとネットワーク インターフェイスによって使用されるパブリック IP アドレス リソースを追加します。

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP1')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName1')]"
        }
      }
    },
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP2')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName2')]"
        }
      }
    },
    ```

7. スケール セットで使用するロード バランサー リソースを追加します。 詳細については、「 [Azure リソース マネージャーによるロード バランサーのサポート](../load-balancer/load-balancer-arm.md)」を参照してください。

    ```json   
    {
      "apiVersion": "2015-06-15",
      "name": "[variables('loadBalancerName')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "loadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
              }
            }
          }
        ],
        "backendAddressPools": [ { "name": "bepool1" } ],
        "inboundNatPools": [
          {
            "name": "natpool1",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "tcp",
              "frontendPortRangeStart": 50000,
              "frontendPortRangeEnd": 50500,
              "backendPort": 3389
            }
          }
        ]
      }
    },
    ```

8. 個別の仮想マシンで使用するネットワーク インターフェイス リソースを追加します。 スケール セット内のマシンには、パブリック IP アドレスでアクセスすることはできません。そのため、同じ仮想ネットワークに別の仮想マシンを作成し、マシンにリモートでアクセスすることになります。

    ```json  
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
              },
              "subnet": {
                "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
              }
            }
          }
        ]
      }
    },
    ```

9. 同じネットワーク内の個別の仮想マシンをスケール セットとして追加します。

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": { "vmSize": "Standard_A1" },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "[concat(parameters('resourcePrefix'), 'os1')]",
            "vhd": {
              "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"        
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        }
      }
    },
    ```

10. 仮想マシン スケール セット リソースを追加し、スケール セット内のすべての仮想マシンにインストールする診断拡張機能を指定します。 このリソースに使用する設定の多くは、仮想マシン リソースと同様です。 主な違いは、スケール セット内の仮想マシンの数を指定する capacity 要素と仮想マシンを更新する方法を指定する upgradePolicy です。 スケール セットは、dependsOn 要素に指定されたすべてのストレージ アカウントが作成されたときに初めて作成されます。

    ```json
    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "apiVersion": "2016-03-30",
      "name": "[parameters('vmSSName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "sku": {
        "name": "Standard_A1",
        "tier": "Standard",
        "capacity": "[parameters('instanceCount')]"
      },
      "properties": {
        "upgradePolicy": {
          "mode": "Manual"
        },
        "virtualMachineProfile": {
          "storageProfile": {
            "osDisk": {
              "vhdContainers": [
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4], '.blob.core.windows.net/vhds')]"
              ],
              "name": "vmssosdisk",
              "caching": "ReadOnly",
              "createOption": "FromImage"
            },
            "imageReference": {
              "publisher": "MicrosoftWindowsServer",
              "offer": "WindowsServer",
              "sku": "2012-R2-Datacenter",
              "version": "latest"
            }
          },
          "osProfile": {
            "computerNamePrefix": "[parameters('vmSSName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "networkconfig1",
                "properties": {
                  "primary": "true",
                  "ipConfigurations": [
                    {
                      "name": "ip1",
                      "properties": {
                        "subnet": {
                          "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                          }
                        ]
                      }
                    }
                  ]
                }
              }
            ]
          },
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
                    "storageAccountKey": "[listkeys(variables('accountid'), '2015-06-15').key1]",
                    "storageAccountEndPoint": "https://core.windows.net"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

11. スケール セットの仮想マシンのプロセッサ使用率に基づく調整方法を定義する autoscaleSettings リソースを追加します。

    ```json
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
                  "metricName": "\\Processor(_Total)\\% Processor Time",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 50.0
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      }
    }
    ```

    このチュートリアルで重要となる値は次のとおりです。
    
    * **metricName**  
    この値は、wadperfcounter 変数で定義したパフォーマンス カウンターと同じです。 診断拡張機能は、この変数を使用して、**Processor(_Total)\% Processor Time** カウンターを収集します。
    
    * **metricResourceUri**  
    この値は、仮想マシン スケール セットのリソース識別子です。
    
    * **timeGrain**  
    この値は、収集するメトリックの粒度です。 このテンプレートでは&1; 分に設定しています。
    
    * **statistic**  
    この値は、自動スケール アクションに対応するためのメトリックの集計方法を指定します。 指定できる値は Average、Min、Max です。 このテンプレートでは、仮想マシンの平均合計 CPU 使用率が収集されます。
    
    * **timeWindow**  
    この値は、インスタンス データを収集する時間の範囲です。 5 分～ 12 時間の範囲で指定する必要があります。
    
    * **timeAggregation**  
    この値は、時間経過に沿った、収集されたデータの集計方法を指定します。 既定値は Average です。 指定できる値は Average、Minimum、Maximum、Last、Total、Count です。
    
    * **operator**  
    この値は、メトリック データとしきい値を比較するときに使用する演算子です。 指定できる値は、Equals、NotEquals、GreaterThan、GreaterThanOrEqual、LessThan、LessThanOrEqual です。
    
    * **threshold**  
    この値は、スケール アクションをトリガーするポイントとなる値です。 このテンプレートでは、スケール セット内のマシンの平均 CPU 使用率が 50% を超えたとき、スケール セットにマシンが追加されます。
    
    * **direction**  
    この値は、しきい値に達したときに実行するアクションを指定します。 指定できる値は Increase または Decrease です。 このテンプレートでは、定義された時間枠 (timeWindow) 内にしきい値が 50% を超えた場合、スケール セット内の仮想マシンの数が増やされます。
    
    * **type**  
    この値は、発生させるアクションの種類です。これは ChangeCount に設定する必要があります。
    
    * **値**  
    この値は、スケール セットに追加する仮想マシンの数またはスケール セットから削除する仮想マシンの数です。 1 以上の値を設定する必要があります。 既定値は 1 です。 このテンプレートでは、しきい値に達したときに、スケール セット内の仮想マシンの数が 1 つ増やされます。
    
    * **cooldown**  
    この値は、前回のスケール アクションから次回のスケール アクションまでの待機時間です。 この値は&1; 分から&1; 週間の範囲で指定する必要があります。

12. テンプレート ファイルを保存します。    

## <a name="step-4-upload-the-template-to-storage"></a>手順 4: テンプレートをストレージにアップロードする
手順 1. で作成したストレージ アカウントの名前とプライマリ キーがわかっていれば、テンプレートをアップロードできます。

1. 手順 1. で作成したストレージ アカウントの名前を指定する変数を Microsoft Azure PowerShell ウィンドウで設定します。
   
    ```powershell
    $storageAccountName = "vmstestsa"
    ```

2. ストレージ アカウントのプライマリ キーを指定する変数を設定します。
   
    ```powershell
    $storageAccountKey = "<primary-account-key>"
    ```
   
   このキーは、Azure ポータルでストレージ アカウント リソースを表示しているときにキー アイコンをクリックして取得できます。
3. ストレージ アカウントのコンテキスト オブジェクトを作成します。ストレージ アカウントに対する操作を検証する際にこのオブジェクトを使用します。
   
    ```powershell
    $ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    ```

4. テンプレートを格納するコンテナーを作成します。

    ```powershell
    $containerName = "templates"
    New-AzureStorageContainer -Name $containerName -Context $ctx  -Permission Blob
    ```

5. その新しいコンテナーにテンプレート ファイルをアップロードします。

    ```powershell   
    $blobName = "VMSSTemplate.json"
    $fileName = "C:\" + $BlobName
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob  $blobName -Context $ctx
    ```

## <a name="step-5-deploy-the-template"></a>手順 5: テンプレートをデプロイする
テンプレートを作成したら、リソースのデプロイを開始できます。 このプロセスを開始するには、次のコマンドを使用します。

```powershell
New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"
```

Enter キーを押すと、先ほど割り当てた変数の値を入力するよう求められます。 次の値を指定します。

```powershell
vmName: vmsstestvm1
  vmSSName: vmsstest1
  instanceCount: 5
  adminUserName: vmadmin1
  adminPassword: VMpass1
  resourcePrefix: vmsstest
```

すべてのリソースが正常にデプロイされるまでに約 15 分かかります。

> [!NOTE]
> Portal の機能を利用してリソースをデプロイすることもできます。 次のリンクを使用します: "https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>"
> 
> 

## <a name="step-6-monitor-resources"></a>手順 6: リソースを監視する
仮想マシン スケール セットに関する情報の一部は、次の方法で入手することができます。

* Azure ポータル - 現在、ポータルを使用して入手できる情報の量は限られています。
* [Azure リソース エクスプローラー](https://resources.azure.com/) - このツールは、スケール セットの現在の状態を調査するうえで最適なツールです。 作成した スケール セットのインスタンス ビューは次のパスをたどって表示できます。
  
    [subscriptions] > {自分のサブスクリプション} > [resourceGroups] > [vmsstestrg1] > [providers] > [Microsoft.Compute] > [virtualMachineScaleSets] > [vmsstest1] > [virtualMachines]

* Azure PowerShell - 次のコマンドを使用して一部の情報を得ることができます。

  ```powershell
  Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  ```
  
  または
  
  ```powershell
  Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView
  ```

* 通常の仮想マシンに接続するときと同じように個別の仮想マシンに接続し、スケール セット内の仮想マシンにリモートからアクセスして個々のプロセスを監視することができます。

> [!NOTE]
> スケール セットに関する情報を得ることができる REST API の一覧については、 [仮想マシン スケール セット](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-7-remove-the-resources"></a>手順 7: リソースを削除する
Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。 リソース グループから各リソースを個別に削除する必要はありません。 リソース グループを削除すると、そのすべてのリソースが自動的に削除されます。

  ```powershell
  Remove-AzureRmResourceGroup -Name vmsstestrg1
  ```

リソース グループを維持する必要がある場合は、スケール セットのみを削除することができます。

  ```powershell
  Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name"
  ```

## <a name="next-steps"></a>次のステップ
* 「 [仮想マシン スケール セットで仮想マシンを管理する](virtual-machine-scale-sets-windows-manage.md)」の情報を参照して、作成したスケール セットを管理します。
* 垂直方向のスケール処理の詳細については、 [仮想マシン スケール セットでの垂直方向の自動スケール](virtual-machine-scale-sets-vertical-scale-reprovision.md)
* Azure Monitor の監視機能の例については、「[Azure Insights の PowerShell クイック スタート サンプル](../monitoring-and-diagnostics/insights-powershell-samples.md)」をご覧ください。
* 通知機能の詳細については、「[Azure Insights で自動スケール操作を使用して電子メールと Webhook アラート通知を送信する](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)」をご覧ください。
* [Azure Monitor で監査ログを使用して電子メールと Webhook アラート通知を送信する](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)方法についてご確認ください。


