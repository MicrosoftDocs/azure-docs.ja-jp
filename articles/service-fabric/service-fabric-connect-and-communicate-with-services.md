---
title: Azure Service Fabric のサービスとの接続と通信
description: Service Fabric のサービスに対して解決、接続、通信を行う方法について説明します。
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 11f525eba89dc963deee0ba9a86566361ef644de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576300"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Service Fabric のサービスとの接続と通信
Service Fabric では、Service Fabric クラスター内のどこかで、通常は複数の VM に分散されてサービスが実行されます。 サービスの場所は、サービスの所有者が移動することも、Service Fabric が自動的に移動することもあります。 サービスは特定のコンピューターまたはアドレスに対して静的に関連付けられてはいません。

Service Fabric アプリケーションは、通常はさまざまなサービスで構成されており、各サービスがそれぞれに特化したタスクを実行します。 これらのサービスが互いに通信することによって、Web アプリケーションの異なる部分のレンダリングのような機能を完成させることができます。 また、サービスに接続して通信するクライアント アプリケーションもあります。 このドキュメントでは、Service Fabric のサービスとの通信やサービス間での通信を設定する方法について説明します。

## <a name="bring-your-own-protocol"></a>独自のプロトコルを使用する
Service Fabric は、サービスのライフサイクル管理に役立ちますが、サービスで何を実行するかを決める機能は備えていません。 このことは通信にも当てはまります。 Service Fabric によってサービスが開かれると、サービスの側で、必要なプロトコルまたは通信スタックを使用して、受信要求向けにエンドポイントを設定することができます。 サービスは、URI などのアドレス指定スキームを使用して通常の **IP:ポート** アドレスでリッスンします。 複数のサービス インスタンスまたはレプリカでホスト プロセスを共有できますが、その場合はそれぞれが別のポートを使用するか、Windows での http.sys カーネル ドライバーのようなポート共有メカニズムを使用する必要があります。 いずれの場合も、ホスト プロセス内の各サービス インスタンスまたはレプリカを一意にアドレス指定できることが必要です。

![サービス エンドポイント][1]

## <a name="service-discovery-and-resolution"></a>サービスの検出と解決
分散システムでは、サービスが時間の経過と共に、あるコンピューターから別のコンピューターに移動することがあります。 移動の理由にはさまざまなものがあり、リソースの分散、アップグレード、フェールオーバー、スケールアウトなどが挙げられます。このため、別の IP アドレスを持つノードにサービスが移動すると、サービス エンドポイントのアドレスが変化することになります。また、サービスで動的に選択されるポートを使用している場合は、別のポートで開かれることがあります。

![Distribution of services][7]

Service Fabric では、ネーム サービスという検出および解決サービスを提供しています。 ネーム サービスでは、名前付きサービス インスタンスを、そのリッスン対象のエンドポイント アドレスにマッピングするテーブルが保持されています。 Service Fabric 内のすべての名前付きサービス インスタンスは、`"fabric:/MyApplication/MyService"` のように、URI として表される一意の名前を持ちます。 サービスの有効期間中にサービスの名前が変わることはありません。サービスの移動時に変化する可能性があるのは、エンドポイント アドレスのみです。 これは、URL は不変でも IP アドレスは変わることがある Web サイトと似ています。 また、Web サイトの URL を IP アドレスに解決する Web 上の DNS に似た機能として、Service Fabric には、サービス名をサービスのエンドポイント アドレスにマッピングするレジストラーがあります。

![Service Fabric に、サービス名をエンドポイント アドレスにマップするレジストラーがあることを示す図。][2]

サービスの解決とサービスへの接続を行う際は、以下の手順を繰り返し実行する必要があります。

* **解決**: サービスによって Naming Service から発行されたエンドポイントを取得します。
* **接続**: 取得したエンドポイントでサービスが使用するプロトコルを介してサービスに接続します。
* **再試行**: 接続試行はさまざまな理由で失敗する可能性があります。たとえば、エンドポイント アドレスの前回の解決時からサービスが移動している場合などに失敗します。 このような場合、前の解決と接続の手順を再試行する必要があり、このサイクルは接続が確立されるまで繰り返されます。

