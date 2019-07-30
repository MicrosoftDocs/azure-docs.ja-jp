---
title: Azure Service Fabric で C# を使ってサービス リモート処理通信をセキュリティで保護する | Microsoft Docs
description: Azure Service Fabric クラスターで実行されている C# Reliable Services のサービス リモート処理ベースの通信をセキュリティで保護する方法について説明します。
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: chackdan
ms.openlocfilehash: 193df34a092d9feea3e0cf370fe38543395dad92
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871730"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>C# サービスでのサービス リモート処理通信をセキュリティで保護する
> [!div class="op_single_selector"]
> * [Windows での C#](service-fabric-reliable-services-secure-communication.md)
> * [Linux での Java](service-fabric-reliable-services-secure-communication-java.md)
>
>

セキュリティは、通信の最も重要な側面の 1 つです。 Reliable Services アプリケーション フレームワークに最初から用意されている通信スタックとツールを利用してセキュリティを確保することができます。 この記事では、C# サービスでサービス リモート処理を使用している場合の、セキュリティの改善方法について説明します。 これは、C# で記述された Reliable Services のリモート処理の設定方法について説明した、既存の[例](service-fabric-reliable-services-communication-remoting.md)を基に構築されます。 

C# サービスでリモート処理を使用している場合、サービスのセキュリティを確保するには、次の手順を実行します。

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

    サービスの通信のセキュリティ保護に使用する証明書が、クラスター内のすべてのノードにインストールされていることを確認します。 
    
    > [!NOTE]
    > Linux ノード上では、証明書は PEM 形式のファイルとして */var/lib/sfcerts* ディレクトリ内に存在している必要があります。 詳しくは、「[Linux ノード上の X.509 証明書の場所と形式](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes)」をご覧ください。 

    リスナー設定とセキュリティ資格情報は、次の 2 とおりの方法で指定できます。

   1. サービスのコード内で直接指定する。

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
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
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. [構成パッケージ](service-fabric-application-and-service-manifests.md)を使用して指定する。

       settings.xml ファイルに、名前付きの `TransportSettings` セクションを追加します。

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
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
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        settings.xml ファイルに `TransportSettings` セクションを追加すると、`FabricTransportRemotingListenerSettings` は、既定で、このセクションからすべての設定を読み込みます。

        ```xml
        <!--"TransportSettings" section .-->
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
3. セキュリティで保護されたサービスのメソッドをリモート処理スタックで呼び出すときは、`Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` クラスを使用してサービス プロキシを作成する代わりに `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` を使用してください。 引数として、`FabricTransportRemotingSettings` が格納されている `SecurityCredentials` を渡します。

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    クライアント コードがサービスの一部として実行されている場合は、settings.xml ファイルから `FabricTransportRemotingSettings` を読み込むことができます。 上記のように、サービス コードに似た HelloWorldClientTransportSettings セクションを作成します。 クライアント コードを次のように変更します。

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    クライアントがサービスの一部として実行されていない場合は、client_name.exe と同じ場所に client_name.settings.xml ファイルを作成し、 そのファイル内に TransportSettings セクションを作成できます。

    サービスと同様、クライアントの settings.xml/client_name.settings.xml でも、`TransportSettings` セクションを追加すると、`FabricTransportRemotingSettings` は既定で、このセクションからすべての設定を読み込みます。

    その場合は、上記のコードがさらに簡略化されます。  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


次の手順については、「[Reliable Services の OWIN 対応 Web API](service-fabric-reliable-services-communication-webapi.md)」を参照してください。
