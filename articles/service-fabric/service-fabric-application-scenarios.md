---
title: アプリケーションのシナリオと設計 | Microsoft Docs
description: Service Fabric のクラウド アプリケーションのカテゴリの概要 ステートフル サービスとステートレス サービスを使用したアプリケーションの設計について説明します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: 6563d6e7c454f44e1a70d725191e56d3f90315c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052606"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric アプリケーションのシナリオ
Azure Service Fabric はさまざまな種類のビジネス アプリケーションやサービスを作成し実行できる信頼性の高い柔軟なプラットフォームを提供します。 これらのアプリケーションとマイクロサービスはステートレスまたはステートフルが考えられますが、最大限に効率化するために仮想マシン間でリソース分散されます。 

Service Fabric 独自のアーキテクチャでは、リアルタイムに近いデータ分析、メモリ内のコンピューティング、並列トランザクション、アプリケーションでのイベント処理を実行できます。 リソース要件の変化に応じて、アプリケーションを簡単にスケール アップまたはスケール ダウン (実際にはスケール インまたはスケール アウト) できます。

アプリケーションの構築での設計のガイダンスについては、「[Azure Service Fabric でのマイクロサービス アーキテクチャ](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)」 および「[Service Fabric を使用したアプリケーションの設計に関するベスト プラクティス](service-fabric-best-practices-applications.md)」をご覧ください。

次の種類のアプリケーションに Service Fabric プラットフォームを使用することを検討してください。

* **データの収集、処理、IoT**: Service Fabric では大規模な処理が行われ、そのステートフル サービスによって待機時間が短縮されます。 これは、デバイスのデータと計算用のデータが併置されている多数のデバイスでのデータ処理に役立ちます。

    Service Fabric を利用して IoT サービスを構築されているお客様としては、[Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure)、[PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure)、[Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure)、[BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/)、[Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)、[Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions) などがあります。

* **ゲームおよびセッション ベースの対話型アプリケーション**: オンライン ゲームやインスタント メッセージングなど、低待機時間の読み取りと書き込みが必要なアプリケーションの場合は、Service Fabric が有用です。 Service Fabric では、専用のストアやキャッシュを作成する必要なしに、対話型のステートフルなアプリケーションを構築できます。 [ゲーム サービスでの Service Fabric の使用](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)に関する設計ガイダンスについては、[Azure のゲーム ソリューション](https://azure.microsoft.com/solutions/gaming/)に関するページをご覧ください。

    ゲーム サービスを構築されているお客様としては、[Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) や [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/) などがあります。 対話型セッションを構築されているお客様としては、[Honeywell with Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens) などがあります

* **データ分析とワークフロー処理**: イベントやデータのストリームを確実に処理する必要のあるアプリケーションには、Service Fabric での最適化された読み取りや書き込みによるメリットがあります。 また、Service Fabric では、信頼できる結果を、損失なく次の処理ステージに渡す必要がある、アプリケーション処理パイプラインがサポートされています。 これらのパイプラインには、データの一貫性と確実な計算が不可欠となる取引システムや金融システムが含まれます。

    ビジネス ワークフロー サービスを構築されているお客様としては、[Zeiss Group](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform)、[Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)、および [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric) などがあります。

* **データの計算**: Service Fabric では、集中型のデータ計算処理を行うステートフル アプリケーションを構築できます。 Service Fabric では、アプリケーションでの処理 (計算) とデータの併置を実現できます。 

   通常、アプリケーションでデータにアクセスする必要があるときは、外部のデータ キャッシュまたはストレージ階層に関連するネットワーク待機時間により、計算時間が制限されます。 ステートフルな Service Fabric サービスでは、その待機時間をなくして、いっそう最適化された読み取りと書き込みを実現できます。 
   
   たとえば、顧客に対してリアルタイムに近い推奨選択を実行するアプリケーションがあり、そのラウンド トリップ時間の要件が 100 ミリ秒未満であるとします。 リモート ストレージから必要なデータを取得する必要がある標準的な実装モデルの場合と比較して、Service Fabric サービスの待機時間とパフォーマンスの特性は、ユーザーに応答性の高いエクスペリエンスを提供します。 推奨選択の計算がデータおよび規則と併置されているため、システムの応答性は高くなります。

    計算サービスを構築されているお客様としては、[Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) や [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura) などがあります。

* **高可用性サービス**: Service Fabric では、複数のセカンダリ サービス レプリカを作成することによって、高速のフェールオーバーが提供されます。 ハードウェアまたはその他の障害によってノード、プロセス、または個々のサービスがダウンした場合、セカンダリ レプリカの 1 つが最小限のサービス中断でプライマリ レプリカに昇格されます。

* **スケーラブルなサービス**: クラスター間でスケールアウトの状態になれるよう個々のサービスをパーティション分割できます。 個々のサービスは、その場で作成したり削除したりすることもできます。 数個のノード上の数個のインスタンスから、多数のノード上の数千個のインスタンスまで、サービスをスケールアウトし、必要に応じて再びそれらをスケールインすることができます。 Service Fabric を使用することで、このようなサービスを構築し、その完全なライフサイクルを管理することができます。

## <a name="application-design-case-studies"></a>アプリケーション設計のケース スタディ
アプリケーション設計における Service Fabric の使用方法を示すケース スタディが、[顧客事例](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/)サイトと [Azure のマイクロサービス](https://azure.microsoft.com/solutions/microservice-applications/) サイトで公開されています。

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>ステートレスとステートフルなマイクロサービスから成るアプリケーションを設計する
Azure Cloud Services worker ロールでのアプリケーションの構築は、ステートレス サービスの一例です。 対照的に、ステートフルなマイクロサービスは、要求とその応答を超える権限のある状態を維持します。 この機能では、レプリケーションによって裏付けられたトランザクションの保証を提供するシンプルな API により、状態の高可用性と一貫性が実現されます。 

Service Fabric のステートフル サービスは、データベースや他のデータ ストアだけでなく、すべての種類のアプリケーションに高可用性をもたらします。 これは自然な進展です。 アプリケーションは既に、高可用性のための純粋なリレーショナル データベースから NoSQL データベースに移っています。 アプリケーション自体を「ホット」な状態にして、データを中で管理し、信頼性、一貫性、可用性を犠牲にすることなく、パフォーマンスを上げることができるようになりました。

マイクロサービスから成るアプリケーションを構築している場合、通常、ステートレスとステートフルなビジネス中間層サービスを呼び出すステートレスな Web アプリの組み合わせ (ASP.NET と Node.js など) があります。 アプリとサービスは、Service Fabric のデプロイ コマンドを使用して、すべて同じ Service Fabric クラスターにデプロイされます。 これらの各サービスは、スケール、信頼性、およびリソースの使用に関して独立しています。 この独立性により、開発およびライフサイクル管理での俊敏性と柔軟性が向上します。

純粋にステートレスなアプリケーションの可用性と待機時間の要件に対応するために従来必要だった追加のキューとキャッシュは、ステートフル マイクロサービスでは不要になるため、アプリケーション設計が単純化されます。 ステートフル サービスは可用性が高く待機時間が短いため、アプリケーションで管理すべき詳細は少なくなります。 

次の図では、ステートレスとステートフルなアプリケーション設計の違いを示しています。 [Reliable Services](service-fabric-reliable-services-introduction.md) と [Reliable Actors](service-fabric-reliable-actors-introduction.md) のプログラミング モデルを利用することで、高スループットと低待機時間を実現しながら、ステートフル サービスによりアプリケーションの複雑さが軽減されます。

ステートレス サービスを使用するアプリケーション例を次に示します。![ステートレス サービスを使用するアプリケーション][Image1]

ステートフル サービスを使用するアプリケーション例を次に示します。![ステートフル サービスを使用するアプリケーション][Image2]

## <a name="next-steps"></a>次の手順

* [パターンとシナリオ](service-fabric-patterns-and-scenarios.md)の詳細について説明します。

* Service Fabric の [Reliable Services](service-fabric-reliable-services-quick-start.md) プログラミング モデルと [Reliable Actors](service-fabric-reliable-actors-get-started.md) プログラミング モデルで、ステートレス サービスとステートフル サービスの構築を始めます。
* [Azure でのマイクロサービスの構築](https://docs.microsoft.com/azure/architecture/microservices/)に関するガイダンスについては、Azure アーキテクチャ センターをご覧ください。
* アプリケーションの設計のガイダンスについては、「[Azure Service Fabric のアプリケーションとクラスターに関するベスト プラクティス](service-fabric-best-practices-overview.md)」をご覧ください。

* 次のトピックもご覧ください。
  * [マイクロサービスについて](service-fabric-overview-microservices.md)
  * [サービスの状態を定義し、管理する](service-fabric-concepts-state.md)
  * [Service Fabric サービスの可用性](service-fabric-availability-services.md)
  * [Service Fabric サービスのスケーリング](service-fabric-concepts-scalability.md)
  * [Service Fabric サービスのパーティション分割](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
