---
title: テンプレートを使用して Azure Monitor で Windows スケール セットのメトリックを収集する
description: Resource Manager テンプレートを使用して Windows 仮想マシン スケール セットのゲスト OS メトリックを Azure Monitor メトリック ストアに送信する
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: c5f536295bce07e70606f1ff286b08f8465d59f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048951"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Azure Resource Manager テンプレートを使用して Windows 仮想マシン スケール セットのゲスト OS メトリックを Azure Monitor メトリック ストアに送信する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor [Microsoft Azure Diagnostics (WAD) 拡張機能](../agents/diagnostics-extension-overview.md)を使用すると、仮想マシン、クラウド サービス、または Azure Service Fabric クラスターの一部として実行されているゲスト オペレーティング システム (ゲスト OS) からメトリックとログを収集できます。 拡張機能により、以前にリンクされた記事に記載されている多くの場所にテレメトリを送信できます。  

この記事では、Windows 仮想マシン スケール セット用のゲスト OS のパフォーマンス メトリックを Azure Monitor データ ストアに送信するプロセスについて説明します。 Microsoft Azure Diagnostics 拡張機能バージョン 1.11 以降、標準プラットフォーム メトリックが既に収集されている Azure Monitor メトリック ストアに、メトリックを直接書き込むことができます。 この場所にこれらを格納することで、プラットフォーム メトリックに対して使用できるのと同じアクションにアクセスできます。 アクションには、ほぼリアルタイムのアラート、グラフ作成、ルーティング、REST API からのアクセスなどの機能があります。 これまで、Microsoft Azure Diagnostics 拡張機能では、Azure Monitor データ ストアではなく Azure Storage に書き込んでいました。  

Resource Manager テンプレートを初めて利用する場合は、[テンプレートのデプロイ](../../azure-resource-manager/management/overview.md)とその構造および構文についてご確認ください。  

## <a name="prerequisites"></a>前提条件

- サブスクリプションを [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md) に登録する必要があります 

- [Azure PowerShell](/powershell/azure) がインストールされている必要があります。[Azure Cloud Shell](../../cloud-shell/overview.md) を使用することもできます。 

