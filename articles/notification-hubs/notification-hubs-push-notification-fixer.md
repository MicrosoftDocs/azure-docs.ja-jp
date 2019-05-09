---
title: Azure Notification Hubs - 欠落した通知の診断
description: Azure Notification Hubs で欠落した通知に関する一般的な問題を診断する方法について説明します。
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/04/2019
ms.author: jowargo
ms.openlocfilehash: 4af86025e714c65d0ae225b271a2d0970bb96ee8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281643"
---
# <a name="azure-notification-hubs---diagnose-dropped-notifications"></a>Azure Notification Hubs - 欠落した通知の診断

Azure Notification Hubs のお客様からよく寄せられる質問の 1 つに、アプリケーションから送信された通知がクライアント デバイスに表示されない場合のトラブルシューティングの方法があります。 お客様は、通知が欠落した場所と理由、さらに問題の解決方法を知りたいと考えます。 この記事では、通知が欠落する、つまりデバイスで受信されない理由を特定します。 根本原因を分析し、特定する方法を説明します。

最初に、Notification Hubs サービスがデバイスにどのようにプッシュ通知を送信するかを理解しておくことが重要です。

![Notification Hubs のアーキテクチャ][0]

一般的な送信通知フローでは、メッセージは*アプリケーション バックエンド*から Notification Hubs に送信されます。 Notification Hubs は、すべての登録に対して処理を行います。 この処理では、構成済みのタグとタグ式を考慮して "ターゲット" が決定されます。 ターゲットは、プッシュ通知を受け取る必要があるすべての登録です。 これらの登録は、iOS、Google、Windows、Windows Phone、Kindle、Baidu for China Android など、サポートされているプラットフォームのいずれかまたはすべてにわたる可能性があります。

ターゲットが確立されると、Notification Hubs サービスは、デバイス プラットフォームの*プッシュ通知サービス*にプッシュ通知を送信します。 例としては、Apple の Apple Push Notification Service (APNs) や、Google の Firebase Cloud Messaging (FCM) があります。 Notification Hubs のプッシュ通知は、複数の登録バッチに分割されます。 Notification Hubs は、Azure Portal の **[Configure Notification Hub]\(通知ハブを構成する\)** で設定された資格情報に基づいて、それぞれのプッシュ通知サービスを認証します。 その後、プッシュ通知サービスが各*クライアント デバイス*に通知を転送します。

通知の配信の最終段階は、プラットフォーム プッシュ通知サービスとデバイスの間で実行されます。 プッシュ通知プロセスの 4 つの主なコンポーネント (クライアント、アプリケーション バックエンド、Notification Hubs、プラットフォーム プッシュ通知サービス) のいずれかが、通知が欠落する原因となる場合があります。 Notification Hubs のアーキテクチャの詳細については、[Notification Hubs の概要]に関する記事をご覧ください。

通知の配信エラーは、初期のテストまたはステージングのフェーズで発生する可能性があります。 このフェーズでの通知の欠落は、構成に問題があることを示している可能性があります。 運用環境で通知の配信エラーが発生した場合、通知の一部またはすべてが欠落することがあります。 この場合、アプリケーションまたはメッセージングのパターンに関して、より難しい問題があることを示しています。

次のセクションでは、通知が欠落するシナリオを、一般的なケースから稀なケースまで見ていきます。

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs の構成の誤り

各プッシュ通知サービスに通知を正常に送信するには、Notification Hubs サービスが、開発者のアプリケーションのコンテキストで自身を認証する必要があります。 開発者は各プラットフォーム (Google、Apple、Windows など) で開発者アカウントを作成します。 次に、開発者は資格情報を取得するプラットフォームに自身のアプリケーションを登録します。

Azure Portal にプラットフォームの資格情報を追加する必要があります。 通知がデバイスに届いていない場合は、まず Notification Hubs に正しい資格情報が構成されていることを確認する必要があります。 この資格情報は、プラットフォーム固有の開発者アカウントで作成されたアプリケーションと一致している必要があります。

このプロセスを実行するための詳細な手順については、[Azure Notification Hubs の使用]に関する記事をご覧ください。

よくある構成の誤りをいくつか次に示します。

**全般**

Notification Hubs 名にタイプミスがなく、次の各場所で同じであることを確認します。
   * クライアントから登録する場所
   * バックエンドから通知を送信する場所
   * プッシュ通知サービスの資格情報を構成した場所

クライアントとアプリケーション バックエンドで、適切な共有アクセス署名の構成文字列を使用していることを確認します。 原則として、クライアントでは **DefaultListenSharedAccessSignature** を、アプリケーション バックエンドでは **DefaultFullSharedAccessSignature** を使用する必要があります (これにより、Notification Hubs に通知を送信する権限が付与されます)。

**APN の構成**

