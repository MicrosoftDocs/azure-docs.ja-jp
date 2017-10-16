---
title: "Azure Service Fabric Reliable Services のライフサイクルの概要 | Microsoft Docs"
description: "Service Fabric Reliable Services のさまざまなライフサイクル イベントに関する説明"
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 80eb68346dd05c256c60725eb082aa0651fe7cbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-services-lifecycle-overview"></a>Reliable Services のライフサイクルの概要
> [!div class="op_single_selector"]
> * [Windows での C# ](service-fabric-reliable-services-lifecycle.md)
> * [Linux での Java](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services のライフサイクルについて考える際には、ライフサイクルの基本が最も重要です。 一般的には次のとおりです。

* スタートアップ時
  * サービスが構築される
  * サービスは 0 個以上のリスナーを構築して返すことができる
  * 返されたリスナーはすべて開かれて、サービスとの通信が許可される
  * サービスの runAsync メソッドが呼び出され、ロング ランやバックグラウンド作業が可能になる
* シャットダウン時
  * runAsync に渡されるキャンセル トークンは取り消され、リスナーが閉じられる
  * 上記の完了後、サービス オブジェクト自体が破棄される

これらのイベントの順序には、詳細な決まりがあります。 特に、イベントの順序は Reliable Services がステートレスかステートフルであるかによって若干異なる場合があります。 さらに、ステートフル サービスでは、プライマリ スワップのシナリオに対処する必要があります。 このシーケンスでは、サービスがシャットダウンすることなく、プライマリのロールが別のレプリカに転送 (または別のレプリカから復帰) されます。 最後に、エラーまたは失敗の条件を考慮する必要があります。

## <a name="stateless-service-startup"></a>ステートレス サービスのスタートアップ
ステートレス サービスのライフサイクルは、非常にわかりやすいものです。 イベントの順序は次の通りです。

1. サービスが構築されます。
2. 次に、2 つのことが並行して行われます。
    - `StatelessService.createServiceInstanceListeners()` が呼び出され、返されたリスナーはすべて開かれます (`CommunicationListener.openAsync()` が各リスナーに呼び出されます)。
    - サービスの runAsync メソッド (`StatelessService.runAsync()`) が呼び出されます。
3. 存在する場合、サービス独自の onOpenAsync が呼び出されます (具体的には、`StatelessService.onOpenAsync()` が呼び出されます。 このオーバーライドは多くは起こりませんが、利用可能なものです)。

また、リスナーを作成して開く呼び出しと runAsync の順番は調整されないことに注意してください。 runAsync が開始される前にリスナーが開くことがあります。 同様に、通信リスナーが開く前、または構築される前に runAsync が呼び出される場合もあります。 同期が必要な場合、実装者がこれを行います。 一般的な解決策は次のとおりです。

* リスナーは、別の情報が作成されるか作業が完了するまで機能しないことがあります。 ステートレス サービスでは、こうした作業は通常、サービスのコンストラクターで、`createServiceInstanceListeners()` の呼び出し時、またはリスナー自体の構築の一環として行うことができます。
* runAsync のコードをリスナーが開くまで開始しないようにする必要のある場合があります。 この場合、追加の調整が必要です。 1 つの一般的な解決策は、リスナー内にリスナーの完了時期を知らせるフラグを設定することです。このフラグは、実際の作業に進む前に runAsync でチェックされます。

## <a name="stateless-service-shutdown"></a>ステートレス サービスのシャットダウン
ステートレス サービスをシャット ダウンするときは、同じパターンが逆の順序で実行されます。

1. 次のことが並行して行われます。
    - 開いているリスナーはすべて閉じられます (各リスナーで `CommunicationListener.closeAsync()` が呼び出されます)。
    - `runAsync()` に渡されたキャンセル トークンが取り消されます (キャンセル トークンの `isCancelled` プロパティをチェックすると true が返され、呼び出された場合、トークンの `throwIfCancellationRequested` メソッドは `CancellationException` をスローします)。
2. 各リスナーで `closeAsync()` が完了し、`runAsync()` も完了すると、存在する場合はサービスの `StatelessService.onCloseAsync()` メソッドが呼び出されます (先ほどと同様に、これも珍しいオーバーライドです)。
3. `StatelessService.onCloseAsync()` が完了すると、サービス オブジェクトは破棄されます。

## <a name="notes-on-service-lifecycle"></a>サービスのライフサイクルに関するメモ
* `runAsync()` メソッドおよび `createServiceInstanceListeners` の呼び出しは両方とも省略可能です。 サービスには、これらのいずれかまたは両方を含めることも、どちらも含めないこともできます。 例えば、サービスがユーザーの呼び出しに応答してすべての処理を実行する場合、`runAsync()` を実装する必要はありません。 通信リスナーおよびそれに関連付けられたコードのみが必要です。 同様に、サービスにはバックグラウンドの作業しかない場合があり、この場合 `runAsync()` の実装のみが必要なため、通信リスナーを作成して返す必要はありません。
* サービスは、正常に `runAsync()` を完了して戻ることができます。 これは失敗条件とは見なされず、完了中のサービスのバックグラウンド作業に相当します。 ステートフル リライアブル サービスで、サービスがプライマリから一度降格して再度プライマリに昇格した場合、`runAsync()` が再度呼び出されます。
* サービスが何らかの予期しない例外をスローして `runAsync()` を終了した場合、障害が起きたことになります。その際、サービス オブジェクトがシャットダウンされ、正常性エラー報告がされます。
* これらのメソッドから制御が戻るときに時間制限はありませんが、書き込みはすぐにできなくなるため、実際の処理を完了できません。 取り消し要求を受信したらできるだけ短時間で制御が戻るようにすることをお勧めします。 サービスが適切な時間内にこれらの API 呼び出しに応答しない場合、Service Fabric はサービスを強制的に終了する場合があります。 通常、これはアプリケーションのアップグレード中か、サービスの削除中のみに発生します。 このタイムアウト時間は既定で 15 分です。
* `onCloseAsync()` パスで障害が起きると、`onAbort()` が呼び出されます。これは、サービスが要求したリソースすべてをクリーンアップし解放するための最後のベストエフォートの機会になります。

> [!NOTE]
> ステートフル Reliable Services は Java ではまだサポートされていません。
>
>

## <a name="next-steps"></a>次のステップ
* [Reliable Services 入門](service-fabric-reliable-services-introduction.md)
* [Reliable Service の概要](service-fabric-reliable-services-quick-start.md)
* [Reliable Services の詳細な使用方法](service-fabric-reliable-services-advanced-usage.md)
