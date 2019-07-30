---
title: Azure Service Bus の geo ディザスター リカバリー | Microsoft Docs
description: フェールオーバーに地理上の地域を使用して、Azure Service Bus のディザスター リカバリーを実行する方法
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190162"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus の geo ディザスター リカバリー

Azure リージョン全体または ([可用性ゾーン](../availability-zones/az-overview.md)が使用されていない) データ センター全体にダウンタイムが発生した場合に、別の地域またはデータ センターでデータ処理が続行されることが重要です。 そのため、*geo ディザスター リカバリー*は、どの企業にとっても重要な機能です。 Azure Service Bus では、名前空間のレベルで、geo ディザスター リカバリーをサポートします。

Service Bus Premium SKU では、geo ディザスター リカバリー機能がグローバルに使用できます。 

>[!NOTE]
> geo ディザスター リカバリーで現在確実に実行されるのは、プライマリ名前空間とセカンダリ名前空間がペアになっている場合に、メタデータ (キュー、トピック、サブスクリプション、フィルター) がプライマリ名前空間からセカンダリ名前空間にコピーされることだけです。

## <a name="outages-and-disasters"></a>故障と災害

"故障" と "災害" の違いに注意してください。 

*故障*とは、Azure Service Bus が一時的に使用不可能になっていることです。メッセージング ストアなどサービスの一部のコンポーネントや、場合によってはデータセンター全体に影響を与えることがあります。 しかし、問題が解決されると、Service Bus は再び使用可能になります。 通常、故障によってメッセージなどのデータが失われることはありません。 故障の例として、データセンターでの電源障害があります。 一時的な問題やネットワークの問題から短時間、接続が失われるだけの故障もあります。 

"*災害*" は、Service Bus クラスター、Azure リージョン、またはデータ センターの永久または長期間損失として定義されています。 リージョンまたはデータセンターは、再び利用可能になる場合も、ならない場合もあります。また、数時間や数日間の停止になる場合もあります。 このような災害の例としては、火災、洪水、地震があります。 永続的な災害により、メッセージやイベントなどのデータが失われる可能性があります。 ただし、ほとんどの場合、データ センターがバックアップされていればデータが失われることはなく、メッセージを復元できます。

Azure Service Bus の geo ディザスター リカバリー機能は、災害復旧ソリューションです。 この記事で説明する概念とワークフローは、一時的な故障ではなく、災害のシナリオに適用されます。 Microsoft Azure でのディザスター リカバリーの詳細については、[こちらの記事](/azure/architecture/resiliency/disaster-recovery-azure-applications)をご覧ください。   

## <a name="basic-concepts-and-terms"></a>基本的な概念と用語

ディザスター リカバリー機能は、メタデータの災害復旧を実装しており、一次および二次障害復旧の名前空間に依存しています。 geo ディザスター リカバリー機能は、[Premium SKU](service-bus-premium-messaging.md) でのみ使用可能です。 別名を使用して接続を確立するので、接続文字列に変更を加える必要はありません。

この記事では、次の用語を使用します。

-  *エイリアス*: 設定したディザスター リカバリー構成の名前です。 エイリアスは、1 つの不変の完全修飾ドメイン名 (FQDN) の接続文字列を示します。 アプリケーションでは、このエイリアスの接続文字列を使用して名前空間に接続します。 エイリアスを使用すると、フェールオーバーがトリガーされたときに接続文字列の変更が発生することはありません。

-  *プライマリ/セカンダリ名前空間*: エイリアスに対応する名前空間です。 プライマリ名前空間が "アクティブ" となり、メッセージを受け取ります (既存の名前空間の場合もあれば、新しい名前空間の場合もあります)。 セカンダリ名前空間は "パッシブ" で、メッセージを受け取りません。 両者間のメタデータは同期しているため、どちらでもアプリケーション コードや接続文字列を変更せずにメッセージをシームレスに受信できます。 確実にアクティブな名前空間にだけメッセージを送信するためには、エイリアスを使用する必要があります。 

-  *メタデータ*: 名前空間に関連付けられているサービスのエンティティ (キュー、トピック、サブスクリプションなど) とそのプロパティです。 自動的にレプリケートされるのはエンティティとその設定だけであることに注意してください。 メッセージはレプリケートされません。

-  *フェールオーバー*:セカンダリの名前空間をアクティブ化するプロセスです。

## <a name="setup"></a>セットアップ

このセクションでは、名前空間のペアリングをセットアップすることの概要を示します。

![1][]

セットアップ プロセスは次のようになります。

1. "***プライマリ***" Service Bus Premium 名前空間をプロビジョニングします。

2. "*プライマリ名前空間のプロビジョニング先とは異なるリージョン*" に、"***セカンダリ***" Service Bus Premium 名前空間をプロビジョニングします。 これは、異なるリージョンのデータセンター間で障害を分離できるようにするために役立ちます。

3. プライマリ名前空間とセカンダリ名前空間の間にペアリングを作成して、"***エイリアス***" を取得します。

    >[!NOTE] 
    > [Azure Service Bus Standard 名前空間を Azure Service Bus Premium に移行済み](service-bus-migrate-standard-premium.md)の場合、既存のエイリアス (つまり、Service Bus Standard 名前空間の接続文字列) を使用して、**PS/CLI** または **REST API** を介したディザスター リカバリー構成を作成する必要があります。
    >
    >
    > これは、移行中に、Azure Service Bus Standard 名前空間の接続文字列/DNS 名自体が Azure Service Bus Premium 名前空間のエイリアスになるためです。
    >
    > クライアント アプリケーションでは、このエイリアス (つまり、Azure Service Bus Standard 名前空間の接続文字列) を利用して、ディザスター リカバリーのペアリングが設定されている Premium 名前空間に接続する必要があります。
    >
    > ポータルを使用してディザスター リカバリー構成を設定する場合、この注意事項を気にする必要はありません。


