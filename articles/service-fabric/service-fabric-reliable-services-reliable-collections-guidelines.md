---
title: "Azure Service Fabric の Reliable Collections のガイドラインと推奨事項 | Microsoft Docs"
description: "Service Fabric Reliable Collections を使用するためのガイドラインと推奨事項"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/3/2017
ms.author: mcoskun
ms.openlocfilehash: 053a7bca76362035e428fc11806b3e4f83d00946
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Azure Service Fabric の Reliable Collections のガイドラインと推奨事項
このセクションでは、Reliable State Manager および Reliable Collection を使用するためのガイドラインを提供します。 目標は、ユーザーがよくある問題を回避できるようにすることです。

ガイドラインは、*してください*、*検討してください*、*避けてください*、および*しないでください*の言葉を使った簡単な推奨事項として編成されています。

* 読み取り操作 (`TryPeekAsync` や `TryGetValueAsync` など) によって返されるカスタム型のオブジェクトを変更しないでください。 Reliable Collection は、同時実行コレクションのように、コピーではなくオブジェクトへの参照を返すからです。
* 返されたカスタム型のオブジェクトは、変更する前に詳細コピーしてください。 構造体型と組み込み型は値渡しであるため、詳細コピーを実行する必要はありません。
* タイムアウトに `TimeSpan.MaxValue` を使用しないでください。 タイムアウトはデッドロックの検出に使用してください。
* コミット、中止、または破棄された後に、トランザクションを使用しないでください。
* 作成済みのトランザクション スコープの外部で、列挙型を使用しないでください。
* デッドロックを引き起こす可能性があるため、別のトランザクションの `using` ステートメント内にトランザクションを作成しないでください。
* `IComparable<TKey>` の実装が正しいことを確認してください。 システムでは、チェックポイントと行を結合するため、`IComparable<TKey>` の依存関係を取得します。
* ある種のデッドロックを防ぐために、更新目的で項目を読み取る場合は更新ロックを使用してください。
* 項目 (Reliable Dictionary の TKey + TValue など) を 80 KB 未満に保つように考慮してください。小さいほど良いです。 これにより、大きなオブジェクト ヒープの使用量だけでなく、ディスクとネットワークの IO 要件が軽減されます。 多くの場合、値のごく一部だけが更新されるときの重複データのレプリケートが軽減されます。 Reliable Dictionary でこれを実現する一般的な方法は、行を複数行に分けることです。
* 障害復旧を行うには、バックアップと復元の機能の使用を検討します。
* 1 つのエンティティ操作と複数のエンティティ操作 (例: `GetCountAsync`、`CreateEnumerableAsync`) は分離レベルが異なるため、同じトランザクション内に混在させないでください。
* InvalidOperationException を処理します。 ユーザー トランザクションは、さまざまな理由でシステムによって中止されることがあります。 たとえば、Reliable State Manager がプライマリからロールを変更する場合や、実行時間の長いトランザクションがトランザクション ログの切り詰めをブロックしている場合などです。 このような場合、ユーザーは、トランザクションが既に終了されたことを示す InvalidOperationException を受け取る可能性があります。 ユーザーが要求したトランザクション終了ではない場合、この例外を処理する最善の方法は、トランザクションを破棄し、キャンセル トークンが通知されたかどうか (または、レプリカのロールが変更されたかどうか) を確認して、そうではない場合は新しいトランザクションを作成して再試行することです。  

次の点に注意してください。

* すべての Reliable Collection API の既定のタイムアウトは 4 秒です。 ほとんどのユーザーは、既定のタイムアウトを使用する必要があります。
* すべての Reliable Collection API で、既定のキャンセル トークンは `CancellationToken.None` です。
* Reliable Dictionary のキー タイプ パラメーター (*TKey*) では、`GetHashCode()` と `Equals()` が正しく実装されている必要があります。 キーは不変である必要があります。
* Reliable Collection で高可用性を実現するには、各サービスに少なくとも 1 つのターゲットと、最低 3 台で構成されるレプリカ セットが必要です。
* セカンダリの読み取り操作では、クォーラムのコミットをしていないバージョンを読み取ることがあります。
  つまり、1 つのセカンダリから読み取られるデータのバージョンが、誤って進められる可能性があります。
  プライマリからの読み取りは常に安定しており、誤って進められることはありません。

### <a name="next-steps"></a>次のステップ
* [Reliable Collection での作業](service-fabric-work-with-reliable-collections.md)
* [トランザクションとロック](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* [Reliable State Manager と Collection の内部](service-fabric-reliable-services-reliable-collections-internals.md)
* データの管理
  * [バックアップと復元](service-fabric-reliable-services-backup-restore.md)
  * [Notifications](service-fabric-reliable-services-notifications.md)
  * [シリアル化とアップグレード](service-fabric-application-upgrade-data-serialization.md)
  * [Reliable State Manager の構成](service-fabric-reliable-services-configuration.md)
* その他
  * [Reliable Service の概要](service-fabric-reliable-services-quick-start.md)
  * [Reliable Collection の開発者向けリファレンス](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
