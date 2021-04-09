---
title: アプリケーション アップグレードのトラブルシューティング
description: この記事では、Service Fabric アプリケーションのアップグレードに関する一般的な問題とその解決方法のいくつかについて説明します。
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009349"
---
# <a name="troubleshoot-application-upgrades"></a>アプリケーションのアップグレードのトラブルシューティング

この記事では、Azure Service Fabric アプリケーションのアップグレードに関する一般的な問題とその解決方法のいくつかについて説明します。

## <a name="troubleshoot-a-failed-application-upgrade"></a>アプリケーションのアップグレードで障害が発生した場合のトラブルシューティング

アップグレードに失敗した場合、 **Get-ServiceFabricApplicationUpgrade** コマンドの出力には、エラーのデバッグに関する追加情報が含まれています。  次の一覧では、この追加情報を使用する方法を指定します。

1. 失敗の種類を識別する。
2. 失敗の理由を識別する。
3. 詳細な調査のために、障害が発生した 1 つ以上のエラー コンポーネントを分離する。

この情報は、Service Fabric がエラーを検出すると、 **FailureAction** をロールバックするか、アップグレードを中断するかに関係なく利用できます。

### <a name="identify-the-failure-type"></a>失敗の種類を識別する

**Get-ServiceFabricApplicationUpgrade** の出力で、**FailureTimestampUtc** は Service Fabric によってアップグレードの失敗が検出されたタイムスタンプ (UTC) を識別し、**FailureAction** がトリガーされました。 **FailureReason** は、エラーの 3 つの潜在的で大まかな原因の 1 つを識別します。

1. UpgradeDomainTimeout - 特定のアップグレード ドメインの完了に時間がかかりすぎ、 **UpgradeDomainTimeout** の有効期限が切れていることを識別します。
2. OverallUpgradeTimeout - アップグレード全体の完了に時間がかかりすぎ、 **UpgradeTimeout** の有効期限が切れていることを識別します。
3. HealthCheck - 更新ドメインをアップグレードした後に、アプリケーションが指定された正常性ポリシーに従って正常でないこと、および **HealthCheckRetryTimeout** の有効期限が切れていることを識別します。

これらのエントリは、アップグレードが失敗し、ロールバックを開始する場合にのみ出力に表示されます。 エラーの種類に応じて、詳細な情報が表示されます。

### <a name="investigate-upgrade-timeouts"></a>アップグレードのタイムアウトを調査する

アップグレードのタイムアウト エラーは、サービスの可用性の問題に起因することが最も一般的です。 以下に示す出力は、サービスのレプリカまたはインスタンスが新しいコード バージョンを開始できないアップグレードの一般的な例です。 **UpgradeDomainProgressAtFailure** フィールドは、エラーの発生時に保留中のアップグレード作業のスナップショットをキャプチャします。

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

この例では、アップグレード ドメイン *MYUD1* でアップグレードが失敗し、2 つのパーティション (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* と *4b43f4d8-b26b-424e-9307-7a7a62e79750*) がスタックしていました。 パーティションがスタックしている原因は、ランタイムが、ターゲット ノード *Node1* と *Node4* にプライマリ レプリカ (*WaitForPrimaryPlacement*) を配置できなかったことです。

**Get-ServiceFabricNode** コマンドを使用して、これら 2 つのノードがアップグレード ドメイン *MYUD1* にあることを確認できます。 *UpgradePhase* は *PostUpgradeSafetyCheck* を出力していますが、アップグレード ドメイン内のすべてのノードがアップグレードを完了した後にこれらの安全性チェックがなされていることを意味します。 これらすべての情報は、潜在的な問題と新しいバージョンのアプリケーション コードを示します。 最も一般的な問題は、プライマリ コード パスのオープンまたは昇格でのサービスのエラーです。

*PreUpgradeSafetyCheck* の *UpgradePhase* は、アップグレードを実行する前のアップグレード ドメインの準備で問題があることを意味します。 この場合の最も一般的な問題は、プライマリ コード パスのクローズまたは降格でのサービス エラーです。

現在の **UpgradeState** は *RollingBackCompleted* であるため、元のアップグレードは、ロールバック **FailureAction** (障害発生時にアップグレードを自動的にロールバックする) を使用して実行済みのはずです。 元のアップグレードが手動の **FailureAction** を使用して実行された場合は、アプリケーションのライブ デバッグを実行できるように、アップグレードが代わりに中断状態になります。

