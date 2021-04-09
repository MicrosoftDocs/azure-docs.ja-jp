---
title: Azure Cosmos DB の Cassandra API の一般的なエラーのトラブルシューティング
description: このドキュメントでは、Azure Cosmos DB の Cassandra API で発生する一般的な問題のトラブルシューティング方法について説明します
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: f9b6e586879b8697660ced7aa6f1e75083e3ee29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658573"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB の Cassandra API の一般的な問題のトラブルシューティング
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB の Cassandra API は互換性レイヤーであり、広く普及しているオープンソースの Apache Cassandra データベースに[ワイヤプロトコルのサポート](cassandra-support.md)を提供し、[Azure Cosmos DB ](./introduction.md)によって機能します。 フル マネージド クラウドネイティブ サービスとして、Azure Cosmos DB は、Cassandra API に[可用性、スループット、および整合性に対する保証](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)を提供します。 Apache Cassandra のレガシ実装では、これらの保証はできません。 また Cassandra API は、メンテナンス不要のプラットフォーム操作とダウンタイム不要の修正プログラム適用を容易にします。 したがって、そのバックエンド操作の多くが Apache Cassandra とは異なるので、一般的なエラーを回避するために特定の設定と方法をお勧めします。 

この記事では、Azure Cosmos DB の Cassandra API を使用するアプリケーションの一般的なエラーと解決策について説明します。 エラーが以下に記載されておらず、[Cassandra API でサポートされる操作](cassandra-support.md)を実行するときにエラーが発生するが、*ネイティブの Apache Cassandra の使用時にはエラーが存在しない場合は*、[Azure サポート要求を作成します](../azure-portal/supportability/how-to-create-azure-support-request.md)。

