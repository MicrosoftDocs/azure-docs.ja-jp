---
title: "Azure Service Fabric Reliable Services のライフサイクルの概要 | Microsoft Docs"
description: "Service Fabric Reliable Services のさまざまなライフサイクル イベントに関する説明"
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider;
translationtype: Human Translation
ms.sourcegitcommit: 1472bd1647b620d1f0489cdabbed58e72585aef7
ms.openlocfilehash: ad1f713f2b12af4f778b9b14edf33a568d2aa8b1

---

# <a name="reliable-services-lifecycle-overview"></a>Reliable Services のライフサイクルの概要
Reliable Services のライフサイクルについて考える際には、ライフサイクルの基本が最も重要です。 一般的には次のとおりです。

* スタートアップ時
  * サービスが構築される
  * サービスは&0; 個以上のリスナーを構築して返すことができる
  * 返されたリスナーはすべて開かれて、サービスとの通信が許可される
  * サービスの RunAsync メソッドが呼び出され、ロング ランやバックグラウンド作業が可能になる
* シャットダウン時
  * RunAsync に渡されるキャンセル トークンは取り消され、リスナーが閉じられる
  * 上記の完了後、サービス オブジェクト自体が破棄される

これらのイベントの順序には、詳細な決まりがあります。 特に、イベントの順序は Reliable Services がステートレスかステートフルであるかによって若干異なる場合があります。 さらに、ステートフル サービスでは、プライマリ スワップのシナリオに対処する必要があります。 このシーケンスでは、サービスがシャットダウンすることなく、プライマリのロールが別のレプリカに転送 (または別のレプリカから復帰) されます。 最後に、エラーまたは失敗の条件を考慮する必要があります。

## <a name="stateless-service-startup"></a>ステートレス サービスのスタートアップ
ステートレス サービスのライフサイクルは、非常にわかりやすいものです。 イベントの順序は次の通りです。

1. サービスが構築されます。
2. 次に、2 つのことが並行して行われます。
    - `StatelessService.CreateServiceInstanceListeners()` が呼び出され、返されたリスナーはすべて開かれます (`ICommunicationListener.OpenAsync()` が各リスナーに呼び出されます)。
    - サービスの RunAsync メソッド (`StatelessService.RunAsync()`) が呼び出されます。
3. 存在する場合、サービス独自の OnOpenAsync が呼び出されます (具体的には、`StatelessService.OnOpenAsync()` が呼び出されます。 このオーバーライドは多くは起こりませんが、利用可能なものです)。

また、リスナーを作成し開く呼び出しと RunAsync の順番は調整されないことに注意してください。 RunAsync が開始する前にリスナーが開くことがあります。 同様に、通信リスナーが開く前、または構築される前に RunAsync が呼び出される場合もあります。 同期が必要な場合、実装者がこれを行います。 一般的な解決策は次のとおりです。

* リスナーは、別の情報が作成されるか作業が完了するまで機能しないことがあります。 ステートレス サービスでは、こうした作業は通常、サービスのコンストラクターで、`CreateServiceInstanceListeners()` の呼び出し時、またはリスナー自体の構築の一環として行うことができます。
* RunAsync のコードをリスナーが開くまで起動しないようにする必要のある場合があります。 この場合、追加の調整が必要です。 1 つの一般的な解決策は、リスナー内にリスナーの完了時期を知らせるフラグを設定することです。このフラグは、実際の作業に進む前に RunAsync でチェックされます。

## <a name="stateless-service-shutdown"></a>ステートレス サービスのシャットダウン
ステートレス サービスをシャット ダウンするときは、同じパターンが逆の順序で実行されます。

1. 次のことが並行して行われます。
    - 開いているリスナーはすべて閉じられます (各リスナーで `ICommunicationListener.CloseAsync()` が呼び出されます)。
    - `RunAsync()` に渡されたキャンセル トークンが取り消されます (キャンセル トークンの `IsCancellationRequested` プロパティをチェックすると true が返され、呼び出された場合、トークンの `ThrowIfCancellationRequested` メソッドは `OperationCanceledException` を返します)。
2. 各リスナーで `CloseAsync()` が完了し、`RunAsync()` も完了すると、存在する場合はサービスの `StatelessService.OnCloseAsync()` メソッドが呼び出されます (先ほどと同様に、これも珍しいオーバーライドです)。
3. `StatelessService.OnCloseAsync()` が完了すると、サービス オブジェクトは破棄されます。

