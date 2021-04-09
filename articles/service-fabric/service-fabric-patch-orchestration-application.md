---
title: Service Fabric クラスターでの Windows オペレーティング システムへのパッチの適用
description: この記事では、パッチ オーケストレーション アプリケーションを使用して、Service Fabric クラスターでのオペレーティング システムへの修正プログラムの適用を自動化する方法について説明します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: e51b247f8c1a5a9ed8f6ec8e24363015afb2f7de
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614413"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Service Fabric クラスターでの Windows オペレーティング システムへのパッチの適用

## <a name="automatic-os-image-upgrades"></a>OS イメージの自動アップグレード

[Virtual Machine Scale Sets での OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)の取得は、Azure でオペレーティング システムにパッチが適用された状態を保つためのベスト プラクティスです。 仮想マシン スケール セット ベースの OS イメージの自動アップグレードでは、スケール セットに Silver 以上の耐久性が必要です。

Virtual Machine Scale Sets による OS イメージの自動アップグレードの要件
-   Service Fabric の[持続性レベル](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)は Silver または Gold であり、Bronze ではありません。
-   スケール セット モデル定義の Service Fabric 拡張機能には、TypeHandlerVersion 1.1 以降が必要です。
-   持続性レベルは、スケール セット モデル定義の Service Fabric クラスターと Service Fabric 拡張機能で同じである必要があります。
- Virtual Machine Scale Sets に追加の正常性プローブやアプリケーション正常性拡張機能を使用する必要はありません。

Service Fabric クラスターと Service Fabric 拡張機能で持続性の設定に不一致がないことを確認します。これは、一致しない場合にアップグレード エラーが発生する可能性があるためです。 持続性レベルは、[このページ](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)で説明されているガイドラインに従って変更できます。

