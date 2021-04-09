---
title: Azure 仮想マシンに監視と診断を追加する
description: Azure Resource Manager テンプレートを使用して、Azure Diagnostics の拡張機能を備えた新しい Windows 仮想マシンを作成します。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 05/31/2017
ms.openlocfilehash: 6d365c7e927c11f52b97fbb0cc01a7aa37ad5afd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560057"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Windows VM と Azure Resource Manager テンプレートで監視と診断を利用する
Azure Diagnostics の拡張機能は、Windows ベースの Azure 仮想マシンに監視および診断機能を提供します。 Azure Resource Manager テンプレートの一部として拡張機能を組み込むことにより、仮想マシンでこれらの機能を有効にすることができます。 仮想マシン テンプレートの一部として拡張機能を含める方法については、「 [VM 拡張機能を使用した Azure リソース マネージャー テンプレートの作成](../windows/template-description.md#extensions) 」を参照してください。 この記事では、Windows 仮想マシン テンプレートに Azure Diagnostics の拡張機能を追加する方法について説明します。  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>VM のリソースの定義に Azure Diagnostics の拡張機能を追加する
Windows 仮想マシンで診断の拡張機能を有効にするには、Resource Manager テンプレートで拡張機能を VM リソースとして追加する必要があります。

単純なリソース マネージャー ベースの仮想マシンでは、次のように拡張機能の構成を仮想マシンの *resources* の配列に追加します。 

```json
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
```

また、拡張機能構成を仮想マシンのリソース ノードで定義するのではなく、テンプレートのルート リソース ノードに追加する方法もよく使われます。 この方法では、拡張機能と仮想マシンの間で *name* と *type* の値を使用して階層関係を明示的に指定する必要があります。 次に例を示します。 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

拡張機能は常に仮想マシンと関連付けられるため、仮想マシンのリソース ノードの下に直接定義するか、ベース レベルで定義して階層的な名前付け規則を使用することで、仮想マシンとの関連付けを行うことができます。

仮想マシン スケール セットでは、拡張機能の構成が *VirtualMachineProfile* の *extensionProfile* プロパティに指定されます。

**Microsoft.Azure.Diagnostics** の *publisher* プロパティや *type* プロパティの **IaaSDiagnostics** によって、Azure Diagnostics の拡張機能が一意に識別されます。

リソース グループの拡張機能を参照するには、 *name* プロパティの値を使用することができます。 このプロパティを具体的に **Microsoft.Insights.VMDiagnosticsSettings** に設定することで、Azure Portal での識別が容易になり、Azure Portal で監視グラフが正しく表示されるようになります。

*typeHandlerVersion* は使用する拡張機能のバージョンを指定します。 *autoUpgradeMinorVersion* マイナー バージョンを **true** に設定すると、使用可能な拡張機能の最新マイナー バージョンが得られます。 新機能とバグの修正がすべて含まれる、最新の診断の拡張機能を常に使用できるように、 *autoUpgradeMinorVersion* は常に **true** に設定することを強くお勧めします。 

*settings* 要素には、拡張機能 (パブリック構成とも呼ばれます) からの設定および読み取りが可能な拡張機能の構成プロパティが含まれています。 *xmlcfg* プロパティには、診断ログの xml ベースの構成や、診断エージェントによって収集されるパフォーマンス カウンターなどが含まれます。 xml スキーマ自体の詳細については、「 [診断構成スキーマ](../../azure-monitor/agents/diagnostics-extension-schema-windows.md) 」を参照してください。 一般的な方法としては、Azure リソース マネージャー テンプレートに実際の xml 構成を変数として格納してから、これを連結および base64 エンコードして *xmlcfg* の値を設定します。 xml を変数として格納する方法の詳細については、「 [診断構成の変数](#diagnostics-configuration-variables) 」のセクションを参照してください。 *storageAccount* プロパティは、診断データの転送先となるストレージ アカウントの名前を指定します。 

*protectedSettings* (プライベート構成とも呼ばれる) のプロパティは設定できますが、設定後に読み取ることができません。 *protectedSettings* が書き込み専用であるという性質は、診断データが書き込まれるストレージ アカウント キーのような機密情報を格納する場合に役立ちます。    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>診断ストレージ アカウントをパラメーターとして指定する
上記の診断拡張機能の JSON スニペットは、診断データが格納される診断ストレージ アカウントを指定する 2 つのパラメーター *existingdiagnosticsStorageAccountName* および *existingdiagnosticsStorageResourceGroup* を想定しています。 診断ストレージ アカウントをパラメーターとして指定すると、たとえばテスト用と運用環境デプロイ用とで異なる診断ストレージ アカウントを使用する場合などに、異なる環境間で診断ストレージ アカウントを変更することが容易になります。  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

診断ストレージ アカウントは仮想マシンのリソース グループとは異なるリソース グループに指定することをお勧めします。 リソース グループは、独自の有効期間を持つデプロイメント単位と見なすことができます。新しい構成の更新により、仮想マシンはデプロイされたり、再デプロイされたりしますが、診断データを、仮想マシンの展開全体の同じストレージ アカウントに保存したい場合もあります。 異なるリソースにストレージ アカウントがあると、そのストレージ アカウントがさまざまな仮想マシンの展開からデータを受け入れられるようになり、異なるバージョンでの問題のトラブルシューティングが簡単になります。

> [!NOTE]
> Windows 仮想マシン テンプレートを Visual Studio から作成する場合、既定のストレージ アカウントが仮想マシン VHD のアップロード先と同じストレージ アカウントを使用するように設定できる場合があります。 これにより、VM の初期セットアップが簡素化します。 パラメーターとして渡すことができる別のストレージ アカウントを使用するように、テンプレートをリファクタリングしてください。 
> 
> 

## <a name="diagnostics-configuration-variables"></a>診断構成の変数
上記の診断の拡張機能の JSON スニペットは *accountid* 変数を定義して、診断ストレージのストレージ アカウント キーの取得を簡素化します。   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

診断の拡張機能の *xmlcfg* プロパティは、連結された複数の変数を使用して定義されます。 これらの変数の値は xml 形式であるため、JSON 変数を設定するときに正しくエスケープする必要があります。

以下の例では、Windows イベント ログおよび診断インフラストラクチャ ログと共に、標準のシステム レベルのパフォーマンス カウンターを収集する診断構成の xml について説明します。 構成をテンプレートの変数セクションに直接貼り付けることができるように、正しくエスケープされ、書式設定されています。 より人間が判読しやすい構成の xml の例については「 [診断構成スキーマ](../../azure-monitor/agents/diagnostics-extension-schema-windows.md) 」を参照してください。

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

上記の構成のメトリック定義の xml ノードは、xml の *PerformanceCounter* で先に定義されているパフォーマンス カウンターを集計して格納する方法を定義するため、重要な構成要素です。 

> [!IMPORTANT]
> これらのメトリックは、Azure Portal でのグラフやアラートの監視を促進します。  Azure Portal で VM 監視データを表示するには、*resourceID* および **MetricAggregation** のある **Metrics** ノードを VM の診断構成に含める必要があります。 
> 
> 

次の例では、メトリックの定義に関する xml を示します。 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

*resourceID* 属性はサブスクリプションで仮想マシンを一意に識別します。 テンプレートがデプロイしているサブスクリプションとリソース グループに基づいて値を自動的に更新するように、subscription() と resourceGroup() 関数を使用してください。

複数の仮想マシンをループ内に作成する場合は、個々の VM を正しく区別するために、copyIndex() 関数に *resourceID* 値を設定する必要があります。 これをサポートするには、 *xmlCfg* の値を次のように更新します。  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

*PT1M* と *PT1H* の MetricAggregation 値は、それぞれ 1 分間以上の集計と 1 時間以上の集計を表します。

## <a name="wadmetrics-tables-in-storage"></a>ストレージの WADMetrics テーブル
上記のメトリックの構成により、以下の名前付け規則を使用してテーブルが診断ストレージ アカウントに生成されます。

* **WADMetrics**: すべての WADMetrics テーブルの標準プレフィックス
* **PT1H** または **PT1M**: テーブルに 1 時間または 1 分間の集計データが含まれることを示します
* **P10D**: テーブルに、そのテーブルがデータの収集を開始してから 10 日間のデータが含まれることを示します
* **V2S**: 文字列定数
* **yyyymmdd**: テーブルがデータの収集を開始した日付

例:*WADMetricsPT1HP10DV2S20151108* には、2015 年 11 月 11 日からの 10 日間に 1 時間にわたって集計されたメトリック データが含まれます    

各 WADMetrics テーブルには次の列が含まれます。

* **PartitionKey**:パーティション キーは、VM リソースを一意に識別するように *resourceID* 値に基づいて構成されます。 例: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: `<Descending time tick>:<Performance Counter Name>` の形式に従います。 降順の時間ティック計算は、最大時間ティックから集計期間の開始時間を引いたものです。 たとえば、サンプル期間が 2015 年 11 月 10 日 00:00 (UTC) に開始された場合、計算は `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)` になります。 メモリで使用可能なバイト数のパフォーマンス カウンターの場合、行キーは `2519551871999999999__:005CMemory:005CAvailable:0020Bytes` のようになります。
* **CounterName**: パフォーマンス カウンターの名前です。 これは、xml 構成に定義されている *counterSpecifier* と一致します。
* **Maximum**: 集計期間中のパフォーマンス カウンターの最大値。
* **最小**:集計期間中のパフォーマンス カウンターの最小値。
* **Total**: 報告されたパフォーマンス カウンターのすべての値の合計。
* **Count**:パフォーマンス カウンターに関して報告された値の総数。
* **Average**: 集計期間中のパフォーマンス カウンターの平均 (合計/個数) 値。

## <a name="next-steps"></a>次の手順
* 診断の拡張機能を備えた Windows 仮想マシンの完全なサンプル テンプレートについては、「[201-vm-monitoring-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)」を参照してください   
* [Azure PowerShell](../windows/ps-template.md) または [Azure コマンド ライン](../linux/create-ssh-secured-vm-from-template.md)を使用した Azure Resource Manager テンプレートのデプロイ
* [Azure リソース マネージャーのテンプレートの作成](../../azure-resource-manager/templates/template-syntax.md)