## <a name="nonodeavailableexception"></a>NoNodeAvailableException
これは最上位レベルのラッパー例外であり、考えられる原因と内部例外が多数あり、その多くがクライアントに関連している可能性があります。 
### <a name="solution"></a>解決策
いくつかの一般的な原因と解決策を次に示します。 
- Azure LoadBalancers のアイドル タイムアウト: これは、`ClosedConnectionException` のように現れる場合もあります。 これを解決するには、ドライバーのキープアライブ設定を行い ([下記](#enable-keep-alive-for-java-driver)参照)、オペレーティング システムのキープアライブ設定を増やすか、または [Azure Load Balancer でアイドル タイムアウトを調整します](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)。 
- **クライアント アプリケーション リソースの枯渇:** クライアント マシンに要求を完了するための十分なリソースがあることを確認します。 

## <a name="cannot-connect-to-host"></a>ホストに接続できない
次のエラーが表示されることがあります: `Cannot connect to any host, scheduling retry in 600000 milliseconds`。 

### <a name="solution"></a>解決策
これはクライアント側での SNAT の枯渇である可能性があります。 この問題を解決するには、[アウトバウンド接続での SNAT](../load-balancer/load-balancer-outbound-connections.md) に関するページの手順に従ってください。 これは、Azure ロード バランサーで既定で 4 分間のアイドル タイムアウトが発生する、アイドル タイムアウトの問題である場合もあります。 [ロード バランサーのアイドル タイムアウト](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)に関するドキュメントを参照してください。 ドライバー設定から TCP キープアライブを有効にし ([下記](#enable-keep-alive-for-java-driver)参照)、オペレーティング システムの `keepAlive` 間隔を 4 分未満に設定してください。

 

## <a name="overloadedexception-java"></a>OverloadedException (Java)
使用される要求ユニットの合計数は、キースペースまたはテーブルでプロビジョニングされた要求ユニットよりも多くなります。 そのため、要求は調整されます。
### <a name="solution"></a>解決策
Azure portal からキースペースまたはテーブルに割り当てられたスループットのスケーリング (Cassandra API でのスケーリング操作については、[こちら](manage-scale-cassandra.md)をご覧ください) を検討するか、または再試行ポリシーを実装することもできます。 Java の場合は、[v3.x ドライバー](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)と [v4.x ドライバー](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4)の再試行サンプルをご覧ください。 「[Azure Cosmos Cassandra Extensions for Java](https://github.com/Azure/azure-cosmos-cassandra-extensions)」もご覧ください。

### <a name="overloadedexception-even-with-sufficient-throughput"></a>OverloadedException でも十分なスループットを確保 
要求の量と消費された要求ユニット コストに対して十分なスループットがプロビジョニングされているにもかかわらず、システムは要求を調整しているように思われます。 予期しないレート制限には、次の 2 つの原因が考えられます。
- **スキーマ レベルの操作:** Cassandra API は、スキーマレベルの操作 (CREATE TABLE、ALTER TABLE、DROP TABLE) に対してシステム スループット予算を実装します。 実稼働システムでのスキーマ操作には、この予算で十分です。 ただし、スキーマレベルの操作の数が多い場合は、この制限を超えている可能性があります。 この予算はユーザーによって制御されないため、実行するスキーマ操作の数を減らすことを検討する必要があります。 このアクションを実行しても問題が解決されない場合、またはワークロードに対して採算が合わない場合は、[Azure サポート要求を作成](../azure-portal/supportability/how-to-create-azure-support-request.md)してください。
- **データ スキュー**: スループットが Cassandra API でプロビジョニングされている場合は、物理パーティション間で均等に分割され、各物理パーティションに上限があります。 1 つの特定のパーティションから大量のデータを挿入または照会する場合、そのテーブルに大量の全体的なスループット (要求ユニット) をプロビジョニングしているにもかかわらず、レートが制限される可能性があります。 データ モデルを確認し、ホット パーティションを発生させる可能性のある過度なスキューがないことを確認してください。 

## <a name="intermittent-connectivity-errors-java"></a>断続的な接続エラー (Java) 
接続が予想外に切断したりタイムアウトしたりします。

### <a name="solution"></a>解決策 
Java 用 Apache Cassandra ドライバーには、`ExponentialReconnectionPolicy` と `ConstantReconnectionPolicy` の 2 つのネイティブ再接続ポリシーが用意されています。 既定では、 `ExponentialReconnectionPolicy`です。 ただし、Azure Cosmos DB の Cassandra API の場合は、2 秒の遅延で `ConstantReconnectionPolicy` を実行することをお勧めします。 Java v4.x のドライバーについては、[ドライバーのドキュメント](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)を、Java 3.x のガイダンスについては、[こちら](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/)をご覧ください (以下の「[Java ドライバーの ReconnectionPolicy の構成](#configuring-reconnectionpolicy-for-java-driver)」の例もご覧ください)。

## <a name="error-with-load-balancing-policy"></a>負荷分散ポリシーのエラー

次のようなコードを使用して、Java Datastax ドライバーの v3.x に負荷分散ポリシーを実装している場合です。

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

`withLocalDc()` の値がコンタクト ポイントのデータセンターと一致しない場合、非常に断続的なエラーが発生することがあります: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)`。 

### <a name="solution"></a>解決策 
[CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) を実装します (これを機能させるには、datastax マイナー バージョンをアップグレードする必要がある場合があります)。

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>大きなテーブルでカウントが失敗する
多数の行で `select count(*) from table` または同様のものを実行すると、サーバーがタイムアウトになります。

### <a name="solution"></a>解決策 
ローカルの CQLSH クライアントを使用している場合は、`--connect-timeout` または `--request-timeout` の設定を変更してみることができます (詳細については、[こちら](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)を参照してください)。 これでは十分ではなく、カウントがまだタイムアウトになる場合は、Azure portal の [メトリック] タブに移動し、メトリック `document count` を選択して、データベースまたはコレクションのフィルターを追加することによって、Azure Cosmos DB バックエンド テレメトリからレコードの数を取得できます (Azure Cosmos DB のテーブルのアナログ)。 その後、レコード数をカウントする特定の時点のグラフにマウス ポインターを合わせることができます。

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="メトリック ビュー":::


## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Java ドライバーの ReconnectionPolicy の構成

### <a name="version-3x"></a>バージョン 3.x

Java ドライバーのバージョン 3.x では、クラスター オブジェクトの作成時に再接続ポリシーを構成します。

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>バージョン 4.x

Java ドライバーのバージョン 4.x では、`reference.conf` ファイルの設定をオーバーライドして再接続ポリシーを構成します。

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-java-driver"></a>Java ドライバーのキープアライブを有効にする

### <a name="version-3x"></a>バージョン 3.x

Java ドライバーのバージョン 3.x では、クラスター オブジェクトの作成時にキープアライブを設定し、キープアライブが[オペレーティング システムで有効になっていること](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)を確認します。

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>バージョン 4.x

Java ドライバーのバージョン 4.x では、`reference.conf` の設定をオーバーライドしてキープアライブを設定し、キープアライブが[オペレーティング システムで有効になっていること](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)を確認します。

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>次のステップ

- Azure Cosmos DB の Cassandra API で[サポートされている機能](cassandra-support.md)について確認します。
- [Apache Cassandra から Azure Cosmos DB の Cassandra API にデータを移行する](cassandra-migrate-cosmos-db-databricks.md)方法について確認します
