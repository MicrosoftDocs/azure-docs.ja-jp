---
title: "Service Fabric のサービスで使用される通信のセキュリティ | Microsoft Docs"
description: "Azure Service Fabric クラスターで実行されている Reliable Services の通信をセキュリティで保護する方法について簡単に説明します。"
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 01/05/2017
ms.author: suchia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f13ccbb5ac1eff7ea8924c9d7b5ea9d9ef09a7ad


---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Azure Service Fabric のサービスで使用される通信のセキュリティ
セキュリティは、通信の最も重要な側面の 1 つです。 Reliable Services アプリケーション フレームワークに最初から用意されている通信スタックとツールを利用してセキュリティを確保することができます。 この記事では、サービスのリモート処理や Windows Communication Foundation (WCF) の通信スタックを使用するときのセキュリティを確保する方法について説明します。

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>リモート処理を使用している場合のサービスのセキュリティ確保
Reliable Services のリモート処理の設定方法について説明した既存の [例](service-fabric-reliable-services-communication-remoting.md) で説明します。 リモート処理を使用している場合、サービスのセキュリティを確保するには、次の手順を実行します。

1. サービスのリモート プロシージャ コールで使用できるメソッドを定義するインターフェイス ( `IHelloWorldStateful`) を作成します。 実際のサービスでは、`Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` 名前空間に宣言されている `FabricTransportServiceRemotingListener` を使用します。 これは、リモート処理機能を提供する `ICommunicationListener` の実装です。
   
    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }
   
    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }
   
        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. リスナー設定とセキュリティ資格情報を追加します。
   
    サービスの通信のセキュリティ保護に使用する証明書が、クラスター内のすべてのノードにインストールされていることを確認します。 リスナー設定とセキュリティ資格情報は、次の 2 とおりの方法で指定できます。
   
   1. サービスのコード内で直接指定する。
      
       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportListenerSettings listenerSettings = new FabricTransportListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }
      
       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           return x509Credentials;
       }
       ```
   2. [構成パッケージ](service-fabric-application-model.md)を使用して指定する。
      
       settings.xml ファイルに `TransportSettings` セクションを追加します。
      
       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateful".-->
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```
      
       この場合、 `CreateServiceReplicaListeners` メソッドは次のようになります。
      
       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
           };
       }
       ```
      
        プレフィックスを指定せずに settings.xml ファイルに `TransportSettings` セクションを追加すると、`FabricTransportListenerSettings` は、既定で、このセクションからすべての設定を読み込みます。
      
        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        この場合、 `CreateServiceReplicaListeners` メソッドは次のようになります。
      
        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. セキュリティで保護されたサービスのメソッドをリモート処理スタックで呼び出すときは、`Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` クラスを使用してサービス プロキシを作成する代わりに `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` を使用してください。 引数として、`FabricTransportSettings` が格納されている `SecurityCredentials` を渡します。
   
    ```csharp
   
    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
   
    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };
   
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));
   
    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));
   
    string message = await client.GetHelloWorld();
   
    ```
   
    クライアント コードがサービスの一部として実行されている場合は、settings.xml ファイルから `FabricTransportSettings` を読み込むことができます。 上記のように、サービス コードに似た TransportSettings セクションを作成します。 クライアント コードを次のように変更します。
   
    ```csharp
   
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));
   
    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));
   
    string message = await client.GetHelloWorld();
   
    ```
   
    クライアントがサービスの一部として実行されていない場合は、client_name.exe と同じ場所に client_name.settings.xml ファイルを作成し、 そのファイル内に TransportSettings セクションを作成できます。
   
    サービスと同様、クライアントの settings.xml/client_name.settings.xml でも、プレフィックスを指定せずに `TransportSettings` セクションを追加すると、`FabricTransportSettings` は既定で、このセクションからすべての設定を読み込みます。
   
    その場合は、上記のコードがさらに簡略化されます。  
   
    ```csharp
   
    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));
   
    string message = await client.GetHelloWorld();
   
    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>WCF ベースの通信スタックを使用している場合のサービスのセキュリティ確保
Reliable Services の WCF ベースの通信スタックを設定する方法について説明した既存の [例](service-fabric-reliable-services-communication-wcf.md) で説明します。 WCF ベースの通信スタックを使用している場合、サービスのセキュリティを確保するには、次の手順を実行します。

1. 目的のサービスについて作成する WCF 通信リスナー (`WcfCommunicationListener`) のセキュリティを確保する必要があります。 そのためには、 `CreateServiceReplicaListeners` メソッドに変更を加えます。
   
    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }
   
    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");
   
        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }
   
    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. クライアントでは、前の[例](service-fabric-reliable-services-communication-wcf.md)で作成した `WcfCommunicationClient` クラスは変更されません。 ただし、`WcfCommunicationClientFactory` の `CreateClientAsync` メソッドをオーバーライドする必要があります。
   
    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }
   
        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```
   
    `SecureWcfCommunicationClientFactory` を使用して WCF 通信クライアント (`WcfCommunicationClient`) を作成します。 クライアントを使用して、サービス メソッドを呼び出します。
   
    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
   
    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);
   
    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);
   
    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## <a name="next-steps"></a>次のステップ
* [Reliable Services の OWIN 対応 Web API](service-fabric-reliable-services-communication-webapi.md)




<!--HONumber=Nov16_HO3-->


