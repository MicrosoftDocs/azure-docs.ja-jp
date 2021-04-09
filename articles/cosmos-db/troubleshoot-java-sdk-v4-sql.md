---
title: Azure Cosmos DB Java SDK v4 の診断およびトラブルシューティング
description: クライアント側ログ、他のサード パーティのツールなどの機能を使って、Java SDK v4 で Azure Cosmos DB の問題を特定、診断、およびトラブルシューティングします。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.custom: devx-track-java
ms.openlocfilehash: cba8b97adb40ca2c277268188ff6ad541c7e9676
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596475"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>SQL API アカウントで Azure Cosmos DB Java SDK v4 を使用する場合の問題のトラブルシューティング
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> この記事では、Azure Cosmos DB Java SDK v4 のトラブルシューティングについてのみ説明します。 詳細については、Azure Cosmos DB Java SDK v4 [リリース ノート](sql-api-sdk-java-v4.md)、[Maven リポジトリ](https://mvnrepository.com/artifact/com.azure/azure-cosmos)、[パフォーマンスのヒント](performance-tips-java-sdk-v4-sql.md)を参照してください。 v4 より前のバージョンを現在使用している場合、v4 へのアップグレードについては、[Azure Cosmos DB Java SDK v4 への移行](migrate-java-v4-sdk.md)ガイドを参照してください。
>

この記事では、Azure Cosmos DB SQL API アカウントで Azure Cosmos DB Java SDK v4 を使用するときの一般的な問題、回避策、診断手順、およびツールについて説明します。
Azure Cosmos DB Java SDK v4 には、Azure Cosmos DB SQL API にアクセスするためのクライアント側の論理表現が用意されています。 この記事では、問題が発生した場合に役立つツールとアプローチについて説明します。

次の一覧から開始します。

* この記事の[一般的な問題と対処法]のセクションを確認します。
* Azure Cosmos DB 中央リポジトリにある Java SDK を参照してください。これは、[GitHub のオープン ソース](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)として利用可能です。 アクティブに監視されている[問題セクション](https://github.com/Azure/azure-sdk-for-java/issues)があります。 回避策が既に提出済みの同様の問題がないか確認します。 役に立つヒントの 1 つは、*cosmos:v4-item* タグによって問題をフィルター処理することです。
* Azure Cosmos DB Java SDK v4 の[パフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md)を確認し、推奨される方法に従います。
* この記事の残りの部分を読みます。解決策が見つからない場合は、 [GitHub の問題](https://github.com/Azure/azure-sdk-for-java/issues)を提出します。 GitHub の問題にタグを追加するオプションがある場合は、*cosmos:v4-item* タグを追加します。

### <a name="retry-logic"></a>再試行ロジック <a id="retry-logics"></a>
SDK での再試行が可能な場合、すべての IO エラーで Cosmos DB SDK は、失敗した操作の再試行を試みます。 エラーが発生した場合に再試行を行うことをお勧めしますが、特に書き込みエラーの処理と再試行は必要です。 再試行ロジックが継続的に改善されているため、最新の SDK を使用することをお勧めします。

1. 読み取りおよびクエリ IO エラーは、エンド ユーザーにはわからないまま SDK によって再試行されます。
2. 書き込み (Create、Upsert、Replace、Delete) はべき等では "なく"、したがって SDK は必ずしも無条件に、失敗した書き込み操作を再試行できるわけではありません。 エラーを処理して再試行するには、ユーザーのアプリケーション ロジックが必要です。
3. [SDK の可用性のトラブルシューティング](troubleshoot-sdk-availability.md)に関するページでは、複数リージョンの Cosmos DB アカウントの再試行について説明しています。

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>一般的な問題と対処法

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>ネットワークの問題、Netty の読み取りタイムアウト エラーの発生、低いスループット、長い待機時間

#### <a name="general-suggestions"></a>一般的な推奨事項
パフォーマンスを最大限高めるためのヒントを示します。
* アプリが Azure Cosmos DB アカウントと同じリージョンで実行されていることを確認します。 
* アプリが実行されているホストの CPU 使用率を確認します。 CPU 使用率が 50% 以上の場合は、より高度な構成のホスト上でアプリを実行します。 また、より多数のマシンに負荷を分散することもできます。
    * Azure Kubernetes Service でアプリケーションを実行している場合は、[Azure Monitor を使用して CPU 使用率を監視する](../azure-monitor/containers/container-insights-analyze.md)ことができます。

#### <a name="connection-throttling"></a>接続の帯域幅調整
接続の帯域幅調整は、[ホスト マシンの接続制限]、または [Azure SNAT (PAT) ポート不足]のいずれかが原因で発生します。

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>ホスト マシンの接続制限
一部の Linux システム (Red Hat など) では、開くファイルの最大数に上限があります。 Linux のソケットはファイルとして実装されるため、この数は接続の合計数も制限します。
次のコマンドを実行します。

```bash
ulimit -a
```
開くことができる最大ファイル数 ("nofile" で指定) は、接続プール サイズの 2 倍以上にする必要があります。 詳細については、Azure Cosmos DB Java SDK v4 の[パフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md)を参照してください。

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

SDK では、Azure Cosmos DB との通信に [Netty](https://netty.io/) IO ライブラリを使用します。 SDK には Async API があり、Netty の非ブロッキング IO API が使用されます。 SDK の IO 作業は IO Netty スレッドで実行されます。 IO Netty スレッドの数は、アプリマシンの CPU コアの数と同じなるように構成されます。 

Netty IO スレッドは、非ブロッキング Netty IO 作業のためだけに使用されます。 SDK は、Netty IO スレッドの 1 つの API 呼び出しの結果をアプリのコードに返します。 アプリが Netty スレッドで結果を受け取ってから長時間の処理を実行すると、内部 IO 作業を実行できる十分な IO スレッドが SDK にない可能性があります。 このようなアプリのコーディングでは、スループットが低く、待ち時間が長く、`io.netty.handler.timeout.ReadTimeoutException` エラーが発生することがあります。 回避策は、操作に時間がかかることがわかっている場合にスレッドを切り替えることです。

たとえば、コンテナーに項目を追加する次のコード スニペットを見てみましょう (データベースとコンテナーの設定に関するガイダンスについては、[こちら](create-sql-api-java.md)を参照してください)。Netty スレッドで数ミリ秒以上かかる長時間の作業を実行することがあります。 このような場合、最終的に IO 作業を処理する Netty IO スレッドが存在しない状態になる可能性があります。 その結果、ReadTimeoutException エラーが発生します。

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootNeedsSchedulerAsync)]

回避策は、時間のかかる作業を実行するスレッドを変更することです。 アプリのスケジューラのシングルトン インスタンスを定義します。

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootCustomSchedulerAsync)]