## <a name="stateful-service-startup"></a>ステートフル サービスのスタートアップ
ステートフル サービスのパターンはステートレス サービスに似ていますが、変更点がいくつかあります。 ステートフル サービスを開始するときのイベントの順序は次の通りです。

1. サービスが構築されます。
2. `StatefulServiceBase.OnOpenAsync()` が呼び出されます (これがサービスでオーバーライドされることはほとんどありません)。
3. 対象のサービス レプリカがプライマリの場合、次のことが並行して行われます。プライマリでない場合、サービスは手順 4 に進みます。
    - `StatefulServiceBase.CreateServiceReplicaListeners()` が呼び出され、返されたリスナーはすべて開かれます (`ICommunicationListener.OpenAsync()` が各リスナーに呼び出されます)。
    - サービスの RunAsync メソッド (`StatefulServiceBase.RunAsync()`) が呼び出されます。
4. レプリカ リスナーの `OpenAsync()` の呼び出しが完了し、`RunAsync()` が開始されると (これらのステップは、このレプリカが現在セカンダリであるためスキップされる場合があります)、`StatefulServiceBase.OnChangeRoleAsync()` が呼び出されます。 (これがサービスでオーバーライドされることはほとんどありません)。

ステートレス サービスと同様に、リスナーが作成され開かれる順序と RunAsync が呼び出される順序は調整されません。 ソリューションはほぼ同じですが、1 つ追加のケースがあります: 通信リスナーが受信する呼び出しが動作するために、[Reliable Collection](service-fabric-reliable-services-reliable-collections.md) に保管されている情報を必要とするとします。 Reliable Collection が読み込み可能または書き込み可能になる前、および RunAsync が起動する前に通信リスナーが開く可能性があるため、追加の調整が必要となります。 最も簡単で一般的なソリューションは、クライアントに要求のリトライを知らせるエラー コードを通信リスナーが返すことです。

## <a name="stateful-service-shutdown"></a>ステートフル サービスのシャットダウン
ステートレス サービスと同様に、シャットダウン中のライフサイクル イベントは起動時と同じですが、順序が逆です。 ステートフル サービスのシャットダウン時には、次のイベントが発生します。

1. 次のことが並行して行われます。
    - 開いているリスナーはすべて閉じられます (各リスナーで `ICommunicationListener.CloseAsync()` が呼び出されます)。
    - `RunAsync()` に渡されたキャンセル トークンが取り消されます (キャンセル トークンの `IsCancellationRequested` プロパティをチェックすると true が返され、呼び出された場合、トークンの `ThrowIfCancellationRequested` メソッドは `OperationCanceledException` を返します)。
2. 各リスナーで `CloseAsync()` が完了し、`RunAsync()` も完了すると (サービス レプリカがプライマリだった場合のみに必要なもの)、サービスの `StatefulServiceBase.OnChangeRoleAsync()` が呼び出されます。 (これがサービスでオーバーライドされることはほとんどありません)。
3. `StatefulServiceBase.OnChangeRoleAsync()` メソッドが完了すると、`StatefulServiceBase.OnCloseAsync()` メソッドが呼び出されます (先ほどと同様に、このオーバーライドは多くは起こりませんが、利用可能なものです)。
3. `StatefulServiceBase.OnCloseAsync()` が完了すると、サービス オブジェクトは破棄されます。

## <a name="stateful-service-primary-swaps"></a>ステートフル サービスのプライマリ スワップ
ステートフル サービスの実行中、通信リスナーを開き、RunAsync メソッドを呼び出すのはそのステートフル サービスのプライマリ レプリカだけです。 セカンダリは構築されますが、それ以上の呼び出しは行われません。 ただし、ステートフル サービスの実行中に、現在プライマリであるレプリカが変更される場合があります。 レプリカに生じるライフサイクル イベントの観点から見ると、これはどういうことでしょうか。 ステートフルなレプリカに起こる動作は、それがスワップ中に降格されているレプリカであるか、昇格されているものかによって変わります。

### <a name="for-the-primary-being-demoted"></a>降格されているプライマリの場合
Service Fabric では、このレプリカでメッセージの処理を終了して、途中のバックグラウンド作業をすべて終了する必要があります。 その結果、この手順はサービスのシャットダウン時と似たものになります。 1 つ違う点は、サービスがセカンダリのままであるために、サービスが破棄されたり閉じられたりしないということです。 次の API が呼び出されます。

