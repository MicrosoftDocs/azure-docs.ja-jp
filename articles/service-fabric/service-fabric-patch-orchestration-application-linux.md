---
title: Linux 用の Azure Service Fabric パッチ オーケストレーション アプリケーション | Microsoft Docs
description: Linux Service Fabric クラスターでオペレーティング システムへのパッチの適用を自動化するためのアプリケーション。
services: service-fabric
documentationcenter: .net
author: novino
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/22/2018
ms.author: nachandr
ms.openlocfilehash: 5efcc92bc2054dfb66b5fe03ae083c49f924d2ce
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668196"
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Service Fabric クラスターでの Linux オペレーティング システムへのパッチの適用

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

パッチ オーケストレーション アプリケーションは、ダウンタイムなしで、Service Fabric クラスターでのオペレーティング システムへのパッチの適用を自動化する Azure Service Fabric アプリケーションです。

パッチ オーケストレーション アプリケーションは、次の機能を備えています。

- **オペレーティング システムの更新プログラムの自動インストール:**  オペレーティング システムの更新プログラムが自動的にダウンロードされ、インストールされます。 必要に応じてクラスター ノードが再起動されます。クラスターのダウンタイムは発生しません。

- **クラスター対応のパッチ適用と正常性の統合:**  パッチ オーケストレーション アプリケーションは、更新プログラムの適用時にクラスター ノードの正常性を監視します。 クラスター ノードは、1 ノードずつ、または 1 アップグレード ドメインずつアップグレードされます。 パッチの適用プロセスが原因でクラスターの正常性が低下すると、問題の悪化を防ぐためにパッチの適用が停止されます。

## <a name="internal-details-of-the-app"></a>アプリケーションの内部の詳細

パッチ オーケストレーション アプリケーションは、次のサブコンポーネントで構成されます。

- **コーディネーター サービス**:このサービスの役割は次のとおりです。
    - クラスター全体における OS 更新ジョブを調整する。
    - 完了した OS 更新操作の結果を保存する。
- **ノード エージェント サービス**:このステートレス サービスは、すべての Service Fabric クラスター ノードで実行されます。 このサービスは次の役割を担います。
    - Linux でノード エージェント デーモンをブートストラップする。
    - デーモン サービスを監視する。
- **ノード エージェント デーモン**: この Linux デーモン サービスは、上位の特権 (ルート) で実行されます。 一方、ノード エージェント サービスとコーディネーター サービスは、下位の特権で実行されます。 このサービスは、全クラスター ノードで次の更新ジョブを実行する役割を担います。
    - ノードの OS の自動更新を無効にする。
    - ユーザーが指定したポリシーに従って、OS 更新プログラムをダウンロードしてインストールする。
    - OS 更新プログラムのインストール後に必要に応じてマシンを再起動する。
    - OS 更新の結果をコーディネーター サービスにアップロードする。
    - 再試行回数の上限に達しても、操作が正常に完了しなかった場合に正常性レポートを生成する。

> [!NOTE]
> パッチ オーケストレーション アプリケーションでは、Service Fabric の修復マネージャー システム サービスを使用して、ノードを無効または有効にし、正常性チェックを実行します。 パッチ オーケストレーション アプリケーションによって作成される修復タスクによって、各ノードの OS 更新の進行状況が追跡されます。

## <a name="prerequisites"></a>前提条件

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Azure VM で、Ubuntu 16.04 が実行されていることを確認する
このドキュメントの執筆時点では、サポートされているバージョンは Ubuntu 16.04 (`Xenial Xerus`) のみです。

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-62x-and-above"></a>Service Fabric Linux クラスターがバージョン 6.2.x 以降であることを確認する

パッチ オーケストレーション アプリケーション Linux では、Service Fabric ランタイム バージョン 6.2.x 以降でのみ使用できる特定のランタイム機能を使用します。

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>修復マネージャー サービスを有効にする (まだ実行されていない場合)

パッチ オーケストレーション アプリケーションを使用するには、クラスターで修復マネージャー システム サービスを有効にする必要があります。

