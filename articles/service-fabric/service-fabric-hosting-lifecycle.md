---
title: Azure Service Fabric ホスティングのアクティブ化と非アクティブ化のライフサイクル
description: ノード上のアプリケーションと ServicePackage のライフ サイクルについて説明します。
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831824"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Azure Service Fabric ホスティングのライフサイクル

この記事では、Azure Service Fabric において、アプリケーションがノードでアクティブ化されるときに発生するイベントの概要について説明します。 ここでは、動作を制御するさまざまなクラスター構成について説明します。

続行する前に、「[Service Fabric でのアプリケーションのモデル化][a1]」で説明されている概念とリレーションシップをよく理解しておいてください。 

> [!NOTE]
> この記事では、いくつかの用語を専門的な方法で使用します。 特記のない場合に限ります。
>
> - "*レプリカ*" は、ステートフル サービスのレプリカおよびステートレス サービスのインスタンスの両方を意味します。
> - *CodePackage* は、ServiceType を登録する ServiceHost プロセスに相当するものとして扱われます。 ServiceType のサービスのレプリカをホストします。
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>ApplicationPackage または ServicePackage をアクティブにする

ApplicationPackage または ServicePackage をアクティブにするには:

1. ApplicationPackage (たとえば、*Applicationpackage.xml*) をダウンロードします。
2. アプリケーションの環境を設定します。 手順には、ユーザーの作成などが含まれます。
3. 非アクティブ化のためにアプリケーションの追跡を開始します。
4. ServicePackage (たとえば、*Servicemanifest.xml*、コード、構成ファイル、データ パッケージ) をダウンロードします。
5. ServicePackage の環境を設定します。 手順には、ファイアウォールの設定、エンドポイントへのポートの割り当てなどが含まれます。
6. 非アクティブ化を行うための ServicePackage の追跡を開始します。
7. Codepackage の SetupEntryPoint を開始し、完了するまで待ちます。
8. Codepackage の MainEntryPoint を開始します。

### <a name="servicetype-blocklisting"></a>ServiceType のブロックリスト
`ServiceTypeDisableFailureThreshold` では、許可されるアクティブ化、ダウンロード、および CodePackage のエラーの数が決定されます。 しきい値に達すると、ServiceType のブロックリストのスケジュールが設定されます。 最初のアクティブ化の失敗、ダウンロードの失敗、または CodePackage のクラッシュによって、ServiceType のブロックリストへの登録がスケジュールされます。 

`ServiceTypeDisableGraceInterval` 構成により、ServiceType がノードのブロックリストに登録されるまでの猶予期間が決まります。 このプロセスを終えると、アクティブ化、ダウンロード、および CodePackage の再起動が並列して再試行されます。 再試行とは、たとえば、CodePackage がクラッシュ後に再び起動するようにスケジュールされるか、Service Fabric によりパッケージのダウンロードが再試行されることを意味します。

