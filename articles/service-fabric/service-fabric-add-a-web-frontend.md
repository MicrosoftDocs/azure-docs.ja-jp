---
title: "ASP.NET Core を使用した Azure Service Fabric アプリ用の Web フロントエンドの作成 | Microsoft Docs"
description: "ASP.NET Core プロジェクト、およびサービスのリモート処理を介したサービス間通信を使用して、Web に Service Fabric アプリケーションを公開します。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: b19aaa652f2c15573ded632ca1348e1a6752f080
ms.contentlocale: ja-jp
ms.lasthandoff: 06/10/2017


---
# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>ASP.NET Core を使用したアプリケーション用の Web サービス フロントエンドの構築
既定では、Azure Service Fabric サービスには、Web に対するパブリック インターフェイスがありません。 HTTP クライアントにアプリケーションの機能を公開するには、エントリ ポイントとして機能する Web プロジェクトを作成し、そこから個々のサービスと通信する必要があります。

このチュートリアルでは、「[最初の Azure Service Fabric アプリケーションを作成する](service-fabric-create-your-first-application-in-visual-studio.md)」のチュートリアルで取り上げなかった部分について説明し、ステートフルなカウンター サービスの前に ASP.NET Core Web サービスを追加します。 Visual Studio での初めてのアプリケーションの作成に関するチュートリアルをまだ終了していない場合は、先にそちらのチュートリアルを進めてください。

## <a name="set-up-your-environment-for-aspnet-core"></a>ASP.NET Core の環境をセットアップする

