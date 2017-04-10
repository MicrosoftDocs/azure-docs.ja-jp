---
title: "Azure マイクロサービスでアプリケーションの状態を保存する | Microsoft Docs"
description: "Service Fabric ステートフル サービスは、可用性と拡張性が高く、待機時間が短いクラウド アプリケーションの作成を可能にする Reliable Collection を提供します。"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 3/27/2017
ms.author: mcoskun
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 6ac47fe040793f2ac4ff596880675df0b331143e
ms.lasthandoff: 03/28/2017


---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Azure Service Fabric ステートフル サービスの Reliable Collection の概要
Reliable Collection では、高可用性でスケーラブルな、待機時間が短いクラウド アプリケーションを、単一のコンピューターのアプリケーションを作成しているかのように作成できます。 **Microsoft.ServiceFabric.Data.Collections** 名前空間のクラスは、状態の可用性を自動的に高める、すぐに使用可能な一連のコレクションを提供します。 開発者は Reliable Collection API に対してプログラミングするだけで、Reliable Collection によって、レプリケートされたローカルの状態が管理されます。

Reliable Collection とその他の高可用性テクノロジ (Redis、Azure Table Service、Azure Queue サービスなど) の主な違いは、状態をローカルでサービス インスタンスに格納したまま、高い可用性を実現している点です。 これは、次のことを意味します。

* すべての読み取りはローカルで実行されるため、待機時間が短くスループットが高い読み取りになります。
* すべての書き込みでネットワーク IO 回数が最小に抑えられるため、待機時間が短くスループットが高い書き込みになります。

