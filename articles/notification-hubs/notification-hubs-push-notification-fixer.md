---
title: "Azure Notification Hubs - 診断ガイドライン"
description: "Azure Notification Hubs に関する一般的な問題を診断する方法のガイドラインです。"
services: notification-hubs
documentationcenter: Mobile
author: ysxu
manager: erikre
editor: 
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b0b067f02f02f722534238891f7412153635df80
ms.lasthandoff: 12/08/2016


---
# <a name="azure-notification-hubs---diagnosis-guidelines"></a>Azure Notification Hubs - 診断ガイドライン
## <a name="overview"></a>Overview
Azure Notification Hubs ユーザーから最もよく寄せられる質問の 1 つは、アプリケーション バックエンドから送信された通知がクライアント デバイスに表示されない理由を解明する方法です。具体的には、通知が破棄された場所とその原因、およびこの問題を解決する方法があります。 この記事では、通知が破棄されたり、デバイスに届かなかったりするさまざまな理由を説明します。 また、根本的な原因を分析して解明する方法も紹介します。 

まず重要なのが、Azure Notification Hubs がデバイスに通知をプッシュする方法を理解することです。
![][0]

一般的な通知の送信フローでは、メッセージは**アプリケーション バックエンド**から **Azure Notification Hubs** に送信されます。Notification Hubs では、すべての登録に対して、構成されているタグとタグ式を考慮して処理を行い、「ターゲット」、つまりプッシュ通知を受信する必要があるすべての登録を特定します。 これらの登録は、iOS、Google、Windows、Windows Phone、Kindle、Baidu for China Android など、サポートされているプラットフォームのいずれかまたはすべてにわたる可能性があります。 ターゲットが確立されると、次に Notification Hubs は、登録の複数のバッチに分割された通知をデバイス プラットフォーム固有の **Push Notification サービス (PNS)** (例: Apple の APNS、Google の GCM など) にプッシュします。Notification Hubs は、Azure クラシック ポータルの [Notification Hubs の構成] ページで設定した資格情報に基づいて、各 PNS への認証を実行します。 その後、PNS は通知を各**クライアント デバイス**に転送します。 これは、プッシュ通知を配信するためにプラットフォームで推奨されている方法です。通知の配信の最終段階はプラットフォームの PNS とデバイスの間で実行される点に注目してください。 そのため、"*クライアント*"、"*アプリケーション バックエンド*"、"*Azure Notification Hubs (NH)*"、および "*プッシュ通知サービス (PNS)*" の 4 つの主要なコンポーネントがあり、そのいずれも、通知の脱落の原因となる可能性があります。 このアーキテクチャの詳細については、「[Notification Hubs の概要]」を参照してください。

初期のテストまたはステージング段階の期間中に通知の配信が失敗する場合は、構成の問題が発生している可能性があります。また、運用環境ですべてまたは一部の通知が破棄される場合は、より深刻なアプリケーションの問題やメッセージング パターンの問題が発生している可能性があります。 以下のセクションでは、一般的なシナリオから比較的珍しいケースまで、さまざまな通知の破棄シナリオについて説明します。中には自明だと思われる原因もありますが、意外と気付かないものも含まれています。 

## <a name="azure-notifications-hub-mis-configuration"></a>Azure Notification Hubs の構成ミス
各 PNS に通知を正常に送信できるようにするためには、Azure Notification Hubs を開発者のアプリケーションのコンテキストで認証する必要があります。 これを実行するには、開発者が各プラットフォーム (Google、Apple、Windows など) に開発者アカウントを作成してから、資格情報を取得するアプリケーションを登録します。この資格情報は、ポータルの [Notification Hubs の構成] セクションで構成する必要があります。 通知が届かない場合、まずはプラットフォーム固有の開発者アカウントで作成されたアプリケーションと一致する正しい資格情報が Notification Hubs で構成されていることを確認する必要があります。 このプロセスを手順ごとに確認するには、「[Notification Hubs の使用]」を参照してください。 一般的な構成ミスの一部を以下に示します。

1. **全般**
   
    a) Notification Hubs の名前にタイプミスがなく、以下と一致することを確認します。
   
   * クライアントから登録する場所 
   * バックエンドから通知を送信する場所  
   * PNS の資格情報を構成した場所 
   * クライアントとバックエンドで構成した SAS 資格情報を持つユーザー 
     
     b) クライアントとアプリケーション バックエンドで正しい SAS 構成文字列を使用していることを確認します。 原則として、クライアントでは **DefaultListenSharedAccessSignature**、アプリケーションのバックエンドでは **DefaultFullSharedAccessSignature** を使用する必要があります (これにより、Notification Hubs に通知を送信できるようになります)。
