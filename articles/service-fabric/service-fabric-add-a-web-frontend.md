---
title: "ASP.NET Core を使用したアプリケーション用の Web フロントエンドの作成 | Microsoft Docs"
description: "ASP.NET Core Web API プロジェクト、および ServiceProxy を介したサービス間通信を使用して、Web に Service Fabric アプリケーションを公開します。"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/29/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: faa452d3ba407d5e1ffb3fc4b60b71b2bc64113b


---
# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>ASP.NET Core を使用したアプリケーション用の Web サービス フロントエンドの構築
既定では、Azure Service Fabric サービスには、Web に対するパブリック インターフェイスがありません。 HTTP クライアントにアプリケーションの機能を公開するには、エントリ ポイントとして機能する Web プロジェクトを作成し、そこから個々のサービスと通信する必要があります。

このチュートリアルでは、 [Visual Studio での初めてのアプリケーションの作成](service-fabric-create-your-first-application-in-visual-studio.md) に関するチュートリアルで取り上げなかった部分について説明し、ステートフルなカウンター サービスの前に Web サービスを追加します。 Visual Studio での初めてのアプリケーションの作成に関するチュートリアルをまだ終了していない場合は、先にそちらのチュートリアルを進めてください。

## <a name="add-an-aspnet-core-service-to-your-application"></a>アプリケーションへの ASP.NET Core サービスの追加
ASP.NET Core は軽量のクロスプラットフォーム Web 開発フレームワークであり、これを使用すると、最新の Web UI と Web API を作成できます。 ASP.NET Web API プロジェクトを既存のアプリケーションに追加してみましょう。

> [!NOTE]
> このチュートリアルを実行するには、[.NET Core 1.0 をインストール][dotnetcore-install]する必要があります。
> 
> 

1. ソリューション エクスプローラーで、アプリケーション プロジェクトの **[サービス]** を右クリックし、**[追加]、[Service Fabric サービスの新規作成]** の順に選択します。
   
    ![既存アプリケーションへの新しいサービスの追加][vs-add-new-service]
2. **サービスの作成**ページで、**[ASP.NET Core]** を選択し、名前を付けます。
   
    ![Choosing ASP.NET web service in the new service dialog][vs-new-service-dialog]
3. 次のページには、一連の ASP.NET Core プロジェクト テンプレートが表示されます。 これらは、Service Fabric アプリケーションの外部で ASP.NET Core プロジェクトを作成した場合に表示されるテンプレートと同じです。 このチュートリアルでは、 **[Web API]**を選択します。 ただし、完全な Web アプリケーションの作成にも同じ概念を適用できます。
   
    ![ASP.NET プロジェクトの種類の選択][vs-new-aspnet-project-dialog]
   
    作成した Web API プロジェクトでは、2 つのサービスがアプリケーションに含まれます。 アプリケーションの作成を続けながら、まったく同じ方法でさらにサービスを追加することができます。 それぞれを個別にバージョン管理およびアップグレードできます。

