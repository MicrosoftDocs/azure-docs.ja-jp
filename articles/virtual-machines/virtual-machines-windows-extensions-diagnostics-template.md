---
title: "Azure 仮想マシンに監視と診断を追加する | Microsoft Docs"
description: "Azure リソース マネージャー テンプレートを使用して Azure 診断の拡張機能を含む新しい Windows 仮想マシンを作成する"
services: virtual-machines-windows
documentationcenter: 
author: sbtron
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 855ecb73854e20eb52f343e5d190cbcbaa9ad080
ms.openlocfilehash: 85adb953254496e969fb8d459a0c79592708c835
ms.lasthandoff: 03/01/2017


---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Windows VM と Azure Resource Manager テンプレートで監視と診断を利用する
Azure 診断の拡張機能は、Windows ベースの Azure 仮想マシンに監視および診断機能を提供します。 Azure リソース マネージャー テンプレートの一部として拡張機能を含めることによって、仮想マシンでこれらの機能を有効にすることができます。 仮想マシン テンプレートの一部として拡張機能を含める方法については、「 [VM 拡張機能を使用した Azure リソース マネージャー テンプレートの作成](virtual-machines-windows-extensions-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 」を参照してください。 この記事では、Windows 仮想マシン テンプレートに Azure 診断の拡張機能を追加する方法について説明します。  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>VM のリソースの定義に Azure 診断の拡張機能を追加する
Windows 仮想マシンで診断の拡張機能を有効にするには、リソース マネージャー テンプレートで拡張機能を VM のリソースとして追加する必要があります。

単純なリソース マネージャー ベースの仮想マシンでは、次のように拡張機能の構成を仮想マシンの *resources* の配列に追加します。 

    "resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


もう&1; つの一般的な方法では、拡張機能の構成を仮想マシンのリソース ノードの下に定義するのではなく、テンプレートのルート リソース ノードに追加します。 この方法では、拡張機能と仮想マシンの間で *name* と *type* の値を使用して階層関係を明示的に指定する必要があります。 次に例を示します。 

    "name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

拡張機能は常に仮想マシンと関連付けられるため、仮想マシンのリソース ノードの下に直接定義するか、ベース レベルで定義して階層的な名前付け規則を使用することで、仮想マシンとの関連付けを行うことができます。

仮想マシン スケール セットでは、拡張機能の構成が *VirtualMachineProfile* の *extensionProfile* プロパティに指定されます。

**Microsoft.Azure.Diagnostics** の *publisher* プロパティや *type* プロパティの **IaaSDiagnostics** によって、Azure 診断の拡張機能が一意に識別されます。

リソース グループの拡張機能を参照するには、 *name* プロパティの値を使用することができます。 このプロパティを具体的に **Microsoft.Insights.VMDiagnosticsSettings** に設定することで、Azure クラシック ポータルでの識別が簡単になり、Azure クラシック ポータルで監視グラフが正しく表示されるようになります。

*typeHandlerVersion* は使用する拡張機能のバージョンを指定します。 *autoUpgradeMinorVersion* を **true** に設定することで、使用できる拡張機能で最新のマイナー バージョンが表示されるようになります。 新機能とバグの修正がすべて含まれる、最新の診断の拡張機能を常に使用できるように、 *autoUpgradeMinorVersion* は常に **true** に設定することを強くお勧めします。 

*settings* 要素には、拡張機能 (パブリック構成とも呼ばれます) からの設定および読み取りが可能な拡張機能の構成プロパティが含まれています。 *xmlcfg* プロパティには、診断ログの xml ベースの構成や診断エージェントによって収集されるパフォーマンス カウンターなどが含まれています。 xml スキーマ自体の詳細については、「 [診断構成スキーマ](https://msdn.microsoft.com/library/azure/dn782207.aspx) 」を参照してください。 一般的な方法としては、Azure リソース マネージャー テンプレートに実際の xml 構成を変数として格納してから、これを連結および base64 エンコードして *xmlcfg*の値を設定します。 xml を変数として格納する方法の詳細については、「 [診断構成の変数](#diagnostics-configuration-variables) 」のセクションを参照してください。 *storageAccount* プロパティは、診断データを転送するストレージ アカウントの名前を指定します。 

*protectedSettings* (プライベート構成とも呼ばれる) のプロパティは設定できますが、設定後に読み取ることができません。 *protectedSettings* が書き込み専用であるという性質は、診断データが書き込まれるストレージ アカウント キーのような機密情報を格納する場合に役立ちます。    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>診断ストレージ アカウントをパラメーターとして指定する
上記の診断の拡張機能の JSON スニペットは、*existingdiagnosticsStorageAccountName* と *existingdiagnosticsStorageResourceGroup* という&2; つのパラメーターによって、診断データが格納される診断ストレージ アカウントが指定されることを想定しています。 診断ストレージ アカウントをパラメーターとして指定すると、たとえばテスト用と運用環境のデプロイメント用で異なる診断ストレージ アカウントを使用する場合などに、異なる環境間で診断ストレージ アカウントを変更することが簡単になります。  

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
            }        
        },
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
              }
        }