1. 次のことが並行して行われます。
    - 開いているリスナーはすべて閉じられます (各リスナーで `ICommunicationListener.CloseAsync()` が呼び出されます)。
    - `RunAsync()` に渡されたキャンセル トークンが取り消されます (キャンセル トークンの `IsCancellationRequested` プロパティをチェックすると true が返され、呼び出された場合、トークンの `ThrowIfCancellationRequested` メソッドは `OperationCanceledException` を返します)。
2. 各リスナーで `CloseAsync()` が完了し、`RunAsync()` も完了すると、サービスの `StatefulServiceBase.OnChangeRoleAsync()` が呼び出されます。 (これがサービスでオーバーライドされることはほとんどありません)。

### <a name="for-the-secondary-being-promoted"></a>昇格されているセカンダリの場合
同様に、Service Fabric では、このレプリカで送信中のメッセージのリッスンを開始して (そうする場合)、関連するバックグラウンド タスクをすべて開始する必要があります。 その結果、レプリカ自体が既に存在している点以外、この手順はサービスのシャットダウン時と似たものになります。 次の API が呼び出されます。

1. 次のことが並行して行われます。
    - `StatefulServiceBase.CreateServiceReplicaListeners()` が呼び出され、返されたリスナーはすべて開かれます (`ICommunicationListener.OpenAsync()` が各リスナーに呼び出されます)。
    - サービスの RunAsync メソッド (`StatefulServiceBase.RunAsync()`) が呼び出されます。
4. レプリカ リスナーの `OpenAsync()` の呼び出しが完了し `RunAsync()` が開始すると (これらのステップは、これがセカンダリであるためにスキップされる場合があります)、`StatefulServiceBase.OnChangeRoleAsync()` が呼び出されます。 (これがサービスでオーバーライドされることはほとんどありません)。

## <a name="notes-on-service-lifecycle"></a>サービスのライフサイクルに関するメモ
* `RunAsync()` メソッドおよび `CreateServiceReplicaListeners/CreateServiceInstanceListeners` の呼び出しは両方とも省略可能です。 サービスには、これらのいずれかまたは両方を含めることも、どちらも含めないこともできます。 例えば、サービスがユーザーの呼び出しに応答してすべての処理を実行する場合、`RunAsync()` を実装する必要はありません。 通信リスナーおよびそれに関連付けられたコードのみが必要です。 同様に、サービスにはバックグラウンドの作業しかない場合があり、この場合 `RunAsync()` の実装のみが必要なため、通信リスナーを作成して返す必要はありません。
* サービスは、正常に `RunAsync()` を完了して戻ることができます。 これは失敗条件とは見なされず、完了中のサービスのバックグラウンド作業に相当します。 ステートフル リライアブル サービスで、サービスがプライマリから一度降格して再度プライマリに昇格した場合、`RunAsync()` が再度呼び出されます。
* サービスが何らかの予期しない例外をスローして `RunAsync()` を終了した場合、障害が起きたことになります。その際、サービス オブジェクトがシャットダウンされ、正常性エラー報告がされます。
* これらのメソッドから制御が戻るときに時間制限はありませんが、Reliable Collection への書き込みはすぐにできなくなるため、実際の処理を完了できません。 取り消し要求を受信したらできるだけ短時間で制御が戻るようにすることをお勧めします。 サービスが適切な時間内にこれらの API 呼び出しに応答しない場合、Service Fabric はサービスを強制的に終了する場合があります。 通常、これはアプリケーションのアップグレード中か、サービスの削除中のみに発生します。 このタイムアウト時間は既定で 15 分です。
* ステートフル サービスでは、ServiceReplicaListeners に追加のオプションがあり、セカンダリ レプリカで開始することができます。 これは一般的ではありませんが、ライフサイクルの変更点は、レプリカがセカンダリの場合でも `CreateServiceReplicaListeners()` が呼び出されること (およびその結果のリスナーが開くこと) のみです。 同様に、後でレプリカがプライマリに変換された場合、リスナーは閉じられて破棄され、プライマリへの変更の一環として新しいファイルが作成され開かれます。
* `OnCloseAsync()` パスで障害が起きると、`OnAbort()` が呼び出されます。これは、サービスが要求したリソースすべてをクリーンアップし解放するための最後のベストエフォートの機会になります。

## <a name="next-steps"></a>次のステップ
* [Reliable Services 入門](service-fabric-reliable-services-introduction.md)
* [Reliable Service の概要](service-fabric-reliable-services-quick-start.md)
* [Reliable Services の詳細な使用方法](service-fabric-reliable-services-advanced-usage.md)



<!--HONumber=Jan17_HO1-->


