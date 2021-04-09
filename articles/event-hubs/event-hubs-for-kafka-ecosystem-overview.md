---
title: Apache Kafka アプリからイベント ハブを使用する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs での Apache Kafka のサポートに関する情報を提供します。
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: b0f0da76bba68f8a66695700d530e871cbd35e3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97861342"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Apache Kafka アプリケーションから Azure Event Hubs を使用する
Event Hubs により、独自の Apache Kafka® クラスターを実行する代わりに、既存のほとんどの Apache Kafka クライアント アプリケーションで使用できる、Apache Kafka プロデューサーおよびコンシューマー API と互換性のあるエンドポイントが提供されます。 Event Hubs によって、Apache Kafka のプロデューサーおよびコンシューマー API クライアントのバージョン 1.0 以降がサポートされています。

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Kafka 用 Event Hubs で提供されるもの

Apache Kafka 機能用の Event Hubs により、Apache Kafka サーバー バージョン 1.0 以降用に構築された Apache Kafka クライアントとプロトコル互換性がある Azure Event Hubs 上のプロトコル ヘッドが提供され、Event Hubs からの読み取りと Event Hubs への書き込みの両方がサポートされます。これは Apache Kafka のトピックと同等です。 

多くの場合、既存の Kafka セットアップと比較してコードを変更せずにアプリケーションから Event Hubs Kafka エンドポイントを使用でき、構成を変更することだけができます。Kafka クラスターを指すのではなく、イベント ハブによって公開されている Kafka エンドポイントを指すように、構成の接続文字列を更新します。 その後、Kafka プロトコルを使用するアプリケーションから Event Hubs へのイベントのストリーミングを開始できます。 

概念的には、Kafka と Event Hubs は非常に似ています。どちらもストリーミング データ用に作成されたパーティション分割されたログであり、クライアントにより、それを使用して保持されているログのどの部分を読み取るかが制御されます。 次の表に、Kafka の概念と Event Hubs の概念のマッピングを示します。

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka と Event Hubs の概念のマッピング

| Kafka での概念 | Event Hubs での概念|
| --- | --- |
| クラスター | 名前空間 |
| トピック | イベント ハブ |
| Partition | Partition|
| コンシューマー グループ | コンシューマー グループ |
| Offset | Offset|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Apache Kafka と Event Hubs の主な違い

