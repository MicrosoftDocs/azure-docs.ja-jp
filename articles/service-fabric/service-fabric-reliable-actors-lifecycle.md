---
title: Azure Service Fabric アクターのライフサイクルの概要
description: Service Fabric 高信頼アクターのライフ サイクル、ガベージ コレクション、およびアクターとその状態の手動による削除について説明します。
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 88db4bb2376cbc418d6954e274a18a6c18a280d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576045"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>アクターのライフ サイクル、自動ガベージ コレクション、および手動による削除
アクターは、いずれかのメソッドが最初に呼び出された時点でアクティブ化されます。 一定期間 (値は構成可能) 使用されていない場合、アクターは非アクティブ化されます (アクター ランタイムでガベージ コレクションが発生します)。 アクターとその状態はいつでも手動で削除できます。

## <a name="actor-activation"></a>アクターのアクティブ化
アクターは次のようにアクティブ化されます。

* アクターが呼び出し時にまだアクティブになっていない場合は、新しいアクターが作成されます。
* アクターの状態が保持されている場合は、状態が読み込まれます。
* `OnActivateAsync` (C#) または `onActivateAsync` (Java) メソッド (アクターの実装でオーバーライドできる) が呼び出されます。
* アクターはアクティブと見なされるようになります。

## <a name="actor-deactivation"></a>アクターの非アクティブ化
アクターは次のように非アクティブ化されます。

* アクターは、一定期間使用されていない場合、アクティブ アクター テーブルから削除されます。
* `OnDeactivateAsync` (C#) または `onDeactivateAsync` (Java) メソッド (アクターの実装でオーバーライドできる) が呼び出されます。 呼び出されると、アクターのタイマーをすべてクリアします。 状態の変更などのアクター操作は、このメソッドから呼び出さないようにしてください。

> [!TIP]
> Fabric アクター ランタイムはいくつかの [アクターのアクティブ化と非アクティブ化に関するイベント](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters)を出力します。 これらは、診断やパフォーマンスの監視に役立ちます。
>
>

### <a name="actor-garbage-collection"></a>アクターのガベージ コレクション
アクターが非アクティブ化されると、アクター オブジェクトへの参照が解放され、共通言語ランタイム (CLR) または Java 仮想マシン (JVM) のガベージ コレクターによる通常のガベージ コレクションが可能になります。 ガベージ コレクションでクリーンアップされるのはアクター オブジェクトのみであり、アクターの状態マネージャーに格納されている状態は削除 **されません** 。 次にアクターがアクティブになると、新しいアクター オブジェクトが作成され、その状態が復元されます。

非アクティブ化やガベージ コレクションにおいて、次の状態は "使用中" と見なされます。

* 呼び出しを受信する
* `IRemindable.ReceiveReminderAsync` メソッドが呼び出されている (アクターがアラームを使用する場合にのみ適用可)

> [!NOTE]
> アクターがタイマーを使用し、そのタイマーのコールバックが呼び出された場合、アクターは "使用中" とは **見なされません** 。
>
>

非アクティブ化の詳細に入る前に、次の用語を定義しておくことが重要です。

* *スキャン間隔*。 これは、アクター ランタイムがアクティブ アクター テーブルで、非アクティブ化およびガベージ コレクションが可能なアクターをスキャンする間隔です。 この既定値は 1 分です。
* *アイドル タイムアウト*。 これは、非アクティブ化およびガベージ コレクションの前に、アクターが未使用 (アイドル) のままでいる必要がある時間です。 この既定値は 60 分です。

通常は、これらの既定値を変更する必要はありません。 ただし、必要であれば、[アクター サービス](service-fabric-reliable-actors-platform.md)の登録時に `ActorServiceSettings` を通じてこの間隔を変更できます。

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
各アクティブ アクターについて、アクター ランタイムはアクターがアイドル (つまり、未使用) 状態だった時間を追跡します。 アクター ランタイムは、`ScanIntervalInSeconds` ごとにアクターをそれぞれチェックして、ガベージ コレクションおよびマークが可能かどうかを確認し、`IdleTimeoutInSeconds` の間アイドル状態だった場合はアクターを収集します。

アクターが使用されるたびに、そのアイドル時間は 0 にリセットされます。 その後、アクターをガベージ コレクトできるのは、再び `IdleTimeoutInSeconds`の間アイドル状態のままになった場合のみです。 アクター インターフェイス メソッドまたはアクター アラーム コールバックが実行された場合、アクターが使用されていると見なされることを思い出してください。 タイマー コールバックが実行された場合、アクターは使用されていると見なされ **ません** 。

次の図は、これらの概念を表す 1 つのアクターのライフ サイクルを示しています。

![アイドル時間の例][1]

この例では、このアクターの有効期間に対するアクター メソッドの呼び出し、アラームおよびタイマーの影響を示しています。 この例では次の点に注意する必要があります。

* ScanInterval は 5、IdleTimeout は 10 にそれぞれ設定されます。 (概念の説明のみが目的であるため、ここでは単位は重要ではありません)。
* ガベージ コレクションの対象となるアクターのスキャンは、5 のスキャン間隔で定義されると、T=0、5、10、15、20、25 で行われます。
* 定期的なタイマーは T=4、8、12、16、20、24 で開始され、そのコールバックが実行されます。 アクターのアイドル時間に影響はありません。
* T=7 のアクター メソッド呼び出しは、アイドル時間を 0 にリセットし、アクターのガベージ コレクションを遅らせます。
* アクター アラームのコールバックは T=14 で実行され、アクターのガベージ コレクションをさらに遅らせます。
* T=25 のガベージ コレクションのスキャン中に、アクターのアイドル時間は最終的に 10 のアイドル タイムアウトを超え、アクターはガベージ コレクトされます。

メソッドの実行にどれだけ時間がかかっても、いずれかのメソッドが実行中のアクターがガベージ コレクションの対象となることはありません。 前述のように、アクター インターフェイス メソッドとアラームのコールバックを実行すると、アクターのアイドル時間が 0 にリセットされるため、ガベージ コレクションが行われなくなります。 タイマー コールバックの実行では、アイドル時間は 0 にリセットされません。 ただし、タイマー コールバックの実行が完了するまで、アクターのガベージ コレクションは遅延されます。

## <a name="manually-deleting-actors-and-their-state"></a>アクターとその状態を手動で削除する
非アクティブ化されたアクターのガベージ コレクションではアクター オブジェクトのみがクリーンアップされ、アクターの状態マネージャーに格納されているデータは削除されません。 アクターが再アクティブ化されると、そのデータは状態マネージャーを介して再び利用可能になります。 状態マネージャーにデータを格納したアクターが非アクティブ化され、その後も再アクティブ化されなかった場合は、そのデータをクリーンアップする必要が生じる場合があります。  アクターを削除する方法の例については、「[アクターとその状態の削除](service-fabric-reliable-actors-delete-actors.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [アクターのタイマーとアラーム](service-fabric-reliable-actors-timers-reminders.md)
* [アクター イベント](service-fabric-reliable-actors-events.md)
* [アクターの再入](service-fabric-reliable-actors-reentrancy.md)
* [アクターの診断とパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md)
* [Actor API リファレンス ドキュメント](/previous-versions/azure/dn971626(v=azure.100))
* [C# コード サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java コード サンプル](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
