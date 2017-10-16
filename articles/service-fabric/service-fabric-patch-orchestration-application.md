---
title: "Azure Service Fabric パッチ オーケストレーション アプリケーション | Microsoft Docs"
description: "Service Fabric クラスターでオペレーティング システムへのパッチの適用を自動化するためのアプリケーション。"
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: nachandr
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 6385dd99e3f5d96eee2cf50016e4af599d91b011
ms.contentlocale: ja-jp
ms.lasthandoff: 09/26/2017

---

# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Service Fabric クラスターでの Windows オペレーティング システムへのパッチの適用

パッチ オーケストレーション アプリケーションは、ダウンタイムなしで、Service Fabric クラスターでのオペレーティング システムへのパッチの適用を自動化する Azure Service Fabric アプリケーションです。

パッチ オーケストレーション アプリケーションは、次の機能を備えています。

- **オペレーティング システムの更新プログラムの自動インストール:**  オペレーティング システムの更新プログラムが自動的にダウンロードされ、インストールされます。 必要に応じてクラスター ノードが再起動されます。クラスターのダウンタイムは発生しません。

- **クラスター対応のパッチ適用と正常性の統合:**  パッチ オーケストレーション アプリケーションは、更新プログラムの適用時にクラスター ノードの正常性を監視します。 クラスター ノードは、1 ノードずつ、または 1 アップグレード ドメインずつアップグレードされます。 パッチの適用プロセスが原因でクラスターの正常性が低下すると、問題の悪化を防ぐためにパッチの適用が停止されます。

## <a name="internal-details-of-the-app"></a>アプリケーションの内部の詳細

パッチ オーケストレーション アプリケーションは、次のサブコンポーネントで構成されます。

- **コーディネーター サービス**: このステートフル サービスは次の役割を担います。
    - クラスター全体における Windows Update ジョブを調整する。
    - 完了した Windows Update 操作の結果を保存する。
- **ノード エージェント サービス**: このステートレス サービスは、すべての Service Fabric クラスター ノードで実行されます。 このサービスは次の役割を担います。
    - ノード エージェント NTService をブートストラップする。
    - ノード エージェント NTService の監視。
- **ノード エージェント NTService**: この Windows NT サービスは、上位の特権 (SYSTEM) で実行されます。 一方、ノード エージェント サービスとコーディネーター サービスは、下位の特権 (NETWORK SERVICE) で実行されます。 このサービスは、全クラスター ノードで次の Windows Update ジョブを実行する役割を担います。
    - ノードの自動 Windows Update を無効にする。
    - ユーザーが指定したポリシーに従って、Windows Update をダウンロードしてインストールする。
    - Windows Update のインストール後にマシンを再起動する。
    - Windows Update の結果をコーディネーター サービスにアップロードする。
    - 再試行回数の上限に達しても、操作が正常に完了しなかった場合に正常性レポートを生成する。

> [!NOTE]
> パッチ オーケストレーション アプリケーションでは、Service Fabric の修復マネージャー システム サービスを使用して、ノードを無効または有効にし、正常性チェックを実行します。 パッチ オーケストレーション アプリケーションによって作成される修復タスクによって、各ノードの Windows Update の進行状況が追跡されます。

## <a name="prerequisites"></a>前提条件

### <a name="minimum-supported-service-fabric-runtime-version"></a>サポートされている Service Fabric ランタイムの最小バージョン

#### <a name="azure-clusters"></a>Azure クラスター
パッチ オーケストレーション アプリケーションは、Service Fabric ランタイム バージョン v5.5 以降がインストールされている Azure クラスターで実行する必要があります。

#### <a name="standalone-on-premises-clusters"></a>スタンドアロン オンプレミス クラスター
パッチ オーケストレーション アプリケーションは、Service Fabric ランタイム バージョン v5.6 以降がインストールされているスタンドアロン クラスターで実行する必要があります。

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>修復マネージャー サービスを有効にする (まだ実行されていない場合)

パッチ オーケストレーション アプリケーションを使用するには、クラスターで修復マネージャー システム サービスを有効にする必要があります。

#### <a name="azure-clusters"></a>Azure クラスター

