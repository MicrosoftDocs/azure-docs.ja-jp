---
title: Java で最初の信頼性の高いサービスを作成する
description: Java でステートレス サービスとステートフル サービスを使用して Microsoft Azure Service Fabric アプリケーションを作成する方法。
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-javai
ms.openlocfilehash: f67957d711958febdb01dfad0b3c44a92cb0bcfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91535238"
---
# <a name="get-started-with-reliable-services-in-java"></a>Java で Reliable Services の使用を開始する
> [!div class="op_single_selector"]
> * [Windows での C#](service-fabric-reliable-services-quick-start.md)
> * [Linux での Java](service-fabric-reliable-services-quick-start-java.md)
>
>

ここでは、Azure Service Fabric Reliable Services の基本と、Java で記述された簡単な Reliable Services アプリケーションを作成およびデプロイする手順について説明します。 

## <a name="installation-and-setup"></a>インストールとセットアップ
開始する前に、マシン上に Service Fabric 開発環境がセットアップされていることを確認します。
設定する必要がある場合は、[Mac での作業](service-fabric-get-started-mac.md)または [Linux での作業](service-fabric-get-started-linux.md)を参照してください。

## <a name="basic-concepts"></a>基本的な概念
Reliable Services の使用を開始するには、いくつかの基本的な概念を理解する必要があります。

* **サービスの種類**: 使用するサービス実装です。 `StatelessService` を拡張する記述したクラスと、そこで使用する他のコードまたは依存関係のほか、名前やバージョン番号によって定義されます。
* **名前付きサービス インスタンス**: サービスを実行するには、サービスの種類の名前付きインスタンスを作成します。これは、クラスの種類のオブジェクト インスタンスを作成するのに似ています。 サービス インスタンスは、実際には、記述するサービス クラスのオブジェクト インスタンスです。
* **サービス ホスト**: 作成した名前付きサービス インスタンスは、ホスト内で実行する必要があります。 サービス ホストは単なる 1 プロセスで、ここでサービスのインスタンスを実行できます。
* **サービス登録**: 登録はすべてを 1 つにまとめます。 サービス ホストでサービスの種類を Service Fabric ランタイムに登録して、Service Fabric がそのインスタンスを作成して実行できるようにする必要があります。  

## <a name="create-a-stateless-service"></a>ステートレス サービスの作成
最初に、Service Fabric アプリケーションを作成します。 Linux 用の Service Fabric SDK には、ステートレス サービスの Service Fabric アプリケーションのスキャフォールディングを提供する Yeoman ジェネレーターが含まれています。 最初に次の Yeoman コマンドを実行します。

```bash
$ yo azuresfjava
```

指示に従って、**信頼性の高いステートレス サービス** を作成します。 このチュートリアルでは、アプリケーションに "HelloWorldApplication" という名前を付け、サービスに "HelloWorld" という名前を付けます。 結果には、`HelloWorldApplication` と `HelloWorld` のディレクトリが含まれます。

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>サービス登録
サービスの種類は、Service Fabric ランタイムに登録する必要があります。 サービスの種類は、`ServiceManifest.xml` で定義され、さらにサービス クラスを実装する `StatelessService` で定義されます。 サービスの登録は、プロセスのメイン エントリ ポイントで実行されます。 この例では、プロセスのメイン エントリ ポイントは `HelloWorldServiceHost.java` です。

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>サービスの実装

**HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java** を開きます。 このクラスは、サービスの種類を定義し、任意のコードを実行することができます。 サービス API には、コードのエントリ ポイントが 2 つあります。

* `runAsync()` という変更可能なエントリ ポイント メソッドでは、実行時間の長いコンピューティング ワークロードなどの任意のワークロードの実行を開始できます。

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* 選択した通信スタックをプラグインできる通信エントリ ポイント。 これは、ユーザーおよびその他のサービスからの要求の受信を開始できる場所です。

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

このチュートリアルでは、`runAsync()` エントリ ポイント メソッドを取り上げます。 これは、コードの実行をすぐに開始できる場所です。

### <a name="runasync"></a>RunAsync
プラットフォームは、サービスのインスタンスが配置され実行準備ができたときに、このメソッドを呼び出します。 ステートレス サービスの場合、これはサービス インスタンスが開いたことを意味します。 サービス インスタンスを終了する必要がある場合のために、キャンセル トークンが提供されています。 Service Fabric では、サービス インスタンスの開始から終了のサイクルは、サービスのライフタイムで何度も発生する可能性があります。 これは、さまざまな理由で発生する可能性があります。

* システムがリソース分散のためにサービス インスタンスを移動している。
* コードでエラーが発生している。
* アプリケーションまたはシステムがアップグレードされている。
* 基礎となるハードウェアで障害が発生している。

この調整は、サービスの可用性を高めて適切なバランスを取るために、Service Fabric によって管理されます。

`runAsync()` は同期的なブロックを行いません。 CompletableFuture の内部で行う必要がある実行時間の長いタスクをワークロードで実装する必要がある場合は、 ランタイムが続行できるように CompletableFuture を RunAsync の実装で返す必要があります。

#### <a name="cancellation"></a>キャンセル
ワークロードの取り消しは、提供されたキャンセル トークンを使用して協調的に調整されます。 システムはタスクが完了 (正常に完了、キャンセル、または失敗) するまで待機してから、次に進みます。 システムからキャンセルが要求された場合は、キャンセル トークンを利用して作業を完了し、できるだけ早く `runAsync()` を終了することが重要です。 次の例は、キャンセル イベントを処理する方法を示しています。

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

このステートレス サービスの例では、カウントはローカル変数に格納されます。 これはステートレス サービスであるため、保存される値は、サービス インスタンスの現在のライフサイクルのみで保持されます。 このサービスを移動または再起動すると、値は失われます。

## <a name="create-a-stateful-service"></a>ステートフル サービスの作成
Service Fabric には、新しい種類のステートフルなサービスが導入されています。 ステートフル サービスは、サービス内に状態を確実に維持でき、それを使用するコードと同じ場所に配置できます。 Service Fabric によって状態の可用性が高まるため、外部ストアに状態を維持する必要がなくなります。

サービスが移動または再起動した場合でも、カウンター値をステートレスから高可用と永続性に変換するには、ステートフル サービスが必要です。

HelloWorld アプリケーションと同じディレクトリで、`yo azuresfjava:AddService` コマンドを実行して、新しいサービスを追加できます。 フレームワークで 「信頼性の高いステートフル サービス」を選択し、サービスに "HelloWorldStateful" という名前を付けます。 

これで、アプリケーションには、ステートレス サービス HelloWorld とステートフル サービス HelloWorldStateful の 2 つのサービスが含まれるようになります。

ステートフル サービスのエントリ ポイントは、ステートレス サービスと同じです。 主な違いは、状態プロバイダーを使用して状態を確実に保存できることです。 Service Fabric には、Reliable Collection という状態プロバイダー実装が用意されています。Reliable Collection では、Reliable State Manager を使用してレプリケートされたデータ構造を作成できます。 ステートフル Reliable Service では、この状態プロバイダーを既定で使用します。

**HelloWorldStateful -> src** で、次の RunAsync メソッドを含む HelloWorldStateful.java を開きます。

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` は、ステートフル サービスとステートレス サービスで同様に動作します。 ただし、ステートフル サービスでは、プラットフォームは、 `RunAsync()`を実行する前に、ユーザーに代わって追加の作業を行います。 この作業には、Reliable State Manager と Reliable Collection が使用できる状態にあることの確認が含まれる場合があります。

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Reliable Collection と Reliable State Manager
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](/java/api/microsoft.servicefabric.data.collections.reliablehashmap) は、サービスに状態を確実に格納するために使用できるディクショナリ実装です。 Service Fabric と Reliable HashMap を使用すると、データをサービスに直接格納できるため、外部の永続ストアが必要ありません。 Reliable HashMap により、データの可用性が向上します。 Service Fabric では、サービスの複数の *レプリカ* を作成して管理することでこれを実現します。 また、これらのレプリカとその状態遷移の管理の複雑さを取り除く API も提供します。

Reliable Collection にはカスタム型を含むすべての Java 型を格納できます。ただし次の点にご注意ください。

* Service Fabric がノード全体で状態を *レプリケート* して状態の可用性を高め、Reliable HashMap が各レプリカでデータをローカル ディスクに保存します。 これは、Reliable HashMap で保存されるすべてのデータは *シリアル化可能である* 必要があることを意味します。 
* Reliable HashMap でトランザクションをコミットすると、可用性を高めるためにオブジェクトがレプリケートされます。 Reliable HashMap に格納されるオブジェクトは、サービスのローカル メモリに保持されます。 これは、オブジェクトへのローカルな参照があることを意味します。
  
   トランザクションの Reliable Collection を更新せずに、これらのオブジェクトのローカル インスタンスを変更しないようにしてください。 オブジェクトのローカル インスタンスの変更は自動的にレプリケートされないためです。 オブジェクトをディクショナリに再挿入するか、ディクショナリで *update* メソッドのいずれかを使用する必要があります。

Reliable HashMap の管理は Reliable State Manager が行います。 サービス内のどの場所でも、Reliable Collection の名前を指定することで、Reliable State Manager に Reliable Collection をいつでも要求できます。 Reliable State Manager により、参照を確実に取得できます。 Reliable Collection インスタンスへの参照をクラス メンバー変数やプロパティに保存することはお勧めしません。 サービスのライフサイクル中、参照が常にインスタンスに設定されていることを保証するために特に注意を払う必要があります。 この作業は Reliable State Manager によって処理され、繰り返されるアクセスのために最適化されます。


### <a name="transactional-and-asynchronous-operations"></a>トランザクション処理と非同期処理
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Reliable HashMap での操作は非同期です。 Reliable Collection での書き込み操作では、データをレプリケートしてディスクに保持するために I/O 操作が実行されるためです。

Reliable HashMap の操作は *トランザクション* であるため、複数の Reliable HashMap と操作で状態の整合性を維持できます。 たとえば、1 つのトランザクション内で、ある Reliable Dictionary から作業項目を取得し、その項目に対して操作を実行した後、結果を別の Reliable HashMap に保存することもできます。 これはアトミック操作として扱われので、操作全体が成功するか、操作全体がロールバックされることが保証されます。 項目をデキューしたが、結果を保存する前にエラーが発生した場合は、トランザクション全体がロールバックされ、項目は処理のためにキューに残ります。


## <a name="build-the-application"></a>アプリケーションのビルド

Yeoman スキャフォールディングには、アプリケーションをビルドするための gradle スクリプトと、アプリケーションをデプロイおよび削除するための bash スクリプトが含まれています。 アプリケーションを実行するには、最初に gradle を使用してアプリケーションをビルドします。

```bash
$ gradle
```

これにより、Service Fabric CLI を使ってデプロイできる Service Fabric アプリケーション パッケージが生成されます。

## <a name="deploy-the-application"></a>アプリケーションの配置

ビルドしたアプリケーションは、ローカル クラスターにデプロイできます。

1. ローカルの Service Fabric クラスターに接続します。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. テンプレートに用意されているインストール スクリプトを実行してクラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

    ```bash
    ./install.sh
    ```

ビルドしたアプリケーションは、他のすべての Service Fabric アプリケーションと同じようにデプロイできます。 詳細な手順については、[Service Fabric CLI を使用した Service Fabric アプリケーションの管理](service-fabric-application-lifecycle-sfctl.md)についてのドキュメントを参照してください。

これらのコマンドのパラメーターは、アプリケーション パッケージ内の生成されたマニフェストで確認できます。

アプリケーションのデプロイ後、ブラウザーを開いて `http://localhost:19080/Explorer` の [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) に移動します。 次に、**Applications** ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認してください。

> [!IMPORTANT]
> アプリケーションを Azure 内のセキュアな Linux クラスターにデプロイするには、Service Fabric ランタイムを使用してアプリケーションを検証するように証明書を構成する必要があります。 これにより、Reliable Services サービスが基盤の Service Fabric ランタイム API と通信できるようになります。 詳しくは、「[Reliable Services アプリを Linux クラスター上で実行するように構成する](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)」をご覧ください。  
>

## <a name="next-steps"></a>次のステップ

* [Service Fabric CLI の概要](service-fabric-cli.md)
