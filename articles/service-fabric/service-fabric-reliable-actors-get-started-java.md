---
title: "Java でアクター ベースの Azure マイクロサービスを初めて作成する | Microsoft Docs"
description: "このチュートリアルでは、Service Fabric Reliable Actors を使用して簡単なアクターベースのサービスを作成、デバッグ、デプロイする手順について説明します。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d31dc8ab-9760-4619-a641-facb8324c759
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: cf8f717d5343ae27faefdc10f81b4feaccaa53b9
ms.openlocfilehash: 8ecc5208237d846d0e81914eee8874ea97744bc3
ms.lasthandoff: 01/24/2017


---
# <a name="getting-started-with-reliable-actors"></a>Reliable Actors の使用
> [!div class="op_single_selector"]
> * [Windows での C#](service-fabric-reliable-actors-get-started.md)
> * [Linux での Java](service-fabric-reliable-actors-get-started-java.md)
> 
> 

ここでは、Azure Service Fabric Reliable Actors の基本と、Java で簡単な Reliable Actors アプリケーションを作成およびデプロイする手順について説明します。

## <a name="installation-and-setup"></a>インストールとセットアップ
開始する前に、マシン上に Service Fabric 開発環境がセットアップされていることを確認します。
設定する必要がある場合は、[Mac での作業](service-fabric-get-started-mac.md)または [Linux での作業](service-fabric-get-started-linux.md)を参照してください。

## <a name="basic-concepts"></a>基本的な概念
いくつかの基本的な概念さえ理解すれば、Reliable Actors の使用を開始できます。

* **アクター サービス**。 Reliable Actors は、Service Fabric インフラストラクチャにデプロイできる Reliable Services にパッケージ化されます。 Actor インスタンスは、名前付きのサービス インスタンス内でアクティブ化されます。
* **アクター登録**。 Reliable Services と同様に、Reliable Actor サービスは Service Fabric ランタイムに登録する必要があります。 さらに、アクターの型を Actor ランタイムに登録する必要があります。
* **アクター インターフェイス**。 アクター インターフェイスは、アクターの厳密に型指定されたパブリック インターフェイスを定義するために使用されます。 Reliable Actor モデルの用語では、アクター インターフェイスに、アクターが理解し、処理できるメッセージの種類が定義されています。 アクター インターフェイスは、他のアクターとクライアント アプリケーションがメッセージをアクターに (非同期に) "送信" するために使用されます。 Reliable Actors は複数のインターフェイスを実装できます。
* **ActorProxy クラス**。 ActorProxy クラスは、アクター インターフェイスを介して公開されるメソッドを呼び出すためにクライアント アプリケーションで使用されます。 ActorProxy クラスは、次の&2; つの重要な機能を提供します。
  
  * 名前解決: クラスター内のアクターを特定できます (ホストされているクラスターのノードを検索できます)。
  * エラー処理: たとえば、アクターをクラスター内の別のノードに再配置する必要があるエラーが発生した後に、メソッドの呼び出しを再試行し、アクターの場所を再解決することができます。

アクター インターフェイスに関する次の規則に注意する必要があります。

* アクター インターフェイス メソッドはオーバー ロードできません。
* アクター インターフェイス メソッドには、out、ref、optional パラメーターを使用できません。
* ジェネリック インターフェイスはサポートされません。

## <a name="create-an-actor-service"></a>アクター サービスの作成
新しい Service Fabric アプリケーションの作成 Linux 用の Service Fabric SDK には、ステートレス サービスの Service Fabric アプリケーションのスキャフォールディングを提供する Yeoman ジェネレーターが含まれています。 最初に次の Yeoman コマンドを実行します。

```bash
$ yo azuresfjava
```

指示に従って、**信頼性の高いアクター サービス**を作成します。 このチュートリアルでは、アプリケーションに "HelloWorldActorApplication" という名前を付け、アクターに "HelloWorldActor" という名前を付けます。 次のスキャフォールディングが作成されます:

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
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

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

    public static void main(String[] args) throws Exception {

        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);

        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Test client
これは、アクター サービスをテストするために Service Fabric アプリケーションとは別に実行できる単純なクライアント アプリケーションです。 これは、ActorProxy を使用して、アクター インスタンスをアクティブ化してインスタンスと通信できるアプリケーションの例です。 サービスと共にデプロイされることはありません。

### <a name="the-application"></a>アプリケーション
最後に、アプリケーションが、アクター サービスと、デプロイメント用に将来的に追加する他のサービスをパッケージ化します。 パッケージには、*ApplicationManifest.xml* と、アクター サービス パッケージのプレース ホルダーが含まれています。

## <a name="run-the-application"></a>アプリケーションの実行
Yeoman スキャフォールディングには、アプリケーションをビルドするための gradle スクリプトと、アプリケーションをデプロイおよびデプロイ解除するための bash スクリプトが含まれています。 アプリケーションを実行するには、最初に gradle を使用してアプリケーションをビルドします。

```bash
$ gradle
```

これにより、Service Fabric Azure CLI を使用してデプロイできる Service Fabric アプリケーション パッケージが生成されます。 Install.sh スクリプトには、アプリケーション パッケージを展開するために必要な Azure CLI コマンドが含まれています。 単純に install.sh スクリプトを実行してデプロイします。

```bask
$ ./install.sh
```

