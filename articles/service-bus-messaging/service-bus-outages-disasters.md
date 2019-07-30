---
title: 障害と災害に対する Azure Service Bus アプリケーションの保護 | Microsoft Docs
description: 発生する可能性がある Service Bus の障害からアプリケーションを保護するために使用できる手法。
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 24fba1961c8fd95f1b9489716d690dd6eaa97b62
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274833"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Service Bus の障害および災害に対するアプリケーションの保護のベスト プラクティス

ミッション クリティカルなアプリケーションは、予期しない障害や災害が発生した場合でも継続して稼働する必要があります。 この記事では、発生する可能性がある Service Bus の障害や災害からアプリケーションを保護するために使用できる手法について説明します。

障害とは、Azure Service Bus が一時的に利用できなくなることです。 障害によって、メッセージング ストアなどの Service Bus の一部のコンポーネント、さらにはデータセンター全体に影響が及ぶ場合があります。 問題が解決されると、Service Bus は再び利用可能になります。 通常、障害によってメッセージなどのデータが失われることはありません。 コンポーネントのエラーの例としては、特定のメッセージング ストアが利用できなくなることが挙げられます。 データセンター全体の障害の例としては、データセンターの電源障害や、データセンターのネットワーク スイッチの障害などがあります。 障害は、数分間から数日間続く場合があります。

災害とは、Service Bus のスケール ユニットまたはデータセンターが永続的に失われることです。 データセンターは、再び利用可能になる場合も、ならない場合もあります。 通常、災害によってメッセージなどのデータの一部またはすべてが失われます。 災害の例としては、火災、洪水、地震があります。

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>障害および災害からの保護 - Service Bus Premium
Azure Service Bus Premium レベルには、高可用性とディザスター リカバリーという概念が組み込まれています。これらは、同じリージョン (Availability Zones 利用) にも異なるリージョン (geo ディザスター リカバリー利用) にも適用されます。

### <a name="geo-disaster-recovery"></a>geo ディザスター リカバリー

Service Bus Premium では、名前空間のレベルで geo ディザスター リカバリーがサポートされています。 詳細については、「[Azure Service Bus の geo ディザスター リカバリー](service-bus-geo-dr.md)」を参照してください。 [Premium SKU](service-bus-premium-messaging.md) でのみ利用できるディザスター リカバリー機能は、メタデータの災害復旧を実装しており、一次および二次障害復旧の名前空間に依存しています。

### <a name="availability-zones"></a>可用性ゾーン

Service Bus Premium SKU では、同じ Azure リージョン内に障害から分離された場所を提供する [Availability Zones](../availability-zones/az-overview.md) がサポートされています。