まれに、システムが現在のアップグレード ドメインのすべての作業を完了するのと同時に全体のアップグレードがタイムアウトになった場合に、**UpgradeDomainProgressAtFailure** フィールドが空になることがあります。 そのような場合は、アップグレード パラメーター **UpgradeTimeout** および **UpgradeDomainTimeout** の値を大きくしてアップグレードを再試行してください。

### <a name="investigate-health-check-failures"></a>正常性チェックの障害を調査する

正常性チェックの障害は、アップグレード ドメインのすべてのノードがアップグレードを終了し、すべての安全性チェックを渡した後で、さまざまな問題によりトリガーされる可能性があります。 以下に示す出力は、正常性チェックの失敗によるアップグレード エラーの一般的な例です。 **UnhealthyEvaluations** フィールドは、アップグレード中に失敗した正常性チェックのスナップショットを、指定した [正常性ポリシー](service-fabric-health-introduction.md)に従って取得します。

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

正常性チェックの障害を調査するには、まず Service Fabric の正常性モデルを理解する必要があります。 ただし、詳しく理解していなくても、*fabric:/DemoApp/Svc3* と *fabric:/DemoApp/Svc2* という 2 つのサービスに問題が発生し、エラーの正常性レポート (この例では "InjectedFault") が生成されていることがわかります。 この例では、4 つのサービスのうち 2 つは異常な状態で、既定のターゲットの 0% の異常な状態を下回っています (*MaxPercentUnhealthyServices*)。

アップグレードを開始するときに **FailureAction** を手動で指定することで、アップグレードは、障害が発生したときに中断されました。 このモードでは、追加のアクションを実行する前に、障害が発生しているライブ システムを調査できます。

### <a name="recover-from-a-suspended-upgrade"></a>中断されたアップグレードから回復する

ロールバック **FailureAction** を使用すると、障害が発生したときにアップグレードが自動的にロールバックされるため、復旧は必要ありません。 手動の **FailureAction** を使用する場合、いくつかの回復オプションがあります。

1.  ロールバックをトリガーする
2. アップグレードの残りの項目を手動で続行する
3. 監視対象のアップグレードを再開する

**Start-ServiceFabricApplicationRollback** コマンドを使用すると、アプリケーションのロールバックをいつでも開始できます。 コマンドが正常に返されると、ロールバック要求はシステムに登録されているため、その後すぐに開始されます。

**Resume-ServiceFabricApplicationUpgrade** コマンドを使用すると、アップグレードの残りの項目を一度にアップグレード ドメインを 1 つずつ手動で続行できます。 このモードでは、安全性チェックのみが自動実行されます。 その他の正常性チェックは実行されません。 このコマンドは、*UpgradeState* が *RollingForwardPending* の場合、つまり、現在のアップグレード ドメインがアップグレードを完了したが、次が開始されていない (保留中) の場合にのみ使用できます。

**Update-ServiceFabricApplicationUpgrade** コマンドを使用すると、安全性チェックと正常性チェックの両方が実行されている、監視対象のアップグレードを再開できます。

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

アップグレードは最後に中断したアップグレード ドメインから続行し、以前と同じアップグレード パラメーターと正常性ポリシーを使用します。 必要に応じて、前の出力に示すアップグレード パラメーターと正常性ポリシーを、アップグレードを再開するときと同じコマンドで変更できます。 この例では、アップグレードは、パラメーターと正常性ポリシーは変更されないまま、監視対象モードで再開されました。

## <a name="further-troubleshooting"></a>詳細なトラブルシューティング

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric が指定された正常性ポリシーに従っていない

考えられる原因 1:

Service Fabric は、すべてのパーセンテージを、正常性評価のためのエンティティ (レプリカ、パーティション、サービスなど) の実際の数値に変換し、全体のエンティティ数に常に切り上げます。 たとえば、最大の *MaxPercentUnhealthyReplicasPerPartition* が 21% で、5 個のレプリカがある場合、Service Fabric は最大 2 個の異常レプリカ (つまり `Math.Ceiling (5*0.21)`) を許可します。 このため、これに基づいて、正常性ポリシーを設定する必要があります。

考えられる原因 2:

