---
title: Azure Notification Hubs での欠落した通知の診断
description: Azure Notification Hubs で欠落した通知に関する一般的な問題を診断する方法について説明します。
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 787cf922fcee0ee613fc0874a490830da9adf38a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455027"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Azure Notification Hubs での欠落した通知の診断

Azure Notification Hubs に関してよく寄せられる質問の 1 つに、アプリケーションからの通知がクライアント デバイスに表示されない場合のトラブルシューティングの方法があります。 お客様は、通知が欠落した場所と理由、さらに問題の解決方法を知りたいと考えます。 この記事では、通知が欠落する、つまりデバイスで受信されない理由を特定します。 また、根本原因を特定する方法についても説明します。

まずは、Notification Hubs がデバイスに通知をプッシュする方法を理解しておくことが重要です。

![Notification Hubs のアーキテクチャ][0]

一般的な送信通知フローでは、メッセージは *アプリケーション バックエンド* から Notification Hubs に送信されます。 Notification Hubs は、すべての登録を処理します。 この処理では、構成済みのタグとタグ式を考慮してターゲットが決定されます。 ターゲットは、プッシュ通知を受け取る必要がある登録です。 Microsoft がサポートしているプラットフォーム、つまり、Android、Baidu (中国の Android デバイス)、Fire OS (Amazon)、iOS、Windows、Windows Phone がこれらの登録の対象となる可能性があります。

ターゲットが確立されると、Notification Hubs は、デバイス プラットフォームの *プッシュ通知サービス* に通知をプッシュします。 例としては、iOS および macOS の Apple Push Notification Service (APNs) や、Android デバイスの Firebase Cloud Messaging (FCM) があります。 Notification Hubs のプッシュ通知は、複数の登録バッチに分割されます。 Azure portal の **[Configure Notification Hub]\(通知ハブを構成する\)** で設定された資格情報に基づいて、それぞれのプッシュ通知サービスを認証します。 その後、プッシュ通知サービスが各 *クライアント デバイス* に通知を転送します。

通知の配信の最終段階は、プラットフォーム プッシュ通知サービスとデバイスの間です。 通知の配信は、プッシュ通知プロセスの 4 つ (クライアント、アプリケーション バックエンド、Notification Hubs、プラットフォームのプッシュ通知サービス) のいずれの段階でも、エラーとなる可能性があります。 Notification Hubs のアーキテクチャの詳細については、[Notification Hubs の概要]に関する記事をご覧ください。

通知の配信エラーは、初期のテストまたはステージングのフェーズで発生する可能性があります。 このフェーズでの通知の欠落は、構成に問題があることを示している可能性があります。 運用環境で通知の配信エラーが発生した場合、通知の一部またはすべてが欠落することがあります。 この場合、アプリケーションまたはメッセージングのパターンに関して、より難しい問題があることを示しています。

次のセクションでは、通知が欠落するシナリオを、一般的なケースから稀なケースまで見ていきます。

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs の構成の誤り

各プッシュ通知サービスに通知を送信できるようにするには、Notification Hubs を自身のアプリケーションのコンテキストで認証する必要があります。 ターゲット プラットフォームの通知サービス (Microsoft、Apple、Google など) で開発者アカウントを作成する必要があります。 次に、ターゲットのプラットフォーム通知システムの操作に使用するトークンまたはキーを取得する OS に、自身のアプリケーションを登録する必要があります。

Azure Portal にプラットフォームの資格情報を追加する必要があります。 通知がデバイスに届いていない場合は、まず Notification Hubs に正しい資格情報が構成されていることを確認します。 この資格情報は、プラットフォーム固有の開発者アカウントで作成されたアプリケーションと一致している必要があります。

このプロセスを実行するための詳細な手順については、[Azure Notification Hubs の使用]に関する記事をご覧ください。

よくある構成の誤りをいくつか次に示します。

### <a name="notification-hub-name-location"></a>通知ハブの名前の場所

Notification Hubs 名にタイプミスがなく、次の各場所で同じであることを確認します。

* クライアントから登録する場所
* バックエンドから通知を送信する場所
* プッシュ通知サービスの資格情報を構成した場所

クライアントとアプリケーション バックエンドで、適切な共有アクセス署名の構成文字列を使用していることを確認します。 原則として、クライアントでは **DefaultListenSharedAccessSignature** を、アプリケーション バックエンドでは **DefaultFullSharedAccessSignature** を使用する必要があります。 これにより、Notification Hubs に通知を送信する権限が付与されます。

### <a name="apn-configuration"></a>APN の構成

運用環境とテスト用に 2 つの異なるハブを維持する必要があります。 サンドボックス環境で使用する証明書と運用環境で使用する証明書/ハブを、それぞれ別のハブにアップロードする必要があります。 異なる種類の証明書を同じハブにアップロードしないでください。 通知エラーの原因になります。