ServiceType がブロックリストに登録されている場合、正常性エラーが表示されます: `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

次のいずれかのイベントが発生した場合、ServiceType はノードで再び有効になります。
- アクティブ化が成功した。 または、失敗した場合に `ActivationMaxFailureCount` の最大再試行回数に達した。
- ダウンロードが成功した。 または、失敗した場合に `DeploymentMaxFailureCount` の最大再試行回数に達した。
- クラッシュした CodePackage が起動し、ServiceType が正常に登録された。

`ActivationMaxFailureCount` と `DeploymentMaxFailureCount` は、Service Fabric によりノード上でアプリケーションがアクティブ化またはダウンロードされる最大試行回数です。 最大値に達すると、Service Fabric で再度アクティブ化するために ServiceType が有効になります。 

これらのしきい値により、サービスでアクティブ化を行う別の機会が得られます。 アクティブ化が成功した場合、問題は自動的に修復されます。 

新しく配置またはアクティブ化されたレプリカでは、新しいアクティブ化またはダウンロード操作をトリガーできます。 このアクションは成功するか、ServiceType ブロックリストを再度トリガーするかのどちらかです。

> [!NOTE]
> ServiceType が登録されていないクラッシュした CodePackage は、ServiceType に影響しません。 レプリカをホストするクラッシュした CodePackage だけが ServiceType に影響します。
>

### <a name="codepackage-crash"></a>CodePackage のクラッシュ
CodePackage がクラッシュした場合は、Service Fabric でバックオフを使用して再起動します。 バックオフは、CodePackage によって Service Fabric ランタイムに種類が登録されたかどうかには関係なく適用されます。

バックオフの値は `Min(RetryTime, ActivationMaxRetryInterval)` です。 値は、`ActivationRetryBackoffExponentiationBase` 構成設定に基づいて、定数、線形、または指数数量単位で増加します。

- **定数**:`ActivationRetryBackoffExponentiationBase == 0` の場合は、`RetryTime = ActivationRetryBackoffInterval` です。
- **線形**:`ActivationRetryBackoffExponentiationBase == 0` の場合は、`RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval` です。ここで `ContinuousFailureCount` は、CodePackage がクラッシュしたり、アクティブ化に失敗したりした回数です。
- **指数**: `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)`。
    
値を変更することで、動作を制御できます。 たとえば、いくつかのクイック再起動を試行する場合は、`ActivationRetryBackoffExponentiationBase` を `0` に設定し、`ActivationRetryBackoffInterval` を `10` に設定することにより、線形数量を使用できます。 したがって、CodePackage がクラッシュした場合、起動間隔は 10 秒後になります。 パッケージがクラッシュし続ける場合、CodePackage のアクティブ化が成功するか、CodePackage が非アクティブ化されるまで、バックオフは 20、30、40 秒などに変わります。 
    
失敗の後に Service Fabric がバックオフ (つまり待機) する最大時間は、`ActivationMaxRetryInterval` によって管理されます。
    
CodePackage がクラッシュし、復帰した場合は、`CodePackageContinuousExitFailureResetInterval` で指定された期間にわたって稼働状態を維持する必要があります。 この間隔が経過すると、Service Fabric で CodePackage が正常と見なされます。 Service Fabric では続いて、以前のエラー正常性レポートが OK として上書きされ、`ContinuousFailureCount` がリセットされます。

### <a name="codepackage-not-registering-servicetype"></a>CodePackage で ServiceType が登録されていない
CodePackage が稼働しており、ServiceType を登録する必要があるが登録していない場合は、`ServiceTypeRegistrationTimeout` 後に Service Fabric によって警告正常性レポートが生成されます。 このレポートには、ServiceType が予想された時間内に登録されていないことが示されます。

### <a name="activation-failure"></a>アクティブ化エラー
Service Fabric でアクティブ化中にエラーが検出されると、CodePackage クラッシュの場合と同じように、常に線形バックオフが使用されます。 アクティブ化操作は、次の間隔で再試行した後で中止されます。(0 + 10 + 20 + 30 + 40) = 100 秒。 (最初の再試行は即時です)。このシーケンスの後で、アクティブ化は再試行されません。
    
最大のアクティブ化バックオフは `ActivationMaxRetryInterval` に設定できます。 再試行は `ActivationMaxFailureCount` に設定できます。

### <a name="download-failure"></a>ダウンロードの失敗
Service Fabric では、ダウンロード中にエラーが検出されると、常に線形バックオフが使用されます。 アクティブ化操作は、次の間隔で再試行した後で中止されます。(0 + 10 + 20 + 30 + 40) = 100 秒。 (最初の再試行は即時です)。このシーケンスの後で、ダウンロードは再試行されません。 

ダウンロードの線形バックオフは、`ContinuousFailureCount` * `DeploymentRetryBackoffInterval` と同じです。 最大バックオフは `DeploymentMaxRetryInterval` に設定できます。 アクティブ化と同様に、ダウンロード操作は `ActivationMaxFailureCount` の制限に対して再試行できます。

> [!NOTE]
> これらの設定を変更する前に、次の例に留意してください。
>
>* CodePackage がクラッシュしバックオフし続ける場合、ServiceType は無効化されます。 しかし、アクティブ化構成でクイック再起動が設定されている場合、CodePackage は、ServiceType が実際にブロックリストに登録されるまでに数回起動する可能性があります。 
>
>    たとえば、CodePackage が起動し、ServiceType を Service Fabric に登録した後にクラッシュしたとします。 この場合、ホスティングで型の登録を受け取ると、`ServiceTypeDisableGraceInterval` 期間は取り消されます。 このプロセスは、CodePackage が `ServiceTypeDisableGraceInterval` 期間よりも大きい値に戻るまで繰り返すことができます。 この場合、ServiceType はノード上でブロックリストに登録されます。 ServiceType のブロックリストへの登録には、予想よりも時間がかかる場合があります。
>
>* アクティブ化の場合、Service Fabric システムでレプリカをノードに配置する必要があるときに、再構成エージェントは、ホスティング サブシステムに対して、アプリケーションをアクティブ化するように要求します。 アクティブ化要求を 15 秒ごとに再試行します。 (期間は、`RAPMessageRetryInterval` 構成設定によって管理されます)。ホスティングのアクティブ化操作の存続期間を再試行間隔および `ServiceTypeDisableGraceInterval` よりも長くしない限り、Service Fabric では、ServiceType がブロックリストに登録されていることが認識できません。 
>
>    たとえば、クラスターの `ActivationMaxFailureCount` が 5 に設定され、`ActivationRetryBackoffInterval` が 1 秒に設定されているとします。 この場合、アクティベーション操作は、(0 + 1 + 2 + 3 + 4) = 10 秒後に中止されます。 (最初の再試行は即時です)。このシーケンスの後、ホスティングによって再試行が中止されます。 アクティブ化操作が完了しましたが、15 秒後に再試行されません。 
>
>    Service Fabric で 15 秒以内に許可されたすべての再試行回数を使い切りました。 したがって、Reconfiguration Agent からの再試行のたびに新しいアクティブ化操作がホスティング サブシステムに作成され、このパターンが繰り返されます。 その結果、ServiceType がノード上でブロックリストに登録されなくなります。 ServiceType はノード上でブロックリストに登録されないため、レプリカが別のノードに移動され、試行されることはなくなります。
> 

## <a name="deactivation"></a>非アクティブ化

ServicePackage はノードでアクティブ化されると、非アクティブ化していないか追跡されます。 非アクティブ化は次の 2 つの方法で動作します。

- **定期的な非アクティブ化**:`DeactivationScanInterval` ごとに、レプリカをホストした *ことのない* Service Package がないかチェックし、それらを非アクティブ化の候補としてマークします。
- **ReplicaClose の非アクティブ化**:レプリカが閉じられると、Deactivator で `DecrementUsageCount` が取得されます。 ServicePackage でレプリカがホストされていない場合、カウントは 0 になります。そのため、ServicePackage は非アクティブ化の候補になります。

アクティブ化モードによって、候補を非アクティブ化するスケジュールが決まります。 共有モードでは、非アクティブ化の候補は `DeactivationGraceInterval` 後にスケジュールされます。 排他モードでは、`ExclusiveModeDeactivationGraceInterval` 後にスケジュールされます。 これらの時間中に新しいレプリカの配置が行われると、非アクティブ化は取り消されます。 

詳細については、[排他モードと共有モード][a2]に関するページをご覧ください。

### <a name="periodic-deactivation"></a>定期的な非アクティブ化
以下に、定期的な非アクティブ化の例をいくつか示します。

* **例 1**:Deactivator が時刻 T (`DeactivationScanInterval`) にスキャンを開始したとします。 次のスキャンは 2T になります。 ServicePackage のアクティブ化が T + 1 に発生したとします。 この ServicePackage ではレプリカをホストしないため、これを非アクティブ化する必要があります。 

    非アクティブ化の候補になるためには、ServicePackage で少なくとも T 時間、レプリカをホストしていないことが必要です。 2T + 1 で非アクティブ化の対象となります。 したがって、2T のスキャンでは、この ServicePackage は非アクティブ化の候補として特定されません。 

    次の非アクティブ化サイクル 3T では、パッケージが time T の間、レプリカなしの状態だったことになるため、この ServicePackage は非アクティブ化するようにスケジュールされます。  

* **例 2**:たとえば、ServicePackage が time T - 1 にアクティブ化され、Deactivator が T にスキャンを開始するとします。ServicePackage はレプリカをホストしていません。 その後、次のスキャン 2T で、ServicePackage は非アクティブ化の候補として特定されるため、非アクティブ化するようにスケジュールされます。  

* **例 3**:たとえば、ServicePackage が time T - 1 にアクティブ化され、Deactivator が T にスキャンを開始するとします。ServicePackage はまだレプリカをホストしていません。 ここで、T + 1 にレプリカが配置されます。 つまり、ホスティングで `IncrementUsageCount` が取得されます。これは、レプリカが作成されることを意味します。 

    2T 時点では、この ServicePackage は非アクティブ化するようにスケジュールされていません。 パッケージにはレプリカが含まれているため、この記事の次のセクションで説明するように、非アクティブ化は ReplicaClose ロジックに従います。

* **例 4**:ServicePackage が 10 GB だとしましょう。 パッケージが大きいため、ノードにダウンロードするのに時間がかかります。 アプリケーションがアクティブ化されると、Deactivator はそのライフ サイクルを追跡します。 `DeactivationScanInterval` が小さい値に設定されている場合、ダウンロードに時間がかかるため、ServicePackage にはノード上でアクティブ化する時間がないことがあります。 この問題を解決するために、[ノードに ServicePackage を事前にダウンロードしておく][p1]か、`DeactivationScanInterval` を増やすことができます。 

> [!NOTE]
> ServicePackage は、(`DeactivationScanInterval` から 2 * `DeactivationScanInterval`) + `DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval` の間の任意の時点で非アクティブ化できます。 
>

### <a name="replicaclose-deactivation"></a>ReplicaClose の非アクティブ化

> [!NOTE]
> このセクションでは、次の構成設定を参照します。
> - **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval**。既にいずれかのレプリカをホストしている場合に、別のレプリカをホストするために ServicePackage に対して与えられる時間です。 
> - **DeactivationScanInterval**: レプリカをホストしたことが *ない* (つまり、使用されていない) 場合に、レプリカをホストするために ServicePackage に対して与えられる最小時間です。
>

システムには、ServicePackage で保持されているレプリカの数が記録されます。 ServicePackage がレプリカを保持していて、そのレプリカが閉じられるか停止されると、ホスティングは `DecrementUsageCount` を取得します。 レプリカが開かれると、ホスティングは `IncrementUsageCount` を取得します。 

デクリメントとは、ServicePackage がホスティングしているレプリカの数が 1 つ減少したことを示します。 カウントが 0 になると、ServicePackage は非アクティブ化するようにスケジュールされます。 非アクティブ化されるまでの時間は、`DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval` です。 

たとえば、T でデクリメントが発生し、ServicePackage が 2T + X (`DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval`) で非アクティブ化するようにスケジュールされているとします。 この期間中、ホスティングが `IncrementUsage` を取得すると、レプリカが作成されるので、非アクティブ化が取り消されます。

### <a name="ctrl--c"></a>Ctrl + C
ServicePackage が `DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval` を渡し、まだレプリカをホストしていない場合、非アクティブ化を取り消すことはできません。 CodePackage は Ctrl + C ハンドラーを受け取ります。 ここで、非アクティブ化パイプラインを完了して、プロセスを停止する必要があります。 

この期間中に、同じ ServicePackage の新しいレプリカを配置しようとした場合は、プロセスが非アクティブ化からアクティブ化に移行できないため、失敗します。

## <a name="cluster-configurations"></a>クラスター構成

このセクションでは、アクティブ化と非アクティブ化に影響を与える既定値を含む構成を示します。

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**:既定値は1. 失敗回数のしきい値。このしきい値に達すると、ノード上のサービスの種類を無効にし、別のノードで配置を試行するように FailoverManager に通知されます。
- **ServiceTypeDisableGraceInterval**:既定値は30 秒です。 この時間が経過した後で、サービスの種類を無効にできます。
- **ServiceTypeRegistrationTimeout**:既定値は300 秒。 ServiceType が Service Fabric に登録されるまでのタイムアウト。

### <a name="activation"></a>アクティブ化
- **ActivationRetryBackoffInterval**:既定値は10 秒。 アクティブ化が失敗するたびに発生するバックオフ間隔。
- **ActivationMaxFailureCount**:既定値は20。 失敗したアクティブ化をシステムで再試行する最大回数。この後、中止されます。 
- **ActivationRetryBackoffExponentiationBase**:既定値は1.5。
- **ActivationMaxRetryInterval**:既定値は3,600 秒。 失敗後のアクティブ化の最大バックオフ再試行間隔。
- **CodePackageContinuousExitFailureResetInterval**:既定値は300 秒。 CodePackage の連続終了失敗カウントをリセットするためのタイムアウト間隔。

### <a name="download"></a>ダウンロード
- **DeploymentRetryBackoffInterval**:既定値は10。 デプロイ エラーのバックオフ間隔。
- **DeploymentMaxRetryInterval**:既定値は3,600 秒。 失敗後のデプロイの最大バックオフ間隔。
- **DeploymentMaxFailureCount**:既定値は20。 ノードへのアプリケーションのデプロイは、`DeploymentMaxFailureCount` 回、再試行された後に失敗します。

### <a name="deactivation"></a>非アクティブ化
- **DeactivationScanInterval**: 既定値は600 秒。 レプリカをホストしたことがない (つまり、使用されていない) 場合に、レプリカをホストするために ServicePackage に対して与えられる最小時間。
- **DeactivationGraceInterval**:既定値は60 秒。 *共有* プロセス モデルで、既にいずれかのレプリカをホストした後で別のレプリカを再度ホストするために ServicePackage に対して与えられる時間。
- **ExclusiveModeDeactivationGraceInterval**:既定値は1 秒です。 *専有* プロセス モデルで、既にいずれかのレプリカをホストした後で別のレプリカを再度ホストするために ServicePackage に対して与えられる時間。

## <a name="next-steps"></a>次のステップ

- [アプリケーションをパッケージ化][a3]して展開できるようにします。
- PowerShell で[アプリケーションのデプロイと削除][a4]を実行します。

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
