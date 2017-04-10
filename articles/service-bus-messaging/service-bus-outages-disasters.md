---
title: "障害と災害に対する Azure Service Bus アプリケーションの保護 | Microsoft Docs"
description: "発生する可能性がある Service Bus の障害からアプリケーションを保護するために使用できる手法について説明します。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: tysonn
ms.assetid: fd9fa8ab-f4c4-43f7-974f-c876df1614d4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 994a379129bffd7457912bc349f240a970aed253
ms.openlocfilehash: 6424d4dabe20514c0e41c6d755d644494afea302
ms.lasthandoff: 01/12/2017


---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Service Bus の障害および災害に対するアプリケーションの保護のベスト プラクティス
ミッション クリティカルなアプリケーションは、予期しない障害や災害が発生した場合でも継続して稼働する必要があります。 このトピックでは、発生する可能性がある Service Bus の障害や災害からアプリケーションを保護するために使用できる手法について説明します。

障害とは、Azure Service Bus が一時的に利用できなくなることです。 障害によって、メッセージング ストアなどの Service Bus の一部のコンポーネント、さらにはデータセンター全体に影響が及ぶ場合があります。 問題が解決されると、Service Bus は再び利用可能になります。 通常、障害によってメッセージなどのデータが失われることはありません。 コンポーネントのエラーの例としては、特定のメッセージング ストアが利用できなくなることが挙げられます。 データセンター全体の障害の例としては、データセンターの電源障害や、データセンターのネットワーク スイッチの障害などがあります。 障害は、数分間から数日間続く場合があります。

災害とは、Service Bus のスケール ユニットまたはデータセンターが永続的に失われることです。 データセンターは、再び利用可能になる場合も、ならない場合もあります。 通常、災害によってメッセージなどのデータの一部またはすべてが失われます。 災害の例としては、火災、洪水、地震があります。

## <a name="current-architecture"></a>現在のアーキテクチャ
Service Bus では、複数のメッセージング ストアを使用して、キューまたはトピックに送信されたメッセージを格納します。 パーティション分割されていないキューまたはトピックは&1; つのメッセージング ストアに割り当てられます。 このメッセージング ストアを使用できない場合、そのキューまたはトピックに対するすべての操作が失敗します。

Service Bus のメッセージング エンティティ (キュー、トピック、リレー) はすべて、データセンターと連携する&1; つのサービスの名前空間に存在します。 Service Bus では、データの自動 geo レプリケーションを有効にすることや、名前空間が複数のデータセンターにまたがることを許可していません。

## <a name="protecting-against-acs-outages"></a>ACS の障害に対する保護
ACS の資格情報を使用している場合に ACS を使用できなくなると、クライアントはトークンを取得できなくなります。 ACS が停止した時点でトークンを取得済みのクライアントは、トークンの期限が切れるまで引き続き Service Bus を使用できます。 トークンの既定の有効期間は 3 時間です。

ACS の障害から保護するには、Shared Access Signature (SAS) トークンを使用します。 この場合、クライアントはシークレット キー付きの自主生成したトークンに署名することで、Service Bus に対して直接認証します。 ACS の呼び出しは必要ありません。 SAS トークンについて詳しくは、「[Service Bus 認証][Service Bus authentication]」をご覧ください。

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>メッセージング ストアのエラーからキューおよびトピックを保護する
パーティション分割されていないキューまたはトピックは&1; つのメッセージング ストアに割り当てられます。 このメッセージング ストアを使用できない場合、そのキューまたはトピックに対するすべての操作が失敗します。 一方、パーティション分割されたキューは複数のフラグメントで構成されます。 各フラグメントは異なるメッセージング ストアに格納されます。 パーティション分割されたキューまたはトピックにメッセージが送信されると、Service Bus はそのメッセージをいずれかのフラグメントに割り当てます。 対応するメッセージング ストアを使用できない場合は、可能であれば Service Bus はメッセージを別のフラグメントに書き込みます。 パーティション分割されたエンティティの詳細については、[パーティション分割されたメッセージング エンティティ][Partitioned messaging entities]に関する記事をご覧ください。

