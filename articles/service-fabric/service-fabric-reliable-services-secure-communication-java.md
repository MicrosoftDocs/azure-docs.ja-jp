---
title: "Azure Service Fabric のサービスで使用される通信のセキュリティ | Microsoft Docs"
description: "Azure Service Fabric クラスターで実行されている Reliable Services の通信をセキュリティで保護する方法について簡単に説明します。"
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: c4634e3d8efb1745fffcfe3e647e43d867038716
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Azure Service Fabric のサービスで使用される通信のセキュリティ
> [!div class="op_single_selector"]
> * [Windows での C# ](service-fabric-reliable-services-secure-communication.md)
> * [Linux での Java](service-fabric-reliable-services-secure-communication-java.md)
>
>

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>リモート処理を使用している場合のサービスのセキュリティ確保
Reliable Services のリモート処理の設定方法について説明した既存の [例](service-fabric-reliable-services-communication-remoting-java.md) で説明します。 リモート処理を使用している場合、サービスのセキュリティを確保するには、次の手順を実行します。

1. サービスのリモート プロシージャ コールで使用できるメソッドを定義するインターフェイス ( `HelloWorldStateless`) を作成します。 実際のサービスでは、`microsoft.serviceFabric.services.remoting.fabricTransport.runtime` パッケージに宣言されている `FabricTransportServiceRemotingListener` を使用します。 これは、リモート処理機能を提供する `CommunicationListener` の実装です。

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. リスナー設定とセキュリティ資格情報を追加します。

    サービスの通信のセキュリティ保護に使用する証明書が、クラスター内のすべてのノードにインストールされていることを確認します。 リスナー設定とセキュリティ資格情報は、次の 2 とおりの方法で指定できます。

   1. [構成パッケージ](service-fabric-application-model.md)を使用して指定する。

       settings.xml ファイルに `TransportSettings` セクションを追加します。

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       この場合、 `createServiceInstanceListeners` メソッドは次のようになります。

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        プレフィックスを指定せずに settings.xml ファイルに `TransportSettings` セクションを追加すると、`FabricTransportListenerSettings` は、既定で、このセクションからすべての設定を読み込みます。

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        この場合、 `CreateServiceInstanceListeners` メソッドは次のようになります。

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. セキュリティで保護されたサービスのメソッドをリモート処理スタックで呼び出すときは、`microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` クラスを使用してサービス プロキシを作成する代わりに `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory` を使用してください。

    クライアント コードがサービスの一部として実行されている場合は、settings.xml ファイルから `FabricTransportSettings` を読み込むことができます。 上記のように、サービス コードに似た TransportSettings セクションを作成します。 クライアント コードを次のように変更します。

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