運用環境用とテスト用に 2 つの異なるハブを用意する必要があります。 つまり、サンドボックス環境で使用する証明書と運用環境で使用する証明書を、それぞれ別のハブにアップロードする必要があります。 異なる種類の証明書を同じハブにアップロードしないでください。 通知エラーの原因になる場合があります。

異なる種類の証明書を誤って同じハブにアップロードした場合には、そのハブを削除し、新しいハブで新たに開始することをお勧めします。 何らかの理由でハブを削除できない場合は、少なくとも、既存のすべての登録をハブから削除する必要があります。

**FCM の構成**

1. Firebase から取得した *"サーバー キー"* が、Azure Portal で登録したサーバー キーと一致していることを確認します。

   ![Firebase サーバー キー][3]

2. クライアントに **[プロジェクト ID]** が構成されていることを確認します。 **[プロジェクト ID]** の値は Firebase のダッシュボードから取得できます。

   ![Firebase プロジェクト ID][1]

## <a name="application-issues"></a>アプリケーションの問題

**タグとタグ式**

タグまたはタグ式を使用して対象ユーザーを分割している場合、通知の送信時に、送信呼び出しで指定したタグまたはタグ式に基づいてターゲットを見つけられない場合があります。

通知の送信時に、登録を確認して一致するタグがあることを確認してください。 その後、これらの登録が存在するクライアントからのみ通知を受け取っていることを確認してください。

たとえば、Notification Hubs での登録を、すべて "ポリシー" タグを使用して行い、"スポーツ" タグを付けて通知を送信した場合、通知はどのデバイスにも送信されません。 複雑なケースには、"Tag A" OR "Tag B" というタグ式を使用して登録したにもかかわらず、送信時に "Tag A && Tag B" を対象としているといった例が挙げられます。 この記事の後半の「自己診断のヒント」セクションで、登録とそのタグを確認する方法について説明します。

**テンプレートの問題**

テンプレートを使用する場合は、「[テンプレート]」に説明されているガイドラインに従ってください。

**無効な登録**

通知ハブが適切に構成されている場合、また、タグやタグ式が適切に使用されている場合は、有効なターゲットが見つかります。 通知はこれらのターゲットに送信する必要があります。 Notification Hubs サービスは次に、複数のバッチ処理を並行して起動します。 各バッチは登録のセットにメッセージを送信します。

> [!NOTE]
> 処理は並列で実行されるため、通知の配信順序は保証されません。

Notification Hubs は、"最大 1 回" のメッセージ配信モデルに最適化されています。 つまり、デバイスに同じ通知が複数回配信されないように、重複の除去を行います。 これを確実に実行するために、メッセージをプッシュ通知サービスに送信する前に、登録をチェックして、デバイス ID ごとにメッセージが 1 回だけ送信されることを確認します。

各バッチがプッシュ通知サービスに送信され、その後、プッシュ通知サービスが登録を受け入れて検証するため、プッシュ通知サービスがバッチ内の 1 つ以上の登録でエラーを検出する可能性があります。 この場合、プッシュ通知サービスが Notification Hubs にエラーを返し、プロセスが停止します。 プッシュ通知サービスは、そのバッチを完全に切断します。 これは、TCP ストリーム プロトコルを使用する APNS に特に当てはまります。

1 回のみの送信に最適化されています。 ただしこの場合、エラーが発生した登録はデータベースから削除されます。 その後、そのバッチで残りのデバイスに通知の配信が再試行されます。

