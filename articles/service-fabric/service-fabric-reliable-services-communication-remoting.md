---
title: Service Fabric での C# を使用したサービスのリモート処理
description: Service Fabric のリモート処理では、クライアントとサービスがリモート プロシージャ コールを使用して C# サービスと通信できるようにします。
ms.topic: conceptual
ms.date: 09/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: a0486a27d76c978a65c4a3cfd81df52a12e4ea1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791579"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>C# での Reliable Services を使用したサービスのリモート処理

> [!div class="op_single_selector"]
> * [Windows での C#](service-fabric-reliable-services-communication-remoting.md)
> * [Linux での Java](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Web API や Windows Communication Foundation など、特定の通信プロトコルやスタックにひも付けされていないサービスでは、サービスのリモート プロシージャ コールを迅速かつ簡単に設定するリモート処理呼び出しを、Reliable Services フレームワークが提供します。 この記事では、C# で作成されたサービス向けにリモート プロシージャ コールを設定する方法について説明します。

## <a name="set-up-remoting-on-a-service"></a>サービスでのリモート処理の設定

サービスのリモート処理は、次の 2 つの簡単な手順で設定できます。

1. 実装するサービスのインターフェイスを作成します。 このインターフェイスは、サービスのリモート プロシージャ コールで使用できるメソッドを定義します。 このメソッドはタスクを返す非同期メソッドである必要があります。 インターフェイスは `Microsoft.ServiceFabric.Services.Remoting.IService` を実装し、そのサービスにリモート処理インターフェイスがあることを示す必要があります。
2. サービスでリモート処理リスナーを使用します。 リモート処理リスナーは、リモート処理機能を提供する `ICommunicationListener` の実装です。 `Microsoft.ServiceFabric.Services.Remoting.Runtime` 名前空間には、ステートレス サービスとステートフル サービスの拡張メソッド `CreateServiceRemotingInstanceListeners` が含まれています。このメソッドは、既定のリモート処理トランスポート プロトコルを使用してリモート処理リスナーを作成するために使用できます。

>[!NOTE]
>`Remoting` 名前空間は、`Microsoft.ServiceFabric.Services.Remoting` という別の NuGet パッケージとして提供されています。

たとえば、次のステートレス サービスでは、リモート プロシージャ コールで "Hello World" を取得する 1 つのメソッドを公開します。

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> サービス インターフェイスの引数と戻り値の型は、単純型、複合型、またはカスタム型のいずれかにできますが、.NET の [DataContractSerializer](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer) によってシリアル化できる必要があります。
>
>

## <a name="call-remote-service-methods"></a>リモート サービス メソッドの呼び出し

リモート処理スタックを使用したサービスでのメソッドの呼び出しは、 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` クラスによるサービスへのローカル プロキシを使用して実行されます。 `ServiceProxy` メソッドは、サービスに実装されている同じインターフェイスを使用してローカル プロキシを作成します。 そのプロキシを使用して、インターフェイス上のメソッドをリモートで呼び出すことができます。

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

リモート処理フレームワークは、サービスでスローされた例外をクライアントに伝達します。 結果として、`ServiceProxy` が使用される場合はクライアントが、サービスによってスローされる例外を処理する必要があります。

## <a name="service-proxy-lifetime"></a>サービス プロキシの有効期間

サービス プロキシの作成は負荷の軽い処理であり、必要だけいくつでも作成できます。 サービス プロキシ インスタンスは、それらが必要とされる限り再利用することができます。 リモート プロシージャ コールから例外がスローされても、同じプロキシ インスタンスを再利用できます。 各サービス プロキシは、メッセージをネットワーク経由で送信するための通信クライアントを含んでいます。 リモート呼び出しが実行されると、通信クライアントが有効かどうかを調べる内部チェックが実行されます。 それらのチェックの結果に基づいて、必要な場合は通信クライアントが再作成されます。 したがって、例外が発生した場合に、ユーザーが `ServiceProxy` を再作成する必要はありません。

### <a name="service-proxy-factory-lifetime"></a>サービス プロキシ ファクトリの有効期間

[ServiceProxyFactory](/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) は、さまざまなリモート処理インターフェイスのプロキシ インスタンスを作成するファクトリです。 API `ServiceProxyFactory.CreateServiceProxy` を使用してプロキシを作成する場合、フレームワークによってシングルトン サービス プロキシが作成されます。
手動での作成は、[IServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) プロパティをオーバーライドする必要があるときに効果的です。
ファクトリの作成は負荷の高い操作です。 サービス プロキシ ファクトリは、通信クライアントの内部キャッシュを管理します。
サービス プロキシ ファクトリは、できるだけ長くキャッシュすることがベスト プラクティスです。

## <a name="remoting-exception-handling"></a>リモート処理の例外処理

サービス API によってスローされるリモート処理の例外はすべて、AggregateException としてクライアントに返されます。 リモート処理の例外は、DataContract によってシリアル化できる必要があります。 そうでない場合、プロキシ API はその内部のシリアル化エラーによって [ServiceException](/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) をスローします。

サービス プロキシは、それが作成されたサービス パーティションのすべてのフェールオーバー例外を処理します。 フェールオーバー例外 (一時的ではない例外) が発生した場合、エンドポイントを再度解決し、正しいエンドポイントでの呼び出しを再試行します。 フェールオーバー例外の再試行回数に上限はありません。
一時的な例外が発生した場合、プロキシは呼び出しを再試行します。

既定の再試行パラメーターは、[OperationRetrySettings](/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) で指定します。

ユーザーは、ServiceProxyFactory コンストラクターに OperationRetrySettings オブジェクトを渡すことによって、これらの値を構成できます。

## <a name="use-the-remoting-v2-stack"></a>リモート処理 V2 スタックを使用する

NuGet リモート処理パッケージのバージョン 2.8 より、リモート処理 V2 スタックを使用するオプションが用意されています。 リモート処理 V2 スタックはパフォーマンスが向上しています。 カスタムのシリアル化や、プラグ可能な範囲が広がった API などの機能も提供されます。
テンプレート コードでは、引き続きリモート処理 V1 スタックが使用されます。
リモート処理 V2 は V1 (前のリモート処理スタック) と互換性がありません。 [V1 から V2 へのアップグレード](#upgrade-from-remoting-v1-to-remoting-v2)に関する記事の手順に従って、サービスの可用性への影響を回避します。

V2 スタックを有効にするには、次の方法を使用できます。

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>アセンブリ属性を利用して V2 スタックを使用する

これらの手順では、V2 スタックを使用するために、アセンブリ属性を使ってテンプレート コードを変更します。

1. サービス マニフェストで、エンドポイント リソースを `"ServiceEndpoint"` から `"ServiceEndpointV2"` に変更します。

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` 拡張メソッドを使用して、リモート処理リスナーを作成します (V1 と V2 の両方で同一)。

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. リモート処理インターフェイスが含まれているアセンブリに、`FabricTransportServiceRemotingProvider` 属性を指定してマークを付けます。

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

クライアント プロジェクトでは、コードの変更は必要ありません。
上に示したアセンブリ属性が確実に使用されるように、目的のインターフェイス アセンブリを使用してクライアント アセンブリをビルドします。

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>明示的な V2 クラスを利用して V2 スタックを使用する

アセンブリ属性を使用する代わりに、明示的な V2 クラスを使用して V2 スタックを有効にすることもできます。

これらの手順では、V2 スタックを使用するために、明示的な V2 クラスを使ってテンプレート コードを変更します。

1. サービス マニフェストで、エンドポイント リソースを `"ServiceEndpoint"` から `"ServiceEndpointV2"` に変更します。

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` 名前空間の [FabricTransportServiceRemotingListener](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener) を使用します。

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
   ```

3. `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` 名前空間の [FabricTransportServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory) を使用してクライアントを作成します。

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>リモート処理 V1 からリモート処理 V2 にアップグレードする

V1 から V2 にアップグレードするには、2 段階のアップグレードが必要です。 この順序で以下の手順に従います。

1. 次の属性を使用して、V1 サービスを V2 サービスにアップグレードします。
この変更によって、サービスが V1 および V2 リスナーでリッスンするようにします。

    a. サービス マニフェストに、"ServiceEndpointV2" という名前のエンドポイント リソースを追加します。
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. 次の拡張メソッドを使用して、リモート処理リスナーを作成します。

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. リモート処理インターフェイスで、V1 および V2 リスナーと V2 クライアントを使用するためのアセンブリ属性を追加します。
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. V2 クライアントの属性を使用して、V1 クライアントを V2 クライアントにアップグレードします。
このステップによって、クライアントが V2 スタックを使用するようにします。
クライアント プロジェクトやサービスでの変更は必要ありません。 更新したインターフェイス アセンブリを使用してクライアント プロジェクトをビルドすれば十分です。

3. この手順は省略可能です。 V2 リスナー属性を使用して、V2 サービスをアップグレードします。
この手順によって、サービスが V2 リスナーでのみリッスンするようにします。

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>リモート処理 V2 (インターフェイス互換) スタックを使用する

 リモート処理 V2 (インターフェイス互換、V2_1 と呼ばれます) スタックは、V2 リモート処理スタックのすべての機能を備えています。 そのインターフェイス スタックは、リモート処理 V1 スタックと互換性がありますが、V2 および V1 との下位互換性はありません。 サービスの可用性に影響を与えずに V1 から V2_1 にアップグレードするには、V1 から V2 (インターフェイス互換) へのアップグレードに関する記事の手順に従ってください。


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>アセンブリ属性を利用してリモート処理 V2 (インターフェイス互換) スタックを使用する

以下の手順に従って V2_1 スタックに変更します。

1. サービス マニフェストに、"ServiceEndpointV2_1" という名前のエンドポイント リソースを追加します。

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. リモート処理拡張メソッドを使用して、リモート処理リスナーを作成します。

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. リモート処理インターフェイスに[アセンブリ属性](/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute)を追加します。

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

クライアント プロジェクトでは、変更は必要ありません。
前記のアセンブリ属性が確実に使用されるように、目的のインターフェイス アセンブリを使用してクライアント アセンブリをビルドします。

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>明示的リモート処理クラスを使用して V2 (インターフェイス互換) バージョンのリスナー/クライアント ファクトリを作成する

次の手順に従います。

1. サービス マニフェストに、"ServiceEndpointV2_1" という名前のエンドポイント リソースを追加します。

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. [リモート処理 V2 リスナーを使用します](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener)。 使用される既定のサービス エンドポイント リソース名は、"ServiceEndpointV2_1" です。 これはサービス マニフェストで定義されている必要があります。

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. V2 [クライアント ファクトリ](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory)を使用します。
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>リモート処理 V1 からリモート処理 V2 (インターフェイス互換) にアップグレードする

V1 から V2 (インターフェイス互換、V2_1 と呼ばれます) にアップグレードするには、2 段階のアップグレードが必要です。 この順序で以下の手順に従います。

> [!NOTE]
> V1 から V2 にアップグレードする場合は、V2 を使用するように `Remoting` 名前空間が更新されていることを確認します。 例:'Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client`
>
>

1. 次の属性を使用して、V1 サービスを V2_1 サービスにアップグレードします。
この変更によって、サービスは V1 および V2_1 リスナーでリッスンするようになります。

    a. サービス マニフェストに、"ServiceEndpointV2_1" という名前のエンドポイント リソースを追加します。
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. 次の拡張メソッドを使用して、リモート処理リスナーを作成します。

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. リモート処理インターフェイスにアンセンブリ属性を追加し、V1、V2_1 リスナー、V2_1 クライアントを使用します。
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. V2_1 クライアントの属性を使用して、V1 クライアントを V2_1 クライアントにアップグレードします。
この手順によって、クライアントは V2_1 スタックを使用するようになります。
クライアント プロジェクトやサービスでの変更は必要ありません。 更新したインターフェイス アセンブリを使用してクライアント プロジェクトをビルドすれば十分です。

3. この手順は省略可能です。 属性から V1 リスナー バージョンを削除してから、V2 サービスをアップグレードします。
この手順によって、サービスが V2 リスナーでのみリッスンするようにします。

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>リモート処理のラップ済みメッセージによるカスタム シリアル化を使用する

リモート処理のラップ済みメッセージについては、1 つのラップ済みオブジェクトを作成し、その中にすべてのパラメーターをフィールドとして含めます。
次の手順に従います。

1. カスタム シリアル化の実装を提供するための `IServiceRemotingMessageSerializationProvider` インターフェイスを実装します。
    このコード スニペットは、その実装がどのようなものかを示しています。

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. リモート処理リスナーのために、`JsonSerializationProvider` を使用して既定のシリアル化プロバイダーをオーバーライドします。

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
   ```

3. リモート処理クライアント ファクトリのために、`JsonSerializationProvider` を使用して既定のシリアル化プロバイダーをオーバーライドします。

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>次のステップ

* [Reliable Services の OWIN 対応 Web API](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Reliable Services との Windows Communication Foundation 通信](service-fabric-reliable-services-communication-wcf.md)
* [Reliable Services のためのセキュリティ保護された通信](service-fabric-reliable-services-secure-communication.md)
