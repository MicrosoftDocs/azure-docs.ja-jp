---
title: "Windows Azure 診断を使用した Azure Service Fabric のイベントの集計 | Microsoft Docs"
description: "Azure Service Fabric クラスターの監視と診断に WAD を使用したイベントの集計と収集について説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: cea811918147a25947ec654bb06f2c994bae5ce6
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---

# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Windows Azure 診断を使用したイベントの集計と収集
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Azure Service Fabric クラスターを実行している場合、1 か所ですべてのノードのログを収集することをお勧めします。 1 か所でログを収集すると、クラスター内の問題と、そのクラスターで実行されているアプリケーションやサービスで発生する問題の分析と解決に役立ちます。

ログをアップロードして収集する 1 つの方法として、Windows Azure 診断 (WAD) 拡張機能を使用します。この機能を使用すると、ログが Azure Storage にアップロードされますが、Azure Application Insights や Event Hubs にログを送信することもできます。 また、外部プロセスを使用してストレージからイベントを読み取り、[OMS Log Analytics](../log-analytics/log-analytics-service-fabric.md) などの分析プラットフォーム製品や別のログ解析ソリューションに配置することもできます。

## <a name="prerequisites"></a>前提条件
このドキュメントの一部の操作は、次のツールを使用して実行されます。