> [!TIP]
> ASP.NET Core サービスの構築の詳細については、 [ASP.NET Core のドキュメント](https://docs.asp.net)を参照してください。
> 
> 

## <a name="run-the-application"></a>アプリケーションの実行
手順を把握できるように、新しいアプリケーションをデプロイし、ASP.NET Core Web API テンプレートによって提供される既定の動作を見てみます。

1. Visual Studio で F5 キーを押してアプリをデバッグします。
2. デプロイが完了すると、Visual Studio はブラウザーを起動して ASP.NET Web API サービスのルート (例: http://localhost:33003) を開きます。 ポート番号はランダムに割り当てられるため、お使いのコンピューターでは異なる場合があります。 ASP.NET Core Web API テンプレートではルートの既定の動作が指定されないため、ブラウザーでエラーが発生します。
3. ブラウザーの場所に `/api/values` を追加します。 これにより、Web API テンプレート内の ValuesController に対して `Get` メソッドが呼び出されます。 テンプレートで指定される既定の応答として、2 つの文字列を格納する JSON 配列が返されます。
   
    ![Default values returned from ASP.NET Core Web API template][browser-aspnet-template-values]
   
    チュートリアルの最後では、これらの既定値をステートフル サービスからの最新のカウンター値で置き換えます。

## <a name="connect-the-services"></a>サービスへの接続
Service Fabric は、Reliable Services との通信方法において完全な柔軟性を提供します。 1 つのアプリケーション内には、TCP 経由でアクセスできるサービスもあれば、HTTP REST API 経由でアクセスできるサービスもあります。また、Web ソケットを使用してアクセスできるサービスもまだあります。 使用可能なオプションとトレードオフについては、[サービスとの通信](service-fabric-connect-and-communicate-with-services.md)に関する記事を参照してください。 このチュートリアルでは、より簡単な方法の 1 つに従い、SDK で提供される `ServiceProxy`/`ServiceRemotingListener` クラスを使用します。

(リモート プロシージャ コール (RPC) でモデル化された) `ServiceProxy` アプローチで、サービスのパブリック コントラクトとして機能するインターフェイスを定義します。 次に、そのインターフェイスを使用して、サービスと対話するためのプロキシ クラスを生成します。

### <a name="create-the-interface"></a>インターフェイスの作成
最初に、ステートフル サービスとそのクライアント (ASP.NET Core プロジェクトなど) の間のコントラクトとして機能するインターフェイスを作成します。

1. ソリューション エクスプローラーでソリューションを右クリックし、 **ブラウザーの場所に** > **[新しいプロジェクト]**する必要があります。
2. 左側のナビゲーション ウィンドウで **[Visual C#]** エントリを選択し、**[クラス ライブラリ]** テンプレートを選択します。 .NET Framework のバージョンが **4.5.2**に設定されていることを確認します。
   
    ![ステートフル サービス用のインターフェイス プロジェクトの作成][vs-add-class-library-project]
3. インターフェイスを `ServiceProxy`で使用可能にするには、そのインターフェイスが IService インターフェイスから派生している必要があります。 このインターフェイスは、Service Fabric NuGet パッケージの 1 つに含まれます。 パッケージを追加するには、新しいクラス ライブラリ プロジェクトを右クリックして、 **[NuGet パッケージの管理]**を選択します。
4. **Microsoft.ServiceFabric.Services** パッケージを探してインストールします。
   
    ![Services NuGet パッケージの追加][vs-services-nuget-package]
5. クラス ライブラリで、1 つのメソッド `GetCountAsync`を含むインターフェイスを作成し、IService からインターフェイスを拡張します。
   
    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;
   
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```

### <a name="implement-the-interface-in-your-stateful-service"></a>ステートフル サービスでのインターフェイスの実装
インターフェイスを定義したので、次にステートフル サービスでそれを実装する必要があります。

1. ステートフル サービスでは、インターフェイスを含むクラス ライブラリ プロジェクトへの参照を追加します。
   
    ![ステートフル サービスのクラス ライブラリ プロジェクトへの参照の追加][vs-add-class-library-reference]
2. `StatefulService` から継承したクラス (`MyStatefulService` など) を探し、そのクラスを拡張して `ICounter` インターフェイスを実装します。
   
    ```c#
    using MyStatefulService.Interfaces;
   
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```
3. 次に、`ICounter` インターフェイスで定義されている単一のメソッド (`GetCountAsync`) を実装します。
   
    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>サービス リモート処理リスナーを使用したステートフル サービスの公開
`ICounter` インターフェイスを実装した後、ステートフル サービスを他のサービスから呼び出すことができるようにする最後の手順は、通信チャネルを開くことです。 ステートフル サービスのために、Service Fabric には、 `CreateServiceReplicaListeners`というオーバーライド可能なメソッドが用意されています。 このメソッドを使用すると、サービスに対して有効にする通信の種類に基づき、1 つ以上の通信リスナーを指定できます。

> [!NOTE]
> ステートレス サービスへの通信チャネルを開く同等のメソッドは、`CreateServiceInstanceListeners` という名前です。
> 
> 

ここでは、既存の `CreateServiceReplicaListeners` メソッドを置き換え、`ServiceRemotingListener` のインスタンスを指定します。これにより、`ServiceProxy` を介してクライアントから呼び出すことができる RPC エンドポイントが作成されます。  

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>ServiceProxy クラスを使用したサービスとの対話
ステートフル サービスでは、他のサービスからトラフィックを受信する準備ができました。 残りの手順は、サービスと通信するためのコードを ASP.NET Web サービスから追加するだけです。

1. ASP.NET プロジェクトで、 `ICounter` インターフェイスを含むクラス ライブラリへの参照を追加します。
2. **[ビルド]** メニューの **[構成マネージャー]** を開きます。 次のような結果が表示されます。
   
    ![任意の CPU としてクラス ライブラリを示す構成マネージャー][vs-configuration-manager]
   
    クラス ライブラリ プロジェクト **MyStatefulService.Interface**は、任意の CPU を構築するために構成されます。 Service Fabric を正常に機能させるには、明示的に x64 を対象にする必要があります。 プラットフォームのドロップダウンをクリックし、 **[新規]**を選択して、x64 プラットフォーム構成を作成します。
   
    ![クラス ライブラリの新しいプラットフォームを作成する][vs-create-platform]
3. 前にクラス ライブラリ プロジェクトで行ったのと同様に、ASP.NET プロジェクトに Microsoft.ServiceFabric.Services パッケージを追加します。 これにより、 `ServiceProxy` クラスが提供されます。
4. **Controllers** フォルダーで `ValuesController` クラスを開きます。 現時点では、`Get` メソッドはハードコーディングされた文字列配列 "value1" と "value2" を返すだけであることに注意してください。これらは、先にブラウザーに表示されていたものと一致します。 この実装を次のコードに置き換えます。
   
    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
   
    ...
   
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
   
    コードの最初の行が重要です。 ステートフル サービスへの ICounter プロキシを作成するには、パーティション ID とサービス名の 2 つの情報を提供する必要があります。
   
    パーティション分割を使用すると、顧客 ID や郵便番号などの定義したキーに基づいて異なるバケットにステートを分割することにより、ステートフル サービスを拡張できます。 この単純なアプリケーションではステートフル サービスはパーティションを 1 つしか持たないので、キーは重要ではありません。 どのキーを指定しても同じパーティションになります。 サービスのパーティション分割の詳細については、「 [Service Fabric Reliable Services をパーティション分割する方法](service-fabric-concepts-partitioning.md)」を参照してください。
   
    サービス名は、fabric:/&lt;アプリケーション名&gt;/&lt;サービス名&gt; の形式の URI です。
   
    これら 2 つの情報により、Service Fabric は要求送信先のコンピューターを一意に識別できます。 また、 `ServiceProxy` クラスは、ステートフル サービス パーティションをホストするコンピューターで障害が発生したため、別のコンピューターを昇格させてその代わりにする必要がある状況をシームレスに処理します。 この抽象化により、他のサービスを処理するクライアント コードの作成が大幅に簡略化されます。
   
    プロキシを作成した後は、 `GetCountAsync` メソッドを呼び出して結果を返すだけです。
5. もう一度 F5 キーを押して、変更したアプリケーションを実行します。 前と同じように、Visual Studio はブラウザーを自動的に起動して Web プロジェクトのルートを表示します。 "api/values" パスを追加すると、現在のカウンター値が返されることがわかります。
   
    ![ブラウザーに表示されたステートフル カウンター値][browser-aspnet-counter-value]
   
    定期的にブラウザーを更新して、カウンターの値が更新されるのを確認します。

> [!WARNING]
> テンプレートで提供される Kestrel という ASP.NET Core Web サーバーは、 [現在、インターネットに直接接続するトラフィックを処理することはできません](https://docs.asp.net/en/latest/fundamentals/servers.html#kestrel)。 運用環境のシナリオでは、[API Management][api-management-landing-page] の背後にある ASP.NET Core エンドポイントまたは他のインターネット接続ゲートウェイをホストすることを検討してください。 IIS 内でのデプロイメントでは、Service Fabric がサポートされていないことに注意してください。
> 
> 

## <a name="what-about-actors"></a>アクターについて
このチュートリアルではステートフル サービスと通信する Web フロントエンドの追加を取り上げました。 ただし、非常によく似たモデルに従ってアクターと対話することもできます。 実際にはもう少し簡単です。

アクター プロジェクトを作成すると、Visual Studio によってインターフェイス プロジェクトが自動的に生成されます。 そのインターフェイスを使用して Web プロジェクトでアクター プロキシを生成し、アクターと通信できます。 通信チャネルは自動的に指定されます。 そのため、このチュートリアルでステートフル サービスに関して行ったような `ServiceRemotingListener` の確立に相当することは何も行う必要がありません。

## <a name="how-web-services-work-on-your-local-cluster"></a>ローカル クラスター上の Web サービスのしくみ
一般に、ローカル クラスターにデプロイした複数コンピューター クラスターにまったく同じ Service Fabric アプリケーションをデプロイすると、高い確率で意図したとおりに動作させることができます。 これは、ローカル クラスターが 5 ノードの構成を 1 つのコンピューターにまとめただけであるためです。

ただし、Web サービスに関しては 1 つの重要な違いがあります。 クラスターが Azure でのようにロード バランサーの背後に配置されている場合、ロード バランサーはコンピューター間にトラフィックをラウンドロビンするだけなので、Web サービスをすべてのコンピューターにデプロイする必要があります。 これは、サービスの `InstanceCount` を特別な値 "-1" に設定することによって実現できます。

これに対して、Web サービスをローカルで実行するときは、必ずサービスのインスタンスが 1 つだけ実行されているようにする必要があります。 そうしないと、同じパスとポートでリッスンしている複数のプロセスから競合が発生します。 そのため、ローカル デプロイの場合は Web サービスのインスタンス数を 1 に設定する必要があります。

異なる環境で異なる値を構成する方法については、「 [複数の環境のアプリケーション パラメーターを管理する](service-fabric-manage-multiple-environment-app-configuration.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [アプリケーションをクラウドにデプロイするために Azure にクラスターを作成します](service-fabric-cluster-creation-via-portal.md)
* [サービスとの通信について学習します](service-fabric-connect-and-communicate-with-services.md)
* [ステートフル サービスのパーティション分割について学習します](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-configuration-manager]: ./media/service-fabric-add-a-web-frontend/vs-configuration-manager.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
[api-management-landing-page]: https://azure.microsoft.com/en-us/services/api-management/



<!--HONumber=Nov16_HO3-->