登録デバイスに対して試行された配信が失敗したときのエラー情報の詳細は、Azure Notification Hubs REST API を使用して取得できます。「[Per Message Telemetry: Get Notification Message Telemetry (メッセージごとのテレメトリ: 通知メッセージのテレメトリを取得する)](https://msdn.microsoft.com/library/azure/mt608135.aspx)」と「[PNS Feedback (PNS フィードバック)](https://msdn.microsoft.com/library/azure/mt705560.aspx)」をご覧ください。 サンプル コードについては、[REST の送信例](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample)に関する記事をご覧ください。

## <a name="push-notification-service-issues"></a>プッシュ通知サービスの問題

プラットフォーム プッシュ通知サービスで通知メッセージが受け取られたら、その通知をデバイスに配信するのはプッシュ通知サービスの役割です。 この時点で、Notification Hubs サービスは無関係であり、通知がデバイスに配信されるタイミングや配信されるかどうかを制御できません。

プラットフォーム通知サービスは堅牢であるため、通知は通常数秒でプッシュ通知サービスからデバイスに届きます。 プッシュ通知サービスが調整中の場合は、Notification Hubs サービスでは指数バックオフ戦略が適用されます。 プッシュ通知サービスが到達できない状態が 30 分間続いた場合には、それらのメッセージが期限切れとなり、完全に欠落するポリシーが適用されます。

プッシュ通知サービスが通知の配信を試行したがデバイスがオフラインの場合、その通知は特定の期間プッシュ通知サービスに保存されます。 この通知は、デバイスが利用可能になるとデバイスに配信されます。

アプリごとに、最新の通知が 1 つのみ保存されます。 デバイスがオフラインの間に複数の通知が送信された場合、新しい通知が配信されるたびに以前の通知が破棄されます。 最新の通知のみを維持するこの動作は、APNs では "*結合通知*" と呼ばれ、FCM では "*折りたたみ*" と呼ばれます (これには、折りたたみキーを使用します)。 デバイスが長時間オフラインのままになると、デバイスに保存されている通知は破棄されます。 詳しくは、APN の概要に関する記事と、「[FCM メッセージについて]」をご覧ください。

Azure Notification Hubs では、汎用 SendNotification API を使用することで、HTTP ヘッダーを介して結合キーを渡すことができます。 たとえば、.NET SDK の場合、`SendNotificationAsync` を使用します。 SendNotification API では、それぞれのプッシュ通知サービスにそのまま渡される HTTP ヘッダーも使用します。

## <a name="self-diagnosis-tips"></a>自己診断のヒント

このセクションでは、Notification Hubs で通知の欠落の根本原因を診断する方法について説明します。

### <a name="verify-credentials"></a>資格情報の確認

**プッシュ通知サービス開発者ポータル**

各プッシュ通知サービス開発者ポータル (APNs、FCM, Windows Notification Service など) で資格情報を確認します。 詳細については、[Azure Notification Hubs の使用]に関する記事をご覧ください。

**Azure ポータル**

資格情報を確認し、プッシュ通知サービス開発者ポータルから取得した資格情報とマッチングするには、Azure Portal の **[アクセス ポリシー]** タブに移動します。

![Azure Portal のアクセス ポリシー][4]

### <a name="verify-registrations"></a>登録の確認

**Visual Studio**

開発で Visual Studio を使用している場合は、サーバー エクスプローラーから Azure に接続して、Notification Hubs を含む複数の Azure サービスを表示、管理できます。 これは、特に開発/テスト環境で便利です。

![Visual Studio のサーバー エクスプローラー][9]

ハブのすべての登録を表示、管理でき、プラットフォーム、ネイティブまたはテンプレートの登録、タグ、プッシュ通知サービス識別子、登録 ID、有効期限ごとに見やすく分類されています。 このページでは、登録を編集することもできます。 タグを編集する場合に特に便利です。

**サーバー エクスプローラー**で自分の**通知ハブ**を右クリックして、**[診断]** を選択します。 

![Visual Studio - サーバー エクスプローラー - [診断] メニュー](./media/notification-hubs-diagnosing/diagnose-menu.png)

次のページが表示されます。 

![Visual Studio - [診断] ページ](./media/notification-hubs-diagnosing/diagnose-page.png)

**[デバイスの登録]** ページに切り替えます。 

![Visual Studio のデバイス登録](./media/notification-hubs-diagnosing/VSRegistrations.png)

**[テスト送信]** ページを使用して、テスト通知メッセージを送信することができます。

![Visual Studio - [テスト送信]](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Visual Studio を使用した登録の編集は開発またはテスト中にのみ可能ですが、編集できる登録の数には制限があります。 登録を一括で修正する必要が生じた場合は、[登録の一括エクスポートと変更](https://msdn.microsoft.com/library/dn790624.aspx)に関する記事に説明されている、登録のエクスポート/インポート機能を使用することを検討してください。

**サービス バス Explorer**

多くのお客様が [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) を使用して、通知ハブを表示、管理します。 Service Bus Explorer はオープンソースのプロジェクトです。 

### <a name="verify-message-notifications"></a>メッセージ通知の確認

**Azure ポータル**

サービス バックエンドを稼働させずにクライアントにテスト通知を送信するには、**[サポート + トラブルシューティング]** から **[テスト送信]** を選択します。

![Azure のテスト送信機能][7]

**Visual Studio**

Visual Studio からテスト通知を送信することもできます。

![Visual Studio のテスト送信機能][10]

Visual Studio サーバー エクスプローラーでの Notification Hubs の使用に関する詳細については、次の記事をご覧ください。

* [通知ハブのデバイス登録の表示]
* [詳細情報: Visual Studio 2013 Update 2 RC および Azure SDK 2.3]
* [Visual Studio 2013 Update 3 および Azure SDK 2.4 のリリースの発表]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>失敗した通知のデバッグと通知結果の確認

**EnableTestSend プロパティ**

Notification Hubs 経由で通知を送信すると、その通知は処理のために Notification Hubs キューに登録されます。 Notification Hubs は適切なターゲットを判断し、その後プッシュ通知サービスに通知を送信します。 REST API やクライアント SDK を使用している場合、送信呼び出しが正常に返されても、それは Notification Hubs でメッセージが正常にキューに追加されたということでしかありません。 Notification Hubs が最終的にメッセージをプッシュ通知サービスに送信したらどうなるかについての情報はありません。

通知がクライアント デバイスに届かない場合、Notification Hubs がプッシュ通知サービスにメッセージを配信しようとしたときにエラーが発生した可能性があります。 たとえば、ペイロードのサイズがプッシュ通知サービスで許容される最大サイズを超えた、あるいは Notification Hubs に構成されている資格情報が有効でない、などです。

プッシュ通知サービス エラーに関する情報を取得するには、[EnableTestSend] プロパティを使用できます。 このプロパティは、ポータルまたは Visual Studio クライアントからテスト メッセージを送信するときに自動的に有効にされます。 このプロパティを使用して、詳細なデバッグ情報を参照できます。 このプロパティは、API を介して使用することもできます。 現時点では、.NET SDK で使用できます。 最終的には、すべてのクライアント SDK に追加される予定です。

`EnableTestSend` プロパティを REST 呼び出しで使用するには、送信呼び出しの最後に *test* というクエリ文字列パラメーターを追加します。 例: 

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

**例 (.NET SDK)**

.NET SDK を使用してネイティブ ポップアップ (トースト) 通知を送信する例を次に示します。

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

実行の最後で、`result.State` が `Enqueued` と出力するだけです。 結果からは、プッシュ通知への処理に関する情報は得られません。

次に、`EnableTestSend` ブール値プロパティを使用できます。 `NotificationHubClient` の初期化中に `EnableTestSend` プロパティを使用すると、通知の送信時に発生したプッシュ通知サービス エラーに関する詳細な状態を取得できます。 送信呼び出しが返されるまで時間がかかります。それは、送信呼び出しが返されるのは、Notification Hubs がプッシュ通知サービスに通知を配信し、結果を判定した後だからです。

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

**サンプル出力**

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

このメッセージは、Notification Hubs に無効な資格情報が構成されていること、または、ハブ内の登録に問題があること、のいずれかを示しています。 メッセージを送信する前に、この登録を削除し、クライアントで登録を再作成することをお勧めします。

> [!NOTE]
> `EnableTestSend` プロパティの使用は厳格に調整されます。 このオプションは、開発環境またはテスト環境でのみ、限られた登録で使用してください。 デバッグ通知は 10 のデバイスのみに送信されます。 また、1 分あたりに処理できるデバッグ送信は 10 件に制限されています。

### <a name="review-telemetry"></a>統計情報の確認

**Azure ポータル**

このポータルでは、Notification Hubs のすべてのアクティビティの概要を簡単に確認できます。

1. **[概要]** タブでは、登録、通知、およびエラーが統合されたビューを、プラットフォームごとに確認できます。

   ![Notification Hubs の概要ダッシュボード][5]

2. **[モニター]** タブでは、理解を深めるために、プラットフォーム固有のその他多数のメトリックを追加できます。 特に、Notification Hubs サービスがプッシュ通知サービスに通知を送信しようとしたときに返される、プッシュ通知サービスに関連したすべてのエラーを参照できます。

   ![Azure Portal のアクティビティ ログ][6]

3. **受信メッセージ**、**登録操作**、**正常通知**を確認することから始めます。 次に、プラットフォームごとのタブに移動して、プッシュ通知サービスに固有のエラーを確認します。

4. 通知ハブの認証設定が正しくない場合、**PNS 認証エラー**のメッセージが表示されます。 プッシュ通知サービスの資格情報を確認してください。

**プログラムによるアクセス**

プログラムによるアクセスの詳細については、[プログラムによるテレメトリへのアクセス]に関する記事をご覧ください。

> [!NOTE]
> 登録のエクスポートおよびインポートや、API を使用したテレメトリへのアクセスなど、テレメトリに関連する一部の機能は、Standard サービス レベルでのみ使用できます。 Free または Basic サービス レベルでこれらの機能を使用しようとすると、SDK を使用している場合は例外メッセージが、REST API から直接これらの機能を使用している場合は HTTP 403 (アクセス不可) エラーが表示されます。
>
> テレメトリに関連する機能を使用するには、まず Azure Portal で、Standard サービス レベルを使用していることを確認してください。  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Notification Hubs の概要]: notification-hubs-push-notification-overview.md
[Azure Notification Hubs の使用]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[テンプレート]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs overview]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[FCM メッセージについて]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[通知ハブのデバイス登録の表示]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[詳細情報: Visual Studio 2013 Update 2 RC および Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Visual Studio 2013 Update 3 および Azure SDK 2.4 のリリースの発表]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[プログラムによるテレメトリへのアクセス]: https://msdn.microsoft.com/library/azure/dn458823.aspx