## <a name="connecting-to-other-services"></a>その他のサービスに接続する
クラスター内の各ノードは同じローカル ネットワーク上にあるため、クラスター内で相互接続しているサービスは、通常、他のサービスのエンドポイントに直接アクセスできます。 サービス間の接続を簡単にするために、Service Fabric では、Naming Service を使用する追加サービスを提供します。 DNS サービスとリバース プロキシ サービスです。


### <a name="dns-service"></a>DNS サービス
コンテナー化されたサービスなど多くのサービスには既存の URL 名が含まれるため、標準の DNS プロトコル (Naming Service プロトコルではなく) を使用してこれらを解決できます。これは特にアプリケーションの移行 (リフトアンドシフト) シナリオで便利です。 まさにこれが、DNS サービスの役割です。 DNS 名をサービス名にマップして、エンドポイントの IP アドレスを解決できます。 

次の図に示すように、Service Fabric クラスターで実行されている DNS サービスが DNS 名をサービス名にマップすると、Naming Service によって解決されて、接続するエンドポイントのアドレスが返されます。 サービスの DNS 名は、作成時に提供されます。 

![Service Fabric クラスターで実行されている DNS サービスによって、どのように DNS 名がサービス名にマップされ、Naming Service によって解決されて、接続するエンドポイントのアドレスが返されるかを示す図。][9]

DNS サービスの使用方法の詳細については、「[DNS service in Azure Service Fabric (Azure Service Fabric での DNS サービス)](service-fabric-dnsservice.md)」をご覧ください。

### <a name="reverse-proxy-service"></a>リバース プロキシ サービス
リバース プロキシは、HTTPS を含む HTTP エンドポイントを公開するクラスター内のサービスを処理します。 リバース プロキシは、特定の URI 形式を持つことにより、他のサービスとそのメソッドの呼び出しを大幅に簡略化します。Naming Serivce を使用して、1 つのサービスが他のサービスと通信するのに必要となる、解決、接続、再試行の各ステップを処理します。 言い換えると、他のサービスを呼び出すとき、それを 1 つの URL を呼び出すのと同様にシンプルにして、Naming Service が見えないようにしています。

![リバース プロキシによって、HTTPS を含む HTTP エンドポイントを公開するクラスター内のサービスが処理される方法を示す図。][10]

リバース プロキシ サービスの使用方法の詳細については、「[Azure Service Fabric のリバース プロキシ](service-fabric-reverseproxy.md)」をご覧ください。

## <a name="connections-from-external-clients"></a>外部クライアントからの接続
クラスター内の各ノードは同じローカル ネットワーク上にあるため、クラスター内で相互接続しているサービスは、通常、他のサービスのエンドポイントに直接アクセスできます。 ただし、一部の環境では、限られた組み合わせのポートを介して受信トラフィックをルーティングするロード バランサーの内側にクラスターが配置されている場合があります。 そのような場合もサービスが互いに通信し、ネーム サービスを使用してアドレスを解決することはできますが、外部クライアントがサービスに接続できるようにするには、追加の手順を実行する必要があります。

## <a name="service-fabric-in-azure"></a>Azure の Service Fabric
Azure の Service Fabric クラスターは、Azure Load Balancer の背後に配置されています。 クラスターへのすべての外部トラフィックは、ロード バランサーを通過する必要があります。 ロード バランサーは、指定されたポートの受信トラフィックを、同じポートが開いているランダムな *ノード* に自動的に転送します。 Azure Load Balancer が把握しているのは *ノード* 上の開いているポートのみであり、個々の *サービス* によって開放されているポートについての情報は持ちません。

![Azure Load Balancer and Service Fabric topology][3]

たとえば、ポート **80** で外部トラフィックを受け入れるには、以下のような構成が必要になります。

1. ポート 80 でリッスンするサービスを記述します。 サービスの ServiceManifest.xml でポート 80 を構成し、サービスでリスナーを開きます (自己ホスト型 Web サーバーなど)。

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Azure の Service Fabric クラスターを作成し、サービスをホストするノード タイプのカスタム エンドポイント ポートとしてポート **80** を指定します。 複数のノード タイプがある場合、サービスで *配置の制約* を設定すると、開放されているカスタム エンドポイント ポートのあるノード タイプでのみサービスが実行されるように指定できます。

    ![Open a port on a node type][4]
