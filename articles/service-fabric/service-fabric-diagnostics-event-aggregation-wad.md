---
title: Windows Azure Diagnostics を使用したイベントの集計
description: Azure Service Fabric クラスターの監視と診断に WAD を使用したイベントの集計と収集について説明します。
author: srrengar
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: eaf40bafb68820b56fc9bf00dd4c11fbb337406f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613461"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Windows Azure Diagnostics を使用したイベントの集計と収集
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Azure Service Fabric クラスターを実行している場合、1 か所ですべてのノードのログを収集することをお勧めします。 1 か所でログを収集すると、クラスター内の問題と、そのクラスターで実行されているアプリケーションやサービスで発生する問題の分析と解決に役立ちます。

ログをアップロードして収集する 1 つの方法として、Windows Azure Diagnostics (WAD) 拡張機能を使用します。この機能を使用すると、ログが Azure Storage にアップロードされますが、Azure Application Insights や Event Hubs にログを送信することもできます。 また、外部プロセスを使用してストレージからイベントを読み取り、[Azure Monitor ログ](./service-fabric-diagnostics-oms-setup.md)などの分析プラットフォーム製品や別のログ解析ソリューションに配置することもできます。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件
この記事では、次のツールが使用されます。

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/)
* [Azure Resource Manager テンプレート](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)

