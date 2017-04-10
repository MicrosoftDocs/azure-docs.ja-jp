---
title: "Azure Service Fabric アプリケーション管理の自動化 | Microsoft Docs"
description: "PowerShell を使用して、Service Fabric アプリケーションをデプロイ、アップグレード、テスト、および削除します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: f03f4294-571d-4262-8a77-cc8b481b959d
ms.service: service-fabric
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 59133d11d2d66daa44f28ef5b9aa3aae92e56a1e
ms.lasthandoff: 03/15/2017


---
# <a name="automate-the-application-lifecycle-using-powershell"></a>PowerShell を使用したアプリケーション ライフサイクルの自動化
[Service Fabric アプリケーション ライフサイクル](service-fabric-application-lifecycle.md) のさまざまな要素を自動化することができます。  この記事では、PowerShell を使用して、Azure Service Fabric アプリケーションのデプロイ、アップグレード、削除、およびテストの一般的なタスクを自動化する方法を説明します。  アプリ管理用のマネージ API と HTTP API も利用できます。 詳細については、 [アプリケーションのライフサイクル](service-fabric-application-lifecycle.md) に関するページを参照してください。  

## <a name="prerequisites"></a>前提条件
この記事に記載されているタスクを進めていく前に、次の作業を行ってください。

* 「 [Service Fabric の技術概要](service-fabric-technical-overview.md)」に記載されている Service Fabric の概念を理解する。
* [ランタイム、SDK、およびツールをインストールする。](service-fabric-get-started.md)これによって、 **ServiceFabric** PowerShell モジュールもインストールされます。
* [PowerShell スクリプトの実行を有効化する](service-fabric-get-started.md#enable-powershell-script-execution)。
* ローカル クラスターを開始する。  管理者として新しい PowerShell ウィンドウを起動し、SDK フォルダー `& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
* この記事の PowerShell コマンドを実行する前に、[Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster): `Connect-ServiceFabricCluster localhost:19000` を使用して、ローカル Service Fabric クラスターに接続する。
* 下記のタスクを実行するうえで必要となる、デプロイ用の v1 アプリケーション パッケージと、アップグレード用の v2 アプリケーション パッケージを用意する。 [**WordCount** サンプル アプリケーション](http://aka.ms/servicefabricsamples) をダウンロードします (「Getting Started Samples (使用の開始サンプル)」にあります)。 Visual Studio でアプリケーションをビルドしてパッケージ化します (ソリューション エクスプローラーで **WordCount** を右クリックし、**[パッケージ]** を選択します)。 `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` 内の v1 パッケージを `C:\Temp\WordCount` にコピーします。 `C:\Temp\WordCount` を `C:\Temp\WordCountV2` にコピーし、アップグレード用の v2 アプリケーション パッケージを作成します。 テキスト エディターで `C:\Temp\WordCountV2\ApplicationManifest.xml` を開きます。 **ApplicationManifest** 要素の **ApplicationTypeVersion** 属性を "1.0.0" から "2.0.0" に変更して、アプリケーションのバージョン番号を更新します。 変更された ApplicationManifest.xml ファイルを保存します。

## <a name="task-deploy-a-service-fabric-application"></a>タスク: Service Fabric アプリケーションのデプロイ
アプリケーションをビルドしてパッケージ化 (またはアプリケーション パッケージをダウンロード) したら、ローカル Service Fabric クラスターにアプリケーションをデプロイできます。 デプロイには、アプリケーション パッケージのアップロード、アプリケーションの種類の登録、アプリケーション インスタンスの作成が含まれます。 クラスターに新しいアプリケーションをデプロイするには、このセクションの手順を使用します。

### <a name="step-1-upload-the-application-package"></a>手順 1: アプリケーション パッケージのアップロード
アプリケーション パッケージをイメージ ストアにアップロードすると、そのパッケージは内部 Service Fabric コンポーネントがアクセスできる場所に置かれます。  アプリケーション パッケージには、必要なアプリケーション マニフェスト、サービス マニフェスト、およびアプリケーションとサービス インスタンスを作成するコード、構成、データのパッケージが含まれています。 アプリケーション パッケージをローカルで検証する場合は、[Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) コマンドレットを使用します。  [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) コマンドは、パッケージをアップロードします。 次に例を示します。

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>手順 2: アプリケーションの種類の登録
アプリケーション パッケージを登録すると、アプリケーション マニフェストで宣言されたアプリケーションの種類とバージョンを利用できるようになります。 システムは、手順 1. でアップロードされたパッケージの読み取り、パッケージの検証 ([Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) をローカルで実行することに相当)、パッケージのコンテンツの処理、内部システムの場所への処理済みのパッケージのコピーを実行します。  [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) コマンドレットを実行します。

```powershell
Register-ServiceFabricApplicationType WordCount
```
クラスターに登録されているアプリケーションの種類をすべて表示するには、[Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) コマンドレットを実行します。

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>手順 3: アプリケーション インスタンスの作成
[New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication) コマンドを使用して正常に登録されたアプリケーションの種類のバージョンを使用して、アプリケーションをインスタンス化できます。 各アプリケーションの名前は、デプロイ時に宣言します。**fabric:** スキームで開始され、各アプリケーション インスタンスに対して一意でなければなりません。 アプリケーションの種類の名前とバージョンは、アプリケーション パッケージの **ApplicationManifest.xml** ファイルで宣言されます。 ターゲット アプリケーションの種類のアプリケーション マニフェストで既定のサービスが定義されている場合、それらのサービスも同時に作成されます。

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

[Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) コマンドは、正常に作成されたすべてのアプリケーション インスタンスとその全体的な状態を一覧表示します。 [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice) コマンドは、特定のアプリケーション インスタンス内で正常に作成されたすべてのサービス インスタンスを一覧表示します。 既定のサービス (指定されている場合) が表示されます。

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>タスク: Service Fabric アプリケーションのアップグレード
以前にデプロイした Service Fabric アプリケーションは、更新したアプリケーション パッケージを使用してアップグレードすることができます。 このタスクは、「タスク: Service Fabric アプリケーションのデプロイ」でデプロイされた WordCount アプリケーションをアップグレードします。 詳細については、「 [Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md) 」を参照してください。

この例では、わかりやすくするために、前提条件で作成した WordCountV2 アプリケーション パッケージにおいて、アプリケーションのバージョン番号のみが更新されています。 より現実に即したシナリオでは、サービスのコード、構成、またはデータ ファイルを更新した後、更新されたバージョン番号を使用してアプリケーションをリビルドし、パッケージ化する必要があります。  

### <a name="step-1-upload-the-updated-application-package"></a>手順 1: 更新したアプリケーション パッケージのアップロード
WordCount v1 アプリケーションをアップグレードする準備は完了しているものとします。 PowerShell ウィンドウを管理者として開き、「[**Get-ServiceFabricApplication**](/powershell/servicefabric/vlatest/get-servicefabricapplication)」と入力すると、バージョン 1.0.0 の WordCount アプリケーションの種類がデプロイされていることが表示されます。  

次に、更新されたアプリケーション パッケージを Service Fabric イメージ ストア (アプリケーション パッケージが Service Fabric により格納される場所) にコピーします。 **ApplicationPackagePathInImageStore** パラメーターから、Service Fabric にアプリケーション パッケージが見つかる場所が通知されます。 次のコマンドは、アプリケーション パッケージをイメージ ストアの **WordCountV2** にコピーします。  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>手順 2: 更新したアプリケーションの種類の登録
次の手順では、Service Fabric を使用して、アプリケーションの新しいバージョンを登録します。これは、[Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) コマンドレットを使用して実行できます。

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>手順 3: アップグレードの開始
アプリケーションのアップグレードには、さまざまなアップグレード パラメーター、タイムアウト、および正常性条件を適用できます。 詳細については、[「アプリケーション アップグレードのパラメーター」](service-fabric-application-upgrade-parameters.md)と[「アップグレード プロセス」](service-fabric-application-upgrade.md)を参照してください。 すべてのサービスとインスタンスは、アップグレード後に*正常*である必要があります。  **HealthCheckStableDuration** を 60 秒に設定します (これにより、次のアップグレード ドメインへアップグレードが進む前に、少なくとも 20 秒間、サービスが正常な状態になります)。  また、**UpgradeDomainTimeout** を 1200 秒に、**UpgradeTimeout** を 3000 秒に設定します。 最後に、**UpgradeFailureAction** を **[ロールバック]** に設定します。この設定を行うと、アップグレード中に問題が発生した場合、Service Fabric はアプリケーションを以前のバージョンにロールバックするよう要求します。

これで、[Start-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade) コマンドレットを使用して、アプリケーションのアップグレードを開始できます。

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

アプリケーション名は、以前にデプロイされた v1.0.0 のアプリケーション名 (fabric:/WordCount) と同じであることに注意してください。 Service Fabric はこの名前を使用して、アップグレードが実行されるアプリケーションを識別します。 タイムアウトを短く設定しすぎている場合、問題を示すタイムアウトのエラー メッセージが表示されることがあります。 「 [アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」を参照するか、タイムアウトの値を増加してください。

### <a name="step-4-check-upgrade-progress"></a>手順 4: アップグレードの進行状況の確認
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)、または [Get-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade) コマンドレットを使用して、アプリケーションのアップグレードの進行状況を監視できます。

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

数分で、[Get-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade) コマンドレットにより、すべてのアップグレード ドメインがアップグレードされた (完了した) ことが示されます。

## <a name="task-test-a-service-fabric-application"></a>タスク: Service Fabric アプリケーションのテスト
高品質なサービスのコードを記述するには、開発者は信頼性の低いインフラストラクチャの障害を誘発してサービスの安定性をテストできる必要があります。 Service Fabric を使用すると、開発者は、混乱とフェールオーバーのテスト シナリオを使用して、障害アクションを誘発し、障害のある状態でサービスをテストできます。  詳細については、「[Fault Analysis Service の概要](service-fabric-testability-overview.md)」を参照してください。

### <a name="step-1-run-the-chaos-test-scenario"></a>手順 1: 混乱テスト シナリオの実行
混乱のテスト シナリオでは、Service Fabric クラスター全体にわたる障害を生成します。 このシナリオには、一般的に数か月間または数年間から数時間で発生するエラーが圧縮されています。 障害率の高い交互に配置された障害の組み合わせにより、通常は見過ごされるめったに発生しないケースが検出されます。 次の例では、混乱テスト シナリオを60 分間実行します。

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>手順 2: フェールオーバー テスト シナリオの実行
フェールオーバー テスト シナリオは、クラスター全体ではなく、特定のサービス パーティションを対象とします。また、影響を受けていないその他のサービスは残します。 このシナリオでは、ビジネス ロジックを実行しながら、サービス検証で一連のシミュレートされた障害を反復処理します。 サービス検証の失敗は、さらに調査の必要な問題があることを示します。 フェールオーバー テストは、一度に&1; つの障害のみを誘発します。これに対し、混乱のテストでは可能性のある複数の障害を発生させることができます。 次の例では、fabric:/WordCount/WordCountService サービスに対して、60 秒間フェールオーバー テストを実行します。

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>タスク: Service Fabric アプリケーションの削除
デプロイされたアプリケーション インスタンスの削除や、クラスターからのプロビジョニングされているアプリケーションの種類の削除、および ImageStore からのアプリケーション パッケージの削除を実行することができます。

### <a name="step-1-remove-an-application-instance"></a>手順 1: アプリケーション インスタンスの削除
アプリケーション インスタンスが不要になったときは、[Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) コマンドレットを使用して完全に削除できます。 また、このコマンドレットでは、アプリケーションに属するすべてのサービスも自動的に削除され、すべてのサービス状態が完全に削除されます。 この操作を元に戻すことはできず、アプリケーションの状態を復元することはできません。

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>手順 2: アプリケーションの種類の登録解除
アプリケーションの種類の特定のバージョンが不要になったときは、[Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) コマンドレットを使用して登録解除します。 使用していない種類の登録を解除すると、イメージ ストアのアプリケーション パッケージによって使用されているストレージ領域を解放できます。 あるアプリケーションの種類でインスタンス化されたアプリケーションがない場合、またはそれを参照している保留中のアプリケーションのアップグレードがない場合、そのアプリケーションの種類を登録解除できます。

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>手順 3: アプリケーション パッケージの削除
アプリケーションの種類を登録解除したら、[Remove-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/remove-servicefabricapplicationpackage) コマンドレットを使用して、イメージ ストアからアプリケーション パッケージを削除できます。

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>次のステップ
[Service Fabric アプリケーションのライフサイクル](service-fabric-application-lifecycle.md)

[アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)

[アプリケーションのアップグレード](service-fabric-application-upgrade.md)

[Azure Service Fabric コマンドレット](/powershell/servicefabric/vlatest/servicefabric)


