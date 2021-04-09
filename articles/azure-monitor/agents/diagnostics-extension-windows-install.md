---
title: Windows Azure Diagnostics 拡張機能 (WAD) のインストールと構成
description: Windows 診断拡張機能のインストールと構成について説明します。 また、データの格納方法と Azure Storage アカウントについても説明します。
services: azure-monitor
author: bwren
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 174f372f9dbe8dc0449c7f9b9f5b34c6206f92de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708562"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Windows Azure Diagnostics 拡張機能 (WAD) のインストールと構成
[Azure Diagnostics 拡張機能](diagnostics-extension-overview.md)は Azure Monitor のエージェントで、ゲスト オペレーティング システムと Azure 仮想マシンと他のコンピューティング リソースのワークロードから監視データを収集します。 この記事では、Windows 診断拡張機能のインストールと構成の詳細と、Azure ストレージ アカウントでデータを保存する方法について説明します。

診断拡張機能は、Azure では[仮想マシン拡張機能](../../virtual-machines/extensions/overview.md)として実装されているため、Resource Manager テンプレート、PowerShell、および CLI を使用した場合と同じインストール オプションがサポートされています。 仮想マシン拡張機能のインストールと保守の詳細については、[Windows 用の仮想マシン拡張機能と機能](../../virtual-machines/extensions/features-windows.md)に関する記事を参照してください。

## <a name="overview"></a>概要
Windows Azure の診断拡張機能を構成するときに、指定したすべてのデータが送信されるストレージ アカウントを指定する必要があります。 必要に応じて、1 つ以上の "*データ シンク*" を追加して、別の場所にデータを送信することもできます。

- Azure Monitor シンク - ゲスト パフォーマンス データを Azure Monitor メトリックに送信します。
- イベント ハブ シンク - Azure の外部に転送するために、ゲスト パフォーマンスおよびログ データを Azure Event Hubs に送信します。 このシンクは、Azure portal では構成できません。


## <a name="install-with-azure-portal"></a>Azure portal を使用してインストールする
Azure portal で、個々の仮想マシンに診断拡張機能をインストールし、構成することができます。構成を直接操作するのではなく、インターフェイスが用意されています。 診断拡張機能を有効にすると、最も一般的なパフォーマンス カウンターとイベントを備えた既定の構成が自動的に使用されます。 特定の要件に応じて、この既定構成を変更できます。

> [!NOTE]
> 次に、診断拡張機能の最も一般的な設定について説明します。 すべての構成オプションの詳細については、「[Windows Diagnostics 拡張機能のスキーマ](diagnostics-extension-schema-windows.md)」をご覧ください。

1. Azure portal で仮想マシンのメニューを開きます。

2. VM メニューの **[監視]** セクションで、 **[診断設定]** をクリックします。