#### <a name="azure-clusters"></a>Azure クラスター

Azure Linux クラスターの持続性層がシルバーおよびゴールドの場合は、修復マネージャー サービスが既定で有効になっています。 Azure クラスターの持続性層がブロンズの場合、既定では修復マネージャー サービスは有効になっていません。 サービスが既に有効になっている場合、Service Fabric Explorer のシステム サービス セクションでサービスが実行されていることを確認できます。

##### <a name="azure-portal"></a>Azure ポータル
クラスターの設定時に Azure Portal から修復マネージャーを有効にすることができます。 クラスターの構成時に **[アドオン機能]** で **[Repair Manager を含める]** オプションを選択します。
![Azure Portal から修復マネージャーを有効にする画像](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager デプロイ モデル
[Azure Resource Manager デプロイ モデル](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)を使用して、新規および既存の Service Fabric クラスターで修復マネージャー サービスを有効にすることもできます。 デプロイするクラスター用テンプレートを用意します。 サンプル テンプレートを使用することも、カスタムの Azure Resource Manager デプロイ モデル テンプレートを作成することもできます。 

[Azure Resource Manager デプロイ モデル テンプレート](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)を使用して修復マネージャー サービスを有効にするには:

1. まず、`Microsoft.ServiceFabric/clusters` リソースの `apiversion` が `2017-07-01-preview` に設定されていることを確認します。 値が異なる場合は、`apiVersion` を `2017-07-01-preview` 以上に更新する必要があります。

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

スタンドアロンの Service Fabric Linux クラスターは、このドキュメントの執筆時点ではサポートされていません。

### <a name="disable-automatic-os-update-on-all-nodes"></a>すべてのノードで OS の自動更新を無効にする

OS の自動更新を行うと、可用性が失われたり、実行中のアプリケーションの動作が変更されたりする可能性があります。 既定では、パッチ オーケストレーション アプリケーションは、クラスター ノードごとに OS の自動更新を無効にしてこのようなシナリオを回避します。
Ubuntu の場合、[unattended-upgrades](https://help.ubuntu.com/community/AutomaticSecurityUpdates) はパッチ オーケストレーション アプリケーションによって自動的に無効にされます。

## <a name="download-the-app-package"></a>アプリ パッケージのダウンロード

インストール スクリプトを備えたアプリケーションは、[アーカイブ リンク](https://go.microsoft.com/fwlink/?linkid=867984)からダウンロード可能です。

sfpkg 形式のアプリケーションは、[sfpkg リンク](https://aka.ms/POA/POA_v2.0.2.sfpkg)からダウンロード可能です。 これは、[Azure Resource Manager に基づくアプリケーションのデプロイ](service-fabric-application-arm-resource.md)に便利です。

## <a name="configure-the-app"></a>Configure the app

パッチ オーケストレーション アプリケーションの動作はニーズに合わせて構成できます。 アプリケーションの作成時または更新時にアプリケーション パラメーターを渡して既定値をオーバーライドします。 アプリケーション パラメーターを渡すには、`Start-ServiceFabricApplicationUpgrade` コマンドレットまたは `New-ServiceFabricApplication` コマンドレットに `ApplicationParameter` を指定します。

|**パラメーター**        |**Type**                          | **詳細**|
|:-|-|-|
|MaxResultsToCache    |long                              | キャッシュする更新結果の最大件数。 <br>既定値は 3000 で、次の条件を想定しています。 <br> - ノード数が 20 個である <br> - ノード上で実行される更新の回数が 1 か月あたり 5 回である <br> - 1 回の処理で 10 件程度の結果が生成される <br> - 過去 3 か月の結果を保存する |
|TaskApprovalPolicy   |列挙型 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy は、コーディネーター サービスが、Service Fabric クラスター ノードに更新プログラムをインストールする際に使用するポリシーを示しています。<br>                         使用できる値は、以下のとおりです。 <br>                                                           <b>NodeWise</b>:  更新プログラムが 1 ノードずつインストールされます。 <br>                                                           <b>UpgradeDomainWise</b>:  更新プログラムが 1 アップグレード ドメインずつインストールされます  (最大で、アップグレード ドメインに属するすべてのノードに更新プログラムを適用できます)。
| UpdateOperationTimeOutInMinutes | int <br>(既定値:180)                   | 更新操作 (ダウンロードまたはインストール) のタイムアウトを指定します。 指定したタイムアウト時間内に操作が完了しなかった場合は、操作が中止されます。       |
| RescheduleCount      | int <br> (既定値:5)                  | 操作が繰り返し失敗する場合に、サービスが OS 更新を再スケジュールする最大回数。          |
| RescheduleTimeInMinutes  | int <br>(既定値:30) | 操作が繰り返し失敗する場合に、サービスが OS 更新を再スケジュールする間隔。 |
| UpdateFrequency           | コンマ区切りの文字列 (既定値:"Weekly, Wednesday, 7:00:00")     | クラスターでの OS 更新プログラムのインストールの頻度。 形式と指定できる値は次のとおりです。 <br>-   Monthly, DD, HH:MM:SS (例: Monthly, 5, 12:22:32)。 <br> -   Weekly, DAY,HH:MM:SS (例: Weekly, Tuesday, 12:22:32)。  <br> -   Daily, HH:MM:SS (例: Daily, 12:22:32)。  <br> -   None は、更新が実行されないことを示します。  <br><br> 時刻はすべて UTC 形式です。|
| UpdateClassification | コンマ区切りの文字列 (既定値: "securityupdates") | クラスター ノードにインストールする必要のある更新プログラムの種類。 指定できる値は、securityupdates, all です。 <br> -  securityupdates - セキュリティ更新プログラムのみをインストールします <br> -  all - apt から利用可能な更新プログラムをすべてインストールします。|
| ApprovedPatches | コンマ区切りの文字列 (既定値: "") | これは、クラスター ノードにインストールする必要がある承認済み更新プログラムの一覧です。 コンマ区切りリストには、承認されているパッケージと、必要に応じて目的のターゲット バージョンが含まれています。<br> 例: "apt-utils = 1.2.10ubuntu1, python3-jwt, apt-transport-https < 1.2.194, libsystemd0 >= 229-4ubuntu16" <br> 上記によって、次がインストールされます <br> - バージョン 1.2.10ubuntu1 の apt-utils (apt-cache で使用可能な場合)。 この特定のバージョンを使用できない場合は、何も行われません。 <br> - python3-jwt は利用可能な最新バージョンにアップグレードされます。 パッケージが存在しない場合は、何も行われません。 <br> - apt-transport-https は最新バージョン (1.2.194 未満) にアップグレードされます。 このバージョンが存在しない場合は、何も行われません。 <br> - libsystemd0 は最新バージョン (229-4ubuntu16 以上) にアップグレードされます。 このようなバージョンが存在しない場合は、何も行われません。|
| RejectedPatches | コンマ区切りの文字列 (既定値: "") | これは、クラスター ノードにインストールしない更新プログラムの一覧です <br> 例: "bash, sudo" <br> 前述の例では、bash、sudo が更新プログラムの受信から除外されます。 |


> [!TIP]
> OS 更新をすぐに実行する場合は、アプリケーションのデプロイ時間を基準として `UpdateFrequency` を設定します。 たとえば、5 ノード テスト クラスターがあり、UTC 時刻の午後 5 時頃にアプリケーションをデプロイする予定であるとします。 アプリケーションのアップグレードまたはデプロイに最大 30 分かかると想定している場合、UpdateFrequency を "Daily, 17:30:00" に設定します。

## <a name="deploy-the-app"></a>アプリケーションのデプロイ

1. 前提条件となるすべての手順を実行してクラスターを準備します。
2. 他の Service Fabric アプリケーションと同様に、パッチ オーケストレーション アプリケーションをデプロイします。 PowerShell または Azure Service Fabric CLI を使用してアプリケーションをデプロイすることができます。 「[PowerShell を使用してアプリケーションのデプロイと削除を実行する](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)」または [Azure Service Fabric CLI を使用したアプリケーションのデプロイ](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)に関するページに記載されている手順に従います
3. デプロイ時にアプリケーションを構成するには、`ApplicationParameter` を `New-ServiceFabricApplication` コマンドレットまたは指定されたスクリプトに渡します。 便宜を図るために、powershell (Deploy.ps1) および bash (Deploy.sh) スクリプトがアプリケーションと共に提供されます。 このスクリプトを使用するには、次の手順に従います。

    - Service Fabric クラスターに接続します。
    - デプロイ スクリプトを実行します。 必要に応じて、アプリケーション パラメーターをスクリプトに渡します。 例: .\Deploy.ps1 -ApplicationParameter @{ UpdateFrequency = "Daily, 11:00:00"} OR ./Deploy.sh "{\"UpdateFrequency\":\"Daily, 11:00:00\"}" 

> [!NOTE]
> このスクリプトは、アプリケーション フォルダー PatchOrchestrationApplication と同じディレクトリに保存してください。

## <a name="upgrade-the-app"></a>アプリケーションのアップグレード

既存のパッチ オーケストレーション アプリケーションをアップグレードするには、「[PowerShell を使用した Service Fabric アプリケーションのアップグレード](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)」または [Azure Service Fabric CLI を使用した Service Fabric アプリケーションのアップグレード](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)に関するページ記載されている手順に従います

## <a name="remove-the-app"></a>アプリケーションの削除

アプリケーションを削除するには、「[PowerShell を使用してアプリケーションのデプロイと削除を実行する](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)」または [Azure Service Fabric CLI を使用したアプリケーションの削除](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)に関するページに記載されている手順に従います

便宜を図るために、powershell (Undeploy.ps1) および bash (Undeploy.sh) スクリプトがアプリケーションと共に提供されます。 このスクリプトを使用するには、次の手順に従います。

  - Service Fabric クラスターに接続します。
  - スクリプト Undeploy.ps1 または Undeploy.sh を実行します

> [!NOTE]
> このスクリプトは、アプリケーション フォルダー PatchOrchestrationApplication と同じディレクトリに保存してください。

## <a name="view-the-update-results"></a>更新結果の確認

パッチ オーケストレーション アプリケーションは、結果の履歴をユーザーに表示するための REST API を公開しています。 サンプル結果を次に示します。```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
```json
[ 
  { 
    "NodeName": "_bronze_0", 
    "UpdateOperationResults": [ 
      { 
        "OperationResult": "succeeded", 
        "NodeName": "_bronze_0", 
        "OperationTime": "2017-11-21T12:39:29.0435917Z", 
        "UpdateDetails": [ 
          { 
            "UpdateId": "linux-cloud-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-headers-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-image-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "python3-apport:amd64=2.20.1-0ubuntu2.13", 
            "ResultCode": "succeeded" 
          }, 
        ], 
        "OperationType": "installation", 
        "UpdateClassification": "securityupdates", 
        "UpdateFrequency": "Daily, 7:00:00", 
        "RebootRequired": true, 
        "ApprovedList": "", 
        "RejectedList": "" 
      } 
    ] 
  } 
] 
```

JSON のフィールドについて以下で説明します。

フィールド | 値 | 詳細
-- | -- | --
OperationResult | 0 - 成功<br> 1 - 成功 (エラーあり)<br> 2 - 失敗<br> 3 - 中止<br> 4 - タイムアウトにより中止 | 操作全体 (通常、1 つ以上の更新プログラムのインストールを含む) の結果を示します。
ResultCode | OperationResult と同じ | このフィールドは、個々の更新プログラムのインストール操作の結果を示します。
OperationType | 1 - インストール<br> 0 - 検索とダウンロード| インストールは、既定で結果に表示される唯一の OperationType です。
UpdateClassification | 既定値は "securityupdates" | 更新操作中にインストールされる更新プログラムの種類
UpdateFrequency | 既定値は "Weekly, Wednesday, 7:00:00" | この更新に対して構成されている更新頻度。
RebootRequired | true - 再起動が必要<br> false - 再起動は不要 | 更新プログラムのインストールを完了するのに再起動が必要かどうかを示します。
ApprovedList | 既定値は "" | この更新に対して承認されているパッチの一覧
RejectedList | 既定値は "" | この更新に対して拒否されているパッチの一覧

更新がまだスケジュールされていない場合、結果の JSON は空になります。

更新の結果を照会するには、クラスターにログインします。 次に、コーディネーター サービスのプライマリのレプリカ アドレスを検索し、ブラウザーから http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults にアクセスします。

コーディネーター サービスの REST エンドポイントには動的ポートがあります。 正確な URL を確認するには、Service Fabric Explorer を参照します。 たとえば、`http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults` で結果を入手できます。

![REST エンドポイントの画像](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>診断/正常性イベント

### <a name="diagnostic-logs"></a>診断ログ

パッチ オーケストレーション アプリケーションのログが Service Fabric のランタイム ログの一部として収集されます。

任意の診断ツール/パイプラインを経由してログを取り込む必要がある場合は、 パッチ オーケストレーション アプリケーションは以下の固定されたプロバイダー ID を使用して [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0) 経由でイベントを記録します

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>正常性レポート

パッチ オーケストレーション アプリケーションでは、次の場合にコーディネーター サービスまたはノード エージェント サービスに対しても正常性レポートを発行します。

#### <a name="an-update-operation-failed"></a>更新操作に失敗した場合

ノードで更新操作に失敗した場合、ノード エージェント サービスに対して正常性レポートが生成されます。 正常性レポートの詳細に、問題のあるノードの名前が記録されます。

問題のあるノードでパッチが正常に適用されると、レポートは自動的に消去されます。

#### <a name="the-node-agent-daemon-service-is-down"></a>ノード エージェント デーモン サービスがダウンしている場合

ノードでノード エージェント デーモン サービスがダウンしている場合、ノード エージェント サービスに対して警告レベルの正常性レポートが生成されます。

#### <a name="the-repair-manager-service-is-not-enabled"></a>修復マネージャー サービスが有効になっていない場合

クラスターで修復マネージャー サービスが見つからない場合、コーディネーター サービスに対して警告レベルの正常性レポートが生成されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

Q. **パッチ オーケストレーション アプリケーションの実行中に、クラスターがエラー状態になるのはなぜですか?**

A. インストール プロセス中に、パッチ オーケストレーション アプリケーションはノードを無効化または再起動します。 この操作によって、クラスターの正常性が一時的に低下します。

アプリケーションのポリシーに基づいて、パッチ適用操作中にいずれか 1 つのノードがダウンするか、*または*アップグレード ドメイン全体が同時にダウンすることがあります。

インストールが完了するまでに、ノードは再起動後にもう一度有効になります。

次の例では、2 つのノードがダウンし、MaxPercentageUnhealthNodes ポリシーに違反しているため、クラスターが一時的にエラー状態になっています。 これはパッチ適用操作が完了するまでの一時的なエラーです。

![異常なクラスターの画像](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

問題が解決しない場合は、「トラブルシューティング」を参照してください。

Q. **パッチ オーケストレーション アプリケーションが警告状態になっています。**

A. アプリケーションに対してポストされた正常性レポートが根本的な原因であるかどうかを確認してください。 通常、警告には問題の詳細が含まれています。 問題が一時的なものであれば、アプリケーションはこの状態から自動的に回復すると考えられます。

Q. **クラスターに異常があり、オペレーティング システムの更新をすぐに実行する必要がある場合はどうすればよいですか?**

A. クラスターに異常がある間は、パッチ オーケストレーション アプリケーションによって更新プログラムはインストールされません。 パッチ オーケストレーション アプリケーションのワークフローをブロック解除するには、クラスターを正常な状態にしてください。

Q. **クラスター全体でのパッチの適用に非常に時間がかかるのはなぜですか?**

A. パッチ オーケストレーション アプリケーションが必要とする時間は、主に次の要素に左右されます。

- コーディネーター サービスのポリシー。 
  - 既定のポリシー (`NodeWise`) では、パッチの適用は 1 ノードずつしか実行されません。 特に大規模なクラスターがある場合は、クラスター全体でのパッチの適用時間を短縮するために、`UpgradeDomainWise` ポリシーを使用することをお勧めします。
- ダウンロードしてインストールできる更新プログラムの数。 
- 更新プログラムをダウンロードしてインストールするために必要な平均時間。平均時間が 2 ～ 3 時間を超えることは通常ありません。
- VM とネットワーク帯域幅のパフォーマンス。

Q. **パッチ オーケストレーション アプリケーションでは、どの更新プログラムがセキュリティ更新プログラムであるかはどのように判断されますか。**

A. パッチ オーケストレーション アプリケーションは、使用可能な更新プログラムのうちどれがセキュリティ更新プログラムであるかの判断にディストリビューション固有のロジックを使用します。 例: Ubuntu では、アプリケーションはアーカイブ $RELEASE-security、$RELEASE-updates ($RELEASE = xenial または linux 標準ベース リリース バージョン) から更新プログラムを検索します。 

 
Q. **パッケージを特定のバージョンにロックするにはどのようにしますか?**

A. パッケージを特定のバージョンにロックするには ApprovedPatches 設定を使用します。 


Q. **Ubuntu で自動更新を有効にするとどうなりますか?**

A. クラスターでパッチ オーケストレーション アプリケーションをインストールするとすぐに、クラスター ノード上の unattended-upgrades が無効になります。 すべての定期的な更新ワークフローは、パッチ オーケストレーション アプリケーションによって駆動されます。
クラスター全体にわたって環境の一貫性を確保するために、パッチ オーケストレーション アプリケーションのみを使用して更新プログラムをインストールすることをお勧めします。 
 
Q. **アップグレード後に、オーケストレーション アプリケーションは使用されていないパッケージのクリーンアップを行いますか?**

A. はい、クリーンアップはインストール後の手順の一部として行われます。 

Q. **パッチ オーケストレーション アプリを、自分の開発クラスター (1 ノード クラスター) にパッチを適用するために使用できますか?**

A. いいえ、パッチ オーケストレーション アプリは、1 ノード クラスターへのパッチ適用には使用できません。 この制限は設計によるものです。パッチ適用の修復ジョブは、[Service Fabric のシステム サービス](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services)または任意の顧客アプリにダウンタイムが発生するため、いずれも修復マネージャーからの承認が得られないからです。

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

パッチ オーケストレーション アプリケーションは、再スケジュール ポリシーに従って、更新プログラムをインストールします。 サービスはアプリケーション ポリシーに従って、ノードを復旧し、更新をスキップします。

このような場合、ノード エージェント サービスに対して警告レベルの正常性レポートが生成されます。 更新の結果にも、エラーの考えられる原因が記録されます。

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>更新プログラムのインストール中にクラスターがエラー状態になる

更新プログラムの問題によって、特定のノードまたはアップグレード ドメインでアプリケーションやクラスターの正常性が低下する場合があります。 パッチ オーケストレーション アプリケーションは、クラスターが正常な状態に戻るまで、以降の更新操作を中止します。

管理者が介入し、インストールされた更新プログラムによってアプリケーションやクラスターが異常な状態になった原因を特定する必要があります。

## <a name="disclaimer"></a>免責事項

パッチ オーケストレーション アプリケーションは、使用状況とパフォーマンスを追跡するためにテレメトリを収集します。 アプリケーションのテレメトリの設定は、Service Fabric ランタイムのテレメトリ設定 (既定でオン) に従います。

## <a name="release-notes"></a>リリース ノート

### <a name="version-010"></a>バージョン 0.1.0
- プライベート プレビュー リリース

### <a name="version-200"></a>バージョン 2.0.0
- 公開リリース

### <a name="version-201"></a>バージョン 2.0.1
- 最新の Service Fabric SDK を使用してアプリを再コンパイル

### <a name="version-202-latest"></a>バージョン 2.0.2 (最新)
- 再起動時に正常性の警告が残る問題を修正しました。
