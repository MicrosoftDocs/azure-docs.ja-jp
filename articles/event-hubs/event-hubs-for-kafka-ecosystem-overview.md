---
title: Apache Kafka アプリからイベント ハブを使用する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs での Apache Kafka のサポートに関する情報を提供します。
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ab29c9c4270514e95752ab2bbd085ffe1b0a2fb0
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534874"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Apache Kafka アプリケーションから Azure Event Hubs を使用する
Event Hubs は、独自の Kafka クラスターを実行する代わりに、既存の Kafka ベースのアプリケーションが使用できる Kafka エンドポイントを提供します。 Event Hubs は、[Apache Kafka 1.0 以降](https://kafka.apache.org/documentation/)をサポートしており、MirrorMaker を含む既存の Kafka アプリケーションに対応します。  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Kafka 用 Event Hubs で提供されるもの

Kafka 機能用 Event Hubs は、Azure Event Hubs 上で、Kafka トピックに対する読み取りと書き込みの両方について、Kafka バージョン 1.0 以降とバイナリ互換性のあるプロトコル ヘッドを提供します。 コードを変更する必要はなく、構成の最小限の変更だけで、アプリケーションから Kafka エンドポイントを使い始めることができます。 Kafka クラスターを指すのではなく、イベント ハブによって公開されている Kafka エンドポイントを指すように、構成の接続文字列を更新します。 その後、Kafka プロトコルを使用するアプリケーションから Event Hubs へのイベントのストリーミングを開始できます。 この統合により、現在プレビュー段階の [Kafka 接続](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect)ようなフレームワークもサポートされます。 

Kafka と Event Hubs は、概念的にはほぼ同じものです。これらはどちらも、ストリーミング データ用に構築されたパーティション分割型のログです。 次の表に、Kafka の概念と Event Hubs の概念のマッピングを示します。

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka と Event Hubs の概念のマッピング

| Kafka での概念 | Event Hubs での概念|
| --- | --- |
| クラスター | 名前空間 |
| トピック | イベント ハブ |
| Partition | Partition|
| コンシューマー グループ | コンシューマー グループ |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kafka と Event Hubs の主な違い

[Apache Kafka](https://kafka.apache.org/) は、選択した任意の場所で実行できるソフトウェアであるのに対して、Event Hubs は、Azure Blob Storage に似たクラウド サービスです。 管理するサーバーやネットワークはなく、構成するブローカーがありません。 ユーザーは名前空間を作成します。それが、トピックが存在する FQDN であり、次に、その名前空間内に Event Hubs またはトピックを作成します。 Event Hubs と名前空間について詳しくは、「[Event Hubs の機能](event-hubs-features.md#namespace)」をご覧ください。 クラウド サービスとしての Event Hubs は、単一の安定した仮想 IP アドレスをエンドポイントとして使用するため、クライアントは、ブローカーやクラスター内のマシンについて知っておく必要はありません。 

Event Hubs のスケールは、購入するスループット単位の数によって制御されます。各スループット単位では、1 秒あたり 1 MB、または受信の 1 秒あたり 1,000 イベントを利用できます。 既定では、Event Hubs は上限に達すると[自動インフレ](event-hubs-auto-inflate.md)機能によってスループット ユニットをスケールアップします。この機能は Kafka 機能用 Event Hubs でも働きます。 

### <a name="security-and-authentication"></a>セキュリティと認証
Kafka 用 Event Hubs からイベントを発行または使用するたびに、クライアントでは、Event Hubs リソースへのアクセスが試行されます。 認可されたエンティティを使用してリソースがアクセスされていることを確認する必要があります。 クライアントで Apache Kafka プロトコルを使用している場合、SASL のメカニズムを使用して認証と暗号化のための構成を設定できます。 Kafka 用 Event Hubs を使用するには、TLS 暗号化が必要となります (Event Hubs では転送中のデータがすべて TLS で暗号化されるため)。 これは、構成ファイルで SASL_SSL オプションを指定することによって実現できます。 

Azure Event Hubs には、セキュリティで保護されたリソースへのアクセスを承認するためのオプションが複数用意されています。 

- OAuth
- Shared Access Signature (SAS)

#### <a name="oauth"></a>OAuth
Event Hubs は、Azure Active Directory (Azure AD) と連携するため、Azure AD の **OAuth** 2.0 に準拠した一元的な承認サーバーを利用できます。 Azure AD では、ロールベースのアクセス制御 (RBAC) を使用して、粒度の細かいアクセス許可をクライアントの ID に与えることができます。 protocol に **SASL_SSL** を、mechanism に **OAUTHBEARER** を指定すれば、この機能を Kafka クライアントで使用することができます。 Azure ロールとレベルを使用したアクセスのスコープ設定について詳しくは、[Azure AD によるアクセスの承認](authorize-access-azure-active-directory.md)に関するページを参照してください。

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Shared Access Signature (SAS)
Event Hubs には、**Shared Access Signature (SAS)** も用意されており、Kafka 用 Event Hubs リソースへの委任アクセスを実現することができます。 OAuth 2.0 トークンベースのメカニズムを使用したアクセス承認の方が、SAS よりもセキュリティが高く、使いやすさの点でも有利です。 また、ACL ベースの承認はユーザーが維持、管理する必要がありますが、組み込みロールであれば ACL ベースの承認は必要ありません。 protocol に **SASL_SSL** を、mechanism に **PLAIN** を指定すれば、この機能を Kafka クライアントで使用することができます。 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!NOTE]
> Kafka クライアントで SAS 認証を使用する場合、SAS キーの再生成時に確立された接続は切断されません。 

#### <a name="samples"></a>サンプル 
SAS または OAuth を使用してイベント ハブを作成しアクセスする手順の**チュートリアル**については、「[クイックスタート: Kafka プロトコルを使用した Event Hubs によるデータ ストリーミング](event-hubs-quickstart-kafka-enabled-event-hubs.md)」を参照してください。

Kafka 用 Event Hubs で OAuth を使用する方法を紹介したその他の**例**については、[GitHub 上のサンプル](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)を参照してください。

## <a name="other-event-hubs-features-available-for-kafka"></a>Kafka で使用できるその他の Event Hubs 機能

Kafka 機能用 Event Hubs では、あるプロトコルで書き込みを、別のプロトコルで読み取りを行えます。そのため、現在の Kafka プロデューサーは Kafka による発行を続けることができます。また、Event Hub で Azure Stream Analytics や Azure Functions などのリーダーを追加できます。 さらに、[Capture](event-hubs-capture-overview.md) や [Geo ディザスター リカバリー](event-hubs-geo-dr.md)などの Event Hubs 機能も、Kafka 機能用 Event Hubs で動作します。

## <a name="features-that-are-not-yet-supported"></a>まだサポートされていない機能 

まだサポートされていない Kafka の機能の一覧を次に示します。

*   トランザクション
*   圧縮
*   サイズ ベースのリテンション期間
*   ログの圧縮
*   HTTP Kafka API のサポート
*   Kafka Streams

## <a name="next-steps"></a>次のステップ
この記事では、Kafka 用 Event Hubs の概要について説明しました。 詳細については、[Azure Event Hubs 用 Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)を参照してください。


