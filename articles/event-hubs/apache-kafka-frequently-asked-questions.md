---
title: よく寄せられる質問 - Apache Kafka 用 Azure Event Hubs
description: この記事では、さまざまなプロトコル (AMQP、Apache Kafka、および HTTPS) を使用するコンシューマーとプロデューサーが、Azure Event Hubs の使用時にイベントを交換する方法を示しています。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8bdd86d9f299a69d5f2d05bb8ec526ed94780608
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031685"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>よく寄せられる質問 - Apache Kafka 用 Event Hubs 
この記事では、Apache Kafka 用 Event Hubs への移行についてよく寄せられる質問とその回答を示します。

## <a name="do-you-run-apache-kafka"></a>Apache Kafka を実行しますか?

いいえ。  Event Hubs のインフラストラクチャに対して Kafka API 操作を実行します。  Apache Kafka と Event Hubs AMQP の機能 (つまり、生成、受信、管理など) の間には密接な相関関係があるため、Event Hubs の既知の信頼性を Kafka PaaS 空間に導入することができます。

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>イベント ハブ コンシューマー グループとKafka コンシューマー グループ
Event Hubs 上のイベント ハブ コンシューマー グループと Kafka コンシューマー グループの違いは何ですか? Event Hubs 上の Kafka コンシューマー グループは、標準 Event Hubs コンシューマー グループと完全に区別されています。

**Event Hubs コンシューマー グループ**

- これらは、ポータル、SDK、または Azure Resource Manager テンプレートを介した作成、取得、更新、削除 (CRUD) 操作で管理されます。 Event Hubs コンシューマー グループは自動作成できません。
- これらはイベント ハブの子エンティティです。 つまり、同じ名前空間内のイベント ハブ間で同じコンシューマー グループ名を再利用できます。これらは個別のエンティティだからです。
- オフセットの格納には使用されません。 調整された AMQP の消費は、Azure Storage などの外部オフセット ストレージを使用して行われます。

**Kafka コンシューマー グループ**

- これらは自動作成されます。  Kafka グループは、Kafka コンシューマー グループ API を使用して管理できます。
- Event Hubs サービスにオフセットを格納できます。
- これらは、実質的にオフセット キー値ストアのキーとして使用されます。 `group.id` と `topic-partition` の一意のペアについて、Azure Storage にオフセットを格納します (3 倍のレプリケーション)。 Kafka オフセットを格納しても、Event Hubs ユーザーに追加の格納コストは発生しません。 オフセットは Kafka コンシューマー グループ API を介して操作できますが、Event Hub ユーザーがオフセット ストレージ "*アカウント*" を直接表示したり、操作したりすることはできません。  
- これらは名前空間にまたがります。 複数のアプリケーションに複数のトピックに関して同じ Kafka グループ名を使用すると、1 つのアプリケーションの再調整のみが必要な場合でも、すべてのアプリケーションとその Kafka クライアントが再調整されます。  グループ名はよく考えて選択してください。
- これは Event Hubs コンシューマー グループとはまったく異なります。 '$Default' を使用する必要は**ありません**。また、AMQP ワークロードに干渉する Kafka クライアントについて心配する必要もありません。
- これらは、Azure portal では表示できません。 コンシューマー グループ情報には、Kafka API を介してアクセスできます。

## <a name="next-steps"></a>次のステップ
Event Hubs と Kafka 用 Event Hubs の詳細については、次の記事を参照してください。  

- [Event Hubs 用 Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)
- [Event Hubs 用 Apache Kafka 移行ガイド](apache-kafka-migration-guide.md)
- [Event Hubs 用 Apache Kafka トラブルシューティング ガイド](apache-kafka-troubleshooting-guide.md)
- [推奨される構成](apache-kafka-configurations.md)

