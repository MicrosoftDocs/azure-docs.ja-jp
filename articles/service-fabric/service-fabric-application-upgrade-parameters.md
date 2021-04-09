---
title: 'アプリケーション アップグレード: アップグレード パラメーター'
description: Service Fabric アプリケーションのアップグレードに関連するパラメーターについて説明します。正常性確認やアップグレードを自動的に取り消すポリシーなどが含まれます。
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 6b6116bf1188fcf191b2d672e6c698bb3c050e6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018479"
---
# <a name="application-upgrade-parameters"></a>アプリケーション アップグレードのパラメーター
この記事では、Azure Service Fabric アプリケーションのアップグレード中に適用されるさまざまなパラメーターについて説明します。 アプリケーション アップグレードのパラメーターは、アップグレード時に適用されるタイムアウトと正常性チェックを制御します。また、パラメーターには、アップグレードの失敗時に適用する必要があるポリシーを指定します。 アプリケーション パラメーターは、以下を使用したアップグレードに適用されます。
- PowerShell
- Visual Studio
- SFCTL
- [REST](/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

アプリケーション アップグレードは、ユーザーが選択できる 3 つのアップグレード モードのいずれかを使用して開始されます。 各モードには、独自のアプリケーション パラメーター セットがあります。
- Monitored
- 監視対象外の自動
- 監視対象外の手動

適用できる必須または省略可能なパラメーターについては、以下の各セクションをご覧ください。

## <a name="visual-studio-and-powershell-parameters"></a>Visual Studio および PowerShell パラメーター

PowerShell を使用する Service Fabric アプリケーションのアップグレードでは、[Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) コマンドを使用します。 アップグレード モードを選択するには、**Monitored**、**UnmonitoredAuto**、または **UnmonitoredManual** パラメーターを [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) に渡します。

Visual Studio Service Fabric のアプリケーション アップグレードのパラメーターを設定するには、Visual Studio の [アップグレード設定] ダイアログ ボックスで設定します。 Visual Studio のアップグレード モードを選択するには、 **[アップグレード モード]** ドロップダウン ボックスで **[Monitored]** 、 **[UnmonitoredAuto]** 、または **[UnmonitoredManual]** のいずれかを選択します。 詳細については、「[Visual Studio での Service Fabric アプリケーションのアップグレードの構成](service-fabric-visualstudio-configure-upgrade.md)」を参照してください。

### <a name="required-parameters"></a>必須のパラメーター
(PS=PowerShell、VS=Visual Studio)

| パラメーター | 適用対象 | 説明 |
| --- | --- | --- |
ApplicationName |PS| アップグレードするアプリケーションの名前です。 例: fabric:/VisualObjects、fabric:/ClusterMonitor。 |
ApplicationTypeVersion|PS|ターゲットをアップグレードするアプリケーションの種類のバージョンです。 |
FailureAction |PS、VS|使用できる値は、**Rollback**、**Manual**、および **Invalid** です。 *Monitored* アップグレードで監視ポリシー違反または正常性ポリシー違反が発生した場合に実行する補正アクションです。 <br>**Rollback** は、アップグレードによってアップグレード前のバージョンに自動的にロールバックされることを示します。 <br>**Manual** は、アップグレードが *UnmonitoredManual* アップグレード モードに切り替わることを示します。 <br>**Invalid** は、失敗アクションが無効であることを示します。|
Monitored |PS|アップグレード モードが監視対象であることを示します。 コマンドレットによってアップグレード ドメインのアップグレードを完了した後、定義した正常性ポリシーをアップグレード ドメインとクラスターの正常性が満たしている場合、Service Fabric は次のアップグレード ドメインをアップグレードします。 アップグレード ドメインまたはクラスターが正常性ポリシーを満たしていない場合、アップグレードは失敗し、Service Fabric は指定されたポリシーに従って、アップグレード ドメインのアップグレードをロールバックするか、手動モードに戻ります。 これは、運用環境でのアプリケーションのアップグレードに推奨されるモードです。 |
UpgradeMode | VS | 使用できる値は、**Monitored** (既定値)、**UnmonitoredAuto**、または **UnmonitoredManual** です。 詳細については、この記事の各モードの PowerShell パラメーターを参照してください。 |
UnmonitoredAuto | PS | アップグレード モードが監視対象外の自動であることを示します。 Service Fabric がアップグレード ドメインをアップグレードした後、アプリケーションの正常性の状態に関係なく、Service Fabric は次のアップグレード ドメインをアップグレードします。 このモードは運用環境にはお勧めできません。アプリケーションの開発中にのみ役に立ちます。 |
UnmonitoredManual | PS | アップグレード モードが監視対象外の手動であることを示します。 Service Fabric は、アップグレード ドメインをアップグレードした後、ユーザーが *Resume-ServiceFabricApplicationUpgrade* コマンドレットを使用して次のアップグレード ドメインをアップグレードするまで待機します。 |

### <a name="optional-parameters"></a>省略可能なパラメーター

正常性の評価パラメーターは省略可能です。 アップグレードを開始するときに、正常性評価の基準が指定されていない場合、Service Fabric はアプリケーション インスタンスの ApplicationManifest.xml で指定されているアプリケーションの正常性ポリシーを使用します。

> [!div class="mx-tdBreakAll"]
> | パラメーター | 適用対象 | 説明 |
> | --- | --- | --- |
> | ApplicationParameter |PS、VS| アプリケーション パラメーターのオーバーライドを指定します。<br>PowerShell のアプリケーション パラメーターは、ハッシュテーブルの名前と値のペアとして指定されます。 たとえば、@{ "VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1" } です。<br>Visual Studio のアプリケーション パラメーターは、[Service Fabric アプリケーションの発行] ダイアログの **[アプリケーション パラメーター ファイル]** フィールドで指定できます。
> | Confirm |PS| 使用可能な値: **True** および **False**。 コマンドレットの実行前に確認メッセージを表示します。 |
> | ConsiderWarningAsError |PS、VS |使用可能な値: **True** および **False**。 既定値は **False** です。 アップグレード中に、アプリケーションの正常性を評価するときに、アプリケーションの警告正常性状態をエラーとして扱います。 既定では、Service Fabric は警告イベントがある場合でもアップグレードを続行できるように、エラーになるイベントの警告正常性のイベントは評価しません。 |
> | DefaultServiceTypeHealthPolicy | PS、VS |MaxPercentUnhealthyPartitionsPerService、MaxPercentUnhealthyReplicasPerPartition、MaxPercentUnhealthyServices の形式で、監視対象のアップグレードに使用する既定のサービスの種類に対して正常性ポリシーを指定します。 たとえば、5,10,15 は、MaxPercentUnhealthyPartitionsPerService = 5、MaxPercentUnhealthyReplicasPerPartition = 10、MaxPercentUnhealthyServices = 15 という値を示します。 |
> | Force | PS、VS | 使用可能な値: **True** および **False**。 バージョン番号が変わっていない場合でも、アップグレード プロセスが警告メッセージをスキップし、アップグレードを強制することを示します。 これは、ローカルのテストには便利ですが、既存の展開を削除する必要があり、ダウンタイムやデータ損失が発生するため、運用環境での使用はお勧めしません。 |
> | ForceRestart |PS、VS |サービス コードを更新せずに構成やデータ パッケージを更新する場合、サービスは、ForceRestart プロパティが **True** に設定されている場合にのみ再起動されます。 更新が完了すると、Service Fabric は新しい構成パッケージやデータ パッケージを使用可能なサービスを知らせます。 サービスは、変更を適用する役割を担います。 必要に応じて、サービス自体を再起動できます。 |
> | HealthCheckRetryTimeoutSec |PS、VS |アップグレードを失敗として宣言する前に、Service Fabric が正常性評価を実行し続ける期間 (秒) です。 既定値は 600 秒です。 この期間は *HealthCheckWaitDurationSec* に達した後に開始されます。 Service Fabric は、この *HealthCheckRetryTimeout* 内で、アプリケーションの正常性についての複数のチェックを実行することがあります。 既定値は 10 分です。アプリケーションに合わせてカスタマイズしてください。 |
> | HealthCheckStableDurationSec |PS、VS |次のアップグレード ドメインへの移行またはアップグレードが完了する前に、アプリケーションが安定していることを確認するための時間 (秒)。 この待機期間を使用して、正常性チェックが実行された直後に、正常性の変更が未検出にならないようにします。 既定値は 120 秒です。アプリケーションに合わせてカスタマイズしてください。 |
> | HealthCheckWaitDurationSec |PS、VS | Service Fabric でアプリケーションの正常性を評価する前に、アップグレード ドメインでのアップグレード完了後の待機時間を秒単位で指定します。 この期間は、アプリケーションが正常と見なされる前に、アプリケーションが実行されている必要がある時間としても考慮されます。 正常性チェックに合格すると、アップグレード プロセスは次のアップグレード ドメインに進みます。  正常性チェックに合格しない場合、Service Fabric は *HealthCheckRetryTimeout* に達するまで、[UpgradeHealthCheckInterval](./service-fabric-cluster-fabric-settings.md#clustermanager) の時間を待機してから、正常性チェックをもう一度実行します。 既定値と推奨値は、0 秒です。 |
> | MaxPercentUnhealthyDeployedApplications|PS、VS |既定値と推奨値は、0 です。 アプリケーションが正常でないと見なされ、アップグレードに失敗する前にデプロイされるアプリケーションの最大数 ([正常性のセクション](service-fabric-health-introduction.md)を参照してください) を指定します。 このパラメーターは、ノード上のアプリケーションの正常性を定義するため、アップグレード中の問題を検出するうえで役立ちます。 通常、アプリケーションのレプリカの負荷はその他のノードに分散されます。これにより、アプリケーションは正常に表示され、アップグレードを続行することができます。 厳密な *MaxPercentUnhealthyDeployedApplications* を指定すると、Service Fabric はアプリケーション パッケージで高速に問題を検出し、フェイルファストなアップグレードが行われるようにします。 |
> | MaxPercentUnhealthyServices |PS、VS |*DefaultServiceTypeHealthPolicy* と *ServiceTypeHealthPolicyMap* のパラメーター。 既定値と推奨値は、0 です。 アップグレードが正常でないと見なされてアップグレードに失敗する前に、正常性がなくなることを許可するアプリケーション インスタンス内のサービスの最大数を指定します。 |
> | MaxPercentUnhealthyPartitionsPerService|PS、VS |*DefaultServiceTypeHealthPolicy* と *ServiceTypeHealthPolicyMap* のパラメーター。 既定値と推奨値は、0 です。 サービス内のパーティション最大数に指定すると、サービスが正常でないと見なされる前に、正常でなくなる可能性があります。 |
> | MaxPercentUnhealthyReplicasPerPartition|PS、VS |*DefaultServiceTypeHealthPolicy* と *ServiceTypeHealthPolicyMap* のパラメーター。 既定値と推奨値は、0 です。 パーティションが正常でないと見なされる前に、正常でない状態になるパーティション内のレプリカの最大数を指定します。 |
> | ServiceTypeHealthPolicyMap | PS、VS | あるサービスの種類に属するサービスの正常性を評価するために使用される正常性ポリシーを表します。 ハッシュ テーブルの入力を、@ {"ServiceTypeName" :"MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} For example: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } という形式で受け取ります。 |
> | TimeoutSec | PS、VS | 操作のタイムアウト期間 (秒) を指定します。 |
> | UpgradeDomainTimeoutSec |PS、VS |1 つのアップグレード ドメインをアップグレードするための最大時間 (秒)。 このタイムアウトに達すると、アップグレードは停止され、*FailureAction* の設定に基づいて操作が続行されます。 既定値はなし (無限) です。アプリケーションに合わせてカスタマイズしてください。 |
> | UpgradeReplicaSetCheckTimeoutSec |PS、VS |秒単位で測定されます。<br>**ステートレス サービス**-- Service Fabric は単一のアップグレード ドメイン内で、サービスの追加インスタンスを確実に使用できるようにしようとします。 ターゲット インスタンス数が複数ある場合、最大タイムアウト値になるまで、複数のインスタンスが使用可能になるまで待機します。 このタイムアウトは、*UpgradeReplicaSetCheckTimeoutSec* プロパティを使用して指定されます。 タイムアウトになると、サービス インスタンス数にかかわらず、Service Fabric はアップグレードを続行します。 ターゲット インスタンス数が 1 つの場合、Service Fabric は待機せずに、すぐにアップグレードを実行します。<br><br>**ステートフル サービス**-- Service Fabric は 1 つのアップグレード ドメイン内で、レプリカ セットに確実にクォーラムが含まれるようにしようとします。 Service Fabric は *UpgradeReplicaSetCheckTimeoutSec* プロパティで指定した最大タイムアウト値になるまで、クォーラムが使用可能になるまで待機します。 タイムアウトになると、クォーラムにかかわらずアップグレードを続行します。 この設定は、ロールフォワード時には、"しない (無限)" に設定され、ロールバック時には 1,200 秒に設定されます。 |
> | UpgradeTimeoutSec |PS、VS |全体のアップグレードに適用されるタイムアウト (秒)。 このタイムアウトに達すると、アップグレードは停止され、*FailureAction* がトリガーされます。 既定値はなし (無限) です。アプリケーションに合わせてカスタマイズしてください。 |
> | WhatIf | PS | 使用可能な値: **True** および **False**。 コマンドレットの実行時に発生する内容を示します。 このコマンドレットは実行されません。 |

*MaxPercentUnhealthyServices*、*MaxPercentUnhealthyPartitionsPerService*、*MaxPercentUnhealthyReplicasPerPartition* の基準は、アプリケーション インスタンスのサービスの種類ごとに指定できます。 このパラメーター per-service を設定すると、アプリケーションにさまざまなサービスの種類を含め、それぞれ異なる評価ポリシーを指定することができます。 たとえば、ステートレスなゲートウェイ サービスの種類には、特定のアプリケーション インスタンスのステートフルなエンジン サービスの種類とは異なる *MaxPercentUnhealthyPartitionsPerService* を含めることができます。

## <a name="sfctl-parameters"></a>SFCTL パラメーター

Service Fabric CLI を使用した Service Fabric アプリケーションのアップグレードでは、[sfctl application upgrade](./service-fabric-sfctl-application.md#sfctl-application-upgrade) コマンドと、次に示す必須および省略可能なパラメーターを使用します。

### <a name="required-parameters"></a>必須のパラメーター

| パラメーター | 説明 |
| --- | --- |
| application-id  |アップグレードするアプリケーションの ID です。 <br> これは、通常は、'fabric:' URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。|
application-version |ターゲットをアップグレードするアプリケーションの種類のバージョンです。|
parameters  |アプリケーションのアップグレード時に適用される、アプリケーション パラメーター オーバーライドの JSON でエンコードされた一覧。|

### <a name="optional-parameters"></a>省略可能なパラメーター

| パラメーター | 説明 |
| --- | --- |
default-service-health-policy | サービスの種類の正常性を評価するために既定で使用される正常性ポリシーの [JSON](/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) でエンコードされた仕様。 このマップは、既定では空です。 |
failure-action | 使用できる値は、**Rollback**、**Manual**、および **Invalid** です。 *Monitored* アップグレードで監視ポリシー違反または正常性ポリシー違反が発生した場合に実行する補正アクションです。 <br>**Rollback** は、アップグレードによってアップグレード前のバージョンに自動的にロールバックされることを示します。 <br>**Manual** は、アップグレードが *UnmonitoredManual* アップグレード モードに切り替わることを示します。 <br>**Invalid** は、失敗アクションが無効であることを示します。|
force-restart | サービス コードを更新せずに構成やデータ パッケージを更新する場合、サービスは、ForceRestart プロパティが **True** に設定されている場合にのみ再起動されます。 更新が完了すると、Service Fabric は新しい構成パッケージやデータ パッケージを使用可能なサービスを知らせます。 サービスは、変更を適用する役割を担います。 必要に応じて、サービス自体を再起動できます。 |
health-check-retry-timeout | アプリケーションまたはクラスターが正常でない場合に、正常性評価を再試行する時間。この時間を超えると、*FailureAction* に指定したアクションが実行されます。 最初に、ISO 8601 の期間を表す文字列として解釈されます。 それが失敗した場合、ミリ秒単位の合計数を表す数値として解釈されます。 既定値はPT0H10M0S です。 |
health-check-stable-duration | アプリケーションまたはクラスターが正常な状態である必要がある時間。この時間を超えると、アップグレードが次のアップグレード ドメインに進みます。 最初に、ISO 8601 の期間を表す文字列として解釈されます。 それが失敗した場合、ミリ秒単位の合計数を表す数値として解釈されます。 既定値はPT0H2M0S です。 |
health-check-wait-duration | ドメインのアップグレードを完了後、待機する時間。この時間を超えると、正常性ポリシーが適用されます。 最初に、ISO 8601 の期間を表す文字列として解釈されます。 それが失敗した場合、ミリ秒単位の合計数を表す数値として解釈されます。 既定値は0。|
max-unhealthy-apps | 既定値と推奨値は、0 です。 アプリケーションが正常でないと見なされ、アップグレードに失敗する前にデプロイされるアプリケーションの最大数 ([正常性のセクション](service-fabric-health-introduction.md)を参照してください) を指定します。 このパラメーターは、ノード上のアプリケーションの正常性を定義するため、アップグレード中の問題を検出するうえで役立ちます。 通常、アプリケーションのレプリカの負荷はその他のノードに分散されます。これにより、アプリケーションは正常に表示され、アップグレードを続行することができます。 厳密な *max-unhealthy-apps* を指定すると、Service Fabric はアプリケーション パッケージで高速に問題を検出し、フェイルファストなアップグレードが行われるようにします。 0 ～ 100 の数値として表されます。 |
mode | 使用できる値は、**Monitored**、**UpgradeMode**、**UnmonitoredAuto**、**UnmonitoredManual** です。 既定値は **UnmonitoredAuto** です。 これらの値の説明については、Visual Studio および PowerShell の "*必須パラメーター*" セクションをご覧ください。|
replica-set-check-timeout |秒単位で測定されます。 <br>**ステートレス サービス**-- Service Fabric は単一のアップグレード ドメイン内で、サービスの追加インスタンスを確実に使用できるようにしようとします。 ターゲット インスタンス数が複数ある場合、最大タイムアウト値になるまで、複数のインスタンスが使用可能になるまで待機します。 このタイムアウトは、*replica-set-check-timeout* プロパティを使用して指定されます。 タイムアウトになると、サービス インスタンス数にかかわらず、Service Fabric はアップグレードを続行します。 ターゲット インスタンス数が 1 つの場合、Service Fabric は待機せずに、すぐにアップグレードを実行します。<br><br>**ステートフル サービス**-- Service Fabric は 1 つのアップグレード ドメイン内で、レプリカ セットに確実にクォーラムが含まれるようにしようとします。 Service Fabric は *replica-set-check-timeout* プロパティで指定した最大タイムアウト値になるまで、クォーラムが使用可能になるまで待機します。 タイムアウトになると、クォーラムにかかわらずアップグレードを続行します。 この設定は、ロールフォワード時には、"しない (無限)" に設定され、ロールバック時には 1,200 秒に設定されます。 |
service-health-policy | サービスの種類名ごとにサービスの種類の正常性ポリシーを持つ JSON でエンコードされたマップ。 このマップは、既定では空です。 [パラメーターの JSON 形式。](/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap) "Value" 部分の JSON には、**MaxPercentUnhealthyServices**、**MaxPercentUnhealthyPartitionsPerService**、および **MaxPercentUnhealthyReplicasPerPartition** が含まれます。 これらのパラメーターの説明については、Visual Studio および PowerShell の省略可能パラメーター セクションをご覧ください。
timeout | 操作のタイムアウト期間 (秒) を指定します。 既定値は60 です。 |
upgrade-domain-timeout | 各ドメインがアップグレードを完了する必要がある時間。この時間を超えると、*FailureAction* が実行されます。 最初に、ISO 8601 の期間を表す文字列として解釈されます。 それが失敗した場合、ミリ秒単位の合計数を表す数値として解釈されます。 既定値はなし (無限) です。アプリケーションに合わせてカスタマイズしてください。 既定値はP10675199DT02H48M05.4775807S です。 |
upgrade-timeout | 各ドメインがアップグレードを完了する必要がある時間。この時間を超えると、*FailureAction* が実行されます。 最初に、ISO 8601 の期間を表す文字列として解釈されます。 それが失敗した場合、ミリ秒単位の合計数を表す数値として解釈されます。 既定値はなし (無限) です。アプリケーションに合わせてカスタマイズしてください。 既定値はP10675199DT02H48M05.4775807S です。|
warning-as-error | 使用可能な値: **True** および **False**。 既定値は **False** です。 フラグとして渡すことができます。 アップグレード中に、アプリケーションの正常性を評価するときに、アプリケーションの警告正常性状態をエラーとして扱います。 既定では、Service Fabric は警告イベントがある場合でもアップグレードを続行できるように、エラーになるイベントの警告正常性のイベントは評価しません。 |

## <a name="next-steps"></a>次のステップ
[Visual Studio を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial.md) に関する記事では、Visual Studio を使用してアプリケーションをアップグレードする方法について説明します。

[PowerShell を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md) に関する記事では、PowerShell を使用したアプリケーションのアップグレードについて説明します。

[Linux での Service Fabric CLI を使用したアプリケーションのアップグレード](service-fabric-application-lifecycle-sfctl.md#upgrade-application)に関する記事では、Service Fabric CLI を使用してアプリケーションをアップグレードする方法について説明します。

[Service Fabric Eclipse プラグインを使用したアプリケーションのアップグレード](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)の方法を学ぶことで、アプリケーションのアップグレードに互換性を持たせます。

「 [高度なトピック](service-fabric-application-upgrade-advanced.md)」を参照して、アプリケーションをアップグレードするときの高度な機能の使用方法を学習します。

「[アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」の手順を参照して、アプリケーションのアップグレードでの一般的な問題を修正します。
