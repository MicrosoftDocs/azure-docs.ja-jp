---
title: Apache Kafka 用 Azure Event Hubs に関する問題をトラブルシューティングする
description: この記事では、Apache Kafka 用 Azure Event Hubs に関する問題をトラブルシューティングする方法を示します
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e32e02947b9f004755381d562fd3f3c897b70674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90061429"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Event Hubs 用 Apache Kafka トラブルシューティング ガイド
この記事では、Apache Kafka 用 Event Hubs を使用するときに発生する可能性がある問題のトラブルシューティングのヒントを示します。 

## <a name="server-busy-exception"></a>サーバー ビジー例外
Kafka の調整により、サーバー ビジー例外が発生する可能性があります。 AMQP クライアントでは、サービスの調整時に Event Hubs から直ちに **サーバー ビジー** 例外が返されます。 これは、"後でもう一度お試しください" というメッセージと同じです。 Kafka では、完了前にメッセージが遅延します。 遅延の長さは、生成およびフェッチ応答の `throttle_time_ms` として、ミリ秒単位で返されます。 ほとんどの場合、これらの遅延要求は、Event Hubs ダッシュボードでサーバー ビジー例外としてログに記録されません。 代わりに、スループットがプロビジョニングされたクォータを超えたことを示すインジケーターとして、応答の `throttle_time_ms` 値を使用する必要があります。

トラフィックが過剰な場合、サービスの動作は次のようになります。

- 生成要求の遅延が要求タイムアウトを超えた場合、Event Hubs から **ポリシー違反** エラー コードが返されます。
- フェッチ要求の遅延が要求タイムアウトを超えた場合、Event Hubs では、要求を調整済みとしてログに記録し、エラー コードのない空のレコード セットで応答します。

[専用クラスター](event-hubs-dedicated-overview.md)には調整メカニズムがありません。 すべてのクラスター リソースを自由に使用できます。

## <a name="no-records-received"></a>レコードを受信していない
コンシューマーがレコードを取得しておらず、継続的に再調整している場合があります。 このシナリオでは、コンシューマーはレコードを取得しておらず、継続的に再調整しています。 この発生時に例外やエラーは生じませんが、Kafka ログには、グループへの再参加とパーティションの割り当てを試行しているときにコンシューマーがスタックしていることが示されます。 次のいくつかの原因が考えられます。

- `request.timeout.ms` が推奨値の 60000 以上であることと、`session.timeout.ms` が推奨値の 30000 以上であることを確認します。 これらの設定が低すぎると、コンシューマーがタイムアウトとなり、再調整が発生する可能性があります (これにより、より多くのタイムアウトが発生したり、より多くの再調整が発生したりします) 
- 構成がこれらの推奨値と一致していても、継続的に再調整されている場合は、問題を開いてもかまいません (デバッグに役立つように、問題に構成全体を含めるようにしてください)。

## <a name="compressionmessage-format-version-issue"></a>圧縮とメッセージ形式バージョンの問題
Kafka では圧縮がサポートされており、現在、Kafka 用 Event Hubs ではサポートされていません。 クライアントで圧縮された Kafka メッセージをブローカーに送信しようとすると、メッセージ形式バージョンを示すエラー (`The message format version on the broker does not support the request.` など) が発生します。

圧縮データが必要な場合は、データをブローカーに送信する前に圧縮し、受信後に圧縮を解除することが有効な回避策です。 メッセージ本文はサービスの単なるバイト配列であるため、クライアント側の圧縮とその解除によって問題が発生することはありません。

## <a name="unknownserverexception"></a>UnknownServerException
Kafka クライアント ライブラリから、次の例のような UnknownServerException を受け取ることがあります。 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Microsoft サポートのチケットを開きます。  デバッグレベルのログ記録と例外のタイムスタンプ (UTC) は、問題のデバッグに役立ちます。 

## <a name="other-issues"></a>その他の問題
Event Hubs で Kafka を使用するときに問題が発生する場合は、次の項目を確認してください。

- **ファイアウォールによるトラフィックのブロック** - ポート **9093** がファイアウォールによってブロックされていないことを確認してください。
- **TopicAuthorizationException** - この例外の最も一般的な原因は次のとおりです。
    - 構成ファイル内の接続文字列の入力ミス。または、
    - Basic レベルの名前空間で Kafka 用 Event Hubs を使用しようとしている。 Kafka 用 Event Hubs 機能は、[Standard および Dedicated レベルの名前空間でのみサポート](https://azure.microsoft.com/pricing/details/event-hubs/)されます。
- **Kafka バージョンの不一致** - Kafka エコシステム用 Event Hubs では、Apache Kafka バージョン 1.0 以降がサポートされます。 Kafka バージョン 0.10 以降を使用する一部のアプリケーションは、Kafka プロトコルの下位互換性のために動作することがありますが、古い API バージョンを使用しないことを強くお勧めします。 Kafka バージョン 0.9 以前では、必要な SASL プロトコルがサポートされず、Event Hubs に接続できません。
- **Kafka で使用するときの AMQP ヘッダーでの異常なエンコーディング** - AMQP 経由でイベント ハブにイベントを送信するときに、AMQP ペイロード ヘッダーが AMQP エンコードでシリアル化されます。 Kafka コンシューマーでは、AMQP からヘッダーを逆シリアル化しません。 ヘッダー値を読み取るには、AMQP ヘッダーを手動でデコードします。 また、Kafka プロトコル経由で使用することがわかっている場合は、AMQP ヘッダーを使用しないようにすることもできます。 詳細については、[こちらの GitHub の問題](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)のページを参照してください。
- **SASL 認証** - Event Hubs で必要な SASL 認証プロトコルと連携するフレームワークを取得することは、見かけ以上に難しい場合があります。 SASL 認証でフレームワークのリソースを使用して、構成のトラブルシューティングを行うことができるかどうかを確認してください。 

## <a name="limits"></a>制限
Apache Kafka と Event Hubs Kafka。 ほとんどの場合、Azure Event Hubs の Kafka インターフェイスには、Apache Kafka の場合と同じ既定値、プロパティ、エラー コード、一般的な動作があります。 これら 2 つが明示的に異なる (Kafka では課されない制限が Event Hubs で課される) インスタンスは、次のとおりです。

- `group.id` プロパティの最大長が 256 文字である
- `offset.metadata.max.bytes` の最大サイズが 1024 バイトである
- オフセット コミットは、最大内部ログ サイズが 1 MB のパーティションごとに、1 秒あたり 4 回の呼び出しに調整される


## <a name="next-steps"></a>次のステップ
Event Hubs と Kafka 用 Event Hubs の詳細については、次の記事を参照してください。  

- [Event Hubs 用 Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)
- [Event Hubs 用 Apache Kafka 移行ガイド](apache-kafka-migration-guide.md)
- [よく寄せられる質問 - Apache Kafka 用 Event Hubs](apache-kafka-frequently-asked-questions.md)
- [推奨される構成](apache-kafka-configurations.md)
