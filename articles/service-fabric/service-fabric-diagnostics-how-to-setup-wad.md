---
title: "Azure 診断でログを収集する方法 | Microsoft Docs"
description: "この記事では、Azure で実行されている Service Fabric クラスターのログを収集するように Azure 診断を設定する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: timlt
editor: 
ms.assetid: 9f7e1fa5-6543-4efd-b53f-39510f18df56
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2017
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: 1b4599848f44a7200f13bd6ddf4e82e96a75e069
ms.openlocfilehash: 41343990d3379aabd129af437ff2edbbd2134dcc


---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Azure 診断でログを収集する方法
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Azure Service Fabric クラスターを実行している場合、1 か所ですべてのノードのログを収集することをお勧めします。 1 か所でログを収集すると、クラスター内の問題と、そのクラスターで実行されているアプリケーションやサービスで発生する問題の分析と解決に役立ちます。

ログを収集してアップロードするための方法としては、Azure 診断拡張機能を使う方法があります。この拡張機能では、Azure Storage、Azure Application Insights、または Azure Event Hubs にログをアップロードできます。 ログは、ストレージ内または Event Hubs 内で直接使った場合はそれほど役に立ちません。 しかし、外部プロセスを使うことにより、ストレージからイベントを読み取って、[Log Analytics](../log-analytics/log-analytics-service-fabric.md) や他のログ解析ソリューションに配置できます。 [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) には、包括的なログ検索と分析サービスが組み込みで付属しています。

## <a name="prerequisites"></a>前提条件
これらのツールは、このドキュメントの操作の一部を実行するために使います。

