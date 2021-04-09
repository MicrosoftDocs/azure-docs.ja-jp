---
title: C# で最初の Service Fabric アプリケーションを作成する
description: ステートレス サービスとステートフル サービスを使用して Microsoft Azure Service Fabric アプリケーションを作成する方法。
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev, devx-track-csharp
ms.openlocfilehash: 45341c98a40cbcabfa8b96f2016f02f1755fe2b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791529"
---
# <a name="get-started-with-reliable-services"></a>Reliable Services 使用

> [!div class="op_single_selector"]
> * [Windows での C#](service-fabric-reliable-services-quick-start.md)
> * [Linux での Java](service-fabric-reliable-services-quick-start-java.md)

Azure Service Fabric アプリケーションには、コードを実行する 1 つ以上のサービスが含まれています。 ここでは、 [Reliable Services](service-fabric-reliable-services-introduction.md)を使用して、ステートレスとステートフルの両方の Service Fabric アプリケーションを作成する方法を説明します。  

## <a name="basic-concepts"></a>基本的な概念

Reliable Services の使用を開始するには、いくつかの基本的な概念を理解する必要があります。

* **サービスの種類**: 使用するサービス実装です。 `StatelessService` を拡張する記述したクラスと、そこで使用する他のコードまたは依存関係のほか、名前やバージョン番号によって定義されます。
* **名前付きサービス インスタンス**: サービスを実行するには、サービスの種類の名前付きインスタンスを作成します。これは、クラスの種類のオブジェクト インスタンスを作成するのに似ています。 サービス インスタンスの名前は、"fabric:/MyApp/MyService" のように、"fabric:/" スキームを使用した URI の形式になります。
* **サービス ホスト**: 作成した名前付きサービス インスタンスは、ホスト プロセス内で実行する必要があります。 サービス ホストは単なる 1 プロセスで、ここでサービスのインスタンスを実行できます。
* **サービス登録**: 登録はすべてを 1 つにまとめます。 サービス ホストでサービスの種類を Service Fabric ランタイムに登録して、Service Fabric がそのインスタンスを作成して実行できるようにする必要があります。  

## <a name="create-a-stateless-service"></a>ステートレス サービスの作成

ステートレス サービスは、クラウド アプリケーションで現在基準となっている種類のサービスです。 ステートレスと見なされるのは、確実に格納する必要があるデータや高可用性を実現する必要があるデータが、サービス自体には含まれていないためです。 ステートレス サービスのインスタンスが終了すると、すべての内部状態が失われます。 この種類のサービスでは、高可用性と高い信頼性を実現するために、Azure テーブルや SQL Database などの外部ストアに状態を格納する必要があります。

Visual Studio 2017 または Visual Studio 2019 を管理者として起動し、*HelloWorld* という名前の新しい Service Fabric アプリケーション プロジェクトを作成します。

![[新しいプロジェクト] ダイアログを使用して新しい Service Fabric アプリケーションを作成する](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

次に、 **.NET Core 2.0** を使用して、*HelloWorldStateless* という名前のステートレス サービス プロジェクトを作成します。

![2 番目のダイアログ ボックスでステートレス サービス プロジェクトを作成する](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

これで、ソリューションには、次の 2 つのプロジェクトが含まれています。

* *HelloWorld*。 これは *サービス* を含む *アプリケーション* プロジェクトです。 また、アプリケーションを説明するアプリケーション マニフェストと、アプリケーションをデプロイするのに役立つ多くの PowerShell スクリプトも含まれます。
* *HelloWorldStateless*。 これはサービス プロジェクトです。 ステートレス サービスの実装が含まれています。

## <a name="implement-the-service"></a>サービスの実装

サービス プロジェクト内にある **HelloWorldStateless.cs** ファイルを開きます。 Service Fabric では、どのようなビジネス ロジックもサービスで実行できます。 サービス API には、コードのエントリ ポイントが 2 つあります。

* *RunAsync* という変更可能なエントリ ポイント メソッドでは、実行時間の長いコンピューティング ワークロードなどの任意のワークロードの実行を開始できます。

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* 選択した通信スタック (ASP.NET Core など) を接続できる通信エントリ ポイント。 これは、ユーザーおよびその他のサービスからの要求の受信を開始できる場所です。

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

このチュートリアルでは、 `RunAsync()` エントリ ポイント メソッドを取り上げます。 これは、コードの実行をすぐに開始できる場所です。
プロジェクト テンプレートには、ローリング カウントをインクリメントする `RunAsync()` の実装例が含まれます。

> [!NOTE]
> 通信スタックを使用する方法の詳細については、[ASP.NET Core とのサービス通信](service-fabric-reliable-services-communication-aspnetcore.md)に関するページを参照してください。

### <a name="runasync"></a>RunAsync

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

プラットフォームは、サービスのインスタンスが配置され実行準備ができたときに、このメソッドを呼び出します。 ステートレス サービスの場合、これは単にサービス インスタンスが開いたことを意味します。 サービス インスタンスを終了する必要がある場合のために、キャンセル トークンが提供されています。 Service Fabric では、サービス インスタンスの開始から終了のサイクルは、サービスのライフタイムで何度も発生する可能性があります。 これは、さまざまな理由で発生する可能性があります。

* システムがリソース分散のためにサービス インスタンスを移動している。
* コードでエラーが発生している。
* アプリケーションまたはシステムがアップグレードされている。
* 基礎となるハードウェアで障害が発生している。

この調整は、サービスの可用性を高めて適切なバランスを取るために、システムによって管理されます。

`RunAsync()` は同期的なブロックを行いません。 RunAsync の実装では、タスクを返すか、長時間にわたって実行される操作またはブロック操作を待機して、ランタイムを続行できるようにします。 前の例の `while(true)` ループでは、タスクを返す `await Task.Delay()` が使用されています。 ワークロードで同期的にブロックする必要がある場合は、`RunAsync` 実装で `Task.Run()` を使用して新しいタスクをスケジュールする必要があります。

ワークロードの取り消しは、提供されたキャンセル トークンを使用して協調的に調整されます。 システムはタスクが完了 (正常に完了、キャンセル、または失敗) するまで待機してから、次に進みます。 システムからキャンセルが要求された場合は、キャンセル トークンを利用して作業を完了し、できるだけ早く `RunAsync()` を終了することが重要です。

このステートレス サービスの例では、カウントはローカル変数に格納されます。 これはステートレス サービスであるため、保存される値は、サービス インスタンスの現在のライフサイクルのみで保持されます。 このサービスを移動または再起動すると、値は失われます。

## <a name="create-a-stateful-service"></a>ステートフル サービスの作成

Service Fabric には、新しい種類のステートフルなサービスが導入されています。 ステートフル サービスは、サービス内に状態を確実に維持でき、それを使用するコードと同じ場所に配置できます。 Service Fabric によって状態の可用性が高まるため、外部ストアに状態を維持する必要がなくなります。

サービスが移動または再起動した場合でも、カウンター値をステートレスから高可用と永続性に変換するには、ステートフル サービスが必要です。

先ほどと同じ *HelloWorld* アプリケーションで、アプリケーション プロジェクトの [サービス] を右クリックし、 **[追加] -> [新しい Service Fabric サービス]** を選択することで、新しいサービスを追加できます。

![Service Fabric アプリケーションにサービスを追加します。](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

**[.NET Core 2.0]、[ステートフル サービス]** の順に選択し、*HelloWorldStateful* という名前を付けます。 **[OK]** をクリックします。

![[新しいプロジェクト] ダイアログを使用して新しい Service Fabric のステートフル サービスを作成する](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

これで、アプリケーションには、ステートレス サービス *HelloWorldStateless* とステートフル サービス *HelloWorldStateful* の 2 つのサービスが含まれるようになります。

ステートフル サービスのエントリ ポイントは、ステートレス サービスと同じです。 主な違いは、*状態プロバイダー* を使用して状態を確実に保存できることです。 Service Fabric には、[Reliable Collection](service-fabric-reliable-services-reliable-collections.md) という状態プロバイダー実装が用意されています。Reliable Collection では、Reliable State Manager を使用してレプリケートされたデータ構造を作成できます。 ステートフル Reliable Service では、この状態プロバイダーを既定で使用します。

**HelloWorldStateful** で、次の RunAsync メソッドを含む *HelloWorldStateful.cs* を開きます。

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync

`RunAsync()` は、ステートフル サービスとステートレス サービスで同様に動作します。 ただし、ステートフル サービスでは、プラットフォームは、 `RunAsync()`を実行する前に、ユーザーに代わって追加の作業を行います。 この作業には、Reliable State Manager と Reliable Collection が使用できる状態にあることの確認が含まれる場合があります。

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Reliable Collection と Reliable State Manager

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2#microsoft_servicefabric_data_collections_ireliabledictionary_2) は、サービスに状態を確実に格納するために使用できるディクショナリ実装です。 Service Fabric と Reliable Collection を使用すると、データをサービスに直接格納できるため、外部の永続ストアが必要ありません。 Reliable Collection により、データの可用性が向上します。 Service Fabric では、サービスの複数の *レプリカ* を作成して管理することでこれを実現します。 また、これらのレプリカとその状態遷移の管理の複雑さを取り除く API も提供します。

Reliable Collection にはカスタム型を含むすべての .NET 型を格納できます。ただし次の点にご注意ください。

* Service Fabric がノード全体で状態を *レプリケート* して状態の可用性を高め、Reliable Collection が各レプリカでデータをローカル ディスクに保存します。 これは、Reliable Collection で保存されるすべてのデータは *シリアル化可能である* 必要があることを意味します。 既定では、Reliable Collection は [DataContract](/dotnet/api/system.runtime.serialization.datacontractattribute) を使用してシリアル化します。そのため、既定のシリアライザーを使用する場合は、使用する型が[データ コントラクト シリアライザーでサポートされている](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)ことを確認することが重要です。
* Reliable Collection でトランザクションをコミットすると、可用性が高めるためにオブジェクトがレプリケートされます。 Reliable Collection に格納されるオブジェクトは、サービスのローカル メモリに保持されます。 これは、オブジェクトへのローカルな参照があることを意味します。
  
   トランザクションの Reliable Collection を更新せずに、これらのオブジェクトのローカル インスタンスを変更しないようにしてください。 オブジェクトのローカル インスタンスの変更は自動的にレプリケートされないためです。 オブジェクトをディクショナリに再挿入するか、ディクショナリで *update* メソッドのいずれかを使用する必要があります。

Reliable Collection の管理は Reliable State Manager が行います。 サービス内のどの場所でも、Reliable Collection の名前を指定することで、Reliable State Manager に Reliable Collection をいつでも要求できます。 Reliable State Manager により、参照を確実に取得できます。 Reliable Collection インスタンスへの参照をクラス メンバー変数やプロパティに保存することはお勧めしません。 サービスのライフサイクル中、参照が常にインスタンスに設定されていることを保証するために特に注意を払う必要があります。 この作業は Reliable State Manager によって処理され、繰り返されるアクセスのために最適化されます。

### <a name="transactional-and-asynchronous-operations"></a>トランザクション処理と非同期処理

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Reliable Collection には、統合言語クエリ (LINQ) を除き、対応する `System.Collections.Generic` と `System.Collections.Concurrent` が実行する操作と同じ操作が多数あります。 Reliable Collection での操作は非同期です。 Reliable Collection での書き込み操作では、データをレプリケートしてディスクに保持するために I/O 操作が実行されるためです。

Reliable Collection の操作は *トランザクション* であるため、複数の Reliable Collection と操作で状態の整合性を維持できます。 たとえば、1 つのトランザクション内で Reliable Queue から作業項目をデキューし、その項目の操作を実行してから、結果を Reliable Dictionary に保存するとします。 これはアトミック操作として扱われので、操作全体が成功するか、操作全体がロールバックされることが保証されます。 項目をデキューしたが、結果を保存する前にエラーが発生した場合は、トランザクション全体がロールバックされ、項目は処理のためにキューに残ります。

## <a name="run-the-application"></a>アプリケーションの実行
*HelloWorld* アプリケーションに戻ります。 ここからサービスを構築してデプロイできます。 **F5** キーを押すと、アプリケーションがビルドされ、ローカル クラスターにデプロイされます。

サービスが開始されたら、生成された Event Tracing for Windows (ETW) イベントを **[診断イベント]** ウィンドウで確認できます。 アプリケーションのステートレス サービスとステートフル サービスの両方のイベントが表示されるのでご注意ください。 **[一時停止]** ボタンをクリックして、ストリームを一時停止できます。 その後、メッセージを展開して、メッセージの詳細を調べることができます。

> [!NOTE]
> アプリケーションを実行する前に、ローカル開発クラスターが実行されていることを確認します。 ローカル環境の設定については、 [ファースト ステップ ガイド](service-fabric-get-started.md) をご覧ください。
> 
> 

![Visual Studio で診断イベントを表示する](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>次のステップ
[Visual Studio での Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)

[はじめに: OWIN 自己ホストによる Service Fabric Web API サービス](./service-fabric-reliable-services-communication-aspnetcore.md)

[Reliable Collection の詳細](service-fabric-reliable-services-reliable-collections.md)

[アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)

[アプリケーションのアップグレード](service-fabric-application-upgrade.md)

[Reliable Services の開発者向けリファレンス](/previous-versions/azure/dn706529(v=azure.100))