![コレクションの進化のイメージです。](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Reliable Collection は **System.Collections** クラスが自然に進化したものと考えることができます。つまり、開発者の作業を複雑にせずに、クラウド アプリケーションや複数コンピューター アプリケーション向けに設計された一連の新しいコレクションです。 そのため Reliable Collection は

* レプリケートされる: 状態の変更がレプリケートされるため、高可用性を実現できる。
* 永続化される: データがディスクに永続化されるため、大規模な障害 (例: データセンターの電源障害) が発生しても影響を受けない。
* 非同期: API は非同期であるため、IO の実行時にスレッドがブロックされない。
* トランザクション: API ではトランザクションの抽象化が使用できるため、サービス内で複数の Reliable Collection を簡単に管理できる。

Reliable Collection では追加設定なしで強力な整合性が保証されるため、アプリケーションの状態を判断するのが簡単です。
強力な整合性は、プライマリ レプリカを含むレプリカのマジョリティ クォーラムにトランザクション全体が適用された後にのみ、トランザクション コミットが完了するようにすることで実現されています。
弱い整合性を実現するには、非同期コミットが戻る前に、アプリケーションがクライアントや要求元に確認応答できます。

Reliable Collection API は同時実行コレクション API ( **System.Collections.Concurrent** 名前空間内にあります) が進化したものです。

* 非同期: 同時実行コレクションとは異なり、操作がレプリケートおよび永続化されるため、タスクを返します。
* out パラメーターを使用しない: `ConditionalValue<T>` を使用して out パラメーターではなく、ブール値および値を返します。 `ConditionalValue<T>` は `Nullable<T>` に似ていますが、構造体にするには「T」は必要ありません。
* トランザクション: トランザクション オブジェクトを使用することで、トランザクション内で複数の Reliable Collection に対してユーザーがグループ操作を実行できます。

現在、 **Microsoft.ServiceFabric.Data.Collections** には次の 2 つのコレクションが含まれています。

* [Reliable Dictionary](https://msdn.microsoft.com/library/azure/dn971511.aspx): レプリケートされた、トランザクションに使用する非同期のキーと値のペアのコレクションです。 **ConcurrentDictionary**と同様に、キーと値のいずれにも任意の型を使用できます。
* [Reliable Queue](https://msdn.microsoft.com/library/azure/dn971527.aspx): レプリケートされた、トランザクションに使用する非同期の厳密な先入れ先出し型 (FIFO) のキューです。 **ConcurrentQueue**と同様に、値には任意の型を使用できます。

## <a name="isolation-levels"></a>分離レベル
分離レベルは、トランザクションを他のトランザクションによって行われた変更から分離する必要がある度合を定義します。
Reliable Collection では、次の 2 つの分離レベルがサポートされています。

* **反復可能読み取り**: 他のトランザクションによって変更されたがまだコミットされていないデータをステートメントから読み取ることができないように指定するほか、現在のトランザクションが完了するまで、現在のトランザクションで読み取られたデータをその他のトランザクションが変更できないように指定します。 詳細については、 [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx)をご覧ください。
* **スナップショット**: トランザクションの任意のステートメントによって読み取られたデータが、トランザクションの開始時に存在していたデータとトランザクション上の整合性を持つように指定します。
  トランザクションで認識されるのは、トランザクション開始前にコミットされたデータ変更のみです。
  現在のトランザクションの開始後に他のトランザクションによって行われたデータ変更は、現在のトランザクションで実行されているステートメントには認識されません。
  それはつまり、トランザクションの開始時に存在していたコミット済みデータのスナップショットを、トランザクション内のステートメントが取得しているかのように機能するということです。
  スナップショットは、Reliable Collection 間で一貫しています。
  詳細については、 [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx)をご覧ください。

Reliable Collection では、トランザクション作成時の操作とレプリカのロールに合わせて、特定の読み取り操作用の分離レベルが自動的に選択されます。
以下の表に、Reliable Dictionary と Reliable Queue の各操作の分離レベルの既定値を示します。

| 操作\ロール | プライマリ | セカンダリ |
| --- |:--- |:--- |
| 1 つのエンティティの読み取り |反復可能読み取り |スナップショット |
| 列挙\カウント |スナップショット |スナップショット |

> [!NOTE]
> 1 つのエンティティ操作の一般的な例としては、`IReliableDictionary.TryGetValueAsync` や `IReliableQueue.TryPeekAsync` があります。
> 
> 

Reliable Dictionary と Reliable Queue では、Read Your Writes がサポートされます。
つまり、トランザクション内でのすべての書き込みは、同じトランザクションで行われる次の読み取りによって認識されるということです。

## <a name="locking"></a>ロック
Reliable Collection では、すべてのトランザクションは 2 段階で実行されます。つまり、トランザクションが中止またはコミットのいずれかで終了するまで、取得したロックを解放しないということです。

Reliable Dictionary では、1 つのエンティティ操作のすべてに対して、行レベルのロックを使用します。
Reliable Queue では、同時実行よりもトランザクション FIFO プロパティの厳密さが優先されます。
Reliable Queue は操作レベルのロックを使用しており、一度に `TryPeekAsync` および/または `TryDequeueAsync` による 1 つのトランザクションと `EnqueueAsync` による 1 つのトランザクションが許可されます。
FIFO の保持のため、`TryPeekAsync` または `TryDequeueAsync` により Reliable Queue が空であると認識されたことがある場合、`EnqueueAsync` のロックも行われることに注意してください。

書き込み操作は、常に排他的ロックを取得します。
読み取り操作では、いくつかの要因に応じてロックが行われます。
スナップショット分離を使用して行う読み取り操作ではロックは使用されません。
反復可能読み取り操作では、既定で共有ロックを取得します。
ただし、反復可能な読み取りをサポートするすべての読み取り操作の場合、ユーザーは共有ロックではなく更新ロックを要求できます。
更新ロックは、複数トランザクションにおいて、後で必要になるかもしれない更新のためにリソースをロックするときに発生する一般的な形式のデッドロックを防ぐための非対称ロックです。

次に示すのは、ロックの互換性対応表です。

| 要求\許可 | なし | 共有 | 更新 | 排他的 |
| --- |:--- |:--- |:--- |:--- |
| 共有 |競合なし |競合なし |競合 |競合 |
| 更新 |競合なし |競合なし |競合 |競合 |
| 排他的 |競合なし |競合 |競合 |競合 |

デッドロック検出のために、Reliable Collection API のタイムアウト引数が使用されることに注意してください。
たとえば、2 つのトランザクション (T1 と T2) が K1 を読み取って更新しようとしているとします。
いずれも共有ロックを取得することになるため、両方にデッドロックが発生する可能性があります。
この場合、一方または両方の操作がタイムアウトになります。

上記のデッドロックのシナリオは、デッドロックを更新ロックによって回避できる良い例です。

## <a name="persistence-model"></a>永続性モデル
Reliable State Manager および Reliable Collection は、ログとチェックポイントと呼ばれる永続性モデルに従います。
このモデルでは、各状態の変更がディスクに記録され、メモリ内でのみ適用されます。
完全な状態自体はごくまれに永続化されます ( チェックポイントとしても知られています)。
このモデルの利点は、差分がディスクに対する追加専用のシーケンシャル書き込みに変換されるため、パフォーマンスが向上することです。

ログとチェックポイント モデルをよく理解するために、まず無限のディスクのシナリオを見てみましょう。
Reliable State Manager は、すべての操作をレプリケートされる前にログに記録します。
これにより、Reliable Collection はメモリ内でのみ操作を適用できます。
ログは永続化されるため、レプリカに障害が発生して再起動する必要があった場合でも、Reliable State Manager のログに十分な情報があるため、レプリカで失われたすべての操作を再生することができます。
ディスクは無限であるためログ レコードを削除する必要はなく、Reliable Collection が実行する必要があるのは、メモリ内にある状態を管理することだけです。

次に、有限のディスクのシナリオを見ていきましょう。
ログ レコードが蓄積すると、Reliable State Manager が使用するディスク領域が不足します。
そのような不足が発生する前に、Reliable State Manager は、ログを切り捨てて新しいレコードを格納する領域を確保する必要があります。
このとき Reliable State Manager は、メモリ内の状態をチェックポイントとしてディスクに記録するように Reliable Collection に要求します。
その時点までの状態を永続化するのは Reliable Collection の役目です。
Reliable Collection がチェックポイントを完了すると、Reliable State Manager はログを切り捨ててディスク領域を解放できます。
このようにして、レプリカの再起動が必要な場合、Reliable Collection はチェックポイントされた状態を回復し、Reliable State Manager はチェックポイント以降に実行された状態の変更すべてを回復して再生します。

> [!NOTE]
> チェックポイントには、一般的なケースでの復旧のパフォーマンスが向上するというメリットもあります。
> これは、チェックポイントには最新バージョンのみが含まれるためです。
> 
> 

## <a name="recommendations"></a>推奨事項
* 読み取り操作 (`TryPeekAsync` や `TryGetValueAsync` など) によって返されるカスタム型のオブジェクトを変更しないでください。 Reliable Collection は、同時実行コレクションのように、コピーではなくオブジェクトへの参照を返すからです。
* 返されたカスタム型のオブジェクトは、変更する前に詳細コピーしてください。 構造体型と組み込み型は値渡しであるため、詳細コピーを実行する必要はありません。
* タイムアウトに `TimeSpan.MaxValue` を使用しないでください。 タイムアウトはデッドロックの検出に使用してください。
* コミット、中止、または破棄された後に、トランザクションを使用しないでください。
* 作成済みのトランザクション スコープの外部で、列挙型を使用しないでください。
* デッドロックを引き起こす可能性があるため、別のトランザクションの `using` ステートメント内にトランザクションを作成しないでください。
* `IComparable<TKey>` の実装が正しいことを確認してください。 システムでは、チェックポイントを結合するため、これについての依存関係を取得します。
* ある種のデッドロックを防ぐために、更新目的で項目を読み取る場合は更新ロックを使用してください。
* 項目 (Reliable Dictionary の TKey + TValue など) を 80 KB 未満に保つように考慮してください。小さいほど良いです。 これにより、大きなオブジェクト ヒープの使用量だけでなく、ディスクとネットワークの IO 要件が軽減されます。 多くの場合は、値のごく一部だけが更新されるときの重複データのレプリケートも軽減されます。 Reliable Dictionary でこれを実現する一般的な方法は、行を複数行に分けることです。 
* 障害復旧を行うには、バックアップと復元の機能の使用を検討します。
* 1 つのエンティティ操作と複数のエンティティ操作 (例: `GetCountAsync`、`CreateEnumerableAsync`) は分離レベルが異なるため、同じトランザクション内に混在させないでください。
* InvalidOperationException を処理します。 ユーザー トランザクションは、さまざまな理由でシステムによって中止されることがあります。 たとえば、Reliable State Manager がプライマリからロールを変更する場合や、実行時間の長いトランザクションがトランザクション ログの切り詰めをブロックしている場合などです。 このような場合、ユーザーは、トランザクションが既に終了されたことを示す InvalidOperationException を受け取る可能性があります。 ユーザーが要求したトランザクション終了ではない場合、この例外を処理する最善の方法は、トランザクションを破棄し、キャンセル トークンが通知されたかどうか (または、レプリカのロールが変更されたかどうか) を確認して、そうではない場合は新しいトランザクションを作成して再試行することです。  

次の点に注意してください。

* すべての Reliable Collection API の既定のタイムアウトは 4 秒です。 ほとんどの場合において、ユーザーがこれをオーバーライドすることはお勧めしません。
* すべての Reliable Collection API で、既定のキャンセル トークンは `CancellationToken.None` です。
* Reliable Dictionary のキー タイプ パラメーター (*TKey*) では、`GetHashCode()` と `Equals()` が正しく実装されている必要があります。 キーは不変である必要があります。
* Reliable Collection で高可用性を実現するには、各サービスに少なくとも 1 つのターゲットと、最低 3 台で構成されるレプリカ セットが必要です。
* セカンダリの読み取り操作では、クォーラムのコミットをしていないバージョンを読み取ることがあります。
  つまり、1 つのセカンダリから読み取られるデータのバージョンが、誤って進められる可能性があります。
  もちろん、プライマリからの読み取りは常に安定しており、誤って進められることはありません。

## <a name="next-steps"></a>次のステップ
* [Reliable Service の概要](service-fabric-reliable-services-quick-start.md)
* [Reliable Collection での作業](service-fabric-work-with-reliable-collections.md)
* [Reliable Services の通知](service-fabric-reliable-services-notifications.md)
* [Reliable Service のバックアップと復元 (障害復旧)](service-fabric-reliable-services-backup-restore.md)
* [Reliable State Manager の構成](service-fabric-reliable-services-configuration.md)
* [Service Fabric Web API サービスの概要](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Service プログラミング モデルの詳細な使用方法](service-fabric-reliable-services-advanced-usage.md)
* [Reliable Collection の開発者向けリファレンス](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)


