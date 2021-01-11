---
title: Apache Kafka 用 Azure Event Hubs への移行
description: この記事では、さまざまなプロトコル (AMQP、Apache Kafka、および HTTPS) を使用するコンシューマーとプロデューサーが、Azure Event Hubs の使用時にイベントを交換する方法を示しています。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cf585cdc0a43c595ec013ba78a2e3ae191c40b8d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090656"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Apache Kafka エコシステム用 Azure Event Hubs への移行
Azure Event Hubs は Apache Kafka エンドポイントを公開します。これにより、Kafka プロトコルを使用して Event Hubs に接続できます。 既存の Kafka アプリケーションに最小限の変更を行うことで、Azure Event Hubs に接続し、Azure エコシステムの利点を享受することができます。 Kafka 用 Event Hubs では、[Apache Kafka バージョン 1.0 以降](https://kafka.apache.org/10/documentation.html)がサポートされています。

## <a name="pre-migration"></a>移行前 

### <a name="create-an-azure-account"></a>Azure アカウントの作成
Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

### <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します
Event Hubs 名前空間とイベント ハブを作成するには、[イベント ハブの作成](event-hubs-create.md)に関する記事の手順に従ってください。 

### <a name="connection-string"></a>接続文字列
[ポータルから接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)方法に関する記事の手順に従います。 また、後で使用するために接続文字列を書き留めておきます。 

### <a name="fully-qualified-domain-name-fqdn"></a>完全修飾ドメイン名 (FQDN)
また、Event Hubs 名前空間を参照する FQDN が必要になる場合もあります。 FQDN は、次のように接続文字列内で確認できます。

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Event Hubs 名前空間がパブリック クラウド以外にデプロイされている場合は、ドメイン名が異なる場合があります (例: \*.servicebus.chinacloudapi.cn、\*.servicebus.usgovcloudapi.net、または\*.servicebus.cloudapi.de)。

## <a name="migration"></a>移行 

### <a name="update-your-kafka-client-configuration"></a>Kafka クライアント構成を更新する

Kafka 対応 Event Hubs に接続するには、Kafka クライアントの構成を更新する必要があります。 見つからない場合は、アプリケーションで `bootstrap.servers` が設定されている場所を検索してみてください。

アプリケーション内の任意の場所に、次の構成を挿入します。 `bootstrap.servers` と `sasl.jaas.config` の値を更新して、正しい認証を使用してクライアントを Event Hubs Kafka エンドポイントに転送するようにしてください。 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

`sasl.jaas.config` がフレームワークでサポートされる構成ではない場合、SASL のユーザー名とパスワードを設定に使用される構成を見つけ、代わりにそれらを使用します。 ユーザー名を `$ConnectionString` に、パスワードを Event Hubs の接続文字列に設定します。

## <a name="post-migration"></a>移行後
イベント ハブにイベントを送信する Kafka アプリケーションを実行します。 次に、Azure portal を使用してイベント ハブがイベントを受信することを確認します。 Event Hubs 名前空間の **[概要]** ページで、 **[メトリック]** セクションの **[メッセージ]** ビューに切り替えます。 ページを最新の情報に更新して、グラフを更新します。 メッセージが受信されたことが示されるまでに数秒かかることがあります。 

[![イベント ハブがメッセージを受信したことを確認する](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>次のステップ
Event Hubs と Kafka 用 Event Hubs の詳細については、次の記事を参照してください。  

- [Event Hubs 用 Apache Kafka トラブルシューティング ガイド](apache-kafka-troubleshooting-guide.md)
- [よく寄せられる質問 - Apache Kafka 用 Event Hubs](apache-kafka-frequently-asked-questions.md)
- [Azure Event Hubs のための Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)
- [推奨構成](apache-kafka-configurations.md)