4. 手順 3 で取得した "***エイリアス***" を使用して、クライアント アプリケーションを Geo-DR が有効なプライマリ名前空間に接続します。 最初、エイリアスは、プライマリ名前空間を指しています。

5. (オプション) フェールオーバーが必要であるかどうかを検出する監視機構を追加します。

## <a name="failover-flow"></a>フェールオーバーの流れ

フェールオーバーはお客様によって (コマンドを使用して明示的に、またはコマンドをトリガーするビジネス ロジックを所有しているクライアントを通して) 手動でトリガーされ、Azure によってトリガーされることはありません。 これにより、Azure のバックボーン上での停止に対するソリューションの完全な所有権と可視性がお客様に与えられます。

![4][]

フェールオーバーがトリガーされると:

1. "***エイリアス***" 接続文字列がセカンダリ Premium 名前空間を指すように更新されます。

2. クライアント (送信側と受信側) がセカンダリ名前空間に自動的に接続されます。

3. プライマリ Premium 名前空間とセカンダリ Premium 名前空間の間の既存のペアリングが解除されます。

フェールオーバーが開始されると:

1. 別の故障が発生した場合は、もう一度フェールオーバーしたいと考えます。 そのために、別のパッシブな名前空間を設定して、ペアリングを更新します。 

2. 以前のプライマリ名前空間が再び利用可能になったら、以前のプライマリ名前空間からメッセージをプルします。 以後、通常のメッセージングにその名前空間を geo リカバリーのセットアップ外で使用するか、または、以前のプライマリ名前空間を削除します。

> [!NOTE]
> サポートされるのは、フェール フォワードのセマンティクスだけです。 このシナリオでは、フェールオーバー後、新しい名前空間との間で再度ペアリングを行います。 フェールバックはサポートされません (SQL クラスターでのフェールバックなど)。 

フェールオーバーは、監視システムを使用して自動化できるほか、独自に構築した監視ソリューションを使用して自動化することもできます。 ただしそのような自動化は、特別な計画と作業が伴い、この記事で取り上げる範囲を超えています。

![2][]

## <a name="management"></a>管理

間違ったリージョンをペアリングしたなど、初期設定にミスがあった場合、2 つの名前空間のペアリングはいつでも解除することができます。 ペアリングした名前空間を通常の名前空間として使用する必要がある場合、エイリアスは削除してください。

## <a name="use-existing-namespace-as-alias"></a>既存の名前空間をエイリアスとして使用する

プロデューサーとコンシューマーの関連付けを変更できないシナリオでは、既存の名前空間名をエイリアス名として再利用することができます。 [こちらで GitHub のサンプル コード](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name)をご覧ください。

## <a name="samples"></a>サンプル

[GitHub のサンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/)には、フェールオーバーの設定と開始の方法が紹介されています。 サンプルで紹介されている概念は次のとおりです。

- Azure Resource Manager と Service Bus を使用して geo ディザスター リカバリーを設定し、有効にするために Azure Active Directory で必要となる .NET のサンプルと設定。
- サンプル コードを実行するために必要な手順。
- 既存の名前空間をエイリアスとして使用する方法。
- 代替方法として PowerShell または CLI から geo ディザスター リカバリーを有効にするための手順。
- エイリアスを利用し、現在のプライマリまたはセカンダリ名前空間との間で行う[送受信](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)。

## <a name="considerations"></a>考慮事項

このリリースでは次の考慮事項にご注意ください。

1. フェールオーバー計画では、時間的要因も考慮する必要があります。 たとえば、接続の喪失時間が 15 ～ 20 分を超えた場合にフェールオーバー開始の判断を下すことが考えられます。

2. レプリケートされるデータが存在しないということは、現在アクティブなセッションがレプリケートされないことを意味します。 また、重複の検出やスケジュールされたメッセージが正しく機能しない可能性があります。 新しいセッションやスケジュールされた新しいメッセージ、新しい重複については正しく機能します。 

3. 複雑な分散インフラストラクチャのフェールオーバーは、少なくとも 1 回は[リハーサル](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)を行うようお勧めします。

4. エンティティの同期には、ある程度時間がかかる場合があります (1 分あたり約 50 ～ 100 エンティティ)。 サブスクリプションやルールもエンティティとしてカウントされます。

## <a name="availability-zones"></a>可用性ゾーン

Service Bus Premium SKU では、Azure リージョン内に障害から分離された場所を提供する [Availability Zones](../availability-zones/az-overview.md) もサポートされています。

> [!NOTE]
> Azure Service Bus Premium に対する Availability Zones のサポートは、可用性ゾーンが利用可能な [Azure リージョン](../availability-zones/az-overview.md#services-support-by-region)でのみ利用できます。でのみ利用できます。

Azure Portal を使用して、新しい名前空間でのみ Availability Zones を有効にすることができます。 Service Bus では、既存の名前空間の移行はサポートされていません。 名前空間でゾーン冗長を有効にした後に、無効にすることはできません。

![3][]

## <a name="next-steps"></a>次の手順

- geo ディザスター リカバリーの [REST API リファレンス](/rest/api/servicebus/disasterrecoveryconfigs)を確認する
- geo ディザスター リカバリーの[ GitHub のサンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)を実行する
- geo ディザスター リカバリーの[別名にメッセージを送信するサンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)を確認する

Service Bus メッセージングの詳細については、次の記事をご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
