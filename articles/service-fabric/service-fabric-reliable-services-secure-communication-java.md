---
title: Java を使用したサービス リモート処理通信をセキュリティで保護する
description: Azure Service Fabric クラスターで実行されている Java Reliable Services のサービス リモート処理ベースの通信をセキュリティで保護する方法について説明します。
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: 01a64fbcfef9f56abb0e1aa6cf7f5d821dd3763b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87325714"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Java サービスでのサービス リモート処理通信をセキュリティで保護する
> [!div class="op_single_selector"]
> * [Windows での C#](service-fabric-reliable-services-secure-communication.md)
> * [Linux での Java](service-fabric-reliable-services-secure-communication-java.md)
>
>

セキュリティは、通信の最も重要な側面の 1 つです。 Reliable Services アプリケーション フレームワークに最初から用意されている通信スタックとツールを利用してセキュリティを確保することができます。 この記事では、Java サービスでサービス リモート処理を使用している場合の、セキュリティの改善方法について説明します。 これは、Java で記述された Reliable Services のリモート処理の設定方法について説明した、既存の[例](service-fabric-reliable-services-communication-remoting-java.md)を基に構築されます。 

Java サービスでリモート処理を使用している場合、サービスのセキュリティを確保するには、次の手順を実行します。

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

    サービスの通信のセキュリティ保護に使用する証明書が、クラスター内のすべてのノードにインストールされていることを確認します。 Linux 上で実行されているサービスの場合、証明書は PEM 形式のファイルとして提供される必要があります。つまり、証明書と秘密キーを格納した `.pem` ファイルを使用するか、証明書を格納した `.crt` ファイルと秘密キーを格納した `.key` ファイルを使用します。 詳しくは、「[Linux ノード上の X.509 証明書の場所と形式](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes)」をご覧ください。
    
    リスナー設定とセキュリティ資格情報は、次の 2 とおりの方法で指定できます。

   1. [構成パッケージ](service-fabric-application-and-service-manifests.md)を使用して指定する。

       settings.xml ファイルに、名前付きの `TransportSettings` セクションを追加します。

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