たとえば、時間のかかる作業 (たとえば、IO をブロックする計算負荷の高い作業) を行う必要がある場合があります。 このような場合は、`.publishOn(customScheduler)` API を使用して、`customScheduler` によって提供される worker にスレッドを切り替えます。

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootPublishOnSchedulerAsync)]

`publishOn(customScheduler)` を使用することによって、Netty IO スレッドを解放し、カスタム スケジューラによって提供される独自のカスタム スレッドに切り替えます。 この変更によって問題が解決します。 `io.netty.handler.timeout.ReadTimeoutException` エラーは発生しなくなります。

### <a name="request-rate-too-large"></a>要求率が大きすぎる
このエラーはサーバー側のエラーです。 これは、プロビジョニングされたスループットを消費したことを示します。 後で再試行してください。 このエラーが頻繁に発生する場合、コレクション スループットを増やすことを検討してください。

* **GetRetryAfterInMilliseconds の間隔でバックオフを実装する**

    パフォーマンス テストでは、調整される要求の割合がわずかになるまで負荷を上げる必要があります。 スロットル状態になった場合は、クライアント アプリケーションでバックオフ値を適用し、サーバー側によって指定された再試行間隔を後退させる必要があります。 バックオフにより、再試行までの待ち時間を最小限に抑えることができます。

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Azure Cosmos DB エミュレーターへの接続の失敗

Azure Cosmos DB エミュレーターの HTTPS 証明書が自己署名されています。 SDK でエミュレーターを動作させるには、エミュレーター証明書を Java トラスト ストアにインポートします。 詳細については、[Azure Cosmos DB エミュレーター証明書のエクスポート](local-emulator-export-ssl-certificates.md)に関するページを参照してください。

### <a name="dependency-conflict-issues"></a>依存関係の競合の問題

Azure Cosmos DB Java SDK には、さまざまな依存関係が取り込まれます。一般に、プロジェクトの依存関係ツリーに Azure Cosmos DB Java SDK が依存している古いバージョンのアーティファクトが含まれている場合、アプリケーションの実行時に予期しないエラーが発生する可能性があります。 アプリケーションから予期しない例外がスローされる原因をデバッグしている場合は、依存関係ツリーに 1 つ以上の Azure Cosmos DB Java SDK 依存関係の古いバージョンが誤って取り込まれていないことを改めて確かめるようにお勧めします。

このような問題を回避するには、プロジェクトのどの依存関係で古いバージョンが取り込まれているかを特定し、その古いバージョンに対する推移的な依存関係を排除して、Azure Cosmos DB Java SDK で新しいバージョンを取り込むことができるようにします。

プロジェクトの依存関係のうち、Azure Cosmos DB Java SDK が依存する古いバージョンを取り込むものを特定するには、プロジェクトの pom.xml ファイルに対して次のコマンドを実行します。
```bash
mvn dependency:tree
```
詳細については、[maven の依存関係ツリー ガイド](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)を参照してください。

プロジェクトの依存関係のうち、古いバージョンに依存しているものがどれかわかったら、次の例に従って、pom ファイル内でその lib に対する依存関係を変更し、推移的な依存関係を排除します (例は "*リアクター コア*" が古い依存関係であることを前提としています)。

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

詳細については、[推移的な依存関係の除外ガイド](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)を参照してください。


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>クライアント SDK のログ記録を有効にする

Azure Cosmos DB Java SDK v4 では、log4j や logback などの一般的なログ記録フレームワークへのログ記録をサポートするロギング ファサードとして SLF4j が使用されます。

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

# Set root logger level to INFO and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.azure.cosmos=INFO
#log4j.category.io.netty=OFF
#log4j.category.io.projectreactor=OFF
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

Windows では、同じコマンドを異なる引数フラグで実行できます。
```bash
netstat -abn
```

Azure Cosmos DB エンドポイントへの接続だけを示すように結果をフィルター処理します。

`ESTABLISHED` 状態の Azure Cosmos DB エンドポイントへの接続の数が、構成済みの接続プール サイズより大きくなることはありません。

Azure Cosmos DB エンドポイントへの接続の多くが `CLOSE_WAIT` 状態である可能性があります。 1,000 を超える可能性があります。 これほど多い数は、接続が確立され、すぐに切断されていることを示します。 このような状況が原因で問題が起こる可能性があります。 詳細については、「[一般的な問題と対処法]」セクションを参照してください。

 <!--Anchors-->
[一般的な問題と対処法]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[ホスト マシンの接続制限]: #connection-limit-on-host
[Azure SNAT (PAT) ポート不足]: #snat
