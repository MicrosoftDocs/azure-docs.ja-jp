---
title: Azure Service Fabric ホスティングのアクティブ化と非アクティブ化のライフサイクル
description: ノード上のアプリケーションと ServicePackage のライフサイクルについて説明します
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: a39aecf16d1c3303c0a590b389ba2aa69d4472f2
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405128"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Azure Service Fabric ホスティングのライフサイクル
この記事では、アプリケーションがノードでアクティブ化されるときに発生するイベントと、動作を制御するために使用されるさまざまなクラスター構成の概要について説明します。

先に進む前に、「[Service Fabric でのアプリケーションのモデル化][a1]」で説明されているさまざまな概念とリレーションシップをよく理解しておいてください。 

> [!NOTE]
> この記事では、別のことが明記されていない限り、以下のとおりとします。
>
> - "*レプリカ*" は、ステートフル サービスのレプリカおよびステートレス サービスのインスタンスの両方を意味します。
> - *CodePackage* は、*ServiceType* を登録する *ServiceHost* プロセスに相当するものとして扱われ、その *ServiceType* のサービスのレプリカをホストします。
>

## <a name="activation-of-applicationservicepackage"></a>アプリケーション/ServicePackage のアクティブ化

アクティブ化のためのパイプラインは次のとおりです。

1. ApplicationPackage をダウンロードします。 次に例を示します。ApplicationManifest.xml など。
2. アプリケーションの環境を設定します (例: ユーザーの作成など)。
3. 非アクティブ化のためにアプリケーションの追跡を開始します。
4. ServicePackage をダウンロードします。 次に例を示します。ServiceManifest.xml、コード、構成、データ パッケージなど。
5. ServicePackage の環境を設定します (例: ファイアウォールの設定、エンドポイント用のポートの割り当てなど)。
6. 非アクティブ化を行うための ServicePackage の追跡を開始します。
7. CodePackage の SetupEntryPoint を開始し、完了するまで待ちます。
8. CodePackage の MainEntryPoint を開始します。

### <a name="servicetype-blocklisting"></a>ServiceType のブロックリスト
**ServiceTypeDisableFailureThreshold** により、ServiceType でブロックリストがスケジュールされるまでの失敗 (アクティブ化、ダウンロード、CodePackage のエラー) の数が決まります。 したがって、最初のアクティブ化/ダウンロードの失敗、または CodePackage のクラッシュによって、ServiceType のブロックリストのスケジュールがトリガーされます。 **ServiceTypeDisableGraceInterval** 構成は、ServiceType がそのノード上でブロックリストに入れられているとして最終的にマークされるまでの猶予期間を決定します。 このすべてが起こるためには、アクティブ化/ダウンロード/CodePackage の再起動が内部ではまだ再試行モードであり、ホスティング サブシステムによって追跡されている必要があることに注意してください。 再試行とは、たとえば、CodePackage がクラッシュ後に再び起動するようにスケジュールされる、または Service Fabric がパッケージのダウンロードを再試行することを意味します。
ブロックリストに入れられると、"'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node." ('システム ホスティング' により、プロパティ 'ServiceTypeRegistration:ServiceType'.のエラーが報告されました。ServiceType がノードで無効化されました。) というエラーが表示されます。

次の場合、ServiceType はノードで再び有効になります。 
- アクティブ化操作が成功した場合、または失敗して **ActivationMaxFailureCount** の再試行回数に達した場合。
- ダウンロード操作が成功した場合、または失敗して **DeploymentMaxFailureCount** の再試行回数に達した場合。
- クラッシュした CodePackage がバックアップを開始し、ServiceType を正常に登録した場合。