Azure クラスターの持続性層がシルバーの場合、修復マネージャー サービスが既定で有効になっています。 Azure クラスターの持続性層がゴールドの場合、クラスターが作成された時期によって、修復マネージャー サービスが有効になっている場合となっていない場合があります。 Azure クラスターの持続性層がブロンズの場合、既定では修復マネージャー サービスは有効になっていません。 サービスが既に有効になっている場合、Service Fabric Explorer のシステム サービス セクションでサービスが実行されていることを確認できます。

##### <a name="azure-portal"></a>Azure ポータル
クラスターの設定時に Azure Portal から修復マネージャーを有効にすることができます。 クラスターの構成時に [`Add on features`] で [`Include Repair Manager`] オプションを選択します。
![Azure Portal から修復マネージャーを有効にする画像](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート
[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)を使用して、新規および既存の Service Fabric クラスターで修復マネージャー サービスを有効にすることもできます。 デプロイするクラスター用テンプレートを用意します。 サンプル テンプレートを使用することも、カスタムの Resource Manager テンプレートを作成することもできます。 

[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)を使用して修復マネージャー サービスを有効にするには:

1. まず、次のスニペットに示すように、`Microsoft.ServiceFabric/clusters` リソースの `apiversion` が `2017-07-01-preview` に設定されていることを確認します。 値が異なる場合は、`apiVersion` を `2017-07-01-preview` に更新する必要があります。

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. `fabricSettings` セクションの後に次の `addonFeatures` セクションを追加して、修復マネージャー サービスを有効にします。

    ```json
    "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "RepairManager"
        ],
    ```

3. これらの変更によってクラスター テンプレートを更新したら、変更を適用してアップグレードを完了します。 アップグレードが完了すると、クラスターで修復マネージャー システム サービスが実行されていることを確認できます。 このサービスは、Service Fabric Explorer のシステム サービス セクションに `fabric:/System/RepairManagerService` という名前で表示されます。 

### <a name="standalone-on-premises-clusters"></a>スタンドアロン オンプレミス クラスター

[スタンドアロン Windows クラスターの構成設定](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)を使用して、新規および既存の Service Fabric クラスターで修復マネージャー サービスを有効にすることができます。

修復マネージャー サービスを有効にするには、次の手順に従います。

1. まず、[一般的なクラスター構成](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations)の `apiversion` が `04-2017` 以上に設定されていることを確認します。

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. 次のように、`fabricSettings` セクションの後に次の `addonFeaturres` セクションを追加して、修復マネージャー サービスを有効にします。

    ```json
    "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "RepairManager"
        ],
    ```

3. これらの変更によってクラスター マニフェストを更新し、更新済みのクラスター マニフェストを使用して、[新しいクラスターを作成](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server)するか、[クラスター構成をアップグレード](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server#Upgrade-the-cluster-configuration)します。 更新済みのクラスター マニフェストを使用してクラスターを実行したら、クラスターで修復マネージャー システム サービスが実行されていることを確認できます。このサービスは、Service Fabric Explorer のシステム サービス セクションに `fabric:/System/RepairManagerService` という名前で表示されます。

### <a name="disable-automatic-windows-update-on-all-nodes"></a>すべてのノードで Windows Update の自動更新を無効にする

Windows Update の自動更新を有効にすると、複数のクラスター ノードが同時に再起動される可能性があるため、可用性が失われる場合があります。 既定では、パッチ オーケストレーション アプリケーションは、クラスター ノードごとに Windows Update の自動更新を無効にします。 ただし、管理者またはグループ ポリシーによって設定が管理されている場合は、"ダウンロードする前に通知する" ように Windows Update ポリシーを明示的に設定することをお勧めします。

### <a name="optional-enable-azure-diagnostics"></a>省略可能: Azure 診断を有効にする

Service Fabric ランタイム バージョン `5.6.220.9494` 以降を実行しているクラスターでは、Service Fabric ログの一部としてパッチ オーケストレーション アプリ ログが収集されます。
Service Fabric ランタイム バージョン `5.6.220.9494` 以降でクラスターが実行されている場合、この手順をスキップすることができます。

バージョン `5.6.220.9494` よりも前の Service Fabric ランタイムを実行しているクラスターの場合、パッチ オーケストレーション アプリのログは、各クラスター ノードのローカルに収集されます。
すべてのノードから中央の場所にログをアップロードするように Azure 診断を構成することをお勧めします。

Azure 診断を有効にする方法については、「[Azure 診断でログを収集する方法](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad)」をご覧ください。

パッチ オーケストレーション アプリケーションのログは、次の固定されたプロバイダー ID で生成されます。

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

Resource Manager テンプレートで、`WadCfg` の下の `EtwEventSourceProviderConfiguration` セクションに移動して、以下のエントリを追加します。

```json
  {
    "provider": "e39b723c-590c-4090-abb0-11e3e6616346",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
      "eventDestination": "PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "fc0028ff-bfdc-499f-80dc-ed922c52c5e9",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "24afa313-0d3b-4c7c-b485-1047fd964b60",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "05dc046c-60e9-4ef7-965e-91660adffa68",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  }
```

> [!NOTE]
> Service Fabric クラスターが複数のノード タイプで構成されている場合は、すべての `WadCfg` セクションに上記のセクションを追加する必要があります。

## <a name="download-the-app-package"></a>アプリ パッケージのダウンロード

このアプリケーションは、[こちらのダウンロード リンク](https://go.microsoft.com/fwlink/P/?linkid=849590)からダウンロードできます。

## <a name="configure-the-app"></a>Configure the app

パッチ オーケストレーション アプリケーションの動作はニーズに合わせて構成できます。 アプリケーションの作成時または更新時にアプリケーション パラメーターを渡して既定値を上書きします。 アプリケーション パラメーターを渡すには、`Start-ServiceFabricApplicationUpgrade` コマンドレットまたは `New-ServiceFabricApplication` コマンドレットに `ApplicationParameter` を指定します。

|**パラメーター**        |**型**                          | **詳細**|
|:-|-|-|
|MaxResultsToCache    |long                              | キャッシュする Windows Update の結果の最大件数。 <br>既定値は 3000 で、次の条件を想定しています。 <br> - ノード数が 20 個である <br> - ノード上で実行される更新の回数が 1 か月あたり 5 回である <br> - 1 回の処理で 10 件程度の結果が生成される <br> - 過去 3 か月の結果を保存する |
|TaskApprovalPolicy   |列挙型 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy は、コーディネーター サービスが、Service Fabric クラスター ノードに Windows Update をインストールする際に使用するポリシーを示しています。<br>                         使用できる値は、以下のとおりです。 <br>                                                           <b>NodeWise</b>:  Windows Update が 1 ノードずつインストールされます。 <br>                                                           <b>UpgradeDomainWise</b>:  Windows Update が 1 アップグレード ドメインずつインストールされます  (最大で、アップグレード ドメインに属するすべてのノードに Windows Update を適用できます)。
|LogsDiskQuotaInMB   |long  <br> (既定値: 1024)               |パッチ オーケストレーション アプリケーションのログの最大サイズ (MB 単位)。このサイズまで、ノードでローカルに保存することができます。
| WUQuery               | string<br>(既定値: "IsInstalled=0")                | Windows 更新プログラムを取得するためのクエリ。 詳細については、[WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx) に関するページをご覧ください。
| InstallWindowsOSOnlyUpdates | ブール値 <br> (既定値: True)                 | このフラグによって、Windows オペレーティング システムの更新プログラムのインストールが許可されます。            |
| WUOperationTimeOutInMinutes | int <br>(既定値: 90)                   | Windows Update 操作 (検索、ダウンロード、インストール) のタイムアウトを指定します。 指定したタイムアウト時間内に操作が完了しなかった場合は、操作が中止されます。       |
| WURescheduleCount     | int <br> (既定値: 5)                  | 操作が繰り返し失敗する場合に、サービスが Windows Update を再スケジュールする最大回数。          |
| WURescheduleTimeInMinutes | int <br>(既定値: 30) | 操作が繰り返し失敗する場合に、サービスが Windows Update を再スケジュールする間隔。 |
| WUFrequency           | コンマ区切りの文字列 (既定値: "Weekly, Wednesday, 7:00:00")     | Windows 更新プログラムをインストールする頻度。 形式と指定できる値は次のとおりです。 <br>-   Monthly, DD,HH:MM:SS (例: Monthly, 5,12:22:32)。 <br> -   Weekly, DAY,HH:MM:SS (例: Weekly, Tuesday, 12:22:32)。  <br> -   Daily, HH:MM:SS (例: Daily, 12:22:32)。  <br> -   None は、Windows Update が実行されないことを示します。  <br><br> 時刻はすべて UTC 形式です。|
| AcceptWindowsUpdateEula | ブール値 <br>(既定値: true) | このフラグを設定すると、コンピューターの所有者に代わって、アプリケーションが Windows Update の使用許諾契約に同意します。              |

> [!TIP]
> Windows Update をすぐに実行する場合は、アプリケーションのデプロイ時間を基準として `WUFrequency` を設定します。 たとえば、5 ノード テスト クラスターがあり、UTC 時刻の午後 5 時頃にアプリケーションをデプロイする予定であるとします。 アプリケーションのアップグレードまたはデプロイに最大 30 分かかると想定している場合、WUFrequency を "Daily, 17:30:00" に設定します。

## <a name="deploy-the-app"></a>アプリケーションのデプロイ

1. 前提条件となるすべての手順を実行してクラスターを準備します。
2. 他の Service Fabric アプリケーションと同様に、パッチ オーケストレーション アプリケーションをデプロイします。 アプリケーションは、PowerShell を使用してデプロイできます。 「[PowerShell を使用してアプリケーションのデプロイと削除を実行する](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)」に記載されている手順に従います。
3. デプロイ時にアプリケーションを構成するには、`ApplicationParamater` を `New-ServiceFabricApplication` コマンドレットに渡します。 利便性のために、アプリケーションと共に Deploy.ps1 スクリプトが用意されています。 このスクリプトを使用するには、次の手順に従います。

    - `Connect-ServiceFabricCluster` を使用して Service Fabric クラスターに接続します。
    - 適切な `ApplicationParameter` 値を指定して、Deploy.ps1 PowerShell スクリプトを実行します。

> [!NOTE]
> このスクリプトは、アプリケーション フォルダー PatchOrchestrationApplication と同じディレクトリに保存してください。

## <a name="upgrade-the-app"></a>アプリケーションのアップグレード

PowerShell を使用して既存のパッチ オーケストレーション アプリケーションをアップグレードするには、「[PowerShell を使用した Service Fabric アプリケーションのアップグレード](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)」に記載されている手順に従います。

## <a name="remove-the-app"></a>アプリケーションの削除

アプリケーションを削除するには、「[PowerShell を使用してアプリケーションのデプロイと削除を実行する](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)」に記載されている手順に従います。

利便性のために、アプリケーションと共に Undeploy.ps1 スクリプトが用意されています。 このスクリプトを使用するには、次の手順に従います。

  - ```Connect-ServiceFabricCluster``` を使用して Service Fabric クラスターに接続します。

  - Undeploy.ps1 PowerShell スクリプトを実行します。

> [!NOTE]
> このスクリプトは、アプリケーション フォルダー PatchOrchestrationApplication と同じディレクトリに保存してください。

## <a name="view-the-windows-update-results"></a>Windows Update の結果の確認

パッチ オーケストレーション アプリケーションは、結果の履歴をユーザーに表示するための REST API を公開しています。 結果の JSON の例を次に示します。
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2017-05-21T11:46:52.1953713Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0
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

JSON のフィールドについて以下で説明します。

フィールド | 値 | 詳細
-- | -- | --
OperationResult | 0 - 成功<br> 1 - 成功 (エラーあり)<br> 2 - 失敗<br> 3 - 中止<br> 4 - タイムアウトにより中止 | 操作全体 (通常、1 つ以上の更新プログラムのインストールを含む) の結果を示します。
ResultCode | OperationResult と同じ | このフィールドは、個々の更新プログラムのインストール操作の結果を示します。
OperationType | 1 - インストール<br> 0 - 検索とダウンロード| インストールは、既定で結果に表示される唯一の OperationType です。
WindowsUpdateQuery | 既定値は "IsInstalled=0" |更新プログラムの検索に使用された Windows 更新プログラムのクエリ。 詳細については、[WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx) に関するページをご覧ください。
RebootRequired | true - 再起動が必要<br> false - 再起動は不要 | 更新プログラムのインストールを完了するのに再起動が必要かどうかを示します。

更新がまだスケジュールされていない場合、結果の JSON は空になります。

Windows Update の結果を照会するには、クラスターにログインします。 次に、コーディネーター サービスのプライマリのレプリカ アドレスを検索し、ブラウザーから http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults にアクセスします。

コーディネーター サービスの REST エンドポイントには動的ポートがあります。 正確な URL を確認するには、Service Fabric Explorer を参照します。 たとえば、`http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults` で結果を入手できます。

![REST エンドポイントの画像](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


クラスターでリバース プロキシが有効になっている場合は、クラスターの外部から URL にアクセスすることもできます。
アクセスする必要があるエンドポイントは、http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults です。

クラスターでリバース プロキシを有効にするには、「[Azure Service Fabric のリバース プロキシ](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)」に記載されている手順に従います。 

> 
> [!WARNING]
> リバース プロキシの構成後は、クラスター内の、HTTP エンドポイントを公開するすべてのマイクロ サービスが、クラスターの外部からアドレス可能になります。

## <a name="diagnosticshealth-events"></a>診断/正常性イベント

### <a name="collect-patch-orchestration-app-logs"></a>パッチ オーケストレーション アプリケーションのログを収集する

ランタイム バージョン `5.6.220.9494` 以降から、パッチ オーケストレーション アプリケーションのログが Service Fabric のログの一部として収集されます。
`5.6.220.9494` より前の Service Fabric ランタイム バージョンを実行しているクラスターでは、次のいずれかの方法を使用してログを収集できます。

#### <a name="locally-on-each-node"></a>個々のノードのローカル

Service Fabric ランタイム バージョンが `5.6.220.9494` よりも前の場合、各 Service Fabric クラスター ノードのローカルでログが収集されます。 ログの場所は、\[Service Fabric\_Installation\_Drive\]:\\PatchOrchestrationApplication\\logs です。

たとえば、Service Fabric が D ドライブにインストールされている場合、このパスは D:\\PatchOrchestrationApplication\\logs になります。

#### <a name="central-location"></a>中央の場所

前提条件となる手順の一環として Azure 診断を構成した場合は、パッチ オーケストレーション アプリケーションのログが Azure Storage で提供されます。

### <a name="health-reports"></a>正常性レポート

パッチ オーケストレーション アプリケーションでは、次の場合にコーディネーター サービスまたはノード エージェント サービスに対しても正常性レポートを発行します。

#### <a name="a-windows-update-operation-failed"></a>Windows Update 操作に失敗した場合

ノードで Windows Update 操作に失敗した場合、ノード エージェント サービスに対して正常性レポートが生成されます。 正常性レポートの詳細に、問題のあるノードの名前が記録されます。

問題のあるノードでパッチが正常に適用されると、レポートは自動的に消去されます。

#### <a name="the-node-agent-ntservice-is-down"></a>ノード エージェント NTService がダウンしている場合

ノードでノード エージェント NTService がダウンしている場合、ノード エージェント サービスに対して警告レベルの正常性レポートが生成されます。

#### <a name="the-repair-manager-service-is-not-enabled"></a>修復マネージャー サービスが有効になっていない場合

クラスターで修復マネージャー サービスが見つからない場合、コーディネーター サービスに対して警告レベルの正常性レポートが生成されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

Q. **パッチ オーケストレーション アプリケーションの実行中に、クラスターがエラー状態になるのはなぜですか?**

A. インストール プロセス中に、パッチ オーケストレーション アプリケーションはノードを無効化または再起動します。これにより、クラスターの正常性が一時的に低下する場合があります。

アプリケーションのポリシーに基づいて、パッチ適用操作中にいずれか 1 つのノードがダウンするか、*または*アップグレード ドメイン全体が同時にダウンすることがあります。

Windows Update のインストールが完了するまでに、ノードは再起動後に再度有効になります。

次の例では、2 つのノードがダウンし、MaxPercentageUnhealthNodes ポリシーに違反しているため、クラスターが一時的にエラー状態になっています。 これはパッチ適用操作が完了するまでの一時的なエラーです。

![異常なクラスターの画像](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

問題が解決しない場合は、「トラブルシューティング」を参照してください。

Q. **パッチ オーケストレーション アプリケーションが警告状態になっています。**

A. アプリケーションに対してポストされた正常性レポートが根本的な原因であるかどうかを確認してください。 通常、警告には問題の詳細が含まれています。 問題が一時的なものであれば、アプリケーションはこの状態から自動的に回復すると考えられます。

Q. **クラスターに異常があり、オペレーティング システムの更新をすぐに実行する必要がある場合はどうすればよいですか?**

A. クラスターに異常がある間は、パッチ オーケストレーション アプリケーションによって更新プログラムはインストールされません。 パッチ オーケストレーション アプリケーションのワークフローを再開させるために、クラスターを正常な状態にしてください。

Q. **クラスター全体でのパッチの適用に非常に時間がかかるのはなぜですか?**

A. パッチ オーケストレーション アプリケーションが必要とする時間は、主に次の要素に左右されます。

- コーディネーター サービスのポリシー。 
  - 既定のポリシー (`NodeWise`) では、パッチの適用は 1 ノードずつしか実行されません。 特に大規模なクラスターの場合、クラスター全体でのパッチの適用時間を短縮するために、`UpgradeDomainWise` ポリシーを使用することをお勧めします。
- ダウンロードしてインストールできる更新プログラムの数。 
- 更新プログラムをダウンロードしてインストールするために必要な平均時間。平均時間が 2 ～ 3 時間を超えることは通常ありません。
- VM とネットワーク帯域幅のパフォーマンス。

Q. **Windows Update の結果で一部の更新プログラムが REST API 経由で取得されたと表示されますが、マシンの Windows Update 履歴に表示されないのはなぜですか?**

A. 一部の製品更新プログラムは、各更新プログラム/パッチ履歴で確認する必要があります。 たとえば、Windows Defender の更新プログラムは、Windows Server 2016 の Windows Update 履歴には表示されません。

## <a name="disclaimers"></a>免責事項

- ユーザーに代わって、パッチ オーケストレーション アプリケーションが Windows Update の使用許諾契約に同意します。 必要に応じて、アプリケーションの構成でこの設定をオフにすることもできます。

- パッチ オーケストレーション アプリケーションは、使用状況とパフォーマンスを追跡するためにテレメトリを収集します。 アプリケーションのテレメトリの設定は、Service Fabric ランタイムのテレメトリ設定 (既定でオン) に従います。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="a-node-is-not-coming-back-to-up-state"></a>ノードが稼働状態に戻らない

**ノードが無効状態に陥る考えられる原因**:

安全性チェックが保留中になっています。 この状況を改善するには、十分な数のノードが正常な状態で稼働するように対策を講じてください。

**ノードが無効状態に陥る考えられる原因**:

- ノードが手動で無効化された。
- 進行中の Azure インフラストラクチャ ジョブによってノードが無効化された。
- ノードにパッチを適用するために、パッチ オーケストレーション アプリケーションによってノードが一時的に無効化された。

**ノードがダウン状態に陥る考えられる原因**:

- ノードが手動でダウン状態にされた。
- ノードが再起動されている (パッチ オーケストレーション アプリケーションによって再起動がトリガーされた可能性があります)。
- VM またはコンピューターの障害あるいはネットワーク接続の問題が原因でノードがダウンしている。

### <a name="updates-were-skipped-on-some-nodes"></a>一部のノードで更新がスキップされた

パッチ オーケストレーション アプリケーションは、再スケジュール ポリシーに従って、Windows Update をインストールします。 サービスはアプリケーション ポリシーに従って、ノードを復旧し、更新をスキップします。

このような場合、ノード エージェント サービスに対して警告レベルの正常性レポートが生成されます。 Windows Update の結果にも、エラーの考えられる原因が記録されます。

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>更新プログラムのインストール中にクラスターがエラー状態になる

Windows Update の問題によって、特定のノードまたはアップグレード ドメインでアプリケーションやクラスターの正常性が低下する場合があります。 パッチ オーケストレーション アプリケーションは、クラスターが正常な状態に戻るまで、以降の Windows Update 操作を中止します。

管理者が介入し、Windows Update によってアプリケーションやクラスターが異常な状態になった原因を特定する必要があります。

## <a name="release-notes"></a>リリース ノート

### <a name="version-110"></a>バージョン 1.1.0
- 公開リリース

### <a name="version-111"></a>バージョン 1.1.1
- NodeAgentNTService をインストールできない NodeAgentService の SetupEntryPoint のバグを修正しました。

### <a name="version-120-latest"></a>バージョン 1.2.0 (最新)

- システム再起動ワークフローに関連するバグを修正しました。
- 修復タスクの準備中に正常性チェックが予定どおりに実行されないために発生する RM タスク作成時のバグを修正しました。
- Windows サービス POANodeSvc のスタートアップ モードを auto から delayed-auto に変更しました。

