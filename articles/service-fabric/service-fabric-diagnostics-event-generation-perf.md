---
title: "Azure Service Fabric のパフォーマンスの監視 | Microsoft Docs"
description: "Azure Service Fabric クラスターの監視と診断に使うパフォーマンス カウンターについて説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: dekapur
ms.openlocfilehash: 9d63148c182c705b6b49733c59ed8fdd13872d72
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="performance-metrics"></a>パフォーマンス メトリック

クラスターおよびその中で実行しているアプリケーションのパフォーマンスを把握するには、メトリックを収集する必要があります。 Service Fabric クラスターでは、次のパフォーマンス カウンターを収集することをお勧めします。

## <a name="nodes"></a>Nodes

クラスター内の各コンピューターの負荷を把握し、クラスターのスケーリングを適切に決定するには、次のパフォーマンス カウンターの収集を検討してください。

| カウンターのカテゴリ | カウンター名 |
| --- | --- |
| PhysicalDisk(per Disk) | Avg.Disk Read Queue Length |
| PhysicalDisk(per Disk) | Avg.Disk Write Queue Length |
| PhysicalDisk(per Disk) | Avg.Disk sec/Read |
| PhysicalDisk(per Disk) | Avg.Disk sec/Write |
| PhysicalDisk(per Disk) | Disk Reads/sec  |
| PhysicalDisk(per Disk) | Disk Read Bytes/sec  |
| PhysicalDisk(per Disk) | Disk Writes/sec |
| PhysicalDisk(per Disk) | Disk Write Bytes/sec |
| メモリ | Available MBytes |
| PagingFile | % Usage |
| Process(Total) | % Processor Time |
| Process (per service) | % Processor Time |
| Process (per service) | ID Process |
| Process (per service) | Private Bytes |
| Process (per service) | Thread Count |
| Process (per service) | Virtual Bytes |
| Process (per service) | Working Set |
| Process (per service) | Working Set - Private |

## <a name="net-applications-and-services"></a>.NET アプリケーションとサービス

クラスターに .NET サービスをデプロイしている場合は、次のカウンターを収集します。 

| カウンターのカテゴリ | カウンター名 |
| --- | --- |
| .NET CLR Memory (per service) | プロセス ID |
| .NET CLR Memory (per service) | # Total committed Bytes |
| .NET CLR Memory (per service) | # Total reserved Bytes |
| .NET CLR Memory (per service) | # Bytes in all Heaps |
| .NET CLR Memory (per service) | # Gen 0 Collections |
| .NET CLR Memory (per service) | # Gen 1 Collections |
| .NET CLR Memory (per service) | # Gen 2 Collections |
| .NET CLR Memory (per service) | % Time in GC |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric のカスタム パフォーマンス カウンター

Service Fabric は、大量のカスタム パフォーマンス カウンターを生成します。 SDK をインストールした場合、Windows コンピューターでの包括的な一覧をパフォーマンス モニター アプリケーションで表示できます ([スタート] > [パフォーマンス モニター])。 

クラスターにデプロイしているアプリケーションで Reliable Actors を使っている場合は、`Service Fabric Actor` および `Service Fabric Actor Method` カテゴリのカウンターを追加します (「[Reliable Actors の診断とパフォーマンス監視](service-fabric-reliable-actors-diagnostics.md)」をご覧ください)。

Reliable Services を使っている場合は、同じように `Service Fabric Service` および `Service Fabric Service Method` カウンター カテゴリのカウンターを収集します。 

Reliable Collections を使っている場合は、`Service Fabric Transactional Replicator` の `Avg. Transaction ms/Commit` を追加して、トランザクションあたりの平均コミット待ち時間メトリックを収集することをお勧めします。


## <a name="next-steps"></a>次のステップ

* Service Fabric における[プラットフォーム レベルでのイベント生成](service-fabric-diagnostics-event-generation-infra.md)についてさらに学習します
* [Azure 診断](service-fabric-diagnostics-event-aggregation-wad.md)を使ってパフォーマンス メトリックを収集してください