**ActivationMaxFailureCount**/**DeploymentMaxFailureCount** の再試行回数後に ServiceType を再び有効にする理由は、それが、Service Fabric がノード上でアプリケーションをアクティブ化/ダウンロードするために実行する最大試行回数だからです。 成功しない場合、現在の操作は再試行されません。そして、Service Fabric は、サービスに対して (成功して、問題が自動的に解決するかもしれない) アクティブ化の新たなチャンスを与えたいため、アクティブ化/ダウンロード操作のライフサイクルに関連付けられます。 レプリカの配置によってトリガーされた新しいアクティブ化/ダウンロード操作は、ServiceType のブロックリスト作成を再度トリガーするか、成功する可能性があります。

> [!NOTE]
> ServiceType を登録していない CodePackage がクラッシュしている場合、ServiceType には影響はありません。 ServiceType に影響を与えるのは、レプリカをホストしている CodePackage のクラッシュだけです。
>

### <a name="codepackage-crash"></a>CodePackage のクラッシュ
CodePackage がクラッシュすると、Service Fabric はバックオフを使用してもう一度開始します。バックオフは、コード パッケージによって型が登録されているかどうかは関係ありません。

バックオフ値は常に Min(RetryTime, **ActivationMaxRetryInterval**) であり、この値は、**ActivationRetryBackoffExponentiationBase** 構成に基づいて定数、線形、または指数の場合があります。

- 定数: **ActivationRetryBackoffExponentiationBase** == 0 の場合、RetryTime = **ActivationRetryBackoffInterval**;
- 線形: **ActivationRetryBackoffExponentiationBase** == 0 の場合、RetryTime = ContinuousFailureCount* **ActivationRetryBackoffInterval**。ここで、ContinousFailureCount は、CodePackage がクラッシュするか、アクティブ化に失敗する回数です。
- 指数: RetryTime = (**ActivationRetryBackoffInterval** (秒単位)) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
クイック再起動などの動作は必要に応じて制御できます。 線形について説明しましょう。 これは、CodePackage がクラッシュした場合、開始間隔は、CodePackage が非アクティブ化されるまで、10、20、30、40 秒後になることを意味します。 
    
失敗の後に Service Fabric がバックオフ (待機) する最大時間は、**ActivationMaxRetryInterval** によって管理されます。
    
CodePackage がクラッシュして、再び起動した場合、Service Fabric によって正常であると見なされるには、**CodePackageContinuousExitFailureResetInterval** の間稼働状態である必要があります。この時点で、正常性レポートは "問題なし" として上書きされ、ContinousFailureCount がリセットされます。

### <a name="codepackage-not-registering-servicetype"></a>CodePackage で ServiceType が登録されていない
CodePackage が稼働状態であり、ServiceType を登録することが予期されているにもかかわらず、していない場合は、Service Fabric により、**ServiceTypeRegistrationTimeout** の後に、ServiceType がタイムアウト以内に構成されていないことを示す警告の HealthReport が生成されます。

### <a name="activation-failure"></a>アクティブ化エラー
Service Fabric では、アクティブ化中にエラーを検出すると、常に線形バックオフが使用されます (CodePackage のクラッシュと同じ)。 つまり、アクティブ化操作は、(0+ 10 + 20 + 30 + 40) = 100 秒 (最初の再試行は即時) 後に中止されます。 この後は、アクティブ化は再試行されません。
    
最大アクティブ化バックオフは **ActivationMaxRetryInterval**、再試行は **ActivationMaxFailureCount** とすることができます。

### <a name="download-failure"></a>ダウンロードの失敗
Service Fabric では、ダウンロード中にエラーが発生すると、常に線形バックオフが使用されます。 つまり、アクティブ化操作は、(0+ 10 + 20 + 30 + 40) = 100 秒 (最初の再試行は即時) 後に中止されます。 この後は、ダウンロードは再試行されません。 ダウンロードの線形バックオフは、ContinuousFailureCount***DeploymentRetryBackoffInterval** と等しく、最大バックオフは **DeploymentMaxRetryInterval** とすることができます。 アクティブ化と同様に、ダウンロード操作は、**ActivationMaxFailureCount** 再試行できます。

> [!NOTE]
> 構成を変更する前に、留意すべきいくつかの例を次に示します。

* CodePackage がクラッシュし続けてバックオフする場合、ServiceType は無効化されます。 しかし、クイック再起動を行うようにアクティブ化構成が設定されている場合、CodePackage は、ServiceType の無効化を確認できるまでに数回起動する可能性があります。 たとえば、CodePackage が起動し、ServiceType を Service Fabric に登録した後にクラッシュしたとします。 この場合、ホスティングが型の登録を受け取ると、**ServiceTypeDisableGraceInterval** 期間は取り消されます。 そして、これは、CodePackage が **ServiceTypeDisableGraceInterval** より大きい値にバックオフするまで繰り返される可能性があり、その後に、ServiceType がノードで無効化されます。 したがって、ノードで ServiceType が無効化されるまでには、しばらく時間がかかる可能性があります。

* アクティブ化の場合、Service Fabric システムがレプリカをノードに配置する必要がある場合、RA (ReconfigurationAgent) は、ホスティング サブシステムに対し、アプリケーションをアクティブ化し、15 秒 (**RAPMessageRetryInterval**) ごとにアクティブ化要求を再試行するよう要求します。 Service Fabric システムによって ServiceType が無効化されていることが確認されるためには、ホスティングのアクティブ化操作の存続期間が再試行間隔および **ServiceTypeDisableGraceInterval** よりも長いことが必要です。 たとえば、クラスターで構成 **ActivationMaxFailureCount** を 5 に設定し、**ActivationRetryBackoffInterval** を 1 秒に設定します。これは、アクティブ化操作が (0 + 1 + 2 + 3 + 4) = 10 秒 (最初の再試行は即時) 後に中止され、その後にホスティングが再試行を中止することを意味します。 この場合、アクティブ化操作は完了し、15 秒後には再試行されません。 この理由は、Service Fabric が 15 秒以内にすべての再試行回数を使い切ってしまったからです。 したがって、ReconfigurationAgent からの再試行のたびに新しいアクティブ化操作がホスティング サブシステムに作成され、このパターンが繰り返されて、ServiceType は決してノード上で無効化されません。 ServiceType がノードで無効化されないため、Sf システムのコンポーネント FM (FailoverManager) は、レプリカを別のノードに移動しません。
> 

## <a name="deactivation"></a>非アクティブ化

ServicePackage はノードでアクティブ化されると、非アクティブ化のために追跡されます。 Deactivator は、その追跡を行うエンティティです。
Deactivator は、次の 2 つの方法で動作します。

1.  定期的: **DeactivationScanInterval** ごとに、レプリカをホストしたことのない ServicePackage がないかチェックし、それらを非アクティブ化の候補としてマークします。
2.  ReplicaClose: レプリカが閉じられると、Deactivator は DecrementUsageCount を取得します。 カウントが 0 になると、これは ServicePackage がどのレプリカもホストしていないことを意味するため、非アクティブ化の候補になります。

 [専有/共有][a2]のアクティブ化モードに基づき、SharedMode の場合は **DeactivationGraceInterval**、ExclusiveMode の場合は **ExclusiveModeDeactivationGraceInterval** 後に、非アクティブ化の候補がスケジュールされます。 この時間中に新しいレプリカの配置が行われると、非アクティブ化は取り消されます。

### <a name="periodically"></a>定期的:
例 1:Deactivator が Time T(**DeactivationScanInterval**) にスキャンを行うとします。 次のスキャンは 2T になります。 ServicePackage のアクティブ化が T+1 に発生したとします。 この ServicePackage はレプリカをホストしていないため、非アクティブ化する必要があります。 この ServicePackage が非アクティブ化の候補になるためには、少なくとも T 時間、レプリカなしの状態である必要があります。 つまり、2T+1 で非アクティブ化の対象となります。 したがって、2T のスキャンでは、この ServicePackage は非アクティブ化の候補として検出されません。 次の非アクティブ化サイクル 3T では、この ServicePackage は time T の間、レプリカなしの状態だったことになるため、非アクティブ化するようにスケジュールされます。  

例 2:たとえば、ServicePackage が time T-1 にアクティブ化され、Deactivator が T にスキャンを実行するとします。ServicePackage はレプリカをホストしていません。 その後、次のスキャン 2T で、この ServicePackage は非アクティブ化の候補として検出されるため、非アクティブ化するようにスケジュールされます。  

例 3: たとえば、ServicePackage が T-1 にアクティブ化され、Deactivator が T にスキャンを行うとします。ServicePackage は、まだレプリカをホストしていません。 T+1 にレプリカが配置されます。つまり、 ホスティングが IncrementUsageCount を取得します。これは、レプリカが作成されることを意味します。 そして、2T 時点で、この ServicePackage は非アクティブ化するようにスケジュールされていません。 次に、非アクティブ化は、以下で説明する ReplicaClose ロジックに移動します。

例 4: ServicePackage が大きい (10 GB など) とします。この場合、ノードにダウンロードするのに若干時間がかかる可能性があります。 アプリケーションがアクティブ化されると、Deactivator はそのライフサイクルを追跡します。 ここで、**DeactivationScanInterval** 構成値が小さいと、すべての時間がダウンロードに使用されてしまうため、ServicePackage ではノードでアクティブ化するための時間を得られないという問題が発生する可能性があります。 この問題を解決するために、[ノードに ServicePackage を事前にダウンロードしておく][p1]ことができます。 

> [!NOTE]
> したがって、ServicePackage は、(**DeactivationScanInterval** から 2***DeactivationScanInterval**) + **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval** の間のいずれかの時点で非アクティブ化できます。 
>

### <a name="replica-close"></a>レプリカを閉じる:
Deactivator は、ServicePackage で保持されているレプリカの数を記録しています。 ServicePackage がレプリカを保持していて、そのレプリカが閉じられる/停止されると、ホスティングは DecrementUsageCount を取得します。 レプリカが開かれると、ホスティングは IncrementUsageCount を取得します。 デクリメントとは、ServicePackage でホスティングしているレプリカが 1 つ減ることを意味します。カウントが 0 になると、ServicePackage は非アクティブ化するようにスケジュールされます。非アクティブ化されるまでの時間は、**DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval** です。 

たとえば、デクリメントが T に発生し、ServicePackage が 2T+X(**DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval**) に非アクティブ化するようにスケジュールされているとします。 この期間中にホスティングが IncrementUsage を取得すると、レプリカが作成され、非アクティブ化が取り消されます。

> [!NOTE]
>つまり、これらの構成で行われることは、基本的に次のことを意味します: **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval**。すなわち、任意のレプリカをホストした後で、再び別のレプリカをホストするために ServicePackage に対して与えられる時間です。 
**DeactivationScanInterval**: レプリカをホストしたことがない ( つまり、使用されていない) 場合に、レプリカをホストするために ServicePackage に対して与えられる最小時間。
>

### <a name="ctrlc"></a>Ctrl+C
ServicePackage が **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval** を渡し、まだレプリカをホストしていない場合、非アクティブ化はキャンセルできません。 CodePackage には Ctrl+C ハンドラーが発行されます。これは、プロセスを停止するためには、非アクティブ化パイプラインの実行が必要であることを意味します。 この期間中に、同じ ServicePackage の新しいレプリカを配置しようとした場合は、非アクティブ化からアクティブ化に移行できないため、失敗します。

## <a name="cluster-configs"></a>クラスター構成

アクティブ化/非アクティブ化に影響を与える、既定値を含む構成。

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**:既定値は 1。 そのノード上のサービスの種類を無効にし、別のノードで配置を試行するよう FM (FailoverManager) が通知されるまでの失敗回数のしきい値。
**ServiceTypeDisableGraceInterval**:既定値は 30 秒。この時間間隔の後に、サービスの種類を無効にできます
**ServiceTypeRegistrationTimeout**:既定値は 300 秒。ServiceType が Service Fabric に登録されるまでのタイムアウト。

### <a name="activation"></a>アクティブ化
**ActivationRetryBackoffInterval**:既定値は 10 秒。アクティブ化が失敗するたびに発生するバックオフ間隔。
**ActivationMaxFailureCount**:既定値は 20。 システムが失敗したアクティブ化を再試行する最大回数。この後、中止されます。 
**ActivationRetryBackoffExponentiationBase**:既定値は 1.5。
**ActivationMaxRetryInterval**:既定値は 3600 秒。アクティブ化が失敗した場合の最大バックオフ。
**CodePackageContinuousExitFailureResetInterval**:既定値は 300 秒。CodePackage の連続終了失敗カウントをリセットするためのタイムアウト。

### <a name="download"></a>ダウンロード
**DeploymentRetryBackoffInterval**:既定値: 10。 デプロイ エラーのバックオフ間隔。
**DeploymentMaxRetryInterval**:既定値は 3600 秒。デプロイが失敗した場合の最大バックオフ。
**DeploymentMaxFailureCount**:既定値は 20。 ノードへのアプリケーションのデプロイは、DeploymentMaxFailureCount 回、再試行された後に失敗します。

### <a name="deactivation"></a>非アクティブ化
**DeactivationScanInterval**: 既定値は 600 秒。今までにレプリカをホストしたことがない ( つまり使用されていない) 場合に、ServicePackage がレプリカをホストするために与えられる最小時間。
**DeactivationGraceInterval**:既定値は 60 秒。**共有**プロセス モデルの場合にレプリカをホストした後に、再び別のレプリカをホストするために ServicePackage に与えられる時間。
**ExclusiveModeDeactivationGraceInterval**:既定値は 1 秒。**専有**プロセス モデルの場合にレプリカをホストした後に、再び別のレプリカをホストするために ServicePackage に与えられる時間。

## <a name="next-steps"></a>次のステップ
[アプリケーションをパッケージ化][a3]して展開できるようにします。

[アプリケーションをデプロイおよび削除][a4]します。 この記事では、PowerShell を使ってアプリケーション インスタンスを管理する方法について説明しています

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