診断ストレージ アカウントは仮想マシンのリソース グループとは異なるリソース グループに指定することをお勧めします。 リソース グループは、独自の有効期間を持つデプロイメント単位と見なすことができます。新しい構成の更新により、仮想マシンはデプロイされたり、再デプロイされたりしますが、診断データを、仮想マシンのデプロイメント全体の同じストレージ アカウントに保存したい場合もあります。 異なるリソースにストレージ アカウントがあると、そのストレージ アカウントがさまざまな仮想マシンのデプロイメントからデータを受け入れられるようになり、異なるバージョンでの問題のトラブルシューティングが簡単になります。

> [!NOTE]
> Windows 仮想マシン テンプレートを Visual Studio から作成する場合、既定のストレージ アカウントが仮想マシン VHD のアップロード先と同じストレージ アカウントを使用するように設定できる場合があります。 これにより、VM の初期セットアップが簡素化します。 パラメーターとして渡すことができる別のストレージ アカウントを使用するようにテンプレートをリファクタリングする必要があります。 
> 
> 

## <a name="diagnostics-configuration-variables"></a>診断構成の変数
上記の診断の拡張機能の JSON スニペットは *accountid* 変数を定義して、診断ストレージのストレージ アカウント キーの取得を簡素化します。   

    "accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


診断の拡張機能の *xmlcfg* プロパティは、連結された複数の変数を使用して定義されます。 これらの変数の値は xml 形式であるため、JSON 変数を設定するときに正しくエスケープする必要があります。

次に、Windows イベント ログおよび診断インフラストラクチャ ログと共に、標準のシステム レベルのパフォーマンス カウンターを収集する診断構成の xml について説明します。 構成をテンプレートの変数セクションに直接貼り付けることができるように、正しくエスケープされ、書式設定されています。 より人間が判読しやすい構成の xml の例については「 [診断構成スキーマ](https://msdn.microsoft.com/library/azure/dn782207.aspx) 」を参照してください。

        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

上記の構成のメトリック定義の xml ノードは、xml の *PerformanceCounter* で先に定義されているパフォーマンス カウンターを集計して格納する方法を定義するため、重要な構成要素です。 

> [!IMPORTANT]
> これらのメトリックは、Azure Portal でのグラフやアラートの監視を促進します。  Azure Portal で VM 監視データを表示するには、*resourceID* および **MetricAggregation** のある **Metrics** ノードを VM の診断構成に含める必要があります。 
> 
> 

メトリックの定義に関する xml の例を次に示します。 

        <Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>

*resourceID* 属性はサブスクリプションで仮想マシンを一意に識別します。 テンプレートがデプロイしているサブスクリプションとリソース グループに基づいて値を自動的に更新するように、subscription() と resourceGroup() 関数を使用してください。

ループ内で複数の Virtual Machines を作成している場合は、各仮想マシンを正しく区別するため、copyIndex() 関数に *resourceID* の値を入力する必要があります。 これをサポートするには、 *xmlCfg* の値を次のように更新します。  

    "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 

*PT1H* と *PT1M* の MetricAggregation 値は、1 分間以上の集計と&1; 時間以上の集計を表します。

## <a name="wadmetrics-tables-in-storage"></a>ストレージの WADMetrics テーブル
上記のメトリックの構成によって、次の名前付け規則による診断ストレージ アカウントのテーブルが生成されます。

* **WADMetrics** : すべての WADMetrics テーブルの標準プレフィックス
* **PT1H** または **PT1M**: テーブルに 1 時間または 1 分間以上の集計データが含まれることを示します
* **P10D** : テーブルがデータの収集を開始してから 10 日間のデータがテーブルに含まれることを示します
* **V2S** : 文字列定数
* **yyyymmdd** : テーブルがデータの収集を開始した日付

例: *WADMetricsPT1HP10DV2S20151108* の場合、2015 年 11 月 11 日から 10 日間に 1 時間以上集計されたメトリック データを含みます    

各 WADMetrics テーブルには次の列が含まれます。

* **PartitionKey**: Partitionkey は *resourceID* の値に基づいて構築され、VM リソースを一意に識別します。 例: 002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
* **RowKey**: <Descending time tick>:<Performance Counter Name> の形式に従います。 降順の時間ティック計算は、最大時間ティックから集計期間の開始時間を引いたものです。 例: たとえば、サンプル期間が 2015 年 11 月 10 日に開始し、00:00 UTC の場合、計算は DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks) になります。 メモリで使用可能なバイトのパフォーマンス カウンターの場合、行キーは2519551871999999999__:005CMemory:005CAvailable:0020Bytes のようになります。
* **CounterName** : パフォーマンス カウンターの名前。 これは、xml 構成に定義されている *counterSpecifier* と一致します。
* **Maximum** : 集計期間にわたるパフォーマンス カウンターの最大値。
* **Minimum** : 集計期間にわたるパフォーマンス カウンターの最小値。
* **Total** : 集計期間に報告されたパフォーマンス カウンターのすべての値の合計。
* **Count** : パフォーマンス カウンターで報告された値の合計数。
* **Average** : 集計期間にわたるパフォーマンス カウンターの平均 (合計/カウント) の値。

## <a name="next-steps"></a>次のステップ
* 診断の拡張機能を含む Windows 仮想マシンの完全なサンプル テンプレートは、「 [201-vm-monitoring-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* [Azure PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) または [Azure コマンド ライン](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使用してリソース マネージャー テンプレートをデプロイします
* [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)


