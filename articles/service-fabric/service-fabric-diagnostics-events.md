---
title: Azure Service Fabric イベント | Microsoft Docs
description: Azure Service Fabric クラスターの監視に役立つように標準で提供されている Service Fabric イベントについて説明します。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: b4270b9438a397ec09537c9d6343515ebc21af98
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393029"
---
# <a name="service-fabric-events"></a>Service Fabric イベント 

Service Fabric プラットフォームでは、クラスター内で発生している重要な運用アクティビティについて、いくつかの構造化されたイベントが書き込まれます。 その範囲は、クラスターのアップグレードから、レプリカの配置決定までにわたります。 Service Fabric が公開している各イベントは、クラスター内の以下のエンティティのいずれかにマッピングされています。
* クラスター
* Application
* Service
* Partition
* レプリカ 
* コンテナー

プラットフォームによって公開されるイベントの完全な一覧については、[Service Fabric イベントの一覧](service-fabric-diagnostics-event-generation-operational.md)を参照してください。

以下では、クラスター内でイベントを確認する必要があるシナリオの例を示します。 
* ノードのライフサイクル イベント: ノードの起動、停止、スケールイン/スケールアウト、再起動、アクティブ化/非アクティブ化が行われると、何が発生したかを示すイベントが公開され、マシン自体に問題があるかどうかや、ノードの状態を変更するために SF を通じて呼び出された API があったかどうかを識別するのに役立ちます。
* クラスターのアップグレード: クラスターがアップグレードされるとき (SF バージョンまたは構成の変更) には、アップグレードの開始、各アップグレード ドメインでの進行、および完了 (またはロールバック) が示されます。 
* アプリケーションのアップグレード: クラスターのアップグレードと同様に、アップグレードが進行するときには包括的なイベントのセットが存在します。 これらのイベントは、アップグレードがスケジュールされているタイミング、アップグレードの現在の状態、およびイベントの全体的なシーケンスを解釈するのに役立つ場合があります。 これは、どのアップグレードが正常にロールアウトされたか、またはロールバックがトリガーされたかどうかを、後から確認するのに便利です。
* アプリケーション/サービスのデプロイ/削除: アプリケーション、サービス、コンテナーが作成または削除されるたびにイベントが発生し、スケールインまたはスケールアウト (たとえば、レプリカの数を増やす) のときに役に立ちます
* パーティションの移動 (再構成): ステートフルなパーティションの再構成が行われる (レプリカ セット内での変更) たびに、イベントがログ記録されます。 これは、パーティションのレプリカ セットが変更またはフェールオーバーされている頻度を把握しようとする場合や、任意の時点でどのノードでプライマリ レプリカが実行されていたかを追跡しようとする場合に役立ちます。
* Chaos イベント: Service Fabric の [Chaos](service-fabric-controlled-chaos.md) サービスの使用時には、サービスが開始または停止されるたびにイベントが示されます。また、サービスがシステムにエラーを挿入するとイベントが示されます。
* 正常性イベント:Service Fabric は、警告またはエラーの正常性レポートが作成されるたび、エンティティが OK の正常性状態に戻るたび、または正常性レポートの有効期限が切れるたびに正常性イベントを公開します。 これらのイベントは、エンティティの正常性に関する履歴の統計を追跡する場合に非常に役立ちます。 

## <a name="how-to-access-events"></a>イベントへのアクセス方法

Service Fabric イベントには、いくつかの異なる方法を通してアクセスすることができます。
* イベントは ETW/Windows イベント ログなどの標準的なチャネルを通じて記録され、Azure Monitor ログのようなこれらをサポートする任意の監視ツールで視覚化することができます。 ポータルでクラスターを作成すると、既定で、診断が有効になり、Windows Azure Diagnostics エージェントによって Azure Table Storage にイベントが送信されるようになりますが、それでも、これをログ分析リソースと統合する必要があります。 より多くのログやパフォーマンス カウンターを収集するようにクラスターの診断構成を変更するための [Microsoft Azure Diagnostics エージェント](service-fabric-diagnostics-event-aggregation-wad.md) の構成と、[Azure Monitor ログの統合](service-fabric-diagnostics-event-analysis-oms.md) に関する詳細を参照してください。
* EventStore サービスの Rest API。この方法では、クラスターに直接、または Service Fabric クライアント ライブラリを介して、クエリを実行することができます。 「[EventStore API でクラスター イベントのクエリを実行する](service-fabric-diagnostics-eventstore-query.md)」を参照してください。

## <a name="next-steps"></a>次の手順
* クラスターの監視についての詳細を表示 - [クラスターとプラットフォームの監視](service-fabric-diagnostics-event-generation-infra.md)。
* EventStore サービスについての詳細を表示 - [EventStore サービスの概要](service-fabric-diagnostics-eventstore.md)