ブロンズ 持続性を使用する場合、OS イメージの自動アップグレードは使用できません。 [パッチ オーケストレーション アプリケーション](#patch-orchestration-application ) (Azure 以外でホストされているクラスターのみが対象) は、Silver 以上の持続性レベルでは *推奨されません* が、Service Fabric アップグレード ドメインに関しては、Windows 更新プログラムを自動化する唯一のオプションです。

> [!IMPORTANT]
> Azure Service Fabric では、OS ディスクを交換せずに、"Windows Update" によってオペレーティング システムのパッチを適用する VM 内アップグレードはサポートされていません。

オペレーティング システムで Windows Update を正しく無効にしてこの機能を有効にするには、2 つの手順を行う必要があります。

1. OS イメージの自動アップグレードを有効にし、Windows Updates ARM を無効にする 
    ```json
    "virtualMachineProfile": { 
        "properties": {
          "upgradePolicy": {
            "automaticOSUpgradePolicy": {
              "enableAutomaticOSUpgrade":  true
            }
          }
        }
      }
    ```
    
    ```json
    "virtualMachineProfile": { 
        "osProfile": { 
            "windowsConfiguration": { 
                "enableAutomaticUpdates": false 
            }
        }
    }
    ```

    Azure PowerShell
    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -AutomaticOSUpgrade $true -EnableAutomaticUpdate $false
    ``` 
    
1. スケール セット モデルを更新する。この構成変更後、変更が有効になるように、スケール セット モデルを更新するには、すべてのマシンの再イメージ化が必要です。
    
    Azure PowerShell
    ```azurepowershell-interactive
    $scaleSet = Get-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName
    $instances = foreach($vm in $scaleSet)
    {
        Set-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -InstanceId $vm.InstanceID -Reimage
    }
    ``` 
    
詳細な手順については、[Virtual Machine Scale Sets による OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)に関する説明を参照してください。

## <a name="patch-orchestration-application"></a>パッチ オーケストレーション アプリケーション

> [!IMPORTANT]
> 2019 年 4 月 30 日の時点で、パッチ オーケストレーション アプリケーション バージョン 1.2.* はサポートされなくなりました。 必ず最新バージョンにアップグレードしてください。

パッチ オーケストレーション アプリケーション (POA) は、Azure Service Fabric Repair Manager サービスのラッパーであり、Azure 以外でホストされるクラスターに対して構成ベースの OS 修正プログラム スケジュールを有効にします。 POA は、Azure 以外でホストされるクラスターには必要ありませんが、更新ドメインによる修正プログラムのインストールのスケジュール設定は、ダウンタイムを発生させることなく Service Fabric クラスター ホストに修正プログラムを適用するために必要です。

POA は、ダウンタイムを発生させることなく、Service Fabric クラスターでのオペレーティング システムへの修正プログラムの適用を自動化する Service Fabric アプリケーションです。

POA では次の機能が提供されます。

- **オペレーティング システムの更新プログラムの自動インストール:** オペレーティング システムの更新プログラムが自動的にダウンロードされ、インストールされます。 クラスター ノードは、クラスターのダウンタイムを発生させることなく、必要に応じて再起動されます。

- **クラスター対応のパッチ適用と正常性の統合:** POA が更新プログラムを適用している間に、クラスター ノードの正常性を監視します。 クラスター ノードの場合、一度に 1 つのノード、または一度に 1 つの更新ドメインが更新されます。 修正プログラムの適用によってクラスターの正常性が低下した場合、問題の悪化を防ぐために修正プログラムの適用が停止されます。

## <a name="internal-details-of-poa"></a>POA の内部詳細

POA は、次のサブコンポーネントで構成されます。

- **コーディネーター サービス**:このサービスの役割は次のとおりです。
    - クラスター全体における Windows Update ジョブを調整する。
    - 完了した Windows Update 操作の結果を保存する。

- **ノード エージェント サービス**:このステートレス サービスは、すべての Service Fabric クラスター ノードで実行されます。 このサービスは次の役割を担います。
    - ノード エージェント NTService をブートストラップする。
    - ノード エージェント NTService の監視。

- **ノード エージェント NTService**:この Windows NT サービスは、上位の特権 (SYSTEM) で実行されます。 一方、ノード エージェント サービスとコーディネーター サービスは、下位の特権 (NETWORK SERVICE) で実行されます。 このサービスは、全クラスター ノードで次の Windows Update ジョブを実行する役割を担います。
    - ノードでの Windows の自動更新を無効にする。
    - ユーザーが指定したポリシーに従って、Windows 更新プログラムをダウンロードしてインストールする。
    - Windows 更新プログラムのインストール後にマシンを再起動する。
    - Windows Update の結果をコーディネーター サービスにアップロードする。
    - 再試行回数の上限に達した後、操作が失敗した場合に正常性レポートを生成する。

> [!NOTE]
> POA では Service Fabric Repair Manager サービスを使用して、ノードを無効または有効にし、正常性チェックを実行します。 POA によって作成される修復タスクでは、各ノードの Windows Update の進行状況を追跡します。

## <a name="prerequisites"></a>前提条件

> [!NOTE]
> 最低限必要な .NET Framework バージョンは 4.6 です。

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Repair Manager サービスを有効にする (まだ実行されていない場合)

POA を使用するには、クラスターで Repair Manager サービスを有効にする必要があります。

#### <a name="azure-clusters"></a>Azure クラスター

シルバー持続性層の Azure クラスターでは、Repair Manager サービスが既定で有効になっています。 ゴールド持続性層の Azure クラスターでは、クラスターが作成された時期に応じて、Repair Manager サービスが有効になっている場合となっていない場合があります。 ブロンズ持続性層の Azure クラスターでは、既定では Repair Manager サービスは有効になっていません。 サービスが既に有効になっている場合、Service Fabric Explorer のシステム サービス セクションでそれが実行されていることを確認できます。

##### <a name="the-azure-portal"></a>Azure ポータル
クラスターを設定するときに、Azure portal から Repair Manager を有効にすることができます。 クラスターの構成時に、 **[アドオン機能]** の下にある **[Repair Manager を含める]** オプションを選択します。

![Azure portal からの Repair Manager の有効化の画像](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Azure Resource Manager デプロイ モデル
[Azure Resource Manager デプロイ モデル](./service-fabric-cluster-creation-via-arm.md)を使用して、新規および既存の Service Fabric クラスターで Repair Manager サービスを有効にすることもできます。 デプロイするクラスター用テンプレートを用意します。 サンプル テンプレートを使用することも、カスタムの Azure Resource Manager デプロイ モデル テンプレートを作成することもできます。 

[Azure Resource Manager デプロイ モデル テンプレート](./service-fabric-cluster-creation-via-arm.md)を使用して Repair Manager サービスを有効にするには、次のようにします。

1. *Microsoft.ServiceFabric/clusters* リソースに対して、`apiVersion` が *2017-07-01-preview* に設定されていることを確認します。 異なる場合は、`apiVersion` を *2017-07-01.txt-preview* 以降に更新する必要があります。

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. `fabricSettings` セクションの後に次の `addonFeatures` セクションを追加して、Repair Manager サービスを有効にします。

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. これらの変更でクラスター テンプレートを更新した後、変更を適用して更新を完了させます。 これで、Repair Manager サービスがクラスターで実行されていることがわかります。 これは、Service Fabric Explorer のシステム サービス セクションでは *fabric:/System/RepairManagerService* と呼ばれます。 

### <a name="standalone-on-premises-clusters"></a>スタンドアロン オンプレミス クラスター

新規または既存の Service Fabric クラスターで Repair Manager サービスを有効にする場合は、[スタンドアロン Windows クラスターの構成設定](./service-fabric-cluster-manifest.md)を使用できます。

Repair Manager サービスを有効にするには、次のようにします。

1. 次のように、[一般的なクラスター構成](./service-fabric-cluster-manifest.md#general-cluster-configurations)の `apiVersion` が *04-2017* 以降に設定されていることを確認します。

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. 次のように、`fabricSettings` セクションの後に以下の `addonFeatures` セクションを追加して、Repair Manager サービスを有効にします。

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. 更新されたクラスター マニフェストを使用して、[新しいクラスターを作成](./service-fabric-cluster-creation-for-windows-server.md)するか、[クラスター構成をアップグレード](./service-fabric-cluster-upgrade-windows-server.md)して、これらの変更でクラスター マニフェストを更新します。 

   更新されたクラスター マニフェストを使用してクラスターが実行された後、クラスターで実行されている Repair Manager サービスを確認できます。 これは *fabric:/System/RepairManagerService* と呼ばれ、Service Fabric Explorer のシステム サービス セクションにあります。

### <a name="configure-windows-updates-for-all-nodes"></a>すべてのノードの Windows 更新プログラムを構成する

Windows の自動更新では、複数のクラスター ノードが同時に再起動される可能性があるため、可用性が失われる場合があります。 POA では、既定で、各クラスター ノードでの Windows の自動更新の無効化が試行されます。 ただし、管理者またはグループ ポリシーによって設定が管理されている場合は、"ダウンロードする前に通知する" ように Windows Update ポリシーを明示的に設定することをお勧めします。

## <a name="download-the-application-package"></a>アプリケーション パッケージをダウンロードする

アプリケーション パッケージをダウンロードするには、GitHub の[パッチ オーケストレーション アプリケーションのリリース ページ](https://github.com/microsoft/Service-Fabric-POA/releases/latest/)に移動します。

## <a name="configure-poa-behavior"></a>POA の動作を構成する

ニーズに合わせて POA の動作を構成することができます。 アプリケーションを作成または更新するときにアプリケーション パラメーターを渡して、既定値をオーバーライドします。 `Start-ServiceFabricApplicationUpgrade` または `New-ServiceFabricApplication` コマンドレットに `ApplicationParameter` を指定することで、アプリケーション パラメーターを指定できます。

| パラメーター        | Type                          | 詳細 |
|:-|-|-|
|MaxResultsToCache    |Long                              | キャッシュする必要がある Windows Update の結果の最大数。 <br><br>既定値は 3000 で、以下が前提となります。 <br> &nbsp;&nbsp;- ノード数が 20 である。 <br> &nbsp;&nbsp;- 月あたりのノードの更新数が 5 である。 <br> &nbsp;&nbsp;- 操作あたりの結果の数を 10 にできる。 <br> &nbsp;&nbsp;- 過去 3 か月の結果を格納する必要がある。 |
|TaskApprovalPolicy   |列挙型 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy は、コーディネーター サービスが、Service Fabric クラスター ノードに Windows Update をインストールする際に使用するポリシーを示しています。<br><br>使用できる値は、次のとおりです。 <br>*NodeWise*:Windows 更新プログラムは、一度に 1 つのノードにインストールされます。 <br> *UpgradeDomainWise*:Windows 更新プログラムは、一度に 1 つの更新ドメインにインストールされます (最大で、更新ドメインに属するすべてのノードに Windows 更新プログラムを適用できます)。<br><br> [FAQ](#frequently-asked-questions) に関するセクションを参照してください。これは、クラスターに最適なポリシーを決定するのに役立ちます。
|LogsDiskQuotaInMB   |Long  <br> (既定値:*1024*)               | パッチ オーケストレーション アプリのログの最大サイズ (MB 単位)。このサイズまで、ノードでローカルに保存することができます。
| WUQuery               | string<br>(既定値:*IsInstalled=0*)                | Windows 更新プログラムを取得するためのクエリ。 詳細については、[WuQuery](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search) に関するページをご覧ください。
| InstallWindowsOSOnlyUpdates | *Boolean* <br> (既定値: false)                 | このフラグを使用して、どの更新プログラムをダウンロードしてインストールするかを制御します。 次の値が許可されています <br>true - Windows オペレーティング システムの更新プログラムのみをインストールします。<br>false - マシンで使用可能なすべての更新プログラムをインストールします。          |
| WUOperationTimeOutInMinutes | int <br>(既定値:*90*)                   | Windows Update 操作 (検索、ダウンロード、インストール) のタイムアウトを指定します。 指定したタイムアウト時間内に操作が完了しなかった場合は、操作が中止されます。       |
| WURescheduleCount     | int <br> (既定値:*5*)                  | 操作が繰り返し失敗する場合に、サービスで Windows 更新プログラムの再スケジュールを行う最大回数。          |
| WURescheduleTimeInMinutes | int <br>(既定値:*30*) | 問題が解決しない場合に、サービスで Windows 更新プログラムの再スケジュールを行う間隔。 |
| WUFrequency           | コンマ区切りの文字列 (既定値:*Weekly, Wednesday, 7:00:00*)     | Windows 更新プログラムをインストールする頻度。 形式と指定できる値は次のとおりです。 <br>- Monthly, DD, HH:MM:SS (例:*Monthly, 5, 12:22:32*)。 _DD_ (日) フィールドに使用できる値は、1 から 28 の数字と _last_ です。 <br>- Weekly, Day, HH:MM:SS (例:*Weekly, Tuesday, 12:22:32*)  <br>- Daily, HH:MM:SS (例:*Daily, 12:22:32*)  <br>- Week, Day, HH:MM:SS (例:*2, Friday, 21:00:00* は、毎月第 2 週の金曜日の午後 9:00 UTC を示します) <br>- *None* は、Windows 更新プログラムが実行されないことを示します。  <br><br> 時刻は UTC 形式です。|
| AcceptWindowsUpdateEula | Boolean <br>(既定値: *true*) | このフラグを設定すると、コンピューターの所有者に代わって、アプリケーションが Windows Update の使用許諾契約に同意します。              |

> [!TIP]
> Windows 更新プログラムをすぐに実行する場合は、アプリケーションのデプロイ時間を基準として `WUFrequency` を設定します。 たとえば、5 ノード テスト クラスターがあり、UTC 時刻の午後 5 時頃にアプリケーションをデプロイする予定であるとします。 アプリケーションのアップグレードまたはデプロイに最大 30 分かかると想定される場合、WUFrequency を *Daily, 17:30:00* に設定します。

## <a name="deploy-poa"></a>POA をデプロイする

1. 前提条件となるすべての手順を実行してクラスターを準備します。
1. 他の Service Fabric アプリと同様に POA をデプロイします。 PowerShell を使用してデプロイする場合は、「[PowerShell を使用してアプリケーションのデプロイと削除を実行する](./service-fabric-deploy-remove-applications.md)」を参照してください。
1. デプロイ時にアプリケーションを構成するには、`ApplicationParameter` を `New-ServiceFabricApplication` コマンドレットに渡します。 利便性のために、アプリケーションと共に Deploy.ps1 スクリプトが用意されています。 このスクリプトを使用するには、次の手順に従います。

    - `Connect-ServiceFabricCluster` を使用して Service Fabric クラスターに接続します。
    - 適切な `ApplicationParameter` 値を指定して、Deploy.ps1 PowerShell スクリプトを実行します。

> [!NOTE]
> スクリプトとアプリケーション フォルダー *PatchOrchestrationApplication* は同じディレクトリに保存してください。

## <a name="upgrade-poa"></a>POA をアップグレードする

PowerShell を使用して POA のバージョンをアップグレードするには、「[PowerShell を使用した Service Fabric アプリケーションのアップグレード](./service-fabric-application-upgrade-tutorial-powershell.md)」の手順に従います。

## <a name="remove-poa"></a>POA を削除する

アプリケーションを削除するには、「[PowerShell を使用してアプリケーションのデプロイと削除を実行する](./service-fabric-deploy-remove-applications.md)」の手順に従います。

参考までに、アプリケーションと共に Undeploy.ps1 スクリプトを提供しています。 このスクリプトを使用するには、次の手順に従います。

  - ```Connect-ServiceFabricCluster``` を使用して Service Fabric クラスターに接続します。
  - Undeploy.ps1 PowerShell スクリプトを実行します。

> [!NOTE]
> スクリプトとアプリケーション フォルダー *PatchOrchestrationApplication* は同じディレクトリに保存してください。

## <a name="view-the-windows-update-results"></a>Windows Update の結果の確認

POA では、結果の履歴をユーザーに表示するための REST API を公開しています。 結果の JSON の例を次に示します。

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

JSON のフィールドについては、次の表で説明します。

フィールド | 値 | 詳細
-- | -- | --
OperationResult | 0 - 成功<br> 1 - 成功 (エラーあり)<br> 2 - 失敗<br> 3 - 中止<br> 4 - タイムアウトにより中止 | 操作全体 (通常は 1 つまたは複数の更新プログラムのインストールを含む) の結果を示します。
ResultCode | OperationResult と同じ | このフィールドでは、個々の更新プログラムのインストール操作の結果が示されます。
OperationType | 1 - インストール<br> 0 - 検索とダウンロード| 既定では、インストールが、結果に表示される唯一の OperationType です。
WindowsUpdateQuery | 既定値は "IsInstalled=0" | 更新プログラムの検索に使用された Windows Update クエリ。 詳細については、[WuQuery](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search) に関するページを参照してください。
RebootRequired | true - 再起動が必要<br> false - 再起動は不要 | 更新プログラムのインストールを完了するのに再起動が必要だったかどうかを示します。
OperationStartTime | DateTime | 操作 (ダウンロード/インストール) が開始した時刻を示します。
OperationTime | DateTime | 操作 (ダウンロード/インストール) が完了した時刻を示します。
HResult | 0 - 成功<br> その他 - 失敗| updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6" で Windows 更新プログラムの失敗の理由を示します。

更新がまだスケジュールされていない場合、結果の JSON は空になります。

Windows Update の結果を照会するには、クラスターにサインインします。 コーディネーター サービスのプライマリ アドレス用のレプリカ IP アドレスを見つけて、ブラウザーから http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults という URL を開きます。

コーディネーター サービスの REST エンドポイントには動的ポートがあります。 正確な URL を確認するには、Service Fabric Explorer を参照します。 たとえば、 *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* で結果を入手できます。

![REST エンドポイントの画像](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

クラスターでリバース プロキシが有効になっている場合は、クラスターの外部からも URL にアクセスできます。

ヒットする必要があるエンドポイントは、*http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults* です。

クラスターでリバース プロキシを有効にするには、「[Azure Service Fabric のリバース プロキシ](./service-fabric-reverseproxy.md)」の手順に従います。 

> 
> [!WARNING]
> リバース プロキシが構成された後、クラスター内の、HTTP エンドポイントを公開するすべてのマイクロサービスが、クラスターの外部からアドレス指定可能になります。

## <a name="diagnostics-and-health-events"></a>診断および正常性イベント

このセクションでは Service Fabric クラスターの POA を使用して、修正プログラムの問題をデバッグまたは診断する方法について説明します。

> [!NOTE]
> 以下に示されている自己診断の機能向上の多くを得るには、POA バージョン 1.4.0 以降がインストールされている必要があります。

ノード エージェント NTService では、ノードに更新プログラムをインストールするために[修復タスク](/dotnet/api/system.fabric.repair.repairtask)を作成します。 その後、各タスクは、タスク承認ポリシーに従って、コーディネーター サービスによって準備されます。 最後に、準備されたタスクは Repair Manager によって承認されますが、クラスターが異常な状態にある場合は、どのタスクも承認されません。 

ノードでの更新プログラムの進行について理解できるように、段階的に見ていきましょう。

1. すべてのノード上で実行されている NodeAgentNTService では、スケジュールされた時刻に使用可能な Windows 更新プログラムを検索します。 更新プログラムが使用可能な場合、それらをノード上にダウンロードします。

1. 更新プログラムがダウンロードされた後、ノード エージェント NTService では *POS___\<unique_id>* の名前で、ノードの対応する修復タスクを作成します。 これらの修復タスクは、[Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask) コマンドレットを使用するか、ノードの詳細セクションで SFX を使用して表示できます。 修復タスクが作成された後、すぐに [*要求済み* 状態](/dotnet/api/system.fabric.repair.repairtaskstate)に移ります。

1. コーディネーター サービスでは、定期的に *要求済み* 状態の修復タスクを検索し、TaskApprovalPolicy に基づいてそれらを *準備中* 状態に更新します。 TaskApprovalPolicy が NodeWise になるように構成されている場合、現在、*準備中*、*承認済み*、*実行中*、または *復元中* の状態になっている他の修復タスクがない場合にのみ、ノードに対応した修復タスクが準備されます。 

   同様に、UpgradeWise TaskApprovalPolicy の場合は、同じ更新ドメインに属しているノードに対してのみ、上記の状態でタスクが存在します。 修復タスクが *準備中* 状態に移された後、対応する Service Fabric ノードが、*再起動* の意図で [無効](/powershell/module/servicefabric/disable-servicefabricnode)になります。

   POA バージョン 1.4.0 以降では、CoordinatorService の ClusterPatchingStatus プロパティを使用してイベントをポストし、修正プログラムが適用されているノードを表示します。 次の画像に示すように、更新プログラムは _poanode_0 にインストールされます。

    [![修正プログラム適用中状態のクラスターの画像](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. ノードが無効になった後、修復タスクは *実行中* 状態に移されます。 
   
   > [!NOTE]
   > *無効* 状態でスタックしているノードでは、新しい修復タスクがブロックされる可能性があり、これにより、クラスターでの修正プログラムの適用操作が停止します。

1. 修復タスクが *実行中* 状態になると、そのノードへの修正プログラムのインストールが開始されます。 修正プログラムがインストールされた後、ノードは、修正プログラムに応じて、再起動される場合とされない場合があります。 次に、修復タスクが *修復中* 状態に移され、これにより、ノードが再び有効になります。 その後、修復タスクが完了済みとマークされます。

   POA バージョン 1.4.0 以降では、WUOperationStatus-\<NodeName> プロパティを使用して NodeAgentService の正常性イベントを表示して、更新プログラムの状態を確認できます。 下の画像で強調表示されたセクションには、ノード *poanode_0* および *poanode_2* での Windows 更新プログラムの状態が示されています。

   [![poanode_0 の Windows Update 操作状態が強調して示されているコンソール ウィンドウのスクリーンショット。](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![poanode_1 の Windows Update 操作状態が強調して示されているコンソール ウィンドウのスクリーンショット。](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   PowerShell を使用して詳細を取得することもできます。 これを行うには、クラスターに接続し、[Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask) を使用して修復タスクの状態を取り込みます。 
   
   次の例では、"POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" タスクは *DownloadComplete* 状態にあります。 これは、更新プログラムが *poanode_2* ノードにダウンロードされており、タスクが *実行中* 状態に移ったときにインストールが試みられることを意味します。

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   問題が解決しない場合は、ご利用の仮想マシン (VM) または複数の VM にサインインし、Windows イベント ログを使用して詳細を確認してください。 前述の修復タスクは、次の Executor の下位状態でのみ存在できます。

      ExecutorSubState | 説明
    -- | -- 
      None=1 |  ノードに実行中の操作がなかったことを意味します。 状態が遷移中である可能性があります。
      DownloadCompleted=2 | ダウンロード操作が成功したか、一部失敗したか、失敗したかを示します。
      InstallationApproved=3 | ダウンロード操作が以前に完了しており、Repair Manager でインストールが承認されたことを示します。
      InstallationInProgress=4 | 修復タスクの実行の状態に対応します。
      InstallationCompleted=5 | インストールが成功したか、一部成功したか、失敗したかを示します。
      RestartRequested=6 | 修正プログラムのインストールが完了し、ノード上に保留中の再起動アクションがあることを示します。
      RestartNotNeeded=7 |  修正プログラムのインストールの完了後に再起動が必要なかったことを示します。
      RestartCompleted=8 | 再起動が正常に完了したことを示します。
      OperationCompleted=9 | Windows Update 操作が正常に完了しました。
      OperationAborted=10 | Windows Update 操作が中止されたことを示します。

1. POA バージョン 1.4.0 以降では、ノードの更新の試行が完了した場合、Windows 更新プログラムのダウンロードとインストールの次回の試行が開始されるときに通知するために、"WUOperationStatus-[NodeName]" プロパティを使用するイベントが NodeAgentService にポストされます。 これは次の画像のように表示されます。

     [![NodeAgentService の Windows Update 操作状態が示されているコンソール ウィンドウのスクリーンショット。](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>診断ログ

パッチ オーケストレーション アプリケーションのログは、Service Fabric のランタイム ログの一部として収集されます。

ログは、任意の診断ツールまたはパイプラインを使用してキャプチャできます。 POA では次の固定プロバイダー ID を使用して、[イベント ソース](/dotnet/api/system.diagnostics.tracing.eventsource)を介してイベントをログに記録します。

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>正常性レポート

また、POA では、次のシナリオでノード エージェント サービスまたはコーディネーター サービスに対して正常性レポートを発行します。

* ノード エージェント NTService がダウンしている場合

   ノードでノード エージェント NTService がダウンしている場合、ノード エージェント サービスに対して警告レベルの正常性レポートが生成されます。

* Repair Manager サービスが有効になっていない場合

   クラスターで Repair Manager サービスが見つからない場合、コーディネーター サービスに対して警告レベルの正常性レポートが生成されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q:POA の実行中に、クラスターがエラー状態になるのはなぜですか?**

A:インストール プロセス中に、POA ではノードを無効にするか再起動します。これにより、一時的にクラスターが異常な状態になる可能性があります。

アプリケーションのポリシーに応じて、修正プログラムの適用操作中にいずれか 1 つのノードがダウンするか、*または* 更新ドメイン全体が同時にダウンすることがあります。

Windows 更新プログラムのインストールが完了するまでに、ノードは再起動後に再び有効になります。

次の例では、2 つのノードがダウンし、MaxPercentageUnhealthyNodes ポリシーに違反しているため、クラスターが一時的にエラー状態になっています。 このエラーは、修正プログラムの適用操作を開始できるようになるまでの一時的なものです。

![異常なクラスターの画像](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

問題が解決しない場合は、「トラブルシューティング」を参照してください。

**Q:POA が警告状態にある場合はどうすればよいですか?**

A:アプリケーションに対してポストされた正常性レポートで根本原因が示されているかどうかを確認してください。 通常、警告には問題の詳細が含まれています。 問題が一時的なものである場合、アプリケーションは自動的に回復することが予想されます。

**Q:クラスターに異常があり、オペレーティング システムの更新をすぐに実行する必要がある場合はどうすればよいですか?**

A:クラスターに異常がある間は、POA によって更新プログラムはインストールされません。 POA ワークフローのブロックを解除するために、クラスターが正常な状態になるようにしてみてください。

**Q:クラスターの "NodeWise" または "UpgradeDomainWise" として TaskApprovalPolicy を設定する必要はありますか?**

A:"UpgradeDomainWise" 設定では、更新ドメインに属するすべてのノードに並列して修正プログラムを適用することで、クラスター全体の修復を高速化します。 このプロセス中は、更新ドメイン全体に属するノードを使用できません ([*無効* 状態](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabled)になっている)。

これに対して、"NodeWise" 設定では、一度に 1 つのノードのみに修正プログラムが適用されます。これは、クラスター全体の修正プログラムの適用に時間がかかる可能性があることを意味します。 ただし、修正プログラムの適用プロセス中に利用不可 (*無効* 状態) になるのは、最大でも 1 つのノードのみです。

修正プログラムの適用サイクル中に、クラスターで N-1 個の更新ドメイン (N はクラスター上の更新ドメインの総数) 上での実行を許容できる場合は、ポリシーを "UpgradeDomainWise" として設定できます。 それ以外の場合は、"NodeWise" に設定します。

**Q:ノードに修正プログラムを適用するにはどのくらい時間がかかりますか?**

A:ノードへの修正プログラムの適用には、数分 (たとえば、[Windows Defender 定義の更新プログラム](https://www.microsoft.com/en-us/wdsi/definitions)) から数時間 (たとえば、[Windows の累積的な更新プログラム](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)) かかることがあります。 ノードに修正プログラムを適用するために必要な時間は、主に以下によって異なります。 
 - 更新プログラムのサイズ。
 - 修正プログラムの適用期間内に適用する必要がある、更新プログラムの数。
 - 更新プログラムのインストール、ノードの再起動 (必要な場合)、および再起動後のインストール手順の完了にかかる時間。
 - VM またはマシンのパフォーマンス、およびネットワークの状態。

**Q:クラスター全体に修正プログラムを適用するにはどのくらい時間がかかりますか?**

A:クラスター全体に修正プログラムを適用するために必要な時間は、以下によって異なります。

- ノードに修正プログラムを適用するために必要な時間。

- コーディネーター サービスのポリシー。 既定のポリシーである "NodeWise" では、一度に 1 つのノードのみに修正プログラムが適用されます。この方法では、"UpgradeDomainWise" を使用するよりも低速になります。 

   次に例を示します。ノードに修正プログラムを適用するのに最大 1 時間かかる場合、5 個の更新ドメインがあり、それぞれに 4 個のノードが含まれている、20 個のノード (同じ種類のノード) のクラスターに修正プログラムを適用する場合は、以下が必要です。
    - "NodeWise" の場合: 最大 20 時間。
    - "UpgradeDomainWise" の場合: 最大 5 時間。

- クラスターの負荷。 修正プログラムの適用操作ごとに、クラスター内の他の利用可能なノードに顧客のワークロードを再配置する必要があります。 修正プログラムが適用されているノードは、この間は [*無効化中* 状態](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabling)になります。 クラスターがピーク時の負荷に近い状態で実行されている場合、無効化プロセスには時間がかかります。 そのため、このような負荷状態では、修正プログラムの適用プロセス全体が遅く見えることがあります。

- 修正プログラムの適用中に発生したクラスターの正常性エラー。 [クラスターの正常性](./service-fabric-health-introduction.md)が[低下](/dotnet/api/system.fabric.health.healthstate#System_Fabric_Health_HealthState_Error)した場合、修正プログラムの適用プロセスは中断されます。 この問題により、クラスター全体に修正プログラムを適用するために必要な全体的な時間が増えます。

**Q:Windows Update の結果で一部の更新プログラムが REST API 経由で取得されたと表示されますが、マシンの Windows Update 履歴に表示されないのはなぜですか?**

A:一部の製品の更新プログラムは、独自の更新プログラムまたは修正プログラムの履歴にのみ表示されます。 たとえば、Windows Defender の更新プログラムは、Windows Server 2016 の Windows Update 履歴に表示される場合とされない場合があります。

**Q:POA を使用して、自分の開発クラスター (1 ノード クラスター) に修正プログラムを適用できますか?**

A:いいえ、POA を使用して 1 ノード クラスターに修正プログラムを適用することはできません。 [Service Fabric システム サービス](./service-fabric-technical-overview.md#system-services)またはその他の顧客アプリでダウンタイムが発生するため、この制限は設計によるものです。 したがって、修復ジョブへの修正プログラムの適用が Repair Manager によって承認されることはありません。

**Q:Linux 上のクラスター ノードに修正プログラムを適用するにはどうすればよいですか?**

A:Linux での更新プログラムのオーケストレーションの詳細については、「[Azure 仮想マシン スケール セットによる OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)」を参照してください。

**Q:更新サイクルにこれほど時間がかかるのはなぜですか?**

A:結果の JSON を照会し、すべてのノードの更新サイクルに入ってから、OperationStartTime と OperationTime(OperationCompletionTime) を使用してすべてのノードで更新プログラムのインストールにかかる時間を確認してみることができます。 

更新が行われていない大きな時間枠がある場合、クラスターはエラー状態にある可能性があるため、Repair Manager では POA の修復タスクを承認できません。 任意のノードで更新プログラムのインストールに時間がかかる場合、そのノードはしばらくの間、更新されていない可能性があります。 多くの更新プログラムのインストールが保留中である可能性があり、これにより、遅延が発生することがあります。 

また、*無効化中* 状態でスタックしているため、ノードへの修正プログラムの適用がブロックされている可能性があります。 これは通常、ノードの無効化により、クォーラムまたはデータ損失の状況が発生する可能性があるためです。

**Q:POA でノードに修正プログラムを適用するときに、そのノードを無効にする必要があるのはなぜですか?**

A:POA では、ノード上で実行されているすべての Service Fabric サービスを停止または再割り当てする、*再起動* の意図でノードを無効にします。 POA ではこれを行って、アプリケーションで新規および古い DLL を組み合わせて使用することにならないようにします。したがって、ノードを無効にせずに修正プログラムを適用することはお勧めできません。

**Q:POA を使用して更新できるノードの最大数はどれだけですか?**

A:POA では、Service Fabric Repair Manager を使用して、更新のためのノードの修復タスクを作成します。 ただし、一度に存在できる修復タスクは 250 個だけです。 現在、POA では各ノードの修復タスクを同時に作成するため、クラスター内の 250 ノードしか更新できません。 

## <a name="disclaimers"></a>免責事項

- POA では、ユーザーに代わって、Windows Update の使用許諾契約書に同意します。 必要に応じて、アプリケーションの構成でこの設定をオフにすることもできます。

- POA では、使用状況とパフォーマンスを追跡するためにテレメトリを収集します。 アプリケーションのテレメトリの設定は、Service Fabric ランタイムのテレメトリ設定 (既定でオン) に従います。

## <a name="troubleshooting"></a>トラブルシューティング

このセクションでは、ノードへの修正プログラムの適用に関する問題に対して考えられるトラブルシューティング ソリューションを提供します。

### <a name="a-node-is-not-coming-back-to-up-state"></a>ノードが稼働状態に戻らない

* 以下の理由により、ノードが *無効中* 状態でスタックしている可能性があります。

  - 安全性チェックが保留中になっています。 この状況を改善するには、十分な数のノードが正常な状態で稼働するように対策を講じてください。

* 次の理由により、ノードが *無効* 状態でスタックしている可能性があります。

  - 手動で無効にされた。
  - 進行中の Azure インフラストラクチャ ジョブによって無効にされた。
  - ノードに修正プログラムを適用するために POA によって一時的に無効にされた。

* 次の理由により、ノードがダウン状態でスタックしている可能性があります。

  - 手動でダウン状態にされた。
  - 再起動されている (POA によってトリガーされる可能性がある)。
  - VM またはマシンに障害があるか、ネットワーク接続に問題がある。

### <a name="updates-were-skipped-on-some-nodes"></a>一部のノードで更新がスキップされた

POA では、再スケジュール ポリシーに従って、Windows 更新プログラムのインストールを試行します。 サービスはアプリケーション ポリシーに従って、ノードを復旧し、更新をスキップします。

このような場合、ノード エージェント サービスに対して警告レベルの正常性レポートが生成されます。 Windows Update の結果には、エラーの考えられる理由も含まれます。

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>更新プログラムのインストール中にクラスターの正常性がエラーになる

Windows 更新プログラムの障害によって、特定のノードまたは更新ドメインでアプリケーションやクラスターの正常性が低下する場合があります。 POA では、クラスターが正常な状態に戻るまで、以降の Windows Update 操作を中断します。

管理者が介入し、Windows Update によってアプリケーションやクラスターが異常な状態になった原因を特定する必要があります。

## <a name="poa-release-notes"></a>POA のリリース ノート

>[!NOTE]
> POA バージョン 1.4.0 以降の場合、リリース ノートとリリースは、GitHub の[パッチ オーケストレーション アプリケーションのリリース ページ](https://github.com/microsoft/Service-Fabric-POA/releases/)で確認できます。

### <a name="version-110"></a>バージョン 1.1.0
- 公開リリース

### <a name="version-111"></a>バージョン 1.1.1
- NodeAgentNTService をインストールできない NodeAgentService の SetupEntryPoint のバグを修正しました。

### <a name="version-120"></a>バージョン 1.2.0

- システム再起動ワークフローに関連するバグを修正しました。
- 修復タスクの準備中に正常性チェックが予定どおりに実行されないために発生する RM タスク作成時のバグを修正しました。
- Windows サービス POANodeSvc のスタートアップ モードを auto から delayed-auto に変更しました。

### <a name="version-121"></a>バージョン 1.2.1

- クラスターのスケール ダウン ワークフローのバグ修正。 導入された POA 用のガベージ コレクション ロジックは、存在しないノードに属するタスクを修正します。

### <a name="version-122"></a>バージョン 1.2.2

- 各種のバグ修正。
- バイナリが署名されるようになりました。
- アプリケーションの sfpkg リンクを追加しました。

### <a name="version-130"></a>バージョン 1.3.0

- InstallWindowsOSOnlyUpdates を false に設定すると、使用可能なすべての更新プログラムがインストールされるようになりました。
- 自動更新を無効にするロジックを変更しました。 これにより、Server 2016 以降で自動更新が無効にならないバグが修正されます。
- 高度なユース ケースのために POA の両方のマイクロサービスのデプロイ制約をパラメーター化しました。

### <a name="version-131"></a>バージョン 1.3.1
- 自動更新の無効化の失敗により、Windows Server 2012 R2 以前で POA 1.3.0 が動作しない回帰を修正しました。 
- InstallWindowsOSOnlyUpdates の構成が常に True として選択されるバグを修正しました。
- InstallWindowsOSOnlyUpdates の既定値を False に変更します。

### <a name="version-132"></a>バージョン 1.3.2
- 現在のノード名のサブセットである名前を持つノードがある場合に、ノードでの修正プログラム適用のライフサイクルに影響する問題を修正しました。 このようなノードでは、修正プログラムが適用されなかったか、再起動が保留中である可能性があります。
