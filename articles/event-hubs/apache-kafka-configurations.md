---
title: Apache Kafka クライアントの推奨される構成 - Azure Event Hubs
description: この記事では、Apache Kafka 用の Azure Event Hubs と対話するクライアントに推奨される Apache Kafka 構成について説明します。
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: be009aae41b2cb26ab02fdbe14bc4e18311ad235
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042353"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>Apache Kafka クライアントに推奨される構成
Apache Kafka クライアント アプリケーションから Azure Event Hubs を使用する場合に推奨される構成を以下に示します。 

## <a name="java-client-configuration-properties"></a>Java クライアント構成のプロパティ

### <a name="producer-and-consumer-configurations"></a>プロデューサーとコンシューマーの構成

プロパティ | 推奨値 | 許可される範囲 | Notes
---|---:|-----:|---
`metadata.max.age.ms` | 180000 (概算) | < 240000 | 低くすると、メタデータの変更をすばやく取得することができます。
`connections.max.idle.ms`   | 180000 | < 240000 | 240,000 ミリ秒を超えてアイドル状態の受信 TCP は Azure によって閉じられますその結果、配信不能の接続での送信が発生する可能性があります (送信タイムアウトのために期限切れのバッチとして表示されます)。

### <a name="producer-configurations-only"></a>プロデューサーの構成のみ
プロデューサーの構成については、[こちら](https://kafka.apache.org/documentation/#producerconfigs)を参照してください。

プロパティ | 推奨値 | 許可される範囲 | Notes
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | 1,046,528 バイトを超える要求が送信されると、サービスによって接続は閉じられます。  "*この値は変更する **必要** があり、高スループットの生成シナリオで問題が発生します。* "
`retries` | > 0 | | 場合によっては、delivery.timeout.ms の値を増やす必要があります。ドキュメントを参照してください。
`request.timeout.ms` | 30000 .. 60000 | > 20000| EH は、内部では既定値で最小 20,000 ミリ秒以上に設定されます。  "*タイムアウト値の低い要求は受け入れられますが、クライアントの動作は保証されません。* "
`metadata.max.idle.ms` | 180000 | > 5000 | プロデューサーでアイドル状態のトピックのメタデータがキャッシュされる期間を制御します。 トピックが最後に作成されてからの経過時間がメタデータのアイドル期間を超えた場合、トピックのメタデータは忘れられ、次のアクセスでメタデータ フェッチ要求が強制的に実行されます。
`linger.ms` | > 0 | | 高スループットのシナリオでは、バッチ処理を利用するために、待機値を許容可能な最大値と等しくする必要があります。
`delivery.timeout.ms` | | | 数式 (`request.timeout.ms` + `linger.ms`) * `retries` に従って設定します。
`compression.type` | `none` | | 現在、圧縮はサポートされていません。

### <a name="consumer-configurations-only"></a>コンシューマーの構成のみ
コンシューマーの構成については、[こちら](https://kafka.apache.org/documentation/#consumerconfigs)を参照してください。

プロパティ | 推奨値 | 許可される範囲 | Notes
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 既定値は 3000 であり、変更することはできません。
`session.timeout.ms` | 30000 |6000 .. 300000| 30000 から開始します。ハートビートを逃したために再調整が頻繁に発生する場合は増やします。


## <a name="librdkafka-configuration-properties"></a>librdkafka の構成プロパティ
メインの `librdkafka` 構成ファイル ([リンク](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)) には、以下のプロパティの詳細な説明が含まれています。

### <a name="producer-and-consumer-configurations"></a>プロデューサーとコンシューマーの構成

プロパティ | 推奨値 | 許可される範囲 | Notes
---|---:|-----:|---
`socket.keepalive.enable` | true | | 接続がアイドル状態になることが予想される場合に必要です。  240,000 ミリ秒を超えてアイドル状態の受信 TCP は Azure によって閉じられます。
`metadata.max.age.ms` | ~ 180000| < 240000 | 低くすると、メタデータの変更をすばやく取得することができます。

### <a name="producer-configurations-only"></a>プロデューサーの構成のみ

プロパティ | 推奨値 | 許可される範囲 | Notes
---|---:|-----:|---
`retries` | > 0 | | 既定値は 2 です。 この値をそのまま使用することをお勧めします。 
`request.timeout.ms` | 30000 .. 60000 | > 20000| EH は、内部では既定値で最小 20,000 ミリ秒以上に設定されます。  `librdkafka` の既定値は 5000 であり、問題が発生する可能性があります。 "*タイムアウト値の低い要求は受け入れられますが、クライアントの動作は保証されません。* "
`partitioner` | `consistent_random` | librdkafka のドキュメントを参照してください | `consistent_random` は既定値であり、最適です。  空のキーと null キーは、ほとんどの場合に最適に処理されます。
`compression.codec` | `none` || 圧縮は現在サポートされていません。

### <a name="consumer-configurations-only"></a>コンシューマーの構成のみ

プロパティ | 推奨値 | 許可される範囲 | Notes
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 既定値は 3000 であり、変更することはできません。
`session.timeout.ms` | 30000 |6000 .. 300000| 30000 から開始します。ハートビートを逃したために再調整が頻繁に発生する場合は増やします。


## <a name="further-notes"></a>その他の注意事項

構成に関連する一般的なエラー シナリオについては、次の表を確認してください。

現象 | 問題 | 解決策
----|---|-----
再調整によるオフセット コミットの失敗 | poll() の呼び出しの間にコンシューマーが待機する時間が長すぎて、サービスによってコンシューマーがグループから削除されます。 | いくつかのオプションがあります。 <ul><li>ポーリング処理のタイムアウトを増加する (`max.poll.interval.ms`)</li><li>メッセージ バッチのサイズを減らして処理を高速化する</li><li>処理の並列化を改善して、consumer.poll() をブロックしないようにする</li></ul> 3 つのいずれかの組み合わせを適用することをお勧めします。
高生成スループットでのネットワーク例外 | Java クライアント + 既定の max.request.size を使用していますか。  要求が大きすぎる可能性があります。 | 上記の Java 構成を参照してください。

## <a name="next-steps"></a>次のステップ
すべての Azure サービスのクォータと制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](..//azure-resource-manager/management/azure-subscription-service-limits.md)」を参照してください。 