[Apache Kafka](https://kafka.apache.org/) は通常インストールして運用する必要があるソフトウェアですが、Event Hubs はフル マネージドのクラウドネイティブ サービスです。 管理および監視する必要のあるサーバー、ディスク、ネットワークはなく、考慮または構成する必要のあるブローカーもありません。 完全修飾ドメイン名を持つエンドポイントである名前空間を作成し、その名前空間内に Event Hubs (トピック) を作成します。 

Event Hubs と名前空間について詳しくは、「[Event Hubs の機能](event-hubs-features.md#namespace)」をご覧ください。 クラウド サービスとしての Event Hubs は、単一の安定した仮想 IP アドレスをエンドポイントとして使用するため、クライアントは、ブローカーやクラスター内のマシンについて知っておく必要はありません。 Event Hubs によって同じプロトコルが実装されますが、この違いは、すべてのパーティションに対するすべての Kafka トラフィックが、クラスターのすべてのブローカーに対してファイアウォール アクセスを必要とするのではなく、この 1 つのエンドポイント経由で予測どおりにルーティングされることを意味します。   

Event Hubs のスケーリングは、購入するスループット ユニットの数によって制御されます。各スループット ユニットでは、1 秒あたり 1 メガバイト、またはイングレスでは 1 秒あたり 1,000 イベント、エグレスではその量の 2 倍を利用できます。 [自動インフレ](event-hubs-auto-inflate.md)機能を使用した場合、スループットの上限に達したときに、Event Hubs でスループット ユニットを自動的にスケールアップすることができます。この機能は、Apache Kafka プロトコルのサポートでも動作します。  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Apache Kafka はワークロードに適したソリューションか

Apache Kafka を使用したアプリケーションの構築だけでなく、Azure Event Hubs は [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) と [Azure Event Grid](../event-grid/overview.md) も含まれるサービスの一部であることを理解することも役に立ちます。 

Apache Kafka の商用ディストリビューションの一部のプロバイダーは、Apache Kafka がすべてのメッセージング プラットフォームのニーズに対するワンストップショップであると示唆していますが、実際には、Apache Kafka に、たとえば[競合コンシューマー](/azure/architecture/patterns/competing-consumers) キュー パターンは実装されておらず、単純なオフセット以外でサーバーで評価されたルールに基づいて受信メッセージにサブスクライバーがアクセスできるレベルでの[パブリッシュ - サブスクライブ](/azure/architecture/patterns/publisher-subscriber)はサポートされておらず、メッセージまたはサイドライン障害メッセージによって開始されたジョブのライフサイクルを配信不能キューまで追跡する機能はありません。これらはすべて、多くのエンタープライズ メッセージング シナリオで基本的なものです。

パターン間の違い、およびどのサービスでどのパターンが最もよくカバーされているかを理解するには、「[Azure での非同期メッセージングのオプション](/azure/architecture/guide/technology-choices/messaging)」ガイダンスをご確認ください。 Apache Kafka ユーザーがこれまで Kafka で実現した通信パスは、Event Grid または Service Bus を使用することで、基本的な複雑さが非常に低く、より強力な機能で実現できます。 

Apache Kafka インターフェイス用 Event Hubs を介して利用できない Apache Kafka の特定の機能が必要な場合、または実装パターンが [Event Hubs のクォータ](event-hubs-quotas.md)を超える場合は、[ネイティブ Apache Kafka クラスターを Azure HDInsight](../hdinsight/kafka/apache-kafka-introduction.md) で実行することもできます。  

## <a name="security-and-authentication"></a>セキュリティと認証
Kafka 用 Event Hubs からイベントを発行または使用するたびに、クライアントでは、Event Hubs リソースへのアクセスが試行されます。 認可されたエンティティを使用してリソースがアクセスされていることを確認する必要があります。 クライアントで Apache Kafka プロトコルを使用している場合、SASL のメカニズムを使用して認証と暗号化のための構成を設定できます。 Kafka 用 Event Hubs を使用するには、TLS 暗号化が必要となります (Event Hubs では転送中のデータがすべて TLS で暗号化されるため)。 これは、構成ファイルで SASL_SSL オプションを指定することによって実現できます。 

Azure Event Hubs には、セキュリティで保護されたリソースへのアクセスを承認するためのオプションが複数用意されています。 

- OAuth 2.0
- Shared Access Signature (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
Event Hubs は、Azure Active Directory (Azure AD) と連携するため、Azure AD の **OAuth** 2.0 に準拠した一元的な承認サーバーを利用できます。 Azure AD では、Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、粒度の細かいアクセス許可をクライアント ID に与えることができます。 protocol に **SASL_SSL** を、mechanism に **OAUTHBEARER** を指定すれば、この機能を Kafka クライアントで使用することができます。 Azure ロールとレベルを使用したアクセスのスコープ設定について詳しくは、[Azure AD によるアクセスの承認](authorize-access-azure-active-directory.md)に関するページを参照してください。

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Shared Access Signature (SAS)
Event Hubs には、**Shared Access Signature (SAS)** も用意されており、Kafka 用 Event Hubs リソースへの委任アクセスを実現することができます。 OAuth 2.0 トークンベースのメカニズムを使用したアクセス承認の方が、SAS よりもセキュリティが高く、使いやすさの点でも有利です。 また、ACL ベースの承認はユーザーが維持、管理する必要がありますが、組み込みロールであれば ACL ベースの承認は必要ありません。 protocol に **SASL_SSL** を、mechanism に **PLAIN** を指定すれば、この機能を Kafka クライアントで使用することができます。 

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}` を Event Hubs 名前空間への接続文字列に置き換えます。 接続文字列を取得する手順については、「[Event Hubs の接続文字列の取得](event-hubs-get-connection-string.md)」を参照してください。 構成の例には、`sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";` などがあります。

> [!NOTE]
> Kafka クライアントで SAS 認証を使用する場合、SAS キーの再生成時に確立された接続は切断されません。 


#### <a name="samples"></a>サンプル 
SAS または OAuth を使用してイベント ハブを作成しアクセスする手順の **チュートリアル** については、「[クイックスタート: Kafka プロトコルを使用した Event Hubs によるデータ ストリーミング](event-hubs-quickstart-kafka-enabled-event-hubs.md)」を参照してください。

Kafka 用 Event Hubs で OAuth を使用する方法を紹介したその他の **例** については、[GitHub 上のサンプル](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)を参照してください。

## <a name="other-event-hubs-features"></a>その他の Event Hubs 機能 

Apache Kafka 機能用の Event Hubs は、HTTP と AMQP を補完して Azure Event Hubs で同時に使用可能な 3 つのプロトコルのうちの 1 つです。 これらのプロトコルのいずれかで書き込んだものを、別のもので読み取ることができるので、現在の Apache Kafka プロデューサーを使用して Apache Kafka 経由で引き続き発行できますが、閲覧者には Azure Stream Analytics や Azure Functions などの Event Hubs の AMQP インターフェイスとのネイティブ統合によるメリットがあります。 逆に、Azure Event Hubs をターゲット エンドポイントとして AMQP ルーティング ネットワークに簡単に統合し、Apache Kafka 統合を通じてデータを読み取ることができます。  

さらに、[キャプチャ](event-hubs-capture-overview.md) (Azure Blob Storage と Azure Data Lake Storage により非常にコスト効率のよい長期的なアーカイブが可能です) や [geo ディザスター リカバリー](event-hubs-geo-dr.md)などの Event Hubs の機能も、Kafka 機能用の Event Hubs で動作します。

## <a name="apache-kafka-feature-differences"></a>Apache Kafka の機能の違い 

Apache Kafka 用 Event Hubs の目的は、Apache Kafka API に固定されていて、それなしでは Apache Kafka クラスターでバックアップされる必要があるアプリケーションに対して、Azure Event Hub の機能へのアクセスを提供することです。 

[上記](#is-apache-kafka-the-right-solution-for-your-workload)で説明したように、Azure メッセージング フリートにより、さまざまなメッセージング シナリオに対して豊富で堅牢な対応が提供されます。現在、以下の機能は Apache Kafka API に対する Event Hubs のサポートではサポートされていませんが、必要な機能がどこでどのように利用できるかを示します。

### <a name="transactions"></a>トランザクション

[Azure Service Bus](../service-bus-messaging/service-bus-transactions.md) には堅牢なトランザクション サポートがあり、メッセージ処理からの送信メッセージを、トランザクションの整合性保護の下で複数のターゲット エンティティに送信しながら、メッセージとセッションを受信して格納できます。 機能セットによって、シーケンス内の各メッセージを 1 回だけ処理できるだけでなく、Apache Kafka のように、別のコンシューマーが誤って同じメッセージを再処理するリスクも回避できます。 Service Bus は、トランザクション メッセージ ワークロードに対する推奨されるサービスです。

### <a name="compression"></a>圧縮

Apache Kafka のクライアント側[圧縮](https://cwiki.apache.org/confluence/display/KAFKA/Compression)機能を使用すると、複数のメッセージのバッチがプロデューサー側で 1 つのメッセージに圧縮され、コンシューマー側でバッチが圧縮解除されます。 Apache Kafka ブローカーにより、バッチが特別なメッセージとして扱われます。

この機能は、Azure Event Hubs のマルチプロトコル モデルと根本的に競合します。マルチプロトコル モデルによって、メッセージを、たとえそれがバッチで送信されたものであっても、ブローカーから、および任意のプロトコルを通して、個別に取得できます。 

Event Hubs イベントのペイロードはバイト ストリームであり、選択したアルゴリズムを使用してコンテンツを圧縮できます。 Apache Avro エンコード形式により、圧縮がネイティブでサポートされます。

### <a name="log-compaction"></a>ログの圧縮

Apache Kafka ログの圧縮は、各キーの最後のレコード以外のすべてをパーティションから除去できる機能です。これにより、実質的に Apache Kafka のトピックは、最後に追加された値によって前の値がオーバーライドされるキー値ストアになります。 現在、この機能は Azure Event Hubs では実装されていません。 キー値ストア パターンは、頻繁に更新される場合でも、[Azure Cosmos DB](../cosmos-db/introduction.md) のようなデータベース サービスによってはるかに適切にサポートされます。 詳細については、Event Hubs フェデレーション ガイダンスの[ログ プロジェクション](event-hubs-federation-overview.md#log-projections)に関するトピックをご覧ください。 

### <a name="kafka-streams"></a>Kafka Streams

Kafka Streams は、Apache Kafka オープンソース プロジェクトの一部であるストリーム分析用クライアント ライブラリですが、Apache Kafka のイベント ストリーム ブローカーとは別のものです。 

Azure Event Hubs のお客様が Kafka Streams のサポートを要求する最も一般的な理由は、Confluent の "ksqlDB" 製品に関心をお持ちであるためです。 "ksqlDB" は、"Confluent の製品やサービスと競合するサービスとしてのソフトウェア、サービスとしてのプラットフォーム、サービスとしてのインフラストラクチャ、またはその他の類似のオンライン サービスを提供する" ベンダーが "ksqlDB" のサポートを使用または提供することが許可されない[ようにライセンスされている](https://github.com/confluentinc/ksql/blob/master/LICENSE)固有の共有ソース プロジェクトです。 実際には、ksqlDB を使用する場合は、Kafka を自分で運用するか、Confluent のクラウド オファリングを使用する必要があります。 ライセンス条項は、ライセンスによって除外された目的でサービスを提供する Azure のお客様にも影響する可能性があります。

スタンドアロンまたは KsqlDB なしの Kafka Streams は、多くの代替フレームワークやサービスより機能が少なく、そのほとんどには、組み込みのストリーミング SQL インターフェイスが用意されており、現在はそのすべてが Azure Event Hubs に統合されています。

- [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)
- [Azure Synapse Analytics (Event Hubs キャプチャ経由)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)

一覧のサービスとフレームワークによって、通常、アダプターを介してさまざまなソースから直接イベント ストリームと参照データを取得できます。 Kafka Streams は Apache Kafka からしかデータを取得できないため、分析プロジェクトは Apache Kafka にロックされます。 他のソースのデータを使用するには、最初に Kafka Connect フレームワークを使用して Apache Kafka にデータをインポートする必要があります。

Azure で Kafka Streams フレームワークを使用する必要がある場合は、[HDInsight の Apache Kafka](../hdinsight/kafka/apache-kafka-introduction.md) でそのオプションが提供されます。 HDInsight の Apache Kafka により、Apache Kafka のすべての構成側面が完全に制御されます。一方、障害および更新ドメインの配置からネットワークの分離や統合の監視まで、Azure プラットフォームのさまざまな側面と完全に統合されています。 

## <a name="next-steps"></a>次のステップ
この記事では、Kafka 用 Event Hubs の概要について説明しました。 詳細については、[Azure Event Hubs 用 Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)を参照してください。