異なる種類の証明書を誤って同じハブにアップロードした場合には、そのハブを削除し、新しいハブで新たに開始する必要があります。 何らかの理由でハブを削除できない場合は、少なくとも、既存のすべての登録をハブから削除する必要があります。

### <a name="fcm-configuration"></a>FCM の構成

1. Firebase から取得した *サーバー キー* が、Azure portal で登録したサーバー キーと一致していることを確認します。

   ![Firebase サーバー キー][3]

2. クライアントに **[プロジェクト ID]** が構成されていることを確認します。 **[プロジェクト ID]** の値は Firebase のダッシュボードから取得できます。

   ![Firebase プロジェクト ID][1]

## <a name="application-issues"></a>アプリケーションの問題

### <a name="tags-and-tag-expressions"></a>タグとタグ式

タグまたはタグ式を使用して対象ユーザーを分割している場合、通知の送信時にターゲットを見つけられない場合があります。 このエラーは、送信呼び出しで指定したタグまたはタグ式に基づくものです。

通知の送信時に、登録を確認してタグが一致することを確認してください。 その後、これらの登録が存在するクライアントからのみ通知を受け取っていることを確認してください。

たとえば、Notification Hubs での登録すべてで "政治" タグを使用しているとします。 その場合に "スポーツ" タグを付けて通知を送信しても、その通知はどのデバイスにも送信されません。 複雑なケースとしては、"Tag A" *or* "Tag B" というタグ式を使用して登録したにもかかわらず、"Tag A && Tag B" をターゲットとした、という例が挙げられます。 この記事の後半の「自己診断のヒント」セクションで、登録とそのタグを確認する方法について説明します。

### <a name="template-issues"></a>テンプレートの問題

テンプレートを使用する場合は、「[テンプレート]」に説明されているガイドラインに従ってください。

### <a name="invalid-registrations"></a>無効な登録

通知ハブが適切に構成されている状態で、タグやタグ式が適切に使用されている場合は、有効なターゲットが見つかります。 通知はこれらのターゲットに送信する必要があります。 その後、Notification Hubs は複数の処理バッチを並列で実行します。 各バッチは登録のセットにメッセージを送信します。

> [!NOTE]
> Notification Hubs ではバッチが並列で実行されるため、通知の配信順序は保証されません。

Notification Hubs は、"最大 1 回" のメッセージ配信モデルに最適化されています。 つまり、デバイスに同じ通知が複数回配信されないように、重複の除去を行います。 メッセージをプッシュ通知サービスに送信する前に、デバイス ID ごとにメッセージが 1 回だけ送信されることを確実にするために登録がチェックされます。

各バッチはプッシュ通知サービスに送信され、ここで登録が受け入れられ検証されます。 この処理の間、プッシュ通知サービスがバッチ内の 1 つ以上の登録でエラーを検出する可能性があります。 この場合、プッシュ通知サービスが Notification Hubs にエラーを返し、プロセスが停止します。 プッシュ通知サービスは、そのバッチを完全に切断します。 これは、TCP ストリーム プロトコルを使用する APNs に特に当てはまります。

この場合、エラーが発生した登録はデータベースから削除されます。 その後、そのバッチで残りのデバイスに通知の配信が再試行されます。

登録デバイスに対して試行された配信が失敗したときのエラー情報の詳細は、Notification Hubs REST API を使用して取得できます。[メッセージごとのテレメトリ: 通知メッセージのテレメトリを取得する](/rest/api/notificationhubs/get-notification-message-telemetry)に関するページと「[PNS フィードバック](/previous-versions/azure/reference/mt705560(v=azure.100))」をご覧ください。 サンプル コードについては、[REST の送信例](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/)に関する記事をご覧ください。

## <a name="push-notification-service-issues"></a>プッシュ通知サービスの問題

プッシュ通知サービスは通知を受け取ると、その通知をデバイスに送信します。 この時点では、Notification Hubs は通知のデバイスへの配信に関して制御することはできません。

プラットフォーム通知サービスは堅牢であるため、通知は通常数秒でデバイスに届きます。 プッシュ通知サービスが調整中の場合は、Notification Hubs は指数バックオフ戦略を適用します。 プッシュ通知サービスが到達できない状態が 30 分間続いた場合には、それらのメッセージが期限切れとなり、完全に欠落するポリシーが適用されます。

プッシュ通知サービスが通知の配信を試行したがデバイスがオフラインの場合、その通知はプッシュ通知サービスにより保存されます。 保存されるのは限られた期間のみです。 この通知は、デバイスが利用可能になるとデバイスに配信されます。

