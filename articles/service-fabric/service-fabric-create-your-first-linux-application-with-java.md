---
title: Linux 上で Azure Service Fabric Reliable Actors Java アプリケーションを作成する
description: Java Service Fabric Reliable Actors アプリケーションを 5 分で作成してデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 06/18/2018
ms.custom: devx-track-java
ms.openlocfilehash: 7d87b72437f86d7dc1ca4e3cf9f3d67609691c70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97655953"
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Linux で初めての Java Service Fabric Reliable Actors アプリケーションを作成する
> [!div class="op_single_selector"]
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

このクイック スタートでは、Linux 開発環境で初めての Azure Service Fabric Java アプリケーションをほんの数分で作成できます。  作業が終了すると、単純な Java 単一サービス アプリケーションがローカル開発クラスターで実行された状態になります。  

## <a name="prerequisites"></a>前提条件
作業を開始する前に、[Linux 開発環境](service-fabric-get-started-linux.md)に Service Fabric SDK と Service Fabric CLI、Yeoman をインストールし、Java 開発環境をセットアップし、開発クラスターをセットアップします。 Mac OS X を使用している場合は、[Docker を使用して Mac に開発環境をセットアップ](service-fabric-get-started-mac.md)することができます。

さらに、[Service Fabric CLI](service-fabric-cli.md) もインストールします。

### <a name="install-and-set-up-the-generators-for-java"></a>Java のジェネレーターのインストールとセットアップ
Service Fabric には、ターミナルから Yeoman テンプレート ジェネレーターを使って Service Fabric Java アプリケーションを作成できるスキャフォールディング ツールが用意されています。  Yeoman がまだインストールされていない場合は、[Linux による Service Fabric の概要](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)に関するページを参照し、Yeoman をセットアップする手順を確認してください。 Java 用 Service Fabric Yeoman テンプレート ジェネレーターをインストールするには次のコマンドを実行します。

  ```bash
  npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>基本的な概念
いくつかの基本的な概念さえ理解すれば、Reliable Actors の使用を開始できます。

* **アクター サービス**。 Reliable Actors は、Service Fabric インフラストラクチャにデプロイできる Reliable Services にパッケージ化されます。 Actor インスタンスは、名前付きのサービス インスタンス内でアクティブ化されます。
* **アクター登録**。 Reliable Services と同様に、Reliable Actor サービスは Service Fabric ランタイムに登録する必要があります。 さらに、アクターの型を Actor ランタイムに登録する必要があります。
* **アクター インターフェイス**。 アクター インターフェイスは、アクターの厳密に型指定されたパブリック インターフェイスを定義するために使用されます。 Reliable Actor モデルの用語では、アクター インターフェイスに、アクターが理解し、処理できるメッセージの種類が定義されています。 アクター インターフェイスは、他のアクターとクライアント アプリケーションがメッセージをアクターに (非同期に) "送信" するために使用されます。 Reliable Actors は複数のインターフェイスを実装できます。
* **ActorProxy クラス**。 ActorProxy クラスは、アクター インターフェイスを介して公開されるメソッドを呼び出すためにクライアント アプリケーションで使用されます。 ActorProxy クラスは、次の 2 つの重要な機能を提供します。

  * 名前解決: クラスター内のアクターを特定できます (ホストされているクラスターのノードを検索できます)。
  * エラー処理: たとえば、アクターをクラスター内の別のノードに再配置することが必要な障害の後、メソッドの呼び出しを再試行し、アクターの場所を再解決できます。

アクター インターフェイスに関する次の規則に注意する必要があります。

* アクター インターフェイス メソッドはオーバー ロードできません。
* アクター インターフェイス メソッドには、out、ref、optional パラメーターを使用できません。
* ジェネリック インターフェイスはサポートされません。

## <a name="create-the-application"></a>アプリケーションを作成する
Service Fabric アプリケーションには、アプリケーションの機能を提供する際にそれぞれ特定の役割を果たすサービスが 1 つ以上含まれます。 直前のセクションでインストールしたジェネレーターを使用すると、初めてサービスを作成したり、後で追加したりする作業が簡単になります。  Eclipse 用のプラグインを使用して、Service Fabric Java アプリケーションを作成、ビルド、およびデプロイすることもできます。 [Eclipse を使用した初めての Java アプリケーションの作成とデプロイ](service-fabric-get-started-eclipse.md)に関するページを参照してください。 このクイック スタートでは、Yeoman を使用して、カウンター値の格納と取得という単一のサービスを行うアプリケーションを作成します。

1. ターミナルで、「``yo azuresfjava``」と入力します。
2. アプリケーションに名前を付けます。
3. 最初のサービスの種類を選択し、名前を付けます。 このチュートリアルでは、[Reliable Actor サービス] を選択します。 他の種類のサービスの詳細については、「[Service Fabric プログラミング モデルの概要](service-fabric-choose-framework.md)」を参照してください。
   ![Java 用 Service Fabric Yeoman ジェネレーター][sf-yeoman]

アプリケーションに "HelloWorldActorApplication"、アクターに "HelloWorldActor" という名前をそれぞれ付けた場合には、以下のスキャフォールディングが作成されます。

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```
## <a name="reliable-actors-basic-building-blocks"></a>Reliable Actors の基本的な構成要素
前に説明した基本的な概念は、信頼性の高いアクター サービスの基本的な構築ブロックになります。