2. **Apple Push Notification Service (APNS) の構成**
   
    運用環境とテスト用に 2 つの異なるハブを維持する必要があります。 つまり、サンドボックス環境で使用する証明書と運用環境で使用する証明書をそれぞれ別のハブにアップロードする必要があります。 後から通知エラーが発生する可能性があるため、異なる種類の証明書を同じハブにアップロードしないでください。 誤って異なる種類の証明書を同じハブに既にアップロードしている場合は、ハブを削除し、新たに開始することをお勧めします。 何らかの理由でハブを削除できない場合は、少なくとも、既存のすべての登録をハブから削除する必要があります。 
3. **Google Cloud Messaging (GCM) の構成** 
   
    a) クラウド プロジェクトで [Google Cloud Messaging for Android] を有効にしていることを確認します。 
   
    ![][2]
   
    b) GCM への認証を実行するために Notification Hubs が使用する資格情報を取得するときに、「サーバー キー」を作成します。 
   
    ![][3]
   
    c) クライアントで「プロジェクト ID」を構成していることを確認します。プロジェクト ID はすべて数値で構成されるエンティティで、ダッシュボードから取得することができます。
   
    ![][1]

## <a name="application-issues"></a>アプリケーションの問題
1) **タグ/タグ式**

対象ユーザーのセグメント化にタグまたはタグ式を使用している場合は、通知を送信するときに、送信呼び出しで指定したタグまたはタグ式に基づくターゲットが見つからない可能性が常にあります。 登録を確認して、通知を送信するときに一致するタグがあることを確認し、それらが登録されているクライアントから受信した通知のみを検証することをお勧めします。 例: Notification Hubs へのすべての登録に「Politics」というタグを使用していて、「Sports」というタグを含む通知を送信した場合、この通知はデバイスに送信されません。 複雑なケースでは、「Tag A」OR「Tag B」というタグ式のみを登録したにもかかわらず、「Tag A && Tag B」を対象として通知を送信するといった例が挙げられます。 以下の「自己診断のヒント」のセクションでは、登録およびそれに含まれるタグを確認できる方法について説明しています。 

2) **テンプレートの問題**

テンプレートを使用している場合は、「 [テンプレート]」に記載されているガイドラインに従っていることを確認してください。 

3) **無効な登録**

Notification Hubs が正しく構成されており、タグまたはタグ式が正しく使用されていて、通知を送信する必要のある有効なターゲットが見つかった場合、Notification Hubs は複数の処理のバッチを並行して開始し、各バッチが一連の登録にメッセージを送信します。 

> [!NOTE]
> 並行して処理を行うため、通知が配信される順序は保証されません。 
> 
> 

現行の Azure Notification Hubs は、「最大 1 回の」メッセージ配信モデルに合わせて最適化されています。 つまり、デバイスに同じ通知が複数回配信されないように、重複の除去を行います。 これを確実に実行するために、実際にメッセージを PNS に送信する前に、登録全体を確認し、各デバイス ID にメッセージが 1 回のみ送信されることを確認しています。 各バッチが PNS へ送信され、登録の承認と検証が行われるときに、PNS によってバッチの 1 つまたは複数の登録でエラーが検出され、Azure Notification Hubs にエラーが返されて処理が停止し、そのバッチが完全に破棄される可能性があります。 これは、TCP ストリーム プロトコルを使用する　APNS に特に当てはまります。 最大 1 回の配信に最適化しているものの、この場合は、失敗している登録をデータベースから削除したうえで、そのバッチに残っているデバイスを対象に通知の配信を再試行します。