アプリごとに、最新の通知が 1 つのみ保存されます。 デバイスがオフラインの間に複数の通知が送信された場合、新しい通知が配信されるたびに前回の通知が破棄されます。 最新の通知のみを維持するこの動作は、APNs では *結合* と呼ばれ、FCM では *折りたたみ* と呼ばれます  (FCM は折りたたみキーを使用します)。デバイスが長時間オフラインのままになると、そのデバイス用に保存されている通知は破棄されます。 詳細については、「[APNs Overview]」 (APNs の概要) と「[FCM メッセージについて]」をご覧ください。

Notification Hubs では、汎用 SendNotification API を使用することで、HTTP ヘッダーを介して結合キーを渡すことができます。 たとえば、.NET SDK の場合、`SendNotificationAsync` を使用します。 SendNotification API では、それぞれのプッシュ通知サービスにそのまま渡される HTTP ヘッダーも使用します。

## <a name="self-diagnosis-tips"></a>自己診断のヒント

このセクションでは、Notification Hubs で通知の欠落の根本原因を診断する方法について説明します。

### <a name="verify-credentials"></a>資格情報の確認

#### <a name="push-notification-service-developer-portal"></a>プッシュ通知サービス開発者ポータル

各プッシュ通知サービス開発者ポータル (APNs、FCM, Windows Notification Service など) で資格情報を確認します。 詳細については、「[チュートリアル: Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム アプリに通知を送信する](./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)」を参照してください。

#### <a name="azure-portal"></a>Azure portal

資格情報を確認し、プッシュ通知サービス開発者ポータルから取得した資格情報とマッチングするには、Azure portal の **[アクセス ポリシー]** タブに移動します。

![Azure Portal のアクセス ポリシー][4]

### <a name="verify-registrations"></a>登録の確認

#### <a name="visual-studio"></a>Visual Studio

Visual Studio では、サーバー エクスプローラーから Azure に接続して、Notification Hubs を含む複数の Azure サービスを表示、管理できます。 このショートカットは、特に開発/テスト環境で便利です。

![Visual Studio のサーバー エクスプローラー][9]