## <a name="protecting-against-datacenter-outages-or-disasters"></a>データセンターの障害や災害から保護する
2 つのデータセンター間でのフェールオーバーを可能にするために、各データセンターに&1; つの Service Bus サービスの名前空間を作成できます。 たとえば、Service Bus サービスの名前空間 **contosoPrimary.servicebus.windows.net** を米国 (北/中央) リージョンに配置し、**contosoSecondary.servicebus.windows.net** を米国 (南部/中央) リージョンに配置することができます。 データセンターの障害発生時にも Service Bus メッセージング エンティティにアクセスできる状態を維持する必要がある場合、両方の名前空間内にそのエンティティを作成します。

詳しくは、「[非同期メッセージング パターンと高可用性][Asynchronous messaging patterns and high availability]」の「Azure データセンター内での Service Bus の障害」のセクションをご覧ください。

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>データセンターの障害や災害からリレー エンドポイントを保護する
リレー エンドポイントの geo レプリケーションにより、Service Bus 障害発生時にリレー エンドポイントを公開するサービスにアクセスできるようになります。 geo レプリケーションを実現するには、異なる名前空間内に&2; つのリレー エンドポイントを作成する必要があります。 これらの名前空間が異なるデータセンターに存在し、2 つのエンドポイントに異なる名前が付けられている必要があります。 たとえば、プライマリ エンドポイントは **contosoPrimary.servicebus.windows.net/myPrimaryService** を使用してアクセスでき、セカンダリ側は **contosoSecondary.servicebus.windows.net/mySecondaryService** を使用してアクセスできます。

その後、サービスは、両方のエンドポイント上でリッスンし、クライアントはどちらかのエンドポイントを介してサービスを呼び出すことができます。 クライアント アプリケーションは、いずれかのリレー エンドポイントをプライマリ エンドポイントとしてランダムに選択して、アクティブなエンドポイントに要求を送信します。 操作が失敗してエラー コードが表示された場合、このエラーは、リレー エンドポイントを使用できないことを示します。 アプリケーションは、バックアップ エンドポイントへのチャネルを開き、要求を再び発行します。 この時点で、アクティブなエンドポイントとバックアップ エンドポイントの役割が切り替わります。クライアント アプリケーションは、前のアクティブなエンドポイントを新しいバックアップ エンドポイントと見なし、前のバックアップ エンドポイントを新しいアクティブなエンドポイントと見なします。 両方の送信操作が失敗した場合、2 つのエンティティの役割は変更されず、エラーが返されます。

[Service Bus リレー メッセージを使用した geo レプリケーション][Geo-replication with Service Bus relayed Messages] のサンプルでは、リレーをレプリケートする方法を説明しています。

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>データセンターの障害や災害からキューおよびトピックを保護する
ブローカー メッセージングを使用する際、データセンターの障害から復旧できるように、Service Bus では、*アクティブ レプリケーション*と*パッシブ レプリケーション*の&2; つの方法がサポートされています。 どちらの方法でも、データセンターの障害時に特定のキューまたはトピックにアクセスできる状態を維持する必要がある場合は、両方の名前空間にそのキューまたはトピックを作成します。 両方のエンティティに同じ名前を付けることができます。 たとえば、プライマリ キューは **contosoPrimary.servicebus.windows.net/myQueue** を使用してアクセスでき、セカンダリ側は **contosoSecondary.servicebus.windows.net/myQueue** を使用してアクセスできます。

アプリケーションが、送信側から受信側への永続的な通信を必要としない場合、アプリケーションで永続的なクライアント側キューを実装することで、メッセージの消失を防止し、送信側を一時的な Service Bus エラーから保護できます。

## <a name="active-replication"></a>アクティブ レプリケーション
アクティブ レプリケーションでは、すべての操作で両方の名前空間のエンティティを使用します。 メッセージを送信するクライアントはすべて、同じメッセージのコピーを&2; つ送信します。 1 つ目のコピーがプライマリ エンティティ (たとえば **contosoPrimary.servicebus.windows.net/sales**) に送信され、そのメッセージの&2; つ目のコピーはセカンダリ エンティティ (たとえば **contosoSecondary.servicebus.windows.net/sales**) に送信されます。