登録デバイスに対して試行された配信が失敗したときのエラー情報は、Azure Notification Hubs REST API を使用して取得できます。「[Per Message Telemetry: Get Notification Message Telemetry (メッセージごとのテレメトリ: 通知メッセージのテレメトリを取得する)](https://msdn.microsoft.com/library/azure/mt608135.aspx)」と「[PNS Feedback (PNS フィードバック)](https://msdn.microsoft.com/library/azure/mt705560.aspx)」を参照してください。 サンプル コードについては、「[SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample)」を参照してください。

## <a name="pns-issues"></a>PNS の問題
各 PNS が通知メッセージを受信した後は、デバイスに通知を配信する責任は PNS にあります。 このとき、Azure Notification Hubs は無関係であり、通知がデバイスに配信されるタイミングや、配信されるかどうかを制御することはできません。 プラットフォーム通知サービスは非常に堅牢なため、通常は PNS からデバイスに数秒で通知が送信されます。 ただし、PNS でスロットルが発生している場合、Azure Notification Hubs は指数バックオフ戦略を適用します。PNS に 30 分間送信できなかった場合は、ポリシーによって、メッセージを有効期限切れにして完全に削除します。 

PNS が通知を配信しようとしてデバイスがオフラインである場合、通知は PNS によって一定期間保存され、デバイスにアクセス可能になったときに配信されます。 保存されるのは、特定のアプリにつき 1 つの最新の通知のみです。 デバイスがオフラインの間に複数の通知が送信された場合、新しい通知が配信されるたびに以前の通知が破棄されます。 この最新の通知のみを維持する動作は、APNS では結合通知と呼ばれ、GCM では折りたたみと呼ばれます (これには、折りたたみキーを使用します)。 デバイスが長時間オフラインのままである場合、保存されている通知は破棄されます (出典: [APNS のガイダンス]および &  [GCM のガイダンス])。

Azure Notification Hubs では、汎用 `SendNotification` API (例: .NET SDK では `SendNotificationAsync`) により、HTTP ヘッダーを使用して結合キーを渡すことができます。この API に HTTP ヘッダーを指定し、各 PNS にそのまま渡します。 

## <a name="self-diagnose-tips"></a>自己診断のヒント
このセクションでは、Notification Hubs の問題を診断し、根本原因を究明するためのさまざまな手段を説明します。

### <a name="verify-credentials"></a>資格情報の確認
1. **PNS 開発者ポータル**
   
    「 [Notification Hubs の使用]」を参照して、各 PNS 開発者ポータル (APNS、GCM、WNS など) で資格情報を確認します。
2. **Azure クラシック ポータル**
   
    [構成] タブに移動して、資格情報を確認し、PNS 開発者ポータルから取得した資格情報と照合します。 
   
    ![][4]

### <a name="verify-registrations"></a>登録の確認
1. **Visual Studio**
   
    開発に Visual Studio を使用している場合は、Microsoft Azure に接続し、[サーバー エクスプローラー] から Notification Hubs を含む多数の Azure サービスを表示、管理することができます。 これは、特に開発/テスト環境で便利です。 
   
    ![][9]
   
    ハブのすべての登録を表示、管理でき、プラットフォーム、ネイティブまたはテンプレートの登録、タグ、PNS 識別子、登録 ID、有効期限ごとに見やすく分類されています。 また、その場で登録を編集することもできます。これは、タグを編集する場合などに便利です。 
   
    ![][8]
   
   > [!NOTE]
   > Visual Studio の登録を編集する機能は、登録の数が限られている開発/テスト期間中にのみ使用してください。 登録を一括で修正する必要が生じた場合は、「 [登録のエクスポート/インポート](https://msdn.microsoft.com/library/dn790624.aspx)
   > 
   > 
2. **Service Bus エクスプローラー**
   
    多くのお客様は、Notification Hubs を表示/管理するために、こちらで説明されている「 [ServiceBus エクスプローラー] 」を使用しています。 これは、code.microsoft.com で提供しているオープン ソース プロジェクトです ( [ServiceBus エクスプローラーのコード]

### <a name="verify-message-notifications"></a>メッセージ通知の確認
1. **Azure クラシック ポータル**
   
    [デバッグ] タブから、サービス バックエンドを稼働させることなく、クライアントにテスト通知を送信することができます。 
   
    ![][7]
2. **Visual Studio**
   
    Visual Studio から簡単にテスト通知を送信することもできます。
   
    ![][10]
   
    Visual Studio の Azure Notification Hubs エクスプローラーの機能の詳細については、以下を参照してください。 
   
   * [VS サーバー エクスプローラーの概要]
   * [VS サーバー エクスプローラーに関するブログ記事 - 1]
   * [VS サーバー エクスプローラーに関するブログ記事 - 2]

### <a name="debug-failed-notifications-review-notification-outcome"></a>失敗した通知のデバッグ/通知の出力の確認
**EnableTestSend プロパティ**

Notification Hubs を使用して通知を送信する場合、通知はまず単にキューに追加され、Notification Hubs がすべてのターゲットを特定する処理を実行してから、最終的に Notification Hubs によって PNS に送信されます。 そのため、REST API やクライアント SDK を使用していて送信呼び出しが正常に返されても、Notification Hubs でメッセージが正常にキューに追加されたということでしかありません。 Notification Hubs が最終的に PNS にメッセージを送信したときに何が起こったのかを確認することはできません。 クライアント デバイスに通知が届かない場合は、Notification Hubs が PNS にメッセージを配信しようとしたときに、エラー (ペイロードのサイズが PNS で許容されている上限を超えている、Notification Hubs で構成されている資格情報が無効である、など) が発生した可能性があります。PNS エラーを確認できるように、[EnableTestSend feature] というプロパティが導入されました。 このプロパティは、ポータルまたは Visual Studio クライアントからテスト メッセージを送信するときに自動的に有効にされ、詳細なデバッグ情報を表示することができます。 これは、現在 .NET SDK などの API で利用可能で、将来的にすべてのクライアント SDK に追加される予定です。 これを REST 呼び出しで使用するには、送信呼び出しの最後に「test」というクエリ文字列パラメーターを追加します。以下がその例です。 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*例 (.NET SDK)*

たとえば、.NET SDK を使用して、ネイティブのトースト通知を送信するとします。

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);

`result.State`result.State では、実行の最後に単に `Enqueued` が返され、プッシュに何が起こったのかを確認することはできません。 `NotificationHubClient` の初期化中にブール型の `EnableTestSend` プロパティを使用すれば、通知の送信中に発生した PNS エラーに関する詳細なステータスを取得できます。 このときの送信呼び出しは、Notification Hubs が PNS に通知を配信し、結果が確認された後に返されるため、通常よりも時間がかかります。 

    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*サンプル出力*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

このメッセージは、Notification Hubs で無効な資格情報が構成されているか、Notification Hubs の登録に問題であることを示しています。この登録を削除して、メッセージを送信する前にクライアントに再作成させることをお勧めします。 

> [!NOTE]
> このプロパティを使用すると大幅なスロットルが発生するため、登録数の限られた開発/テスト環境でのみ使用してください。 デバッグ通知は 10 のデバイスのみに送信されます。 また、1 分あたりに処理できるデバッグ送信は 10 件に制限されています。 
> 
> 

### <a name="review-telemetry"></a>統計情報の確認
1. **Azure クラシック ポータルの使用**
   
    ポータルでは、Notification Hubs のすべてのアクティビティの概要を簡単に確認できます。 
   
    a) [ダッシュボード] タブでは、登録、通知、プラットフォーム別のエラーの集計データを表示できます。 
   
    ![][5]
   
    b) また、[モニター] タブからその他多数のプラットフォーム固有のメトリックを追加して、Notification Hubs が PNS に通知を送信しようとしたときに返された PNS 固有のエラーの詳細を確認することもできます。 
   
    ![][6]
   
    c) PNS 固有のエラーを確認するには、まず**受信メッセージ**、**登録操作**、**正常に送信された通知**を確認してから、プラットフォーム別のタブを確認します。 
   
    d) Notification Hubs で認証設定が正しく構成されていない場合は、PNS 認証エラーが表示されます。 この場合は、PNS の資格情報を確認してください。 