* [Azure 診断](../cloud-services/cloud-services-dotnet-diagnostics.md) (Azure Cloud Services と関連性はありますが、お勧めの情報と例が掲載されています)
* [Azure リソース マネージャー](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Azure Resource Manager クライアント](https://github.com/projectkudu/ARMClient)
* [Azure Resource Manager テンプレート](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-sources-that-you-might-want-to-collect"></a>収集することができるログ ソース
* **Service Fabric ログ:** プラットフォームから標準の Windows イベント トレーシング (ETW) と EventSource チャネルに対して生成されます。 次のような種類のログがあります。
  * 操作イベント: Service Fabric プラットフォームで実行される操作のログです。 たとえば、アプリケーションとサービスの作成、ノードの状態変化、アップグレード情報などです。
  * [Reliable Actors プログラミング モデル イベント](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services プログラミング モデル イベント](service-fabric-reliable-services-diagnostics.md)
* **アプリケーション イベント**: サービス コードから生成され、Visual Studio テンプレートで指定された EventSource ヘルパー クラスを使用して出力されるイベントです。 アプリケーションからログを出力する方法については、「[ローカル コンピューターの開発のセットアップでのサービスの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)」を参照してください。

## <a name="deploy-the-diagnostics-extension"></a>診断拡張機能のデプロイ
ログ収集の最初の手順は、Service Fabric クラスター内の各 VM に診断拡張機能をデプロイすることです。 診断拡張機能を使用すると、各 VM のログが収集され、指定したストレージ アカウントにアップロードされます。 手順は、Azure Portal と Azure Resource Manager のどちらを使用するかに応じて少し変わります。 手順は、デプロイがクラスター作成の一環であるか、または既に存在しているクラスターに関するものであるかによっても変わります。 各シナリオの手順を見てみましょう。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>ポータルを使用してクラスター作成の一環で診断拡張機能をデプロイする
クラスター作成の一環としてクラスター内の VM に診断拡張機能をデプロイするには、次の図の [診断設定] パネルを使用します。 Reliable Actors または Reliable Services イベントの収集を有効にするには、[診断] が **[オン]** (既定の設定) に設定されていることを確認してください。 クラスターを作成した後、ポータルを使用してこれらの設定を変更することはできません。

![ポータルでのクラスター作成のための Azure 診断設定](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

Azure サポート チームは、サポート要求を解決するためにサポート ログを*必要*とします。 これらのログはリアルタイムで収集され、リソース グループで作成されたストレージ アカウントの&1; つに保存されます。 診断設定により、アプリケーション レベルのイベントが構成されます。 これらのイベントには、[Reliable Actors](service-fabric-reliable-actors-diagnostics.md) イベント、 [Reliable Services](service-fabric-reliable-services-diagnostics.md) イベント、および Azure Storage に格納されるいくつかのシステム レベル Service Fabric イベントが含まれます。

[Elasticsearch](https://www.elastic.co/guide/index.html) などの製品や独自のプロセスで、ストレージ アカウントからイベントを取得できます。 現在のところ、テーブルに送信されるイベントを絞り込む方法はありません。 テーブルからイベントを削除するプロセスを実装しない場合、テーブルは増加を続けます。

ポータルを使ってクラスターを作成する場合、**[OK] をクリックしてクラスターを作成する前に**テンプレートをダウンロードすることを強くお勧めします。 詳細については、[Azure Resource Manager テンプレートを使用して Service Fabric クラスターをセットアップする方法](service-fabric-cluster-creation-via-arm.md)に関するページを参照してください。 いくつかの変更はポータルを使用して行うことができないため、後で変更を行うためにこのテンプレートが必要になります。

次の手順を使用して、ポータルからテンプレートをエクスポートできます。 ただし、これらのテンプレートは必要な情報が欠落している null 値を含んでいる場合があるため、さらに使いにくくなる可能性があります。

1. リソース グループを開きます。
2. **[設定]** を選択し、設定パネルを表示します。
3. **[デプロイ]** を選択し、デプロイ履歴パネルを表示します。
4. デプロイを選択し、そのデプロイの詳細を表示します。
5. **[テンプレートのエクスポート]** を選択し、[テンプレート] パネルを表示します。
6. **[保存]** を選択し、テンプレート、パラメーター、PowerShell ファイルを含む .zip ファイルをエクスポートします。

ファイルをエクスポートした後は、変更を加える必要があります。 parameters.json ファイルを編集し、**adminPassword** 要素を削除します。 これでデプロイ スクリプトの実行時にパスワードが要求されます。 デプロイ スクリプトの実行時に、null パラメーター値の修正が必要になる場合があります。

ダウンロードしたテンプレートを使用して構成を更新するには、次のようにします:

1. ローカル コンピューター上のフォルダーに内容を展開します。
2. 新しい構成を反映するように内容を変更します。
3. PowerShell を起動し、内容を展開したフォルダーに移動します。
4. **deploy.ps1** を実行し、サブスクリプション ID、リソース グループ名 (構成を更新するために同じ名前を使用します)、および一意のデプロイ名を入力します。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Azure リソース マネージャー を使用してクラスター作成の一環で診断拡張機能をデプロイする
Resource Manager を使用してクラスターを作成するには、診断の構成 JSON を完全なクラスターの Resource Manager テンプレートに追加してから、クラスターを作成します。 ここでは、リソース マネージャー テンプレート サンプルの一部として、診断構成を追加した five-VM クラスター リソース マネージャー テンプレートのサンプルを用意しました。 このサンプルは、Azure サンプル ギャラリーの「 [Five-node cluster with Diagnostics Resource Manager template sample](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)」で参照できます。

Resource Manager テンプレートの診断設定を確認するには、azuredeploy.json ファイルを開き、**IaaSDiagnostics** を検索します。 このテンプレートを使用してクラスターを作成するには、前のリンクにある **[Azure にデプロイ]** ボタンをクリックしてください。

または、Resource Manager サンプルをダウンロードし、変更を加え、Azure PowerShell ウィンドウで `New-AzureRmResourceGroupDeployment` コマンドを使用して、変更したテンプレートでクラスターを作成する方法もあります。 コマンドに渡すパラメーターについては、次のコードを参照してください。 PowerShell を利用してリソース グループをデプロイする方法については、[Azure Resource Manager テンプレートを使用したリソース グループのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)に関する記事を参照してください。

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

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

## <a name="update-diagnostics-to-collection-health-and-load-events"></a>正常性と負荷のイベントを収集するように診断を更新する

Service Fabric リリース 5.4 以降、正常性と負荷のメトリック イベントを収集できるようになりました。 これらのイベントは、[ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) や [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx) などの正常性または負荷レポート API を使うことでシステムやユーザーのコードによって生成されるイベントを反映します。 これにより、一定期間のシステムの正常性を集計および表示したり、正常性または負荷のイベントに基づいてアラートを生成したりできます。 Visual Studio の診断イベント ビューアーでこれらのイベントを表示するには、ETW プロバイダーのリストに "Microsoft-ServiceFabric:4:0x4000000000000008" を追加します。

イベントを収集するには、リソース マネージャー テンプレートに次の行を追加します。

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

## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>新しい EventSource チャネルのログを収集およびアップロードするように診断を更新する
デプロイする予定の新しいアプリケーションを示す新しい EventSource チャネルのログを収集するように診断を更新するには、既存のクラスターに対する診断の設定に関する[前述のセクション](#deploywadarm)と同じ手順を実行してください。

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

パフォーマンス カウンターまたはイベント ログを収集するには、「[Azure リソース マネージャー テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」に記載されている例を使用して Resource Manager テンプレートを変更します。 その後、Resource Manager テンプレートを再発行します。

## <a name="next-steps"></a>次のステップ
問題を解決する際に確認する必要があるイベントの詳細については、[Reliable Actors](service-fabric-reliable-actors-diagnostics.md) と [Reliable Services](service-fabric-reliable-services-diagnostics.md) で生成される診断イベントを参照してください。

## <a name="related-articles"></a>関連記事
* [診断拡張機能を使用してパフォーマンス カウンターまたはログを収集する方法についての説明](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Log Analytics の Service Fabric ソリューション](../log-analytics/log-analytics-service-fabric.md)




<!--HONumber=Jan17_HO3-->


