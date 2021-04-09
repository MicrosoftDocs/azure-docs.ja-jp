---
title: Azure Cosmos DB Async Java SDK v2 の診断およびトラブルシューティング
description: クライアント側ログ、他のサード パーティのツールなどの機能を使って、Async Java SDK v2 で Azure Cosmos DB の問題を特定、診断、およびトラブルシューティングします。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: b39a74bd06f942cf21d201c8cef48bc6dfc57d46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96548088"
---
# <a name="troubleshoot-issues-when-you-use-the-azure-cosmos-db-async-java-sdk-v2-with-sql-api-accounts"></a>SQL API アカウントで Azure Cosmos DB Async Java SDK v2 を使用する場合の問題のトラブルシューティング
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> これは Azure Cosmos DB 用の最新の Java SDK では "*ありません*"。 対象のプロジェクトを [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) にアップグレードしてから、Azure Cosmos DB Java SDK v4 の[トラブルシューティング ガイド](troubleshoot-java-sdk-v4-sql.md)をお読みください。 [Azure Cosmos DB Java SDK v4 への移行](migrate-java-v4-sdk.md)に関するガイド、および[リアクターと RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) に関するガイドの手順に従ってアップグレードします。 
>
> この記事では、Azure Cosmos DB Async Java SDK v2 のトラブルシューティングについてのみ説明します。 詳細については、Azure Cosmos DB Async Java SDK v2 の[リリース ノート](sql-api-sdk-async-java.md)、[Maven リポジトリ](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)、および[パフォーマンスに関するヒント](performance-tips-async-java.md)を参照してください。
>

この記事では、Azure Cosmos DB SQL API アカウントで [Java Async SDK](sql-api-sdk-async-java.md) を使用するときの一般的な問題、回避策、診断手順、およびツールについて説明します。
Java Async SDK には、Azure Cosmos DB SQL API にアクセスするためのクライアント側の論理表現が用意されています。 この記事では、問題が発生した場合に役立つツールとアプローチについて説明します。

次の一覧から開始します。