クライアントは両方のキューからメッセージを受信します。 受信側はメッセージの&1; つ目のコピーを処理し、2 つ目のコピーは抑制されます。 重複したメッセージを抑制するには、送信側が各メッセージに一意の識別子のタグを付ける必要があります。 メッセージのコピーには、両方とも同じ識別子のタグを付ける必要があります。 [BrokeredMessage.MessageId][BrokeredMessage.MessageId] プロパティ、[BrokeredMessage.Label][BrokeredMessage.Label] プロパティ、またはカスタム プロパティを使用して、メッセージにタグを付けることができます。 受信側は、既に受信したメッセージの一覧を保持する必要があります。

[Service Bus の仲介型メッセージを使用した geo レプリケーション][Geo-replication with Service Bus Brokered Messages] のサンプルでは、メッセージング エンティティのアクティブ レプリケーションについて説明しています。

> [!NOTE]
> アクティブ レプリケーションの手法では操作の数が&2; 倍になるので、この手法はコストの増加につながる可能性があります。
> 
> 

## <a name="passive-replication"></a>パッシブ レプリケーション
障害のない場合は、パッシブ レプリケーションは&2; つのメッセージング エンティティのうち&1; つのみを使用します。 クライアントは、アクティブなエンティティにメッセージを送信します。 アクティブなエンティティでの操作が失敗し、アクティブなエンティティをホストしているデータセンターが使用できない可能性があることを示すエラー コードが表示された場合、クライアントはメッセージのコピーをバックアップ エンティティに送信します。 この時点で、アクティブなエンティティとバックアップ エンティティの役割が切り替わります。送信側クライアントは、前のアクティブなエンティティを新しいバックアップ エンティティと見なし、前のバックアップ エンティティを新しいアクティブなエンティティと見なします。 両方の送信操作が失敗した場合、2 つのエンティティの役割は変更されず、エラーが返されます。

クライアントは両方のキューからメッセージを受信します。 受信側は同じメッセージのコピーを&2; つ受信する可能性があるので、受信側では重複したメッセージを抑制する必要があります。 アクティブ レプリケーションの説明と同じ方法で重複を抑制することができます。

一般的に、パッシブ レプリケーションではほとんどの場合に操作が&1; つだけ実行されるので、アクティブ レプリケーションよりも経済的です。 遅延、スループット、および金銭的コストは、レプリケーションなしのシナリオと同じです。

パッシブ レプリケーションを使用する場合、次のシナリオではメッセージを喪失したり&2; 回受信したりする可能性があります。

* **メッセージの遅延または喪失**: 送信側からメッセージ m1 がプライマリ キューに正常に送信されてから、受信側で m1 を受信する前にプライマリ キューが使用不可になったとします。 送信側が後続のメッセージ m2 をセカンダリ キューに送信します。 プライマリ キューが一時的に使用できない場合、受信側はキューが再び使用可能になった後で m1 を受信します。 災害の場合、受信側は m1 をまったく受信できない可能性があります。
* **重複した受信**: 送信側がメッセージ m をプライマリ キューに送信するとします。 Service Bus により m が適切に処理されますが、応答の送信に失敗します。 送信操作がタイムアウトになった後で、送信側が m の同一のコピーをセカンダリ キューに送信します。 プライマリ キューが使用不可になる前に受信側が m の&1; つ目のコピーを受信できる場合、受信側は m の両方のコピーをほぼ同時に受信します。 プライマリ キューが使用不可になる前に受信側が m の&1; つ目のコピーを受信できない場合、受信側は m の&2; つ目のコピーのみを最初に受信しますが、プライマリ キューが使用可能になったときに m の&1; つ目のコピーを受信します。

[Service Bus の仲介型メッセージを使用した geo レプリケーション][Geo-replication with Service Bus Brokered Messages] のサンプルでは、メッセージング エンティティのパッシブ レプリケーションについて説明しています。

## <a name="next-steps"></a>次のステップ
障害復旧の詳細については、次の記事を参照してください。

* [Azure SQL Database の継続性][Azure SQL Database Business Continuity]
* [Azure の回復性技術ガイダンス][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[Geo-replication with Service Bus Relayed Messages]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
[BrokeredMessage.MessageId]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[BrokeredMessage.Label]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label
[Geo-replication with Service Bus Brokered Messages]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: ../resiliency/resiliency-technical-guidance.md

