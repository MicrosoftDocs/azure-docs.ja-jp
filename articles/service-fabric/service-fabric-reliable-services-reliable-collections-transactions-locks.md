---
title: Azure Service Fabric Reliable Collection のトランザクションとロック モード | Microsoft Docs
description: Azure Service Fabric Reliable State Manager と Reliable Collection トランザクションとロック。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: 9785a09a3ac3e119507b4ac28075d887c7edc619
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662042"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Azure Service Fabric Reliable Collection のトランザクションとロック モード

## <a name="transaction"></a>トランザクション
トランザクションは、作業の単一の論理ユニットとして実行される一連の操作です。
トランザクションは、次の ACID プロパティを示す必要があります。 (参照: https://technet.microsoft.com/library/ms190612)
* **原子性**:トランザクションはアトミック作業ユニットである必要があります。 つまり、そのすべてのデータ変更が実行されるか、いずれもが実行されないということです。
* **整合性**:完了すると、トランザクションが整合性のある状態ですべてのデータを残す必要があります。 すべての内部データ構造は、トランザクション終了時に正しくなければなりません。
* **分離**:同時実行トランザクションによって行われた変更は、その他の同時実行トランザクションによって加えられた変更から分離する必要があります。 ITransaction 内の操作に使用される分離レベルは、操作を実行する IReliableState によって決まります。
* **持続性**:トランザクションが完了すると、その影響は完全にシステム内に存在します。 システム障害が発生しても変更は永続化します。

### <a name="isolation-levels"></a>分離レベル
分離レベルは、トランザクションを他のトランザクションによって行われた変更から分離する必要がある度合を定義します。
Reliable Collection では、次の 2 つの分離レベルがサポートされています。

* **反復可能読み取り**:他のトランザクションによって変更されたがまだコミットされていないデータをステートメントから読み取ることができないように指定するほか、現在のトランザクションが完了するまで、現在のトランザクションで読み取られたデータをその他のトランザクションが変更できないように指定します。 詳細については、[https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx) を参照してください。
* **スナップショット**:トランザクションの任意のステートメントによって読み取られたデータが、トランザクションの開始時に存在していたデータとトランザクション上の整合性を持つように指定します。
  トランザクションで認識されるのは、トランザクション開始前にコミットされたデータ変更のみです。
  現在のトランザクションの開始後に他のトランザクションによって行われたデータ変更は、現在のトランザクションで実行されているステートメントには認識されません。
  それはつまり、トランザクションの開始時に存在していたコミット済みデータのスナップショットを、トランザクション内のステートメントが取得しているかのように機能するということです。
  スナップショットは、Reliable Collection 間で一貫しています。
  詳細については、[https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx) を参照してください。

Reliable Collection では、トランザクション作成時の操作とレプリカのロールに合わせて、特定の読み取り操作用の分離レベルが自動的に選択されます。
以下の表に、Reliable Dictionary と Reliable Queue の各操作の分離レベルの既定値を示します。

| 操作\ロール | プライマリ | セカンダリ |
| --- |:--- |:--- |
| 1 つのエンティティの読み取り |反復可能読み取り |スナップショット |
| リスト、カウント |スナップショット |スナップショット |

> [!NOTE]
> 1 つのエンティティ操作の一般的な例としては、`IReliableDictionary.TryGetValueAsync` や `IReliableQueue.TryPeekAsync` があります。
> 

Reliable Dictionary と Reliable Queue では、Read Your Writes がサポートされます。
つまり、トランザクション内でのすべての書き込みは、同じトランザクションで行われる次の読み取りによって認識されるということです。

## <a name="locks"></a>ロック
Reliable Collection では、すべてのトランザクションは緻密な 2 段階ロックを実装しています。つまり、トランザクションが中止またはコミットのいずれかで終了するまで、取得したロックを解放しないということです。

Reliable Dictionary では、1 つのエンティティ操作のすべてに対して、行レベルのロックを使用します。
Reliable Queue では、コンカレンシーよりもトランザクション FIFO プロパティの厳密さが優先されます。
Reliable Queue は操作レベルのロックを使用しており、一度に `TryPeekAsync` および/または `TryDequeueAsync` による 1 つのトランザクションと `EnqueueAsync` による 1 つのトランザクションが許可されます。
FIFO の保持のため、`TryPeekAsync` または `TryDequeueAsync` により Reliable Queue が空であると認識されたことがある場合、`EnqueueAsync` のロックも行われることに注意してください。

書き込み操作は、常に排他的ロックを取得します。
読み取り操作では、いくつかの要因に応じてロックが行われます。
スナップショット分離を使用して行う読み取り操作ではロックは使用されません。
反復可能読み取り操作では、既定で共有ロックを取得します。
ただし、反復可能な読み取りをサポートするすべての読み取り操作の場合、ユーザーは共有ロックではなく更新ロックを要求できます。
更新ロックは、複数トランザクションにおいて、後で必要になるかもしれない更新のためにリソースをロックするときに発生する一般的な形式のデッドロックを防ぐための非対称ロックです。

次に示すのは、ロックの互換性対応表です。

| 要求\許可 | なし | 共有 | アップデート | 排他的 |
| --- |:--- |:--- |:--- |:--- |
| 共有 |競合なし |競合なし |競合 |競合 |
| アップデート |競合なし |競合なし |競合 |競合 |
| 排他的 |競合なし |競合 |競合 |競合 |

デッドロック検出のために、Reliable Collection API のタイムアウト引数が使用されます。
たとえば、2 つのトランザクション (T1 と T2) が K1 を読み取って更新しようとしているとします。
いずれも共有ロックを取得することになるため、両方にデッドロックが発生する可能性があります。
この場合、一方または両方の操作がタイムアウトになります。

このデッドロックのシナリオは、デッドロックを更新ロックによって回避できる良い例です。

## <a name="next-steps"></a>次の手順
* [Reliable Collection での作業](service-fabric-work-with-reliable-collections.md)
* [Reliable Services の通知](service-fabric-reliable-services-notifications.md)
* [Reliable Service のバックアップと復元 (障害復旧)](service-fabric-reliable-services-backup-restore.md)
* [Reliable State Manager の構成](service-fabric-reliable-services-configuration.md)
* [Reliable Collection の開発者向けリファレンス](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