![[サーバー エクスプローラー]](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

ハブのすべての登録を表示、管理することができます。 登録は、プラットフォーム、ネイティブまたはテンプレートの登録、タグ、プッシュ通知サービス識別子、登録 ID、有効期限ごとに分類することができます。 このページでは、登録を編集することもできます。 タグを編集する場合に特に便利です。

**サーバー エクスプローラー** で自分の通知ハブを右クリックして、**[診断]** を選択します。 

![Visual Studio - サーバー エクスプローラー: [診断] メニュー](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

次のページが表示されます。

![Visual Studio: [診断] ページ](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

**[デバイスの登録]** ページに切り替えます。

![Visual Studio: [デバイスの登録]](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

**[テスト送信]** ページを使用して、テスト通知メッセージを送信することができます。

![Visual Studio: [テスト送信]](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Visual Studio を使用した登録の編集は開発またはテスト中にのみ可能ですが、編集できる登録の数には制限があります。 登録を一括で修正する必要が生じた場合は、[登録の一括エクスポート方法と変更方法](/previous-versions/azure/azure-services/dn790624(v=azure.100))に関する記事に説明されている、登録のエクスポートおよびインポート機能を使用することを検討してください。

#### <a name="service-bus-explorer"></a>サービス バス Explorer

多くのお客様が [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) を使用して、通知ハブを表示、管理します。 Service Bus Explorer はオープンソースのプロジェクトです。 

### <a name="verify-message-notifications"></a>メッセージ通知の確認

#### <a name="azure-portal"></a>Azure portal

サービス バックエンドを稼働させずにクライアントにテスト通知を送信するには、**[サポート + トラブルシューティング]** から **[テスト送信]** を選択します。

![Azure のテスト送信機能][7]

#### <a name="visual-studio"></a>Visual Studio

Visual Studio からテスト通知を送信することもできます。

![Visual Studio のテスト送信機能][10]

Visual Studio サーバー エクスプローラーでの Notification Hubs の使用に関する詳細については、次の記事をご覧ください。

* [通知ハブのデバイス登録の表示方法](/previous-versions/windows/apps/dn792122(v=win.10))
* [詳細情報: Visual Studio 2013 Update 2 RC および Azure SDK 2.3]
* [Visual Studio 2013 Update 3 および Azure SDK 2.4 のリリースの発表]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>失敗した通知のデバッグと通知結果の確認

#### <a name="enabletestsend-property"></a>EnableTestSend プロパティ

Notification Hubs 経由で通知を送信すると、その通知は最初はキューに登録されます。 Notification Hubs は適切なターゲットを判断し、その後プッシュ通知サービスに通知を送信します。 REST API やクライアント SDK を使用している場合、送信呼び出しが返されても、それは Notification Hubs でメッセージがキューに追加されたということでしかありません。 Notification Hubs が最終的に通知をプッシュ通知サービスに送信したらどうなるかについての情報は提供されません。

通知がクライアント デバイスに届かない場合、Notification Hubs がプッシュ通知サービスに通知を配信しようとしたときにエラーが発生した可能性があります。 たとえば、ペイロードのサイズがプッシュ通知サービスで許容される最大サイズを超えた、あるいは Notification Hubs に構成されている資格情報が有効でない、などです。

プッシュ通知サービス エラーに関する情報を取得するには、[EnableTestSend] プロパティを使用できます。 このプロパティは、ポータルまたは Visual Studio クライアントからテスト メッセージを送信するときに自動的に有効にされます。 このプロパティは、詳細なデバッグ情報の参照に使用できます。また、API を介して使用することもできます。 現時点では、.NET SDK で使用できます。 最終的には、すべてのクライアント SDK に追加される予定です。

`EnableTestSend` プロパティを REST 呼び出しで使用するには、送信呼び出しの最後に *test* というクエリ文字列パラメーターを追加します。 次に例を示します。

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>.NET SDK の例

.NET SDK を使用してネイティブ ポップアップ (トースト) 通知を送信する例を次に示します。

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

実行の最後で、`result.State` が `Enqueued` と出力するだけです。 結果からは、プッシュ通知への処理に関する情報は得られません。

次に、`EnableTestSend` ブール値プロパティを使用できます。 `NotificationHubClient` の初期化中に `EnableTestSend` プロパティを使用すると、通知の送信時に発生したプッシュ通知サービス エラーに関する詳細な状態を取得できます。 送信呼び出しが返されるまで時間がかかります。それは、まず Notification Hubs による通知のプッシュ通知サービスへの配信が必要であるためです。

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>サンプル出力

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

このメッセージは、Notification Hubs に構成されている資格情報が無効であること、または、ハブ内の登録に問題があること、のいずれかを示しています。 メッセージを送信する前に、この登録を削除し、クライアントで登録を再作成してください。

> [!NOTE]
> `EnableTestSend` プロパティの使用は厳格に調整されます。 このオプションは、開発環境またはテスト環境でのみ、限られた登録で使用してください。 デバッグ通知は 10 個のデバイスにのみ送信されます。 また、1 分あたりに処理できるデバッグ送信は 10 件に制限されています。

### <a name="review-telemetry"></a>統計情報の確認

#### <a name="azure-portal"></a>Azure portal

このポータルでは、Notification Hubs のすべてのアクティビティの概要を簡単に確認できます。

1. **[概要]** タブでは、登録、通知、およびエラーが統合されたビューを、プラットフォームごとに確認できます。

   ![Notification Hubs の概要ダッシュボード][5]

2. **[モニター]** タブでは、理解を深めるために、プラットフォーム固有のその他多数のメトリックを追加できます。 Notification Hubs がプッシュ通知サービスに通知を送信しようとするときに返されるエラーを重点的に確認できます。

   ![Azure Portal のアクティビティ ログ][6]

3. **受信メッセージ**、**登録操作**、**正常通知** を確認することから始めます。 次に、プラットフォームごとのタブに移動して、プッシュ通知サービスに固有のエラーを確認します。

4. 通知ハブの認証設定が正しくない場合、**PNS 認証エラー** のメッセージが表示されます。 プッシュ通知サービスの資格情報を確認してください。

#### <a name="programmatic-access"></a>プログラムによるアクセス

プログラムによるアクセスの詳細については、「[プログラムによるアクセス](/previous-versions/azure/azure-services/dn458823(v=azure.100))」をご覧ください。

> [!NOTE]
> 登録のエクスポートおよびインポートや、API を使用したテレメトリへのアクセスなど、テレメトリに関連する一部の機能は、Standard サービス レベルでのみ使用できます。 Free または Basic サービス レベルでこれらの機能を使用しようとすると、SDK を使用している場合は例外メッセージが表示されます。 REST API から直接これらの機能を使用している場合は、HTTP 403 (アクセス不可) エラーが表示されます。
>
> テレメトリに関連する機能を使用するには、まず Azure portal で、Standard サービス レベルを使用していることを確認してください。  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Notification Hubs の概要]: notification-hubs-push-notification-overview.md
[Azure Notification Hubs の使用]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[テンプレート]: /previous-versions/azure/azure-services/dn530748(v=azure.100)
[APNs の概要]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[FCM メッセージについて]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: /previous-versions/azure/azure-services/dn790624(v=azure.100)
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: /previous-versions/windows/apps/dn792122(v=win.10)
[詳細情報: Visual Studio 2013 Update 2 RC および Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Visual Studio 2013 Update 3 および Azure SDK 2.4 のリリースの発表]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: /dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend
[Programmatic telemetry access]: /previous-versions/azure/azure-services/dn458823(v=azure.100)