### <a name="actor-interface"></a>アクター インターフェイス
アクターのインターフェイス定義が含まれています。 このインターフェイスは、アクター実装とアクターを呼び出すクライアントが共有するアクター コントラクトを定義するため、通常は、他の複数のプロジェクトで共有できる、アクター実装とは別の場所で定義します。

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>アクター サービス
これには、アクターの実装とアクター登録コードが含まれます。 アクターのクラスでは、アクター インターフェイスを実装します。 アクターは、ここで処理を実行します。

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends FabricActor implements HelloWorldActor {
    private Logger logger = Logger.getLogger(this.getClass().getName());

    public HelloWorldActorImpl(FabricActorService actorService, ActorId actorId){
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>アクター登録
アクター サービスは、Service Fabric ランタイムのサービスの種類に登録する必要があります。 アクター サービスでアクター インスタンスを実行するには、アクター型もアクター サービスに登録する必要があります。 `ActorRuntime` 登録メソッドは、アクターに対してこの処理を実行します。

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

private static final Logger logger = Logger.getLogger(HelloWorldActorHost.class.getName());

public static void main(String[] args) throws Exception {

        try {

            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new FabricActorService(context, actorType, (a,b)-> new HelloWorldActorImpl(a,b)), Duration.ofSeconds(10));
            Thread.sleep(Long.MAX_VALUE);
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Exception occurred", e);
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>アプリケーションのビルド
Service Fabric Yeoman テンプレートには、[Gradle](https://gradle.org/) のビルド スクリプトが含まれています。このスクリプトを使用して、ターミナルからアプリケーションをビルドすることができます。
Service Fabric Java 依存関係は、Maven からフェッチされます。 Service Fabric Java アプリケーションをビルドして操作するには、JDK と Gradle がインストールされている必要があります。 まだインストールされていない場合は、[Linux による Service Fabric の概要](service-fabric-get-started-linux.md#set-up-java-development)に関するページを参照し、JDK と Gradle のインストール手順を確認してください。

アプリケーションをビルドしてパッケージ化するには、次のコマンドを実行します。

  ```bash
  cd HelloWorldActorApplication
  gradle
  ```

## <a name="deploy-the-application"></a>アプリケーションの配置
ビルドしたアプリケーションは、ローカル クラスターにデプロイできます。

1. ローカルの Service Fabric クラスターに接続します (クラスターが[セットアップ済みで稼働中](service-fabric-get-started-linux.md#set-up-a-local-cluster)であることが必要)。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. テンプレートに用意されているインストール スクリプトを実行してクラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

    ```bash
    ./install.sh
    ```

ビルドしたアプリケーションは、他のすべての Service Fabric アプリケーションと同じようにデプロイできます。 詳細な手順については、[Service Fabric CLI を使用した Service Fabric アプリケーションの管理](service-fabric-application-lifecycle-sfctl.md)についてのドキュメントを参照してください。

これらのコマンドのパラメーターは、アプリケーション パッケージ内の生成されたマニフェストで確認できます。

アプリケーションのデプロイ後、ブラウザーを開いて `http://localhost:19080/Explorer` の [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) に移動します。
次に、**Applications** ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認してください。

> [!IMPORTANT]
> アプリケーションを Azure 内のセキュアな Linux クラスターにデプロイするには、Service Fabric ランタイムを使用してアプリケーションを検証するように証明書を構成する必要があります。 これにより、Reliable Actors サービスが基盤の Service Fabric ランタイム API と通信できるようになります。 詳しくは、「[Reliable Services アプリを Linux クラスター上で実行するように構成する](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)」をご覧ください。  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>テスト クライアントの起動と、フェールオーバーの実行
アクターは単独では何も実行しません。メッセージを送信するには、別のサービスまたはクライアントが必要です。 アクター テンプレートには、アクター サービスとの対話に使用できる簡単なテスト スクリプトが含まれています。

> [!Note]
> テスト クライアントは、ActorProxy クラスを使用してアクターと通信します。アクターは、アクター サービスと同じクラスター内で実行するか、同じ IP アドレス空間を共有する必要があります。  テスト クライアントは、ローカル開発クラスターと同じコンピューターで実行できます。  ただし、リモート クラスター内のアクターと通信するには、アクターとの外部通信を処理するゲートウェイをクラスターにデプロイする必要があります。

1. ウォッチ ユーティリティを使用してスクリプトを実行し、アクター サービスの出力を確認します。  テスト スクリプトは、アクターに対して `setCountAsync()` メソッドを呼び出してカウンターを増分させ、`getCountAsync()` メソッドを呼び出して新しいカウンター値を取得し、その値をコンソールに表示します。

   MAC OS X の場合は、次の追加コマンドを実行して、HelloWorldTestClient フォルダーをコンテナー内の場所にコピーする必要があります。    

    ```bash
     docker cp HelloWorldTestClient [first-four-digits-of-container-ID]:/home
     docker exec -it [first-four-digits-of-container-ID] /bin/bash
     cd /home
     ```

    ```bash
    cd HelloWorldActorTestClient
    watch -n 1 ./testclient.sh
    ```

2. Service Fabric Explorer で、アクター サービスのプライマリ レプリカをホストしているノードを見つけます。 次のスクリーンショットでは、ノード 3 です。 プライマリ サービス レプリカは、読み取り操作と書き込み操作を処理します。  サービスの状態の変更がセカンダリ レプリカにレプリケートされます (次のスクリーンショットでは、ノード 0 のノード 1 で実行されています)。

    ![Finding the primary replica in Service Fabric Explorer][sfx-primary]

3. **[ノード]** で、前の手順で見つけたノードをクリックし、[アクション] メニューの **[非アクティブにする (再起動)]** を選択します。 この操作によって、プライマリ サービス レプリカを実行しているノードが再起動され、別のノードで実行されているセカンダリ レプリカのいずれかに強制的にフェールオーバーされます。  フェールオーバーされたセカンダリ レプリカがプライマリに昇格し、別のノードに別のセカンダリ レプリカが作成され、プライマリ レプリカによる読み取り/書き込み操作が開始されます。 ノードが再起動したら、テスト クライアントからの出力を監視して、フェールオーバーにかかわらず、カウンターが増加し続けることを確認してください。

## <a name="remove-the-application"></a>アプリケーションを削除する
テンプレートに用意されているアンインストール スクリプトを使用してアプリケーション インスタンスを削除し、アプリケーション パッケージの登録を解除して、クラスターのイメージ ストアからアプリケーション パッケージを削除します。

```bash
./uninstall.sh
```

Service Fabric explorer で、アプリケーションとアプリケーションの種類が **[アプリケーション]** ノードに表示されていないことを確認します。

## <a name="service-fabric-java-libraries-on-maven"></a>Maven 上の Service Fabric Java ライブラリ
Service Fabric Java ライブラリは、Maven でホストされてきました。 **mavenCentral** から Service Fabric Java ライブラリを使用するために、プロジェクトの ``pom.xml`` または ``build.gradle`` に依存関係を追加することができます。 

### <a name="actors"></a>アクター

アプリケーションの Service Fabric Reliable Actors サポート。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>サービス

アプリケーションの Service Fabric Reliable Services サポート。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>その他
#### <a name="transport"></a>トランスポート

Service Fabric Java アプリケーションのトランスポート層サポート。 トランスポート層でプログラムを作成する場合以外は、Reliable Actors または Service アプリケーションにこの依存関係を明示的に追加する必要はありません。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric サポート

Service Fabric のシステム レベルのサポート。ネイティブの Service Fabric ランタイムと対話します。 Reliable Actors または Service アプリケーションにこの依存関係を明示的に追加する必要はありません。 上記の他の依存関係を取り込むときに、Maven から自動的にフェッチされます。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="next-steps"></a>次のステップ

* [Linux で初めての Service Fabric Java アプリケーションを作成する](service-fabric-get-started-eclipse.md)
* [Service Fabric Reliable Actors の概要](service-fabric-reliable-actors-introduction.md)
* [Service Fabric CLI を使用した Service Fabric クラスターの対話操作](service-fabric-cli.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。
* [Service Fabric CLI の概要](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