このチュートリアルを実行するには、Visual Studio 2017 が必要です。 エディションは任意です。 

 - [Visual Studio 2017 をインストールする](https://www.visualstudio.com/)

ASP.NET Core Service Fabric アプリケーションを開発するには、次のワークロードをインストールする必要があります。
 - **Azure 開発** (*[Web & Cloud]\(Web とクラウド\)* 以下)
 - **ASP.NET および Web 開発** (*[Web & Cloud]\(Web とクラウド\)* 以下)
 - **.NET Core クロスプラットフォーム** (*[他のツールセット]* 以下)

>[!Note] 
>Visual Studio 2015 用 .NET Core ツールは更新されなくなりましたが、まだ Visual Studio 2015 を使用している場合は、[.NET Core VS 2015 Tooling Preview 2](https://www.microsoft.com/net/download/core) をインストールする必要があります。

## <a name="add-an-aspnet-core-service-to-your-application"></a>アプリケーションへの ASP.NET Core サービスの追加
ASP.NET Core は軽量のクロスプラットフォーム Web 開発フレームワークであり、これを使用すると、最新の Web UI と Web API を作成できます。 

ASP.NET Web API プロジェクトを既存のアプリケーションに追加してみましょう。

1. ソリューション エクスプローラーで、アプリケーション プロジェクトの **[サービス]** を右クリックし、**[追加]、[Service Fabric サービスの新規作成]** の順に選択します。
   
    ![既存アプリケーションへの新しいサービスの追加][vs-add-new-service]
2. **サービスの作成**ページで、**[ASP.NET Core]** を選択し、名前を付けます。
   
    ![Choosing ASP.NET web service in the new service dialog][vs-new-service-dialog]

3. 次のページには、一連の ASP.NET Core プロジェクト テンプレートが表示されます。 これらは、Service Fabric アプリケーションの外部で ASP.NET Core プロジェクトを作成した場合に表示されるテンプレートと同じですが、サービスを Service Fabric ランタイムに登録するための少量の追加コードが含まれています。 このチュートリアルでは、**[Web API]** を選択します。 ただし、完全な Web アプリケーションの作成にも同じ概念を適用できます。
   
    ![ASP.NET プロジェクトの種類の選択][vs-new-aspnet-project-dialog]
   
    作成した Web API プロジェクトでは、2 つのサービスがアプリケーションに含まれます。 アプリケーションの作成を続けながら、まったく同じ方法でさらにサービスを追加することができます。 それぞれを個別にバージョン管理およびアップグレードできます。

## <a name="run-the-application"></a>アプリケーションの実行
手順を把握できるように、新しいアプリケーションをデプロイし、ASP.NET Core Web API テンプレートによって提供される既定の動作を見てみます。

1. Visual Studio で F5 キーを押してアプリをデバッグします。
2. デプロイが完了すると、Visual Studio からブラウザーが起動し、ASP.NET Web API サービスのルートにアクセスされます。 ASP.NET Core Web API テンプレートではルートの既定の動作が指定されないため、ブラウザーで 404 エラーが発生します。
3. ブラウザーの場所に `/api/values` を追加します。 すると、Web API テンプレートの ValuesController で `Get` メソッドが呼び出されます。 このメソッドは、テンプレートで指定される既定の応答として、2 つの文字列を格納する JSON 配列を返します。
   
    ![Default values returned from ASP.NET Core Web API template][browser-aspnet-template-values]
   
    このチュートリアルを完了すると、このページには既定の文字列ではなくステートフル サービスの最近のカウンター値が表示されます。

## <a name="connect-the-services"></a>サービスへの接続
Service Fabric は、Reliable Services との通信方法において完全な柔軟性を提供します。 1 つのアプリケーション内には、TCP 経由でアクセスできるサービスもあれば、HTTP REST API 経由でアクセスできるサービスもあります。また、Web ソケットを使用してアクセスできるサービスもまだあります。 使用可能なオプションとトレードオフについては、[サービスとの通信](service-fabric-connect-and-communicate-with-services.md)に関する記事を参照してください。 このチュートリアルでは、SDK に含まれている [Service Fabric Service Remoting](service-fabric-reliable-services-communication-remoting.md) を使用します。

(リモート プロシージャ コール (RPC) でモデル化された) Service Remoting アプローチで、サービスのパブリック コントラクトとして機能するインターフェイスを定義します。 次に、そのインターフェイスを使用して、サービスと対話するためのプロキシ クラスを生成します。

### <a name="create-the-remoting-interface"></a>リモート処理インターフェイスを作成する
最初に、ステートフル サービスと他のサービス (ここでは ASP.NET Core Web プロジェクト) の間のコントラクトとして機能するインターフェイスを作成します。 RPC の呼び出しに使用するすべてのサービスでこのインターフェイスを共有する必要があるため、クラス ライブラリ プロジェクト内で作成します。

1. ソリューション エクスプローラーでソリューションを右クリックし、 **ブラウザーの場所に** > **[新しいプロジェクト]**する必要があります。

2. 左側のナビゲーション ウィンドウで **[Visual C#]** エントリを選択し、**[クラス ライブラリ]** テンプレートを選択します。 .NET Framework のバージョンが **4.5.2**に設定されていることを確認します。
   
    ![ステートフル サービス用のインターフェイス プロジェクトの作成][vs-add-class-library-project]

3. **Microsoft.ServiceFabric.Services.Remoting** NuGet パッケージをインストールします。 NuGet パッケージ マネージャーで **Microsoft.ServiceFabric.Services.Remoting** を検索し、以下のように Service Remoting を使用するソリューションのすべてのプロジェクトについてインストールします。
   - サービス インターフェイスを含むクラス ライブラリ プロジェクト
   - ステートフル サービス プロジェクト
   - ASP.NET Core Web サービス プロジェクト
   
    ![Services NuGet パッケージの追加][vs-services-nuget-package]

4. クラス ライブラリで、1 つのメソッド `GetCountAsync`を含むインターフェイスを作成し、`Microsoft.ServiceFabric.Services.Remoting.IService` からインターフェイスを拡張します。 リモート処理インターフェイスは、Service Remoting インターフェイスであることを示すために、このインターフェイスから派生している必要があります。
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;
    using System.Threading.Tasks;
        
    ...

    namespace MyStatefulService.Interface
    {
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
    using MyStatefulService.Interface;
   
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
         ...
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
`ICounter` インターフェイスを実装したら、Service Remoting 通信チャネルを開くことが最後の手順です。 ステートフル サービスのために、Service Fabric には、 `CreateServiceReplicaListeners`というオーバーライド可能なメソッドが用意されています。 このメソッドを使用すると、サービスに対して有効にする通信の種類に基づき、1 つ以上の通信リスナーを指定できます。

> [!NOTE]
> ステートレス サービスへの通信チャネルを開く同等のメソッドは、`CreateServiceInstanceListeners` という名前です。

ここでは、既存の `CreateServiceReplicaListeners` メソッドを置き換え、`ServiceRemotingListener` のインスタンスを指定します。これにより、`ServiceProxy` を介してクライアントから呼び出すことができる RPC エンドポイントが作成されます。  

`IService` インターフェイス上で `CreateServiceRemotingListener` 拡張メソッドを使用すると、すべて既定の設定の `ServiceRemotingListener` を簡単に作成できます。 この拡張メソッドを使用するには、`Microsoft.ServiceFabric.Services.Remoting.Runtime` 名前空間をインポートしておきます。 

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
ステートフル サービスでは、RPC を介して他のサービスからトラフィックを受信する準備ができました。 残りの手順は、サービスと通信するためのコードを ASP.NET Web サービスから追加するだけです。

1. ASP.NET プロジェクトで、 `ICounter` インターフェイスを含むクラス ライブラリへの参照を追加します。

2. 前述の手順で **Microsoft.ServiceFabric.Services.Remoting** NuGet パッケージを ASP.NET プロジェクトに追加しました。 このパッケージには、ステートフル サービスに対する RPC の呼び出しに使用する `ServiceProxy` クラスがあります。 この NuGet パッケージが ASP.NET Core Web サービス プロジェクトにインストールされていることを確認します。

4. **Controllers** フォルダーで `ValuesController` クラスを開きます。 現時点では、`Get` メソッドはハードコーディングされた文字列配列 "value1" と "value2" を返すだけであることに注意してください。これらは、先にブラウザーに表示されていたものと一致します。 この実装を次のコードに置き換えます。
   
    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
   
    ...

    [HttpGet]
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

## <a name="kestrel-and-weblistener"></a>Kestrel と WebListener

Kestrel という既定の ASP.NET Core Web サーバーは、[現在、インターネットに直接接続するトラフィックを処理](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel)することはできません。 そのため、Service Fabric 用の ASP.NET Core ステートレス サービス テンプレートでは、既定で [WebListener](https://docs.microsoft.com/aspnet/core/fundamentals/servers/weblistener) が使用されます。 

Service Fabric サービスの Kestrel および WebListener の詳細については、「[Service Fabric リライアブル サービスでの ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)」を参照してください。

## <a name="connecting-to-a-reliable-actor-service"></a>Reliable Actor サービスへの接続
このチュートリアルではステートフル サービスと通信する Web フロントエンドの追加を取り上げました。 ただし、非常によく似たモデルに従ってアクターと対話することもできます。 Reliable Actor プロジェクトを作成すると、Visual Studio によってインターフェイス プロジェクトが自動的に生成されます。 そのインターフェイスを使用して Web プロジェクトでアクター プロキシを生成し、アクターと通信できます。 通信チャネルは自動的に指定されます。 そのため、このチュートリアルでステートフル サービスに関して行ったような `ServiceRemotingListener` の確立に相当することは何も行う必要がありません。

## <a name="how-web-services-work-on-your-local-cluster"></a>ローカル クラスター上の Web サービスのしくみ
一般に、ローカル クラスターにデプロイした複数コンピューター クラスターにまったく同じ Service Fabric アプリケーションをデプロイすると、高い確率で意図したとおりに動作させることができます。 これは、ローカル クラスターが 5 ノードの構成を 1 つのコンピューターにまとめただけであるためです。

ただし、Web サービスに関しては 1 つの重要な違いがあります。 クラスターが Azure でのようにロード バランサーの背後に配置されている場合、ロード バランサーはコンピューター間にトラフィックをラウンドロビンするだけなので、Web サービスをすべてのコンピューターにデプロイする必要があります。 これは、サービスの `InstanceCount` を特別な値 "-1" に設定することによって実現できます。

これに対して、Web サービスをローカルで実行するときは、必ずサービスのインスタンスが 1 つだけ実行されているようにする必要があります。 そうしないと、同じパスとポートでリッスンしている複数のプロセスから競合が発生します。 そのため、ローカル デプロイの場合は Web サービスのインスタンス数を 1 に設定する必要があります。

異なる環境で異なる値を構成する方法については、「 [複数の環境のアプリケーション パラメーターを管理する](service-fabric-manage-multiple-environment-app-configuration.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
ASP.NET Core を使用したアプリケーションの Web フロントエンドのセットアップが完了したので、「[Service Fabric リライアブル サービスでの ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)」で ASP.NET Core と Service Fabric の連携方法についてさらに理解を深めます。

次に、一般的な[サービスとの通信の詳細](service-fabric-connect-and-communicate-with-services.md)を学習して、Service Fabric でのサービス通信のしくみの全体像を把握してください。

サービス通信のしくみを十分に理解したら、[Azure でクラスターを作成し、クラウドにアプリケーションをデプロイ](service-fabric-cluster-creation-via-portal.md)してください。

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows

