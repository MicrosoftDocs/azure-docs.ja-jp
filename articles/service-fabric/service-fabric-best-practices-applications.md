---
title: Azure Service Fabric アプリケーションの設計に関するベスト プラクティス | Microsoft Docs
description: Service Fabric アプリケーションの開発に関するベスト プラクティス。
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 06af1f4326e3f6a6dcb53c8710a126f43e2d2f6a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875096"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric アプリケーションの設計に関するベスト プラクティス

この記事では、Azure Service Fabric でのアプリケーションとサービスの構築に関するベスト プラクティス ガイダンスを提供します。
 
## <a name="get-familiar-with-service-fabric"></a>Service Fabric の理解を深める
* [Service Fabric の詳細](service-fabric-content-roadmap.md)に関する記事を読む。
* [Service Fabric アプリケーションのシナリオ](service-fabric-application-scenarios.md)について確認する。
* 「[Service Fabric プログラミング モデルの概要](service-fabric-choose-framework.md)」を読んでプログラミング モデルの選択肢について理解する。



## <a name="application-design-guidance"></a>アプリケーションの設計ガイダンス
Service Fabric アプリケーションの[全般的なアーキテクチャ](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)と、それらの[設計に関する考慮事項](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)について理解を深めます。

### <a name="choose-an-api-gateway"></a>API ゲートウェイを選択する
スケール アウト可能な、バックエンド サービスと通信する API ゲートウェイ サービスを使用します。使用される最も一般的な API ゲートウェイ サービスは次のとおりです。

- [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview)。[Service Fabric と統合](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)されます。
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) または [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)。[ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) を使用して、イベント ハブ パーティションから読み取ります。
- [Træfik リバース プロキシ](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)。[Azure Service Fabric プロバイダー](https://docs.traefik.io/configuration/backends/servicefabric/)を使用します。
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/)。

   > [!NOTE] 
   > Azure Application Gateway は、Service Fabric と直接統合されません。 Azure API Management は、一般に推奨される選択肢です。
- 独自のカスタム ビルド [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) Web アプリケーション ゲートウェイ。

### <a name="stateless-services"></a>ステートレス サービス
常に、[Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) を使用し、Azure データベース、Azure Cosmos DB、または Azure Storage に状態を格納することで、ステートレス サービスの構築を開始することをお勧めします。 状態の外部化は、ほとんどの開発者にとってより使い慣れた方法です。 このアプローチでは、ストアでクエリ機能の利点を活用することもできます。  

### <a name="when-to-use-stateful-services"></a>ステートフル サービスを使用する場合
低待機時間のシナリオで、データをコンピューティングの近くに維持する必要がある場合は、ステートフル サービスを検討してください。 いくつかのシナリオ例として、IoT デジタル ツイン デバイス、ゲームの状態、セッションの状態、データベースからのデータのキャッシュおよび他のサービスの呼び出しを追跡するための長期ワークロードがあります。

データ保持の期間を決定する:

- **キャッシュ データ**。 外部ストアに対する待機時間が問題である場合は、キャッシュを使用します。 独自のデータ キャッシュとしてステートフル サービスを使用するか、[オープンソースの SoCreate Service Fabric Distributed Cache](https://github.com/SoCreate/service-fabric-distributed-cache) の使用を検討してください。 このシナリオでは、キャッシュ内のすべてのデータが失われる可能性がありますが、問題ありません。
- **期限付きのデータ**。 このシナリオでは、待機時間のために一定の期間、コンピューティングの近くにデータを保持する必要がありますが、"*障害*" によってデータが失われても差し支えありません。 たとえば、多くの IoT ソリューションでは、過去数日間の平均温度を計算する場合など、データをコンピューティングに近づける必要があります。しかし、このデータが失われた場合、記録された特定のデータ ポイントはそれほど重要ではありません。 また、このシナリオでは、通常、個々のデータ ポイントのバックアップは考慮しません。 外部ストレージに定期的に書き込まれる、計算された平均値のみをバックアップします。  
- **長期的なデータ**。 リライアブル コレクションではデータを永続的に格納できます。 しかし、この場合、クラスターの[定期的なバックアップ ポリシーの構成](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup)を含む、[ディザスター リカバリーの準備](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)が必要となります。 実際には、障害によってクラスターが破棄された場合の動作、新しいクラスターを作成する必要がある場所、および新しいアプリケーション インスタンスをデプロイし、最新のバックアップから復元する方法を構成します。

コストを削減し、可用性を向上させる:
- リモート ストアからのデータ アクセスおよびトランザクション コストは発生せず、Azure Cache for Redis などの別のサービスを使用する必要がないため、ステートフル サービスの使用によってコストを削減することができます。
- ステートフル サービスは、コンピューティングではなく、主にストレージのために使用する場合、コストがかかるため、お勧めできません。 ローカル ストレージのコストが低いコンピューティングとして、ステートフル サービスを検討してください。
- 他のサービスへの依存関係を取り除くことで、サービスの可用性を向上させることができます。 クラスター内の HA で状態を管理することで、他のサービスのダウンタイムや待機時間の問題から切り離すことができます。

## <a name="how-to-work-with-reliable-services"></a>Reliable Services を操作する方法
Service Fabric Reliable Services では、ステートレス サービスとステートフル サービスを簡単に作成できます。 詳細については、「[Reliable Services の概要](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)」を参照してください。
- ステートレス サービスとステートフル サービスの `RunAsync()` メソッドと、ステートフル サービスの `ChangeRole()`メソッドでは、常に[キャンセル トークン](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps)を優先します。 そうしないと、Service Fabric では、サービスを閉じることができるかどうかが認識されません。 たとえば、キャンセル トークンを優先しない場合、アプリケーションのアップグレード時間がかなり長くなる可能性があります。
-   適時に[通信リスナー](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication)を開いたり閉じたりして、キャンセル トークンを優先します。
-   同期コードと非同期コードを混在させないでください。 たとえば、非同期呼び出しで `.GetAwaiter().GetResult()` を使用しないでください。 コール スタックでは "*最初から最後まで*" 非同期を使用します。

## <a name="how-to-work-with-reliable-actors"></a>Reliable Actors を操作する方法
Service Fabric Reliable Actors では、ステートフルな仮想アクターを簡単に作成できます。 詳細については、[Reliable Actors の概要](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)に関する記事を参照してください。

- アプリケーションのスケーリングのために、アクター間でのパブリッシュ/サブスクライブ メッセージングの使用を真剣に検討してください。 このサービスを提供するツールには、[オープンソースの SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) や [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/) などがあります。
- アクターの状態を[できるだけ詳細](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)に定めます。
- [アクターのライフ サイクル](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)を管理します。 アクターを再び使用することがない場合は削除します。 [揮発状態プロバイダー](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)を使用している場合は、すべての状態がメモリに格納されるため、不要なアクターを削除することが特に重要です。
- [ターンベースのコンカレンシー](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)により、アクターを独立したオブジェクトとして使用するのが最適です。 複数アクターの同期メソッド呼び出し (それぞれが別のネットワーク呼び出しになる可能性が最も高い) のグラフを作成したり、循環アクター要求を作成したりしないでください。 これらは、パフォーマンスとスケールに大きく影響します。
- 同期コードと非同期コードを混在させないでください。 パフォーマンスの問題を防ぐために、常に非同期を使用します。
- アクターで長期呼び出しを行わないでください。 長期呼び出しを行うと、ターンベースのコンカレンシーにより、同じアクターへの他の呼び出しがブロックされます。
- [Service Fabric リモート処理](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting)を使用して他のサービスと通信しており、`ServiceProxyFactory` を作成する場合は、アクター レベル*ではなく*、[アクター サービス](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) レベルでファクトリーを作成します。


## <a name="application-diagnostics"></a>アプリケーション診断
サービス呼び出しでの[アプリケーションのログ記録](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app)の追加を徹底してください。 これは、サービスが相互に呼び出されるシナリオの診断に役立ちます。 たとえば、A から B、B から C、C から D という呼び出しの場合、呼び出しはどの場所でも失敗する可能性があります。 ログ記録が十分でない場合、エラーの診断は困難です。 呼び出しのボリュームにより、サービスのログ記録が多すぎる場合は、少なくともエラーと警告のログを記録するようにしてください。

## <a name="iot-and-messaging-applications"></a>IoT とメッセージング アプリケーション
[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) または [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) からメッセージを読み取る場合は、[ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) を使用します。 ServiceFabricProcessor は、イベント ハブ パーティションからの読み取り状態を維持するために Service Fabric Reliable Services と統合され、`IEventProcessor::ProcessEventsAsync()` メソッドを使用してサービスに新しいメッセージがプッシュされます。


## <a name="design-guidance-on-azure"></a>Azure での設計ガイダンス
* [Azure でのマイクロサービスの構築](https://docs.microsoft.com/azure/architecture/microservices/)に関する設計ガイダンスについては、[Azure アーキテクチャ センター](https://docs.microsoft.com/azure/architecture/microservices/)にアクセスしてください。

* [ゲーム サービスでの Service Fabric の使用](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)に関する設計ガイダンスについては、「[Get Started with Azure for Gaming (ゲームで Azure を使ってみる)](https://docs.microsoft.com/gaming/azure/)」を参照してください。