3. クラスターが作成されたら、トラフィックをポート 80 に転送するようにクラスターのリソース グループの Azure Load Balancer を構成します。 Azure ポータルを通じてクラスターを作成している場合、これは構成されているカスタム エンドポイント ポートごとに自動的に設定されます。

    ![[負荷分散規則] の下にある [バックエンド ポート] フィールドが強調表示されているスクリーンショット。][5]
4. Azure Load Balancer では、特定のノードにトラフィックを送信するかどうかを決めるためにプローブを使用しています。 プローブは、ノードが応答しているかどうかを判定するために、各ノードでエンドポイントを定期的にチェックします。 プローブが応答を受信できなかった回数が構成済みの回数を超えると、ロード バランサーはそのノードへのトラフィックの送信を停止します。 Azure ポータルを通じてクラスターを作成している場合、プローブは構成されているカスタム エンドポイント ポートごとに自動的に設定されます。

    ![Forward traffic in the Azure Load Balancer][8]

Azure Load Balancer とプローブが把握しているのは *ノード* についての情報のみであり、ノードで実行されている *サービス* については把握していないことを覚えておいてください。 Azure Load Balancer は、プローブに応答するノードに対して常にトラフィックを送信します。そのため、プローブに応答できるノードでサービスを利用可能にする必要があります。

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services:組み込みの通信 API オプション
Reliable Services フレームワークには、事前に構築されたいくつかの通信オプションが用意されています。 最適なオプションの決定は、プログラミング モデル、通信フレームワーク、およびサービスが作成されているプログラミング言語に応じて異なります。

* **特定のプロトコルがない場合**: 特定の通信フレームワークを選択していないものの、すぐに利用できるようにすることが必要という場合、最適なオプションは [サービスのリモート処理](service-fabric-reliable-services-communication-remoting.md)です。これにより、Reliable Services と Reliable Actors 向けの厳密に型指定されたリモート プロシージャ コールが可能になります。 これは、サービスの通信を開始する最も簡単ですばやい方法です。 サービスのリモート処理では、サービス アドレスの解決、接続、再試行、エラー処理を扱います。 これは、C# と Java のアプリケーションの両方で利用できます。
* **HTTP**:言語に依存しない通信の場合、HTTP ではさまざまな言語で利用できる業界標準のツールと HTTP サーバーを選択でき、そのすべてが Service Fabric でサポートされています。 サービスでは、C# アプリケーションの [ASP.NET Web API](./service-fabric-reliable-services-communication-aspnetcore.md) など、任意の HTTP スタックを利用できます。 C# で記述されたクライアントでは `ICommunicationClient` クラスと `ServicePartitionClient` クラス、Java では `CommunicationClient` クラスと `FabricServicePartitionClient` クラスを[サービスの解決、HTTP 通信、再試行ループ](service-fabric-reliable-services-communication.md)に活用できます。
* **WCF**: 通信フレームワークとして WCF を使用する既存のコードがある場合、サーバー側で `WcfCommunicationListener` を使用し、クライアントで `WcfCommunicationClient` クラスおよび `ServicePartitionClient` クラスを使用することができます。 ただし、これは Windows ベースのクラスター上の C# アプリケーションでのみ利用できます。 詳細については、 [WCF ベースの通信スタックの実装](service-fabric-reliable-services-communication-wcf.md)に関するこの記事を参照してください。

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>カスタム プロトコルとその他の通信フレームワークの使用
サービスでは、通信用の任意のプロトコルまたはフレームワークを使用できるため、TCP ソケットでのカスタム バイナリ プロトコルも、 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) または [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) を介したストリーミング イベントも使用することができます。 Service Fabric では、通信スタックを接続できる通信 API が提供されるだけでなく、検出と接続のためのすべての作業が不要になります。 詳細については、 [Reliable Services 通信モデル](service-fabric-reliable-services-communication.md) に関するこの記事を参照してください。

## <a name="next-steps"></a>次のステップ
[Reliable Services 通信モデル](service-fabric-reliable-services-communication.md)の概念と利用できる API の詳細について確認し、[サービスのリモート処理](service-fabric-reliable-services-communication-remoting.md)の利用をすぐに開始するか、[OWIN 自己ホストによる Web API](./service-fabric-reliable-services-communication-aspnetcore.md) を使用して通信リスナーを記述する方法についてさらに深く理解します。

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