## <a name="service-fabric-platform-events"></a>Service Fabric プラットフォームのイベント
Service Fabric には[すぐに使用できるログ チャネル](service-fabric-diagnostics-event-generation-infra.md)がいくつか用意されています。そのうち、次のチャネルは、監視および診断データをストレージ テーブルや他の場所に送信する拡張機能が事前に構成されています。
  * [操作イベント](service-fabric-diagnostics-event-generation-operational.md): Service Fabric プラットフォームが実行する高度なレベルの操作です。 たとえば、アプリケーションとサービスの作成、ノードの状態変化、アップグレード情報などです。 これらは Event Tracing for Windows (ETW) ログとして出力されます。
  * [Reliable Actors プログラミング モデル イベント](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services プログラミング モデル イベント](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>ポータルを使用して診断拡張機能をデプロイする
ログ収集の最初の手順は、Service Fabric クラスター内の仮想マシン スケール セット ノード上に診断拡張機能をデプロイすることです。 診断拡張機能を使用すると、各 VM のログが収集され、指定したストレージ アカウントにアップロードされます。 以下の手順は、Azure Portal と Azure Resource Manager テンプレートを使用して、新規と既存のクラスターでこれを行う方法を示しています。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Azure Portal を使用してクラスター作成の一環として診断拡張機能をデプロイする
クラスター構成手順の中でクラスターを作成する場合は、省略可能な設定を展開し、診断が **[オン]** (既定の設定) に設定されていることを確認します。

![ポータルでのクラスター作成のための Azure Diagnostics 設定](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

最後の手順で **[作成] をクリックする前に** テンプレートをダウンロードしておくことを強くお勧めします。 詳細については、[Azure Resource Manager テンプレートを使用して Service Fabric クラスターをセットアップする方法](service-fabric-cluster-creation-via-arm.md)に関するページを参照してください。 データを収集するチャンネル (上記) を変更するためにテンプレートが必要です。

![クラスター テンプレート](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Azure Storage にイベントを集計するため、Azure Monitor ログ ポータルで、分析情報の取得とクエリを実行するように [Azure Monitor ログを設定](service-fabric-diagnostics-oms-setup.md)します。

>[!NOTE]
>現在のところ、テーブルに送信されるイベントをフィルター処理したり調整したりする方法はありません。 テーブルからイベントを削除するプロセスを実装しない場合、テーブルは増加し続けます (既定の上限は 50 GB です)。 これを変更する方法については、[この記事の中で後で説明します](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota)。 さらに、[ウォッチドッグ サンプル](https://github.com/Azure-Samples/service-fabric-watchdog-service)で実行されるデータ グルーミング サービスの例があります。30 日または 90 日の期間を超えてログを保存する正当な理由がない限り、データ グルーミング サービスを自分で作成することをお勧めします。



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Azure Resource Manager を使用して診断拡張機能をデプロイする

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>診断拡張機能付きのクラスターを作成する
Resource Manager を使用してクラスターを作成するには、診断構成 JSON を完全なクラスターの Resource Manager テンプレートに追加する必要があります。 ここでは、リソース マネージャー テンプレート サンプルの一部として、診断構成を追加した five-VM クラスター リソース マネージャー テンプレートのサンプルを用意しました。 このサンプルは、Azure サンプル ギャラリーの「 [Five-node cluster with Diagnostics Resource Manager template sample](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/)」で参照できます。

Resource Manager テンプレートの診断設定を確認するには、azuredeploy.json ファイルを開き、**IaaSDiagnostics** を検索します。 このテンプレートを使用してクラスターを作成するには、前のリンクにある **[Azure にデプロイ]** ボタンをクリックしてください。

または、Resource Manager サンプルをダウンロードし、変更を加え、Azure PowerShell ウィンドウで `New-AzResourceGroupDeployment` コマンドを使用して、変更したテンプレートでクラスターを作成する方法もあります。 コマンドに渡すパラメーターについては、次のコードを参照してください。 PowerShell を利用してリソース グループをデプロイする方法については、[Azure Resource Manager テンプレートを使用したリソース グループのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)に関する記事を参照してください。

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>既存のクラスターに診断拡張機能を追加する
まだ診断がデプロイされていない既存のクラスターがある場合は、クラスター テンプレートを使用して追加または更新を実行できます。 既存クラスターの作成に使用された Resource Manager テンプレートを変更するか、前の説明に基づき、ポータルからテンプレートをダウンロードします。 次のタスクを実行して、template.json ファイルを変更します。

リソース セクションを増やすことで新しいストレージ リソースをテンプレートに追加します。

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 次に、ストレージ アカウント定義の直後の `supportLogStorageAccountName` との間に、パラメーター セクションを追加します。 プレースホルダー テキストの *storage account name goes here* をストレージ アカウントの名前に置き換えます。

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
extensions 配列内に次のコードを追加し、 template.json ファイルの `VirtualMachineProfile` セクションを更新します。 挿入箇所に合わせ、始めまたは終わりにコンマを追加します。

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
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
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

前述のように template.json ファイルを変更したら、Resource Manager テンプレートを再発行します。 テンプレートのエクスポート後、deploy.ps1 ファイルを実行すると、テンプレートが再発行されます。 デプロイ後、**ProvisioningState** が **Succeeded** になっていることを確認します。

> [!TIP]
> 使用しているクラスターにコンテナーをデプロイする場合は、WAD を有効にして、ご自身の **WadCfg > DiagnosticMonitorConfiguration** セクションに次のコードを追加し、Docker の統計情報を取得します。

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},
```

### <a name="update-storage-quota"></a>ストレージ クォータを更新する

拡張機能によってデータが入力されるテーブルは、クォータに達するまで大きくなり続けるため、クォータ サイズの縮小を検討できます。 既定値は 50 GB です。この値は、テンプレートの `DiagnosticMonitorConfiguration` の下の `overallQuotaInMB` フィールドで構成可能です。

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>ログ収集の構成
追加チャネルのログも収集できます。Azure で実行されているクラスターを対象とした、テンプレートで作成できる最も一般的な構成をいくつか次に示します。

* 稼働チャネル - 基本: 既定で有効。Service Fabric とクラスターで実行される高度な操作。ノードの起動、新しいアプリケーションのデプロイ、アップグレードのロールバックなどのイベントが含まれます。イベントの一覧については、[稼働チャネル イベント](./service-fabric-diagnostics-event-generation-operational.md)に関するページをご覧ください。
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* 稼働チャネル - 詳細: 正常性レポートと負荷分散の決定、および基本稼働チャネルのすべてが含まれます。 これらのイベントは、[ReportPartitionHealth](/previous-versions/azure/reference/mt645153(v=azure.100))、[ReportLoad](/previous-versions/azure/reference/mt161491(v=azure.100)) などの正常性または負荷レポート API を使うことで、システムまたはユーザーのコードのいずれかによって生成されます。 Visual Studio の診断イベント ビューアーでこれらのイベントを表示するには、ETW プロバイダーのリストに "Microsoft-ServiceFabric:4:0x4000000000000008" を追加します。

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* データおよびメッセージング チャネル - 基本: 詳細な稼働チャネル ログに加えて、メッセージング (現時点では ReverseProxy のみ) とデータ パスで生成された重要なログおよびイベント。 これらのイベントには、ReverseProxy で発生した要求処理エラーや他の重要な問題に加え、処理された要求も含まれます。 **包括的なログ記録のための推奨構成です**。 Visual Studio の診断イベント ビューアーでこれらのイベントを表示するには、ETW プロバイダーのリストに "Microsoft-ServiceFabric:4:0x4000000000000010" を追加します。

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* データおよびメッセージング チャネル - 詳細: クラスター内のデータおよびメッセージングからの重大でないすべてのログを含む詳細チャネル、および詳細稼働チャネル。 すべてのリバース プロキシ イベントの詳細なトラブルシューティングについては、[リバース プロキシの診断ガイド](service-fabric-reverse-proxy-diagnostics.md)を参照してください。  Visual Studio の診断イベント ビューアーでこれらのイベントを表示するには、ETW プロバイダーのリストに "Microsoft-ServiceFabric:4:0x4000000000000020" を追加します。

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>このチャネルには大量のイベントが含まれているため、この詳細チャネルからのイベント収集を有効にすると、大量のトレースが短時間で生成され、ストレージ容量を消費する可能性があります。 これを有効にするのは、どうしても必要な場合だけにしてください。


最小限のノイズで包括的なログを記録するための推奨構成である **基本的な稼働チャネル** を有効にするには、テンプレートの `WadCfg` の `EtwManifestProviderConfiguration` は次のようになります。

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
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>新しい EventSource チャネルから収集する

デプロイしようとしている新しいアプリケーションを表す新しい EventSource チャネルからログを収集するように診断を更新するには、前述の既存のクラスターの診断を設定する場合と同じ手順を実行します。

template.json ファイル内の `EtwEventSourceProviderConfiguration` セクションを更新して、新しい EventSource チャネル用のエントリを追加してから、`New-AzResourceGroupDeployment` PowerShell コマンドを使用して構成の更新を適用します。 イベント ソースの名前は、Visual Studio で生成された ServiceEventSource.cs ファイル内でコードの一部として定義されます。

たとえば、イベント ソースの名前が My-Eventsource である場合、My-Eventsource からのイベントを MyDestinationTableName という名前のテーブルに配置するには次のコードを追加します。

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

パフォーマンス カウンターまたはイベント ログを収集するには、「[Azure リソース マネージャー テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)」に記載されている例を使用して Resource Manager テンプレートを変更します。 その後、Resource Manager テンプレートを再発行します。

## <a name="collect-performance-counters"></a>パフォーマンス カウンターを収集する

クラスターからパフォーマンス メトリックを収集するには、クラスターの Resource Manager テンプレートの "WadCfg > DiagnosticMonitorConfiguration" にパフォーマンス カウンターを追加します。 `WadCfg` を変更して特定のパフォーマンス カウンターを収集する手順については、[WAD を使用したパフォーマンスの監視](service-fabric-diagnostics-perf-wad.md)に関するページを参照してください。 収集が推奨されるパフォーマンス カウンターの一覧については、[Service Fabric のパフォーマンス カウンター](service-fabric-diagnostics-event-generation-perf.md)に関するページを参照してください。
  
次のセクションで説明するように、Application Insights のシンクを使用しており、これらのメトリックを Application Insights に表示する場合は、上記の "sinks" セクションにシンク名を追加する必要があります。 これにより、Application Insights リソースに個別に構成されているパフォーマンス カウンターが自動的に送信されます。


## <a name="send-logs-to-application-insights"></a>ログを Application Insights に送信する

### <a name="configuring-application-insights-with-wad"></a>WAD を使用した Application Insights の構成

>[!NOTE]
>これは現在、Windows クラスターにのみ当てはまります。

WAD から Azure Application Insights にデータを送信するには Application Insights シンクを WAD 構成に追加しますが、これには主に Azure portal から行う方法と Azure Resource Manager テンプレートから行う方法があります。

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Azure portal でクラスターを作成するときに、Application Insights のインストルメンテーション キーを追加する

![AIKey の追加](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

クラスターを作成するときに、診断が "オン" になっている場合、Application Insights のインストルメンテーション キーを入力するオプションのフィールドが表示されます。 ここに Application Insights のキーを貼り付けると、クラスターの展開に使用される Resource Manager テンプレートで、Application Insights シンクが自動的に構成されます。

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Resource Manager テンプレートに Application Insights シンクを追加する

Resource Manager テンプレートの "WadCfg" に、次の 2 つの変更を含めることによって "シンク" を追加します。

1. `DiagnosticMonitorConfiguration` の宣言の完了直後にシンク構成を追加します。

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. `DiagnosticMonitorConfiguration` にシンクを含めます。`WadCfg` の `DiagnosticMonitorConfiguration` に次の行を追加します (`EtwProviders` の宣言の直前に)。

    ```json
    "sinks": "applicationInsights"
    ```

上記の両方のコード スニペットには、シンクを記述するために "applicationInsights" という名前が使用されていました。 これは要件ではなく、シンクの名前が "sinks" に含まれている限り、任意の文字列で名前を設定できます。

現時点では、クラスターのログは Application Insights のログ ビューアーに **トレース** として表示されます。 プラットフォームからのトレースのほとんどは "情報" レベルであるため、シンクの構成を変更して "警告" または "エラー" タイプのログのみを送信することも検討できます。 これは、[この記事](../azure-monitor/agents/diagnostics-extension-to-application-insights.md)で説明するように、シンクに "チャネル" を追加することで実現できます。

>[!NOTE]
>ポータルまたは Resource Manager テンプレートのいずれかで間違った Application Insights キーを使用した場合、手動でキーを変更してクラスターを更新するか、再デプロイする必要があります。

## <a name="next-steps"></a>次のステップ

Azure Diagnostics を正しく構成すると、ETW ログと EventSource ログのデータがストレージ テーブルに表示されます。 Azure Monitor ログ、Kibana、または Resource Manager テンプレートで直接構成されていないその他のデータ分析および視覚化プラットフォームを使用する場合は、これらのストレージ テーブルからデータを読み取るように、選択したプラットフォームを設定する必要があります。 Azure Monitor ログでこれを行うのは比較的簡単です。方法については、[イベントとログの分析](service-fabric-diagnostics-event-analysis-oms.md)に関する記事を参照してください。 Application Insights は、診断拡張機能の構成の一部として構成できるので、少し特殊と言えます。AI を使用する場合は、[こちらの記事](service-fabric-diagnostics-event-analysis-appinsights.md)をご覧ください。

>[!NOTE]
>現在のところ、テーブルに送信されるイベントを絞り込む方法はありません。 テーブルからイベントを削除するプロセスを実装しない場合、テーブルは増加を続けます。 現在、[ウォッチドッグ サンプル](https://github.com/Azure-Samples/service-fabric-watchdog-service)で実行されるデータ グルーミング サービスの例があります。30 日または 90 日の期間を超えてログを保存する正当な理由がない限り、データ グルーミング サービスを自分で作成することをお勧めします。

* [診断拡張機能を使用してパフォーマンス カウンターまたはログを収集する方法についての説明](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)
* [Application Insights を使用したイベントの分析と視覚化](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Azure Monitor ログを使用したイベントの分析と視覚化](service-fabric-diagnostics-event-analysis-oms.md)
* [Application Insights を使用したイベントの分析と視覚化](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Azure Monitor ログを使用したイベントの分析と視覚化](service-fabric-diagnostics-event-analysis-oms.md)