2) **プログラムによるアクセス**

詳細については、以下を参照してください。 

* [プログラムによる統計情報へのアクセス]
* [API サンプルを使用した統計情報へのアクセス] 

> [!NOTE]
> **登録のエクスポート/インポート**や **API による統計情報へのアクセス**といった一部のテレメトリ関連機能は Standard レベルでのみ利用可能です。 Free または Basic レベルでこれらの機能を使用しようとすると、SDK を使用している場合はこの効果に対する例外メッセージが表示され、REST API から直接使用している場合は HTTP 403 (Forbidden) が表示されます。 Azure クラシック ポータルから Standard レベルに移行してください。  
> 
> 

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Notification Hubs の概要]: notification-hubs-push-notification-overview.md
[Notification Hubs の使用]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[テンプレート]: https://msdn.microsoft.com/library/dn530748.aspx 
[APNS のガイダンス]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[GCM のガイダンス]: http://developer.android.com/google/gcm/adv.html
[Export/Import Registrations]: http://msdn.microsoft.com/library/dn790624.aspx
[ServiceBus エクスプローラー]: http://msdn.microsoft.com/library/dn530751.aspx
[ServiceBus エクスプローラーのコード]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[VS サーバー エクスプローラーの概要]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[VS サーバー エクスプローラーに関するブログ記事 - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[VS サーバー エクスプローラーに関するブログ記事 - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend feature]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[プログラムによる統計情報へのアクセス]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[API サンプルを使用した統計情報へのアクセス]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel


