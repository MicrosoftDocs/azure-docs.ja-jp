---
title: Azure Service Fabric Reliable Services のライフサイクル
description: ステートフル サービス向け、およびステートレス サービス向けの Java を使用した Azure Service Fabric Reliable Services アプリケーションでのライフサイクル イベントについて説明します。
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: b22c78a0259e4430ac6bfae1c0a9379c4a832cd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87324609"
---
# <a name="reliable-services-lifecycle"></a>Reliable Services のライフサイクル
> [!div class="op_single_selector"]
> * [Windows での C#](service-fabric-reliable-services-lifecycle.md)
> * [Linux での Java](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services は、Azure Service Fabric で利用できるプログラミング モデルの 1 つです。 Reliable Services のライフサイクルについて知ろうとするときは、基本的なライフサイクル イベントを理解することが最も重要です。 イベントの厳密な順序は、構成の詳細によって異なります。 

一般に、Reliable Services のライフサイクルには次のイベントが含まれます。

* スタートアップ時
  * サービスが構築されます。
  * サービスは 0 個以上のリスナーを構築して返すことができます。
  * 返されたリスナーはすべて、サービスとの通信のために開かれます。
  * サービスの `runAsync` メソッドが呼び出されて、長時間実行される作業やバックグラウンド作業を行うことができるようになります。
* シャットダウン時
  * `runAsync` に渡されたキャンセル トークンが取り消されて、リスナーが閉じられます。
  * サービス オブジェクト自体が破棄されます。

Reliable Services でのイベントの順序は、リライアブル サービスがステートレスかステートフルかによって若干異なる場合があります。 

また、ステートフル サービスでは、プライマリ スワップ シナリオに対処する必要があります。 このシーケンスでは、サービスがシャットダウンすることなく、プライマリのロールが別のレプリカに転送 (または別のレプリカから復帰) されます。 

最後に、エラーまたは障害の状況を考慮する必要があります。

## <a name="stateless-service-startup"></a>ステートレス サービスのスタートアップ
ステートレス サービスのライフサイクルは、非常にわかりやすいものです。 イベントの順序は次の通りです。

1. サービスが構築されます。
2. 次のイベントが並列に発生します。
    - `StatelessService.createServiceInstanceListeners()` が呼び出されて、返されたリスナーがすべて開かれます。 `CommunicationListener.openAsync()` が各リスナーに呼び出されます。
    - サービスの `runAsync` メソッド (`StatelessService.runAsync()`) が呼び出されます。
3. 存在する場合は、サービス自体の `onOpenAsync` メソッドが呼び出されます。 具体的には、`StatelessService.onOpenAsync()` が呼び出されます。 このオーバーライドは一般的ではありませんが利用可能です。

リスナーを作成して開く呼び出しと、`runAsync` の呼び出しの順序は調整されないことに注意してください。 `runAsync` が開始される前に、リスナーが開く可能性があります。 同様に、通信リスナーが開く前、または通信リスナーが作成される前に、`runAsync` が呼び出される可能性もあります。 何らかの同期が必要な場合は、実装者が行う必要があります。 一般的な解決策をいくつか次に示します。

* リスナーは、他の情報が作成されるか別の処理が完了するまで、機能できないことがあります。 ステートレス サービスの場合、通常、そのような処理はサービスのコンストラクターで行うことができます。 `createServiceInstanceListeners()` の呼び出しの間に、またはリスナー自体の構築の一部として、実行できます。
* リスナーが開くまで `runAsync` のコードが開始しない場合があります。 この場合、追加の調整が必要です。 一般的な解決策は、リスナーにフラグを追加することです。 フラグは、リスナーが完了したことを示します。 `runAsync` メソッドでは、実際の処理を続行する前にこのフラグをチェックします。

## <a name="stateless-service-shutdown"></a>ステートレス サービスのシャットダウン
ステートレス サービスをシャット ダウンするときは、同じパターンが逆の順序で実行されます。

1. 次のイベントが並列に発生します。
    - 開いているすべてのリスナーが閉じられます。 `CommunicationListener.closeAsync()` が各リスナーに呼び出されます。
    - `runAsync()` に渡されたキャンセル トークンが取り消されます。 キャンセル トークンの `isCancelled` プロパティをチェックすると `true` が返され、メソッドを呼び出した場合、トークンの `throwIfCancellationRequested` メソッドは `CancellationException` をスローします。
2. 各リスナーで `closeAsync()` が完了し、`runAsync()` も完了すると、サービスの `StatelessService.onCloseAsync()` メソッドが呼び出されます (存在する場合)。 繰り返しになりますが、これは一般的なオーバーライドではなく、安全にすべてのリソースを閉じたり、バック グラウンド処理を停止したり、外部の状態の保存を完了したり、既存の接続を終了したりすることができます。
3. `StatelessService.onCloseAsync()` が完了すると、サービス オブジェクトは破棄されます。

## <a name="stateful-service-startup"></a>ステートフル サービスのスタートアップ
ステートフル サービスのパターンはステートレス サービスに似ていますが、変更点がいくつかあります。  ステートフル サービスを開始する場合のイベントの順序は次のとおりです。

1. サービスが構築されます。
2. `StatefulServiceBase.onOpenAsync()` が呼び出されます この呼び出しがサービスでオーバーライドされることはほとんどありません。
3. 次のイベントが並列に発生します。
    - `StatefulServiceBase.createServiceReplicaListeners()` が呼び出されます。 
      - サービスがプライマリ サービスである場合は、返されたすべてのリスナーが開かれます。 `CommunicationListener.openAsync()` が各リスナーに呼び出されます。
      - サービスがセカンダリ サービスである場合は、`listenOnSecondary = true` とマークされているリスナーのみが開かれます。 開いているリスナーがセカンダリにあるのは、あまり一般的ではありません。
    - サービスが現在プライマリである場合、サービスの `StatefulServiceBase.runAsync()` メソッドが呼び出されます。
4. すべてのレプリカ リスナーの `openAsync()` 呼び出しが完了して、`runAsync()` が呼び出されると、`StatefulServiceBase.onChangeRoleAsync()` が呼び出されます。 この呼び出しがサービスでオーバーライドされることはほとんどありません。

ステートレス サービスと同様に、ステートフル サービスでも、リスナーが作成されて開かれるときと `runAsync` が呼び出されるときの順序は調整されません。 調整が必要な場合、解決策はほぼ同じです。 ただし、ステートフル サービスでは 1 つ追加のケースがあります。 通信リスナーが受信する呼び出しで、[Reliable Collection](service-fabric-reliable-services-reliable-collections.md) に保管されている情報が必要です。 Reliable Collection が読み込み可能または書き込み可能になる前、および `runAsync` が開始する前に、通信リスナーが開く可能性があるため、追加の調整が必要になります。 最も簡単で一般的な解決策は、通信リスナーがエラー コードを返すことです。 クライアントはエラー コードを使って、要求の再試行が必要なことを知ります。

## <a name="stateful-service-shutdown"></a>ステートフル サービスのシャットダウン
ステートレス サービスと同様に、シャットダウン中のライフサイクル イベントは起動時と同じですが、順序が逆です。 ステートフル サービスのシャットダウン時には、次のイベントが発生します。

1. 次のイベントが並列に発生します。
    - 開いているすべてのリスナーが閉じられます。 `CommunicationListener.closeAsync()` が各リスナーに呼び出されます。
    - `runAsync()` に渡されたキャンセル トークンが取り消されます。 キャンセル トークンの `isCancelled()` メソッドを呼び出すと `true` が返され、メソッドを呼び出した場合、トークンの `throwIfCancellationRequested()` メソッドは `OperationCanceledException` をスローします。
2. 各リスナーで `closeAsync()` が完了し、`runAsync()` も完了すると、サービスの `StatefulServiceBase.onChangeRoleAsync()` が呼び出されます。 この呼び出しがサービスでオーバーライドされることはほとんどありません。

   > [!NOTE]  
   > `runAsync` の完了の待機は、このレプリカがプライマリ レプリカである場合にのみ必要です。

3. `StatefulServiceBase.onChangeRoleAsync()` メソッドが完了すると、`StatefulServiceBase.onCloseAsync()` メソッドが呼び出されます。 この呼び出しのオーバーライドは一般的ではありませんが利用可能です。
3. `StatefulServiceBase.onCloseAsync()` が完了すると、サービス オブジェクトは破棄されます。

## <a name="stateful-service-primary-swaps"></a>ステートフル サービスのプライマリ スワップ
ステートフル サービスの実行中、通信リスナーは開かれていて、`runAsync` メソッドはそのステートフル サービスのプライマリ レプリカに対してだけ呼び出されます。 セカンダリ レプリカは構築されますが、それ以上の呼び出しは行われません。 ステートフル サービスの実行中に、現在プライマリであるレプリカが変更される場合があります。 ステートフル レプリカが認識できるライフサイクル イベントは、それがスワップ中に降格されているレプリカか、昇格されているレプリカかによって異なります。

### <a name="for-the-demoted-primary"></a>降格されたプライマリの場合
Service Fabric では、メッセージの処理およびすべてのバックグラウンド処理を停止するために、降格されたプライマリ レプリカが必要です。 このステップは、サービスがシャットダウンされるときに似ています。 1 つ違う点は、サービスがセカンダリのままであるために、サービスが破棄されたり閉じられたりしないということです。 次のイベントが発生します。

1. 次のイベントが並列に発生します。
    - 開いているすべてのリスナーが閉じられます。 `CommunicationListener.closeAsync()` が各リスナーに呼び出されます。
    - `runAsync()` に渡されたキャンセル トークンが取り消されます。 キャンセル トークンの `isCancelled()` メソッドをチェックすると、`true` が返ります トークンの `throwIfCancellationRequested()` メソッドを呼び出すと、`OperationCanceledException` がスローされます。
2. 各リスナーで `closeAsync()` が完了し、`runAsync()` も完了すると、サービスの `StatefulServiceBase.onChangeRoleAsync()` が呼び出されます。 この呼び出しがサービスでオーバーライドされることはほとんどありません。

### <a name="for-the-promoted-secondary"></a>昇格されたセカンダリの場合
同様に、Service Fabric では、メッセージのリッスンを開始し、完了する必要があるバックグラウンド タスクを開始するために、昇格されたセカンダリ レプリカが必要です。 このプロセスは、サービスが作成されるときに似ています。 違いは、レプリカ自体が既に存在していることです。 次のイベントが発生します。

1. 次のイベントが並列に発生します。
    - `StatefulServiceBase.createServiceReplicaListeners()` が呼び出され、返されたリスナーはすべて開かれます。 `CommunicationListener.openAsync()` が各リスナーに呼び出されます。
    - サービスの `StatefulServiceBase.runAsync()` メソッドが呼び出されます。
2. すべてのレプリカ リスナーの `openAsync()` 呼び出しが完了して、`runAsync()` が呼び出されると、`StatefulServiceBase.onChangeRoleAsync()` が呼び出されます。 この呼び出しがサービスでオーバーライドされることはほとんどありません。

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>ステートフル サービスのシャットダウンとプライマリの降格における一般的な問題
Service Fabric は、複数の理由でステートフル サービスのプライマリを変更します。 最も一般的な理由は、[クラスターの再分散](service-fabric-cluster-resource-manager-balancing.md)と[アプリケーションのアップグレード](service-fabric-application-upgrade.md)です。 これらの操作では、サービスが `cancellationToken` に従うことが重要です。 これは、サービスが削除された場合など、サービスの通常のシャットダウンにも適用されます。

取り消しを適切に処理しないサービスでは、いくつかの問題が発生する可能性があります。 サービスが正常に停止するのを Service Fabric が待機するため、それらの操作の速度が遅くなります。 最終的には、アップグレードがタイムアウトしてロールバックし、アップグレードが失敗する可能性があります。 キャンセル トークンに従わないと、クラスターが不均衡になる場合もあります。 クラスターが不均衡になる原因は、ノードの負荷が高くなっても、 サービスの移動に時間がかかるので、サービスの分散を調整できないためです。 

サービスがステートフルなため、サービスが [Reliable Collection](service-fabric-reliable-services-reliable-collections.md) を使う可能性も高くなります。 Service Fabric では、プライマリが降格されたときに最初に発生することの 1 つは、基になる状態への書き込みアクセスの取り消しです。 これは、サービスのライフサイクルに影響を与える第 2 の一連の問題につながります。 レプリカが移動中なのかあるいはシャットダウンされているのかと、そのタイミングに基づいて、コレクションが例外を返します。 これらの例外を正しく処理することが重要です。 

Service Fabric によってスローされる例外には、永続的なもの [(`FabricException`)](/java/api/system.fabric.exception) と一時的なもの [(`FabricTransientException`)](/java/api/system.fabric.exception.fabrictransientexception) があります。 永続的な例外は、ログに記録されてスローされる必要があります。 一時的な例外は、再試行ロジックに基づいて再試行できます。

Reliable Services のテストと検証の重要な部分は、`ReliableCollections` とサービスのライフサイクル イベントを組み合わせて使ったことによる例外を処理することです。 負荷がかかった状態で常にサービスを実行することをお勧めします。 また、運用環境にデプロイする前に、アップグレードと[混乱テスト](service-fabric-controlled-chaos.md)を実行する必要もあります。 これらの基本手順を行うことで、サービスが正しく実装され、ライフサイクル イベントが正しく処理されるようになります。

## <a name="notes-on-service-lifecycle"></a>サービスのライフサイクルに関するメモ
* `runAsync()` メソッドおよび `createServiceInstanceListeners/createServiceReplicaListeners` の呼び出しは両方とも省略可能です。 サービスでは、どちらか一方だけまたは両方を使うか、どちらも使わない場合があります。 たとえば、サービスがユーザーの呼び出しに応答してすべての処理を実行する場合、`runAsync()` を実装する必要はありません。 通信リスナーおよびそれに関連付けられたコードのみが必要です。  同様に、通信リスナーを作成して返すことも省略可能です。 サービスで行うのがバックグラウンド処理だけの場合、`runAsync()` を実装することだけが必要です。
* サービスは、正常に `runAsync()` を完了して戻ることができます。 これは障害状態とは見なされません。 これは、サービスのバックグラウンド処理の終了を表します。 ステートフル リライアブル サービスでは、サービスがプライマリから一度降格して再度プライマリに昇格した場合、`runAsync()` が再度呼び出されます。
* サービスが予期しない例外をスローして `runAsync()` を終了した場合はエラーです。 サービス オブジェクトがシャットダウンされ、正常性のエラーが報告されます。
* これらのメソッドから制御が戻るときに時間制限はありませんが、書き込みはすぐにできなくなります。 そのため、実際の処理を完了できません。 取り消し要求を受信したらできるだけ短時間で制御を戻すことをお勧めします。 サービスが適切な時間内にこれらの API 呼び出しに応答しない場合、Service Fabric はサービスを強制的に終了する場合があります。 通常、これはアプリケーションのアップグレード中か、サービスの削除中のみに発生します。 このタイムアウト時間は既定で 15 分です。
* `onCloseAsync()` のパスで障害が起きると、`onAbort()` が呼び出されます。 これは、サービスが要求したリソースすべてをクリーンアップし解放するための最後のベストエフォートの機会になります。 これは一般に、ノードで永続的なエラーが検出されたときや Service Fabric が内部エラーのために、サービス インスタンスのライフ サイクルを確実に管理できないときに呼び出されます。
* `OnChangeRoleAsync()` は、ステートフル サービス レプリカのロールが、プライマリやセカンダリなどに変更されるときに呼び出されます。 プライマリ レプリカには書き込み状態が与えられます (Reliable Collection の作成と Reliable Collection への書き込みが可能)。 セカンダリ レプリカには読み取り状態が与えられます (既存の Reliable Collection からの読み取りのみが可能)。 ステートフル サービスの作業のほとんどは、プライマリ レプリカで実行されます。 セカンダリ レプリカでは、読み取り専用の検証、レポートの生成、データ マイニングなど、読み取り専用のジョブを実行できます。

## <a name="next-steps"></a>次のステップ
* [Reliable Services 入門](service-fabric-reliable-services-introduction.md)
* [Reliable Services の概要](service-fabric-reliable-services-quick-start-java.md)