* この記事の[一般的な問題と対処法]のセクションを確認します。
* SDK を確認します。これは [GitHub 上でオープン ソース](https://github.com/Azure/azure-cosmosdb-java)として入手できます。 アクティブに監視されている[問題セクション](https://github.com/Azure/azure-cosmosdb-java/issues)があります。 回避策が既に提出済みの同様の問題がないか確認します。
* [パフォーマンスに関するヒント](performance-tips-async-java.md)を確認し、推奨される方法に従います。
* この記事の残りの部分を読みます。解決策が見つからない場合は、 [GitHub の問題](https://github.com/Azure/azure-cosmosdb-java/issues)を提出します。

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>一般的な問題と対処法

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>ネットワークの問題、Netty の読み取りタイムアウト エラーの発生、低いスループット、長い待機時間

#### <a name="general-suggestions"></a>一般的な推奨事項
* アプリが Azure Cosmos DB アカウントと同じリージョンで実行されていることを確認します。 
* アプリが実行されているホストの CPU 使用率を確認します。 CPU 使用率が 90% 以上の場合は、より高度な構成のホスト上でアプリを実行します。 また、より多数のマシンに負荷を分散することもできます。

#### <a name="connection-throttling"></a>接続の帯域幅調整
接続の帯域幅調整は、[ホスト マシンの接続制限]、または [Azure SNAT (PAT) ポート不足]のいずれかが原因で発生します。

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>ホスト マシンの接続制限
一部の Linux システム (Red Hat など) では、開くファイルの最大数に上限があります。 Linux のソケットはファイルとして実装されるため、この数は接続の合計数も制限します。
次のコマンドを実行します。

```bash
ulimit -a
```
開くことができる最大ファイル数 ("nofile" で指定) は、接続プール サイズの 2 倍以上にする必要があります。 詳細については、[パフォーマンスのヒント](performance-tips-async-java.md)に関するページを参照してください。

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) ポート不足

パブリック IP アドレスを使わずにアプリを Azure Virtual Machines にデプロイした場合、既定では [Azure SNAT ポート](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)によって VM 外の任意のエンドポイントへの接続が確立されます。 VM から Azure Cosmos DB エンドポイントへの許可される接続の数は、[Azure SNAT 構成](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)によって制限されます。

 Azure SNAT ポートが使用されるのは、VM がプライベート IP アドレスを持ち、VM からのプロセスが、パブリック IP アドレスに接続しようとしている場合に限られます。 Azure SNAT の制限を回避するには次の 2 つの回避策があります。

* Azure Virtual Machines 仮想ネットワークのサブネットに Azure Cosmos DB サービス エンドポイントを追加します。 詳細については、[Azure 仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)に関するページを参照してください。 

    サービス エンドポイントが有効になると、要求はパブリック IP から Azure Cosmos DB に送信されなくなります。 代わりに、仮想ネットワークとサブネット ID が送信されます。 この変更により、パブリック IP のみが許可された場合はファイアウォール ドロップが発生することがあります。 ファイアウォールを使用している場合、サービス エンドポイントを有効にするときに、[Virtual Network ACL](/previous-versions/azure/virtual-network/virtual-networks-acl) を使用してファイアウォールにサブネットを追加します。
* Azure VM にパブリック IP を割り当てます。

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>サービスに到達できない - ファイアウォール
``ConnectTimeoutException`` は、SDK がサービスに到達できないことを示します。
直接モードを使用しているときに、次のようなエラーが発生することがあります。
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

アプリ マシン上でファイアウォールが実行されている場合は、直接モードで使用されるポート範囲 10,000 ～ 20,000 を開きます。
また、「[ホスト マシンの接続制限](#connection-limit-on-host)」にも従ってください。

#### <a name="http-proxy"></a>HTTP プロキシ

HTTP プロキシを使用する場合は、SDK `ConnectionPolicy` で構成されている接続の数をサポートできることを確認します。
できない場合、接続の問題が発生します。

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>コーディング パターンが無効です: Netty IO スレッドのブロック

SDK では、Azure Cosmos DB との通信に [Netty](https://netty.io/) IO ライブラリを使用します。 SDK には Async API があり、Netty の非ブロッキング IO API を使用します。 SDK の IO 作業は IO Netty スレッドで実行されます。 IO Netty スレッドの数は、アプリマシンの CPU コアの数と同じなるように構成されます。 

Netty IO スレッドは、非ブロッキング Netty IO 作業のためだけに使用されます。 SDK は、Netty IO スレッドの 1 つの API 呼び出しの結果をアプリのコードに返します。 アプリが Netty スレッドで結果を受け取ってから長時間の処理を実行すると、内部 IO 作業を実行できる十分な IO スレッドが SDK にない可能性があります。 このようなアプリのコーディングでは、スループットが低く、待ち時間が長く、`io.netty.handler.timeout.ReadTimeoutException` エラーが発生することがあります。 回避策は、操作に時間がかかることがわかっている場合にスレッドを切り替えることです。

たとえば、次のコード スニペットを見てみましょう。 Netty スレッドで数ミリ秒以上かかる長時間の作業を実行することがあります。 このような場合、最終的に IO 作業を処理する Netty IO スレッドが存在しない状態になる可能性があります。 その結果、ReadTimeoutException エラーが発生します。

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-readtimeout"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
回避策は、時間のかかる作業を実行するスレッドを変更することです。 アプリのスケジューラのシングルトン インスタンスを定義します。

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-scheduler"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
```
たとえば、時間のかかる作業 (たとえば、IO をブロックする計算負荷の高い作業) を行う必要がある場合があります。 このような場合は、`.observeOn(customScheduler)` API を使用して、`customScheduler` によって提供される worker にスレッドを切り替えます。

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-applycustomscheduler"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
`observeOn(customScheduler)` を使用することによって、Netty IO スレッドを解放し、カスタム スケジューラによって提供される独自のカスタム スレッドに切り替えます。 この変更によって問題が解決します。 `io.netty.handler.timeout.ReadTimeoutException` エラーは発生しなくなります。

### <a name="connection-pool-exhausted-issue"></a>接続プール不足の問題

`PoolExhaustedException` はクライアント側のエラーです。 このエラーは、SDK 接続プールが対応できるよりもアプリのワークロードが高いことを示します。 接続プールのサイズを増やすか、複数のアプリに負荷を分散します。

### <a name="request-rate-too-large"></a>要求率が大きすぎる
このエラーはサーバー側のエラーです。 これは、プロビジョニングされたスループットを消費したことを示します。 後で再試行してください。 このエラーが頻繁に発生する場合、コレクション スループットを増やすことを検討してください。

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Azure Cosmos DB エミュレーターへの接続の失敗

Azure Cosmos DB エミュレーターの HTTPS 証明書が自己署名されています。 SDK でエミュレーターを動作させるには、エミュレーター証明書を Java トラスト ストアにインポートします。 詳細については、[Azure Cosmos DB エミュレーター証明書のエクスポート](local-emulator-export-ssl-certificates.md)に関するページを参照してください。

### <a name="dependency-conflict-issues"></a>依存関係の競合の問題

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

上記の例外は、RxJava lib (1.2.2 など) の古いバージョンに依存関係があることを示しています。 弊社の SDK は、以前のバージョンの RxJava では使用できない API がある、RxJava 1.3.8 に依存しています。 

このような問題の回避策は、他のどの依存関係が RxJava-1.2.2 に取り込まれ、RxJava-1.2.2 への過渡的な依存関係が除外されるかを識別し、CosmosDB SDK が新しいバージョンを取り込むのを許可することです。

どのライブラリが RxJava 1.2.2 に取り込まれるかを識別するには、プロジェクト pom.xml ファイルの次のコマンドを実行します。
```bash
mvn dependency:tree
```
詳細については、[maven の依存関係ツリー ガイド](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)を参照してください。

RxJava 1.2.2 のプロジェクトの他の依存関係の過渡的依存関係を識別したら、pom ファイルでその lib に対する依存関係を変更して、それへの RxJava の過渡的依存関係を除外できます。

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

詳細については、[推移的な依存関係の除外ガイド](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)を参照してください。


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>クライアント SDK のログ記録を有効にする

Java Async SDK では、log4j や logback などの一般的なログ記録フレームワークへのログ記録をサポートするロギング ファサードとして SLF4j を使用 します。

たとえば、ログ記録フレームワークとして log4j を使用する場合は、Java classpath に次の libs を追加します。

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

また log4j の構成を追加します。
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

詳細については、[sfl4j ロギング マニュアル](https://www.slf4j.org/manual.html)を参照してください。

## <a name="os-network-statistics"></a><a name="netstats"></a>OS ネットワーク統計
netstat コマンドを実行して、`ESTABLISHED` や `CLOSE_WAIT` などの状態の接続がいくつあるか把握します。

Linux では、次のコマンドを実行できます。
```bash
netstat -nap
```
Azure Cosmos DB エンドポイントへの接続だけを示すように結果をフィルター処理します。

`ESTABLISHED` 状態の Azure Cosmos DB エンドポイントへの接続の数が、構成済みの接続プール サイズより大きくなることはありません。

Azure Cosmos DB エンドポイントへの接続の多くが `CLOSE_WAIT` 状態である可能性があります。 1,000 を超える可能性があります。 これほど多い数は、接続が確立され、すぐに切断されていることを示します。 このような状況が原因で問題が起こる可能性があります。 詳細については、「[一般的な問題と対処法]」セクションを参照してください。

 <!--Anchors-->
[一般的な問題と対処法]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[ホスト マシンの接続制限]: #connection-limit-on-host
[Azure SNAT (PAT) ポート不足]: #snat
