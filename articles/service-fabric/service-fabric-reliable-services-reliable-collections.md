---
title: Azure Service Fabric ステートフル サービスの Reliable Collection の概要 | Microsoft Docs
description: Service Fabric ステートフル サービスは、可用性と拡張性が高く、待機時間が短いクラウド アプリケーションの作成を可能にする Reliable Collection を提供します。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 1/3/2019
ms.author: aljo
ms.openlocfilehash: 4ed76b207db4712058b5524cd1b31fd65b0e19a4
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664410"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Azure Service Fabric ステートフル サービスの Reliable Collection の概要

Reliable Collection では、高可用性でスケーラブルな、待機時間が短いクラウド アプリケーションを、単一のコンピューターのアプリケーションを作成しているかのように作成できます。 **Microsoft.ServiceFabric.Data.Collections** 名前空間のクラスは、状態の可用性を自動的に高める、一連のコレクションを提供します。 開発者は Reliable Collection API に対してプログラミングするだけで、Reliable Collection によって、レプリケートされたローカルの状態が管理されます。

Reliable Collection とその他の高可用性テクノロジ (Redis、Azure Table service、Azure Queue サービスなど) の主な違いは、状態をローカルでサービス インスタンスに格納したまま、高い可用性を実現している点です。 これは、次のことを意味します。

* すべての読み取りはローカルで実行されるため、待機時間が短くスループットが高い読み取りになります。
* すべての書き込みでネットワーク IO 回数が最小に抑えられるため、待機時間が短くスループットが高い書き込みになります。

![コレクションの進化のイメージです。](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Reliable Collection は **System.Collections** クラスが自然に進化したものと考えることができます。つまり、開発者の作業を複雑にせずに、クラウド アプリケーションや複数コンピューター アプリケーション向けに設計された一連の新しいコレクションです。 そのため Reliable Collection は

* レプリケートされる:状態の変更がレプリケートされるため、高可用性を実現できる。
* 永続化される:データがディスクに永続化されるため、大規模な障害 (例: データセンターの電源障害) が発生しても影響を受けない。
* 書き込みが永続化されてレプリケートされるため、揮発性の ReliableDictionary、ReliableQueueや、メモリ内のデータのみを永続化する信頼性の高いその他のコレクションは作成できません。
* 非同期:API は非同期であるため、IO の実行時にスレッドがブロックされない。
* トランザクション:API ではトランザクションの抽象化が使用できるため、サービス内で複数の Reliable Collection を簡単に管理できる。

Reliable Collection では追加設定なしで強力な整合性が保証されるため、アプリケーションの状態を判断するのが簡単です。
強力な整合性は、プライマリ レプリカを含むレプリカのマジョリティ クォーラムにトランザクション全体が適用された後にのみ、トランザクション コミットが完了するようにすることで実現されています。
弱い整合性を実現するには、非同期コミットが戻る前に、アプリケーションがクライアントや要求元に確認応答できます。

Reliable Collection API は同時実行コレクション API ( **System.Collections.Concurrent** 名前空間内にあります) が進化したものです。

* 非同期:同時実行コレクションとは異なり、操作がレプリケートおよび永続化されるため、タスクを返します。
* out パラメーターを使用しない:`ConditionalValue<T>` を使用して out パラメーターではなく、`bool` およびおよび値を返します。 `ConditionalValue<T>` は `Nullable<T>` に似ていますが、構造体にするには「T」は必要ありません。
* トランザクション:トランザクション オブジェクトを使用することで、トランザクション内で複数の Reliable Collection に対してユーザーがグループ操作を実行できます。

現在、 **Microsoft.ServiceFabric.Data.Collections** には次の 3 つのコレクションが含まれています。

* [Reliable Dictionary](https://msdn.microsoft.com/library/azure/dn971511.aspx):レプリケートされた、トランザクションに使用する非同期のキーと値のペアのコレクションです。 **ConcurrentDictionary**と同様に、キーと値のいずれにも任意の型を使用できます。
* [リライアブル キュー](https://msdn.microsoft.com/library/azure/dn971527.aspx):レプリケートされた、トランザクションに使用する非同期の厳密な先入れ先出し型 (FIFO) のキューです。 **ConcurrentQueue**と同様に、値には任意の型を使用できます。
* [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md):高スループットのためにレプリケートされたトランザクションの非同期のベスト エフォートで順序付けされるキューを表します。 **ConcurrentQueue** と同様に、値には任意の型を使用できます。

## <a name="next-steps"></a>次の手順

* [Reliable Collection のガイドラインと推奨事項](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Reliable Collection での作業](service-fabric-work-with-reliable-collections.md)
* [トランザクションとロック](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* データの管理
  * [バックアップと復元](service-fabric-reliable-services-backup-restore.md)
  * [Notifications](service-fabric-reliable-services-notifications.md)
  * [Reliable Collection のシリアル化](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [シリアル化とアップグレード](service-fabric-application-upgrade-data-serialization.md)
  * [Reliable State Manager の構成](service-fabric-reliable-services-configuration.md)
* その他
  * [Reliable Service の概要](service-fabric-reliable-services-quick-start.md)
  * [Reliable Collection の開発者向けリファレンス](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