- お使いの VM リソースが、[カスタム メトリックをサポートするリージョン](./metrics-custom-overview.md#supported-regions)に存在する必要があります。

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor をデータ シンクとして設定する 
Azure Diagnostics 拡張機能では、**データ シンク** と呼ばれる機能を使って、メトリックとログをさまざまな場所にルーティングします。 次の手順では、Resource Manager テンプレートと PowerShell を使用して、新しい Azure Monitor データ シンクを使って VM をデプロイする方法を説明します。 

## <a name="author-a-resource-manager-template"></a>Resource Manager テンプレートを作成する 
この例では、公開されている[サンプル テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)を使用できます。  

- **Azuredeploy.json** は、仮想マシン スケール セットのデプロイ用に事前構成された Resource Manager テンプレートです。

- **Azuredeploy.parameters.json** は、VM 用に設定するユーザー名やパスワードなどの情報が格納されているパラメーター ファイルです。 デプロイ中、このファイルで設定されているパラメーターが、Resource Manager テンプレートによって使用されます。 

両方のファイルをダウンロードし、ローカルに保存します。 

###  <a name="modify-azuredeployparametersjson"></a>azuredeploy.parameters.json ファイルを更新する
**azuredeploy.parameters.json** ファイルを開きます。  
 
- デプロイする **vmSKU** を設定します。 Standard_D2_v3 をお勧めします。 
- 仮想マシン スケール セットに使用する **windowsOSVersion** を指定します。 2016-Datacenter をお勧めします。 
- **vmssName** プロパティを使用して、デプロイする仮想マシン スケール セット リソースに名前を付けます (**VMSS-WAD-TEST** など)。    
- **instanceCount** プロパティを使用して、仮想マシン スケール セットで実行する VM の数を指定します。
- 仮想マシン スケール セットの **adminUsername** と **adminPassword** について値を入力します。 これらのパラメーターはスケール セット内の VM へのリモート アクセスに使用されます。 お使いの VM がハイジャックされないように、このテンプレートのパラメーターは使用 **しないでください**。 ボットは、インターネット上にあるパブリック GitHub リポジトリのユーザー名やパスワードをスキャンします。 多くの場合、このようなボットは、これらの既定値を使用して VM をテストします。 


###  <a name="modify-azuredeployjson"></a>azuredeploy.json を変更する
**azuredeploy.json** ファイルを開きます。 

Resource Manager テンプレートでストレージ アカウント情報を保持するための変数を追加します。 診断構成ファイルで指定されたすべてのログまたはパフォーマンス カウンターは、Azure Monitor メトリック ストアと、ここで指定したストレージ アカウントの両方に書き込まれます。 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
resources セクションで仮想マシン スケール セットの定義を見つけて、**identity** セクションを構成に追加します。 この追加により、Azure によってシステム ID が割り当てられます。 また、この手順により、スケール セット内の VM が自身に関するゲスト メトリックを Azure Monitor に確実に出力できます。  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

仮想マシン スケール セット リソースで、**virtualMachineProfile** セクションを見つけます。 拡張機能を管理するための **extensionsProfile** と呼ばれる新しいプロファイルを追加します。  


**extensionProfile** で、**VMSS-WAD-extension** セクションに示されているように、新しい拡張機能をテンプレートに追加します。  このセクションは、出力されたメトリックが Azure Monitor で確実に受け入れられるようにする、Azure リソース拡張機能用のマネージド ID です。 **name** フィールドには任意の名前を含めることができます。 

MSI 拡張機能の次のコードはまた、診断拡張機能および構成を拡張機能リソースとして仮想マシン スケール セット リソースに追加します。 必要に応じて、パフォーマンス カウンターを自由に追加または削除してください。 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
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
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


ストレージ アカウントに対して **dependsOn** を追加し、正しい順序で作成されるようにします。 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

ストレージ アカウントを作成します (テンプレートでまだ作成されていない場合)。 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

両方のファイルを保存して閉じます。 

## <a name="deploy-the-resource-manager-template"></a>Resource Manager テンプレートをデプロイする 

> [!NOTE]  
> Azure Diagnostics 拡張機能バージョン 1.5 以上を実行する必要があります。**また**、Resource Manager テンプレートで **autoUpgradeMinorVersion:** プロパティが **true** に設定されていなければなりません。 その後、Azure によって VM の開始時に適切な拡張機能が読み込まれます。 ご自身のテンプレートにこれらの設定がない場合は、テンプレートを変更して再デプロイします。 


Resource Manager テンプレートをデプロイするために、Azure PowerShell を使用します。  

1. PowerShell を起動します。 
1. `Login-AzAccount` を使用して Azure にサインインします。
1. `Get-AzSubscription` を使用して、サブスクリプションの一覧を取得します。
1. 作成するサブスクリプションを設定するか、仮想マシンを更新します。 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. デプロイされている VM の新しいリソース グループを作成します。 次のコマンドを実行します。 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > 必ず、カスタム メトリックに対して有効になっている Azure リージョンを使用してください。 必ず、[カスタム メトリックに対して有効になっている Azure リージョン](./metrics-custom-overview.md#supported-regions)を使用してください。
 
1. 次のコマンドを実行して、VM をデプロイします。  

   > [!NOTE]  
   > 既存のスケール セットを更新する場合は、 **-Mode Incremental** をコマンドの末尾に追加します。 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. デプロイが成功したら、Azure portal に仮想マシン スケール セットが表示されるようになります。 これによって、メトリックが Azure Monitor に出力されます。 

   > [!NOTE]  
   > 選択した **vmSkuSize** に関連するエラーが発生することがあります。 この場合は、**azuredeploy.json** ファイルに戻り、**vmSkuSize** パラメーターの既定値を更新します。 **Standard_DS1_v2** を試してみることをお勧めします。 


## <a name="chart-your-metrics"></a>メトリックをグラフ化する 

1. Azure portal にサインインします。 

1. 左側のメニューで **[モニター]** を選択します。 

1. **[モニター]** ページで、 **[メトリック]** を選択します。 

   ![[モニター] - [メトリック] ページ](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. 集計の期間を **[過去 30 分間]** に変更します。  

1. リソースのドロップダウン メニューで、作成した仮想マシン スケール セットを選択します。  

1. 名前空間のドロップダウン メニューで、 **[azure.vm.windows.guest]** を選択します。 

1. メトリックのドロップダウン メニューで、 **[Memory\%Committed Bytes in Use]** を選択します。  

その後、このメトリックのディメンションを使用して、特定の VM に対してこのメトリックをグラフ化するか、スケール セット内の各 VM をプロットできます。 



## <a name="next-steps"></a>次のステップ
- [カスタム メトリック](./metrics-custom-overview.md)の詳細を確認します。