3. 診断拡張機能がまだ有効になっていない場合は、 **[ゲスト レベルの監視を有効にする]** をクリックします。

   ![監視を有効にする](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. VM のリソース グループの名前に基づいた名前を持つ VM 用の新しい Azure Storage アカウントが作成され、既定のゲスト パフォーマンス カウンターおよびログのセットが選択されます。

   ![診断設定](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. **[パフォーマンス カウンター]** タブで、この仮想マシンから収集するゲスト メトリックを選択します。 詳細な選択を行うには、 **[カスタム]** 設定を使用します。

   ![パフォーマンス カウンター](media/diagnostics-extension-windows-install/performance-counters.png)

6. **[ログ]** タブで、仮想マシンから収集するログを選択します。 ログはストレージまたはイベント ハブに送信できますが、Azure Monitor には送信できません。 [Log Analytics エージェント](../agents/log-analytics-agent.md)を使用して、Azure Monitor にゲスト ログを収集します。

   ![スクリーンショットには、仮想マシンに対して異なるログが選択された [ログ] タブが示されています。](media/diagnostics-extension-windows-install/logs.png)

7. **[クラッシュ ダンプ]** タブで、クラッシュ後にメモリ ダンプを収集するプロセスを指定します。 データは、診断設定用にストレージ アカウントに書き込まれ、必要に応じて BLOB コンテナーを指定できます。

   ![クラッシュ ダンプ](media/diagnostics-extension-windows-install/crash-dumps.png)

8. **[シンク]** タブで、Azure Storage 以外の場所にデータを送信するかどうかを指定します。 **[Azure Monitor]** を選択した場合、ゲスト パフォーマンス データは、Azure Monitor メトリックに送信されます。 Azure portal を使用してイベント ハブ シンクを構成することはできません。

   ![スクリーンショットには、[Send diagnostic data to Azure Monitor]\(Azure Monitor への診断データの送信\) オプションが [有効] になった [シンク] タブが示されています。](media/diagnostics-extension-windows-install/sinks.png)
   
   仮想マシン用に構成されたシステム割り当て ID を有効にしていない場合、Azure Monitor シンクで構成を保存したときに、下の警告が表示されることがあります。 バナーをクリックして、システム割り当て ID を有効にします。
   
   ![マネージド エンティティ](media/diagnostics-extension-windows-install/managed-entity.png)

9. **エージェント** では、ストレージ アカウントを変更し、ディスク クォータを設定し、診断インフラストラクチャ ログを収集するかどうかを指定できます。  

   ![スクリーンショットには、ストレージ アカウントを設定するオプションを備えた [エージェント] タブが示されています。](media/diagnostics-extension-windows-install/agent.png)

10. **[保存]** をクリックして構成を保存します。 

> [!NOTE]
> 診断拡張機能の構成は、JSON と XML のどちらの形式にすることもできますが、Azure portal で実行される構成は常に JSON 形式で保存されます。 別の構成方法で XML を使用していて、Azure portal で構成を変更した場合、設定は JSON に変更されます。

## <a name="resource-manager-template"></a>Resource Manager テンプレート
Azure Resource Manager テンプレートを使用した診断拡張機能のデプロイについては、「[Windows VM と Azure Resource Manager テンプレートで監視と診断を利用する](../../virtual-machines/extensions/diagnostics-template.md)」を参照してください。 

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ
次の例のように、Azure CLI を使用して、[az vm extension set](/cli/azure/vm/extension#az-vm-extension-set) を使用して Azure Diagnostics 拡張機能を既存の仮想マシンにデプロイできます。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

保護された設定は、構成スキーマの [PrivateConfig 要素](diagnostics-extension-schema-windows.md#privateconfig-element)で定義されます。 ストレージ アカウントを定義する保護された設定ファイルの最小限の例を次に示します。 プライベート設定の詳細については、[構成例](diagnostics-extension-schema-windows.md#privateconfig-element)に関する記事を参照してください。

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

パブリック設定は、構成スキーマの [Public 要素](diagnostics-extension-schema-windows.md#publicconfig-element)で定義されます。 診断インフラストラクチャのログ、1 つのパフォーマンス カウンター、および 1 つのイベント ログの収集を有効にするパブリック設定ファイルの最小限の例を次に示します。 パブリック設定の詳細については、[構成例](diagnostics-extension-schema-windows.md#publicconfig-element)に関する記事を参照してください。

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell でのデプロイ
次の例のように、PowerShell を使用し、[Set-AzVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) を使用して、Azure Diagnostics 拡張機能を既存の仮想マシンにデプロイできます。 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

プライベート設定は [PrivateConfig 要素](diagnostics-extension-schema-windows.md#privateconfig-element)で定義され、パブリック設定は構成スキーマの [Public 要素](diagnostics-extension-schema-windows.md#publicconfig-element)で定義されます。 また、ストレージ アカウントの詳細をプライベート設定に含めるのではなく、Set-AzVMDiagnosticsExtension コマンドレットのパラメーターとして指定することもできます。

診断インフラストラクチャのログ、1 つのパフォーマンス カウンター、および 1 つのイベント ログの収集を有効にする構成ファイルの最小限の例を次に示します。 プライベートおよびパブリックの設定の詳細については、[構成例](diagnostics-extension-schema-windows.md#publicconfig-element)に関する記事を参照してください。 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
                "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
                },
                "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "PerformanceCounterConfiguration": [
                        {
                            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                            "sampleRate": "PT3M",
                            "unit": "percent"
                        }
                    ]
                },
                "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                        "DataSource": [
                        {
                            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                        }
                    ]
                }
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

また、「[PowerShell を使用して Windows を実行している仮想マシンで Azure Diagnostics を有効にする](../../virtual-machines/extensions/diagnostics-windows.md)」も参照してください。

## <a name="data-storage"></a>データ ストレージ
次の表は、診断拡張機能から収集されるさまざまな種類のデータと、それらがテーブルまたは BLOB のどちらとして格納されているかを示しています。 テーブルに格納されているデータは、パブリック構成の [StorageType 設定](diagnostics-extension-schema-windows.md#publicconfig-element)に応じて BLOB に保存することもできます。


| Data | ストレージの種類 | 説明 |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | テーブル | 診断モニターと構成の変更。 |
| WADDirectoriesTable | テーブル | 診断モニターで監視されているディレクトリ。  これには、IIS ログ、IIS 失敗要求ログ、およびカスタム ディレクトリが含まれます。  BLOB ログ ファイルの場所は Container フィールドで指定され、BLOB の名前は RelativePath フィールドで指定されます。  AbsolutePath フィールドでは、Azure 仮想マシンに存在するファイルの場所と名前を示します。 |
| WadLogsTable | テーブル | トレース リスナーを使用してコードに書き込まれたログ。 |
| WADPerformanceCountersTable | テーブル | パフォーマンス カウンター。 |
| WADWindowsEventLogsTable | テーブル | Windows イベント ログ。 |
| wad-iis-failedreqlogfiles | BLOB | IIS の失敗した要求ログの情報が含まれています。 |
| wad-iis-logfiles | BLOB | IIS ログに関する情報が含まれています。 |
| "custom" | BLOB | 診断モニターによって監視されるディレクトリの構成に基づくカスタム コンテナーです。  この BLOB コンテナーの名前は WADDirectoriesTable で指定されます。 |

## <a name="tools-to-view-diagnostic-data"></a>診断データを表示するツール
ストレージへの転送後にデータを表示するには、いくつかのツールを利用できます。 次に例を示します。

* Visual Studio のサーバー エクスプローラー - Azure Tools for Microsoft Visual Studio がインストールされている場合、サーバー エクスプローラーの Azure Storage ノードを使用して、Azure ストレージ アカウントの読み取り専用の BLOB およびテーブル データを表示できます。 データは、ローカルのストレージ エミュレーター アカウントから表示できます。また、Azure 用に作成したストレージ アカウントから表示することもできます。 詳細については、「[サーバー エクスプローラーを使用したストレージ リソースの参照と管理](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)」を参照してください。
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) は、Windows、OSX、Linux で Azure Storage データを容易に操作できるスタンドアロン アプリです。
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) に含まれている Azure Diagnostics Manager では、Azure で実行されているアプリケーションによって収集された診断データの表示、ダウンロード、管理を行うことができます。

## <a name="next-steps"></a>次のステップ
- Azure Event Hubs への監視データの転送の詳細については、[Windows Azure Diagnostics 拡張機能から Event Hubs へのデータ送信](diagnostics-extension-stream-event-hubs.md)に関する記事を参照してください。