* [Azure 診断](../cloud-services/cloud-services-dotnet-diagnostics.md) (Azure Cloud Services と関連性はありますが、お勧めの情報と例が掲載されています)
* [Azure リソース マネージャー](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager クライアント](https://github.com/projectkudu/ARMClient)
* [Azure Resource Manager テンプレート](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>ログとイベントのソース

### <a name="service-fabric-platform-events"></a>Service Fabric プラットフォームのイベント
[こちらの記事](service-fabric-diagnostics-event-generation-infra.md)で説明するように、Service Fabric にはすぐに使用できるログ チャネルがいくつか用意されています。そのうち、次のチャネルは、監視および診断データをストレージ テーブルや他の場所に送信するように WAD で簡単に構成できます。
  * 操作イベント: Service Fabric プラットフォームで実行される高度な操作です。 たとえば、アプリケーションとサービスの作成、ノードの状態変化、アップグレード情報などです。 これらは Event Tracing for Windows (ETW) ログとして出力されます。
  * [Reliable Actors プログラミング モデル イベント](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services プログラミング モデル イベント](service-fabric-reliable-services-diagnostics.md)

### <a name="application-events"></a>アプリケーション イベント
 アプリケーションやサービスのコードから生成され、Visual Studio テンプレートで指定された EventSource ヘルパー クラスを使用して出力されるイベントです。 アプリケーションから EventSource ログを出力する方法の詳細については、「[ローカル コンピューターの開発のセットアップでのサービスの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)」をご覧ください。

## <a name="deploy-the-diagnostics-extension"></a>診断拡張機能のデプロイ
ログ収集の最初の手順は、Service Fabric クラスター内の各 VM に診断拡張機能をデプロイすることです。 診断拡張機能を使用すると、各 VM のログが収集され、指定したストレージ アカウントにアップロードされます。 手順は、Azure Portal と Azure Resource Manager のどちらを使用するかに応じて少し変わります。 手順は、デプロイがクラスター作成の一環であるか、または既に存在しているクラスターに関するものであるかによっても変わります。 各シナリオの手順を見てみましょう。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Azure Portal を使用してクラスター作成の一環として診断拡張機能をデプロイする
クラスター作成の一環としてクラスター内の VM に診断拡張機能をデプロイするには、次の図に示す [診断設定] パネルを使用します。[診断] が **[オン]** (既定の設定) に設定されていることを確認してください。 クラスターを作成した後、ポータルを使用してこれらの設定を変更することはできません。

![ポータルでのクラスター作成のための Azure 診断設定](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

ポータルを使ってクラスターを作成する場合、**[OK] をクリックしてクラスターを作成する前に**テンプレートをダウンロードすることを強くお勧めします。 詳細については、[Azure Resource Manager テンプレートを使用して Service Fabric クラスターをセットアップする方法](service-fabric-cluster-creation-via-arm.md)に関するページを参照してください。 いくつかの変更はポータルを使用して行うことができないため、後で変更を行うためにこのテンプレートが必要になります。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Azure リソース マネージャー を使用してクラスター作成の一環で診断拡張機能をデプロイする
Resource Manager を使用してクラスターを作成するには、診断の構成 JSON を完全なクラスターの Resource Manager テンプレートに追加してから、クラスターを作成します。 ここでは、リソース マネージャー テンプレート サンプルの一部として、診断構成を追加した five-VM クラスター リソース マネージャー テンプレートのサンプルを用意しました。 このサンプルは、Azure サンプル ギャラリーの「 [Five-node cluster with Diagnostics Resource Manager template sample](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)」で参照できます。

Resource Manager テンプレートの診断設定を確認するには、azuredeploy.json ファイルを開き、**IaaSDiagnostics** を検索します。 このテンプレートを使用してクラスターを作成するには、前のリンクにある **[Azure にデプロイ]** ボタンをクリックしてください。

または、Resource Manager サンプルをダウンロードし、変更を加え、Azure PowerShell ウィンドウで `New-AzureRmResourceGroupDeployment` コマンドを使用して、変更したテンプレートでクラスターを作成する方法もあります。 コマンドに渡すパラメーターについては、次のコードを参照してください。 PowerShell を利用してリソース グループをデプロイする方法については、[Azure Resource Manager テンプレートを使用したリソース グループのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)に関する記事を参照してください。

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>既存のクラスターに診断拡張機能をデプロイする
まだ診断がデプロイされていない既存のクラスターがある場合、または既存の構成を変更する場合、診断を追加または更新できます。 既存クラスターの作成に使用された Resource Manager テンプレートを変更するか、前の説明に基づき、ポータルからテンプレートをダウンロードします。 次のタスクを実行し、template.json ファイルを変更します。

リソース セクションを増やすことで新しいストレージ リソースをテンプレートに追加します。

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 次に、ストレージ アカウント定義の直後の、`supportLogStorageAccountName` と `vmNodeType0Name` の間でパラメーター セクションを増やします。 プレースホルダー テキストの *storage account name goes here* をストレージ アカウントの名前に置き換えます。

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
      "defaultValue": "storage account name goes here",
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

## <a name="collect-health-and-load-events"></a>正常性と負荷のイベントを収集する

Service Fabric リリース 5.4 以降、正常性と負荷のメトリック イベントを収集できるようになりました。 これらのイベントは、[ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) や [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx) などの正常性または負荷レポート API を使うことでシステムやユーザーのコードによって生成されるイベントを反映します。 これにより、一定期間のシステムの正常性を集計および表示したり、正常性または負荷のイベントに基づいてアラートを生成したりできます。 Visual Studio の診断イベント ビューアーでこれらのイベントを表示するには、ETW プロバイダーのリストに "Microsoft-ServiceFabric:4:0x4000000000000008" を追加します。

イベントを収集するには、Resource Manager テンプレートに次の行を追加します。

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387912",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

## <a name="collect-reverse-proxy-events"></a>リバース プロキシ イベントの収集

Service Fabric リリース 5.7 以降、[リバース プロキシ](service-fabric-reverseproxy.md) イベントを収集できるようになりました。
リバース プロキシは 2 つのチャネルに対してイベントを生成します。1 つは要求処理エラーを反映するエラー イベントを含むチャネル、もう 1 つはリバース プロキシで処理されるすべての要求に関する詳細イベントを含むチャネルです。 

1. エラー イベントの収集: Visual Studio の診断イベント ビューアーでこれらのイベントを表示するには、ETW プロバイダーのリストに "Microsoft-ServiceFabric:4:0x4000000000000010" を追加します。
Azure クラスターからイベントを収集するには、Resource Manager テンプレートに次の行を追加します。

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387920",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

2. すべての要求処理イベントの収集: Visual Studio の診断イベント ビューアーで、ETW プロバイダーのリストの Microsoft-ServiceFabric エントリを "Microsoft-ServiceFabric:4:0x4000000000000020" に変更します。
Azure Service Fabric クラスターの場合、Resource Manager テンプレートを変更して以下の内容を含めます。

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387936",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```
> このチャネルはリバース プロキシ経由のすべてのトラフィックを収集し、ストレージ容量を短時間で消費する可能性があるため、このチャネルから生成されるイベントの収集を有効にする場合は慎重に検討することをお勧めします。

Azure Service Fabric クラスターの場合、すべてのノードから生成されたイベントが収集され、SystemEventTable に集計されます。
リバース プロキシ イベントのトラブルシューティングの詳細については、[リバース プロキシの診断ガイド](service-fabric-reverse-proxy-diagnostics.md)を参照してください。

## <a name="collect-from-new-eventsource-channels"></a>新しい EventSource チャネルから収集する

デプロイしようとしている新しいアプリケーションを表す新しい EventSource チャネルからログを収集するように診断を更新するには、前述の既存のクラスターの診断を設定する場合と同じ手順を実行します。

template.json ファイル内の `EtwEventSourceProviderConfiguration` セクションを更新して、新しい EventSource チャネル用のエントリを追加してから、`New-AzureRmResourceGroupDeployment` PowerShell コマンドを使用して構成の更新を適用します。 イベント ソースの名前は、Visual Studio で生成された ServiceEventSource.cs ファイル内でコードの一部として定義されます。

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

パフォーマンス カウンターまたはイベント ログを収集するには、「[Azure リソース マネージャー テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」に記載されている例を使用して Resource Manager テンプレートを変更します。 その後、Resource Manager テンプレートを再発行します。

## <a name="collect-performance-counters"></a>パフォーマンス カウンターを収集する

クラスターからパフォーマンス メトリックを収集するには、クラスターの Resource Manager テンプレートの "WadCfg > DiagnosticMonitorConfiguration" にパフォーマンス カウンターを追加します。 収集することが推奨されるパフォーマンス カウンターについては、[Service Fabric のパフォーマンス カウンター](service-fabric-diagnostics-event-generation-perf.md)に関する記事をご覧ください。

たとえば、ここでは 15 秒ごとにサンプリングされ、1 分ごとに適切なストレージ テーブルに転送されるパフォーマンス カウンターを 1 つ設定しています (サンプリング間隔は変更できます。"PT\<時間>\<単位>" の形式に従います。たとえば、PT3M の場合、3 分間隔でサンプリングされます)。

  ```json
  "PerformanceCounters": {
      "scheduledTransferPeriod": "PT1M",
      "PerformanceCounterConfiguration": [
          {
              "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
              "sampleRate": "PT15S",
              "unit": "Percent",
              "annotation": [
              ],
              "sinks": ""
          }
      ]
  }
  ```
  
次のセクションで説明するように、Application Insights のシンクを使用しており、これらのメトリックを Application Insights に表示する場合は、上記の "sinks" セクションにシンク名を追加する必要があります。 さらに、パフォーマンス カウンターによって、有効にしている他のログ チャネルからのデータが押し出されないように、パフォーマンス カウンターの送信先として別のテーブルを作成することを検討してください。


## <a name="send-logs-to-application-insights"></a>ログを Application Insights に送信する

Application Insights (AI) への監視および診断データの送信は、WAD の構成の一部として実行できます。 イベントの分析と視覚化に AI を使用する場合は、「[Event Analysis and Visualization with Application Insights (Application Insights を使用したイベントの分析と視覚化)](service-fabric-diagnostics-event-analysis-appinsights.md)」を参照して、"WadCfg" の一部として AI のシンクを設定してください。

## <a name="next-steps"></a>次のステップ

Azure 診断を正しく構成すると、ETW ログと EventSource ログのデータがストレージ テーブルに表示されます。 OMS、Kibana、または Resource Manager テンプレートで直接構成されていないその他のデータ分析および視覚化プラットフォームを使用する場合は、これらのストレージ テーブルからデータを読み取るように、選択したプラットフォームを設定する必要があります。 OMS でこれを行うのは比較的簡単です。方法については、[OMS を使用したイベントとログの分析](service-fabric-diagnostics-event-analysis-oms.md)に関する記事をご覧ください。 Application Insights は、診断拡張機能の構成の一部として構成できるので、少し特殊と言えます。AI を使用する場合は、[こちらの記事](service-fabric-diagnostics-event-analysis-appinsights.md)をご覧ください。

>[!NOTE]
>現在のところ、テーブルに送信されるイベントを絞り込む方法はありません。 テーブルからイベントを削除するプロセスを実装しない場合、テーブルは増加を続けます。 現在、[ウォッチドッグ サンプル](https://github.com/Azure-Samples/service-fabric-watchdog-service)で実行されるデータ グルーミング サービスの例があります。30 日または 90 日の期間を超えてログを保存する正当な理由がない限り、データ グルーミング サービスを自分で作成することをお勧めします。

* [診断拡張機能を使用してパフォーマンス カウンターまたはログを収集する方法についての説明](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Application Insights を使用したイベントの分析と視覚化](service-fabric-diagnostics-event-analysis-appinsights.md)
* [OMS を使用したイベントの分析と視覚化](service-fabric-diagnostics-event-analysis-oms.md)
