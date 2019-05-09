---
title: Azure Service Bus の Premium および Standard メッセージング価格レベルの概要 | Microsoft Docs
description: Service Bus の Premium および Standard メッセージング レベル
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: 9e9c8918556b7ff003bcfed062ea1e15233b2845
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761958"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus の Premium および Standard メッセージング レベル

Service Bus メッセージングには、キューやトピックなどのエンティティが含まれており、エンタープライズ メッセージング機能と、クラウド スケールの豊富な発行/サブスクライブ セマンティクスが結合されます。 Service Bus メッセージングは、多くの高度なクラウド ソリューションで、通信のバックボーンとして使用されます。

Service Bus メッセージングに *Premium* レベルを導入して、ミッション クリティカルなアプリケーションのスケール、パフォーマンス、および可用性に関する顧客の一般的な要求に対処しています。 運用環境シナリオには、Premium レベルをお勧めします。 機能セットとほぼ同じですが、Service Bus メッセージングのこれら 2 つのレベルは、さまざまなユース ケースに応えるように設計されています。

次の表に、大まかな違いをいくつか示します。

| Premium | Standard |
| --- | --- |
| 高スループット |変わりやすいスループット |
| 予測可能なパフォーマンス |変わりやすい待機時間 |
| 固定価格 |従量課金制の変わりやすい料金 |
| ワークロードをスケールアップおよびスケールダウンする機能 |該当なし |
| 最大 1 MB のメッセージ サイズ |最大 256 KB のメッセージ サイズ |

**Service Bus Premium メッセージング**では、各顧客のワークロードが分離した状態で実行されるように、CPU とメモリのレベルでリソースが分離されます。 このリソースのコンテナーを、*メッセージング ユニット*と呼びます。 各 Premium 名前空間には、1 つ以上のメッセージング ユニットが割り当てられます。 Service Bus の Premium 名前空間ごとに、1 個、2 個、または 4 個のメッセージング ユニットを購入できます。 1 つのワークロードまたはエンティティは、複数のメッセージング ユニットにまたがることができます。課金は 24 時間単位、すなわち日単位ですが、メッセージング ユニットの数は自由に変更できます。 その結果、Service Bus ベースのソリューションのパフォーマンスは、予測可能で反復可能になります。

このパフォーマンスは、より予測可能かつ利用可能なだけでなく、より高速です。 Service Bus Premium メッセージングは、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) に導入されたストレージ エンジンが基盤になっています。 Premium メッセージングでのピークのパフォーマンスは、Standard レベルよりもはるかに高速です。

## <a name="premium-messaging-technical-differences"></a>Premium メッセージングの技術的な相違点

次のセクションでは、Premium メッセージング レベルと Standard メッセージング レベルのいくつかの違いについて説明します。

### <a name="partitioned-queues-and-topics"></a>パーティション分割されたキューとトピック

Premium メッセージングでは、パーティション分割されたキューとトピックをサポートしていません。 パーティション分割の詳細については、「[パーティション分割されたキューとトピック](service-bus-partitioning.md)」を参照してください。

### <a name="express-entities"></a>エクスプレス エンティティ

完全に分離されたランタイム環境で Premium メッセージングが実行されるため、Premium 名前空間ではエクスプレス エンティティがサポートされません。 エクスプレス機能の詳細については、[QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) プロパティを参照してください。

Standard メッセージングで実行しているコードがあり、それを Premium レベルに移植したい場合は、[EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) プロパティが **false** (既定値) に設定されていることを確認します。

## <a name="premium-messaging-resource-usage"></a>Premium メッセージング リソースの使用
一般に、エンティティに対するどの操作でも、CPU とメモリが使用されます。 これらいくつかの操作を次に示します。 

- キュー、トピック、サブスクリプションに対する CRUD (Create、Retrieve、Update、Delete (作成、取得、更新、削除)) などの管理操作。
- ランタイム操作 (メッセージの送受信)
- 操作とアラートの監視

ただし追加の CPU とメモリ使用によりさらに課金されることはありません。 Premium メッセージング レベルでは、メッセージング ユニットの料金は単一です。

CPU とメモリの使用は追跡され、次の理由で表示されます。 

- システム内部に透明性を提供する。
- 購入したリソースの容量を把握する。
- スケール アップ/スケール ダウンを判断するのに役立つ容量計画。

## <a name="get-started-with-premium-messaging"></a>Premium メッセージングを使ってみる

Premium メッセージングは簡単に使い始めることができ、そのプロセスは Standard メッセージングと似ています。 まず、[Azure Portal](https://portal.azure.com) で[名前空間を作成](service-bus-create-namespace-portal.md)します。 **[価格レベル]** で **[Premium]** を選択します。 **[価格の詳細を表示]** をクリックして、各レベルについての詳細を表示します。

![create-premium-namespace][create-premium-namespace]

[Azure Resource Manager テンプレートを使用して Premium 名前空間](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/)を作成することもできます。

## <a name="next-steps"></a>次の手順

Service Bus メッセージングの詳細については、次のリンクをご覧ください。

* [Azure Service Bus Premium メッセージングの概要 (ブログの投稿)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure Service Bus Premium メッセージングの概要 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