正常性ポリシーは、特定のサービス インスタンスではなく、サービスの合計のパーセンテージにより指定されます。 たとえば、アップグレードの前に、アプリケーションに A、B、C、および D の 4 つのサービス インスタンスがあり、サービス D が異常だが、アプリケーションにほとんど影響がないとします。 この場合は、アップグレード中、既知の問題のあるサービス D を無視するように、パラメーター *MaxPercentUnhealthyServices* を 25% になるよう設定し、正常であることが必要とされるのが A、B、および C のみとなるようにします。

ただし、アップグレード中、C が異常になり、D が正常になる可能性があります この場合でも、アップグレードは正常に完了します。異常なのは、サービスの 25% のみだからです。 ただし、D ではなく C により予期しないエラーが発生する可能性があります。この状態では、D は A、B、および C とは異なるサービス型としてモデル化する必要があります。正常性ポリシーはサービス型ごとに指定されるため、さまざまな異常の割合のしきい値を、さまざまなサービスに割り当てることができます。 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>アプリケーションのアップグレードの正常性ポリシーを指定していないが、未指定のタイムアウトによりアップグレードが失敗する

正常性ポリシーがアップグレード要求に指定されていない場合、現在のアプリケーションのバージョンの *ApplicationManifest.xml* から取得されます。 たとえば、アプリケーション X をバージョン 1.0 から バージョン 2.0 にアップグレードしている場合は、バージョン 1.0 に指定されたアプリケーションの正常性ポリシーが使用されます。 別の正常性ポリシーをアップグレードに使用するには、アプリケーションのアップグレードの API 呼び出しの一部としてポリシーを指定する必要があります。 アップグレード中は、API 呼び出しの一部として指定されたポリシーのみが適用されます。 アップグレードが完了すると、 *ApplicationManifest.xml* で指定されたポリシーが使用されます。

### <a name="incorrect-time-outs-are-specified"></a>不適切なタイムアウトが指定されている

タイムアウトの設定に一貫性がないとどうなるでしょうか。 たとえば、*UpgradeTimeout* が *UpgradeDomainTimeout* より小さい場合があります。 このような場合は、エラーが返されます。 *UpgradeDomainTimeout* が *HealthCheckWaitDuration* と *HealthCheckRetryTimeout* の合計より小さい場合や、*UpgradeDomainTimeout* が *HealthCheckWaitDuration* と *HealthCheckStableDuration* の合計より小さい場合も、エラーが返されます。

### <a name="my-upgrades-are-taking-too-long"></a>アップグレードの時間がかかりすぎる

アップグレードにかかる時間は、指定された正常性チェックとタイムアウトによって異なります。 正常性チェックとタイムアウトの時間は、アプリケーションのコピー、デプロイ、および安定化にかかる時間によって異なります。 タイムアウトが短すぎるとアップグレードが失敗する可能性が高くなるため、タイムアウトは長めにして開始することをお勧めします。

タイムアウトが、アップグレードの時間にどのように影響するのか、以下で簡単に確認できます。

アップグレード ドメインのアップグレードは、*HealthCheckWaitDuration* + *HealthCheckStableDuration* よりも速く完了できません。

アップグレードの失敗が、*HealthCheckWaitDuration* + *HealthCheckRetryTimeout* より速く発生することはあり得ません。

アップグレード ドメインのアップグレード時間は、 *UpgradeDomainTimeout* によって制限されます。  *HealthCheckRetryTimeout* と *HealthCheckStableDuration* が両方とも 0 以外であり、アプリケーションの正常性が切り替わる場合は、アップグレードが最終的に *UpgradeDomainTimeout* でタイムアウトします。 *UpgradeDomainTimeout* は、現在のアップグレード ドメインのアップグレードが開始されると、カウント ダウンを開始します。

## <a name="next-steps"></a>次のステップ

[Visual Studio を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial.md) に関する記事では、Visual Studio を使用してアプリケーションをアップグレードする方法について説明します。

[PowerShell を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md) に関する記事では、PowerShell を使用したアプリケーションのアップグレードについて説明します。

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)を使用して、アプリケーションのアップグレード方法を制御します。

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)の方法を学ぶことで、アプリケーションのアップグレードに互換性を持たせます。

「 [高度なトピック](service-fabric-application-upgrade-advanced.md)」を参照して、アプリケーションをアップグレードするときの高度な機能の使用方法を学習します。