> [!NOTE]
> Azure Service Bus Premium に対する Availability Zones のサポートは、可用性ゾーンが利用可能な [Azure リージョン](../availability-zones/az-overview.md#services-support-by-region)でのみ利用できます。でのみ利用できます。

Azure Portal を使用して、新しい名前空間でのみ Availability Zones を有効にすることができます。 Service Bus では、既存の名前空間の移行はサポートされていません。 名前空間でゾーン冗長を有効にした後に、無効にすることはできません。

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>障害および災害からの保護 - Service Bus Standard
Standard メッセージング価格レベルが使用されているときに、データセンターの障害からの復旧を実現するために、Service Bus では、*アクティブ レプリケーション*と*パッシブ レプリケーション*という 2 つの方法がサポートされています。 どちらの方法でも、データセンターの障害時に特定のキューまたはトピックにアクセスできる状態を維持する必要がある場合は、両方の名前空間にそのキューまたはトピックを作成します。 両方のエンティティに同じ名前を付けることができます。 たとえば、プライマリ キューは **contosoPrimary.servicebus.windows.net/myQueue** を使用してアクセスでき、セカンダリ側は **contosoSecondary.servicebus.windows.net/myQueue** を使用してアクセスできます。

>[!NOTE]
> **アクティブ レプリケーション**と**パッシブ レプリケーション**のセットアップは汎用ソリューションであり、Service Bus の固有の機能ではありません。 送信側アプリケーションにはレプリケーション ロジック (2 つの異なる名前空間への送信) が存在し、受信側には、重複を検出するためのカスタム ロジックが存在する必要があります。

アプリケーションが、送信側から受信側への永続的な通信を必要としない場合、アプリケーションで永続的なクライアント側キューを実装することで、メッセージの消失を防止し、送信側を一時的な Service Bus エラーから保護できます。

### <a name="active-replication"></a>アクティブ レプリケーション
アクティブ レプリケーションでは、すべての操作で両方の名前空間のエンティティを使用します。 メッセージを送信するクライアントはすべて、同じメッセージのコピーを 2 つ送信します。 1 つ目のコピーがプライマリ エンティティ (たとえば **contosoPrimary.servicebus.windows.net/sales**) に送信され、そのメッセージの 2 つ目のコピーはセカンダリ エンティティ (たとえば **contosoSecondary.servicebus.windows.net/sales**) に送信されます。

クライアントは両方のキューからメッセージを受信します。 受信側はメッセージの 1 つ目のコピーを処理し、2 つ目のコピーは抑制されます。 重複したメッセージを抑制するには、送信側が各メッセージに一意の識別子のタグを付ける必要があります。 メッセージのコピーには、両方とも同じ識別子のタグを付ける必要があります。 [BrokeredMessage.MessageId][BrokeredMessage.MessageId] プロパティ、[BrokeredMessage.Label][BrokeredMessage.Label] プロパティ、またはカスタム プロパティを使用して、メッセージにタグを付けることができます。 受信側は、既に受信したメッセージの一覧を保持する必要があります。

[Service Bus Standard レベルの geo レプリケーション][Geo-replication with Service Bus Standard Tier]のサンプルで、メッセージング エンティティのアクティブ レプリケーションが説明されています。

> [!NOTE]
> アクティブ レプリケーションの手法では操作の数が 2 倍になるので、この手法はコストの増加につながる可能性があります。
> 
> 

### <a name="passive-replication"></a>パッシブ レプリケーション
障害のない場合は、パッシブ レプリケーションは 2 つのメッセージング エンティティのうち 1 つのみを使用します。 クライアントは、アクティブなエンティティにメッセージを送信します。 アクティブなエンティティでの操作が失敗し、アクティブなエンティティをホストしているデータセンターが使用できない可能性があることを示すエラー コードが表示された場合、クライアントはメッセージのコピーをバックアップ エンティティに送信します。 この時点で、アクティブなエンティティとバックアップ エンティティの役割が切り替わります。送信側クライアントは、前のアクティブなエンティティを新しいバックアップ エンティティと見なし、前のバックアップ エンティティを新しいアクティブなエンティティと見なします。 両方の送信操作が失敗した場合、2 つのエンティティの役割は変更されず、エラーが返されます。

クライアントは両方のキューからメッセージを受信します。 受信側は同じメッセージのコピーを 2 つ受信する可能性があるので、受信側では重複したメッセージを抑制する必要があります。 アクティブ レプリケーションの説明と同じ方法で重複を抑制することができます。

一般的に、パッシブ レプリケーションではほとんどの場合に操作が 1 つだけ実行されるので、アクティブ レプリケーションよりも経済的です。 遅延、スループット、および金銭的コストは、レプリケーションなしのシナリオと同じです。

パッシブ レプリケーションを使用する場合、次のシナリオではメッセージを喪失したり 2 回受信したりする可能性があります。

* **メッセージの遅延または喪失**:送信側からメッセージ m1 がプライマリ キューに正常に送信されてから、受信側で m1 を受信する前にプライマリ キューが使用不可になったとします。 送信側が後続のメッセージ m2 をセカンダリ キューに送信します。 プライマリ キューが一時的に使用できない場合、受信側はキューが再び使用可能になった後で m1 を受信します。 災害の場合、受信側は m1 をまったく受信できない可能性があります。
* **重複した受信**:送信側がメッセージ m をプライマリ キューに送信するとします。 Service Bus により m が適切に処理されますが、応答の送信に失敗します。 送信操作がタイムアウトになった後で、送信側が m の同一のコピーをセカンダリ キューに送信します。 プライマリ キューが使用不可になる前に受信側が m の 1 つ目のコピーを受信できる場合、受信側は m の両方のコピーをほぼ同時に受信します。 プライマリ キューが使用不可になる前に受信側が m の 1 つ目のコピーを受信できない場合、受信側は m の 2 つ目のコピーのみを最初に受信しますが、プライマリ キューが使用可能になったときに m の 1 つ目のコピーを受信します。

[Service Bus Standard レベルの geo レプリケーション][Geo-replication with Service Bus Standard Tier]のサンプルで、メッセージング エンティティのパッシブ レプリケーションが説明されています。

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>データセンターの障害や災害からリレー エンドポイントを保護する
[Azure Relay](../service-bus-relay/relay-what-is-it.md) エンドポイントの geo レプリケーションにより、Service Bus 障害発生時にリレー エンドポイントを公開するサービスにアクセスできるようになります。 geo レプリケーションを実現するには、異なる名前空間内に 2 つのリレー エンドポイントを作成する必要があります。 これらの名前空間が異なるデータセンターに存在し、2 つのエンドポイントに異なる名前が付けられている必要があります。 たとえば、プライマリ エンドポイントは **contosoPrimary.servicebus.windows.net/myPrimaryService** を使用してアクセスでき、セカンダリ側は **contosoSecondary.servicebus.windows.net/mySecondaryService** を使用してアクセスできます。

その後、サービスは、両方のエンドポイント上でリッスンし、クライアントはどちらかのエンドポイントを介してサービスを呼び出すことができます。 クライアント アプリケーションは、いずれかのリレー エンドポイントをプライマリ エンドポイントとしてランダムに選択して、アクティブなエンドポイントに要求を送信します。 操作が失敗してエラー コードが表示された場合、このエラーは、リレー エンドポイントを使用できないことを示します。 アプリケーションは、バックアップ エンドポイントへのチャネルを開き、要求を再び発行します。 この時点で、アクティブなエンドポイントとバックアップ エンドポイントの役割が切り替わります。クライアント アプリケーションは、前のアクティブなエンドポイントを新しいバックアップ エンドポイントと見なし、前のバックアップ エンドポイントを新しいアクティブなエンドポイントと見なします。 両方の送信操作が失敗した場合、2 つのエンティティの役割は変更されず、エラーが返されます。

## <a name="next-steps"></a>次の手順
ディザスター リカバリーの詳細については、次の記事を参照してください。

* [Azure Service Bus の geo ディザスター リカバリー](service-bus-geo-dr.md)
* [Azure SQL Database の継続性][Azure SQL Database Business Continuity]
* [回復性に優れた Azure 用アプリケーションの設計][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
