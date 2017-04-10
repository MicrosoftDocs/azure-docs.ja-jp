---
title: "Azure 通知ハブ"
description: "Azure Notification Hubs を使用してプッシュ通知機能を追加する方法を説明します。"
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: 
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 1/17/2017
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 58458cdc99cc8f78e63c9640a7c71a8ce5dc70f1
ms.openlocfilehash: a1be0b13cd1feb582a23965df142e44d90ac6851


---
# <a name="azure-notification-hubs"></a>Azure 通知ハブ
## <a name="overview"></a>概要
Azure Notification Hubs は、使いやすいマルチプラットフォーム対応のスケール アウトされたプッシュ エンジンを提供します。 プラットフォームをまたぐ単一の API 呼び出しで、任意のバックエンド (クラウドまたはオンプレミス) から任意のモバイル プラットフォームに、対象を指定して個人用に設定したプッシュ通知を送信できます。

Notification Hubs は、エンタープライズ向けとコンシューマー向けのどちらのシナリオにも適しています。 以下に、Notification Hubs の主な用途をいくつか示します。

* ニュース速報の通知を数百万台のデバイスに速やかに送信する。
* 場所に基づいたクーポンを対象のユーザー セグメントに送信する。
* イベント関連の通知を、メディア/スポーツ/金融/ゲーム アプリケーションのユーザーまたはグループに送信する。
* ユーザーの関心を惹き付けて利用を促すために、プロモーション コンテンツをアプリにプッシュする。
* 新着メッセージや作業項目などのエンタープライズ イベントをユーザーに通知する。
* 多要素認証のコードを送信する。

## <a name="what-are-push-notifications"></a>プッシュ通知とは?
プッシュ通知は、アプリからユーザーへの通信手段の&1; つで、モバイル アプリのユーザーが関心を持っている情報について、ポップアップやダイアログ ボックスなどで通知を受け取ることができる機能です。 通常、ユーザーはメッセージを表示するか無視するかを選択することができ、表示を選択した場合は該当するモバイル アプリが開きます。

プッシュ通知は、コンシューマー向けのアプリにとってはユーザーを惹き付け、アプリの使用率をアップさせるために不可欠であり、エンタープライズ向けのアプリにとっては、最新のビジネス情報を伝達するのに欠かすことのできない機能です。 モバイル デバイスにとってはエネルギー効率が良く、通知の送信者にとっては柔軟性が高く、対象アプリがアクティブでないときにも利用可能なことから、アプリからユーザーへの最適な通信方法といえます。

主なプラットフォームにおけるプッシュ通知の詳細については、それぞれ該当するリンク先のドキュメントを参照してください。
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>プッシュ通知の動作
プッシュ通知は、"*プラットフォーム通知システム*" (PNS: Platform Notification System) と呼ばれるプラットフォーム独自のインフラストラクチャを利用して配信されます。 PNS のプッシュ機能は、提供されたハンドルを使用してデバイスにメッセージを配信するという最小限のもので、共通のインターフェイスはありません。 iOS、Android、および Windows の各バージョンのアプリのすべてのユーザーに通知を送信するには、送信をバッチ処理する間に、開発者は APNS (Apple Push Notification Service)、FCM (Firebase Cloud Messaging)、および WNS (Windows Notification Service) を操作する必要があります。

次の図に、プッシュ機能の概要を示します。

1. クライアント アプリがプッシュを受信することを決定したら、対応する PNS にアクセスして、システムに固有の一時的なプッシュ ハンドルを取得します。 ハンドルの種類はシステムによって異なります (たとえば、WNS の場合は URI、APNS の場合はトークン)。
2. クライアント アプリは、このハンドルをアプリのバックエンドまたはプロバイダーに格納します。
3. プッシュ通知を送信する際には、アプリのバックエンドがハンドルを使用して PNS にアクセスし、特定のクライアント アプリを対象として指定します。
4. PNS はハンドルで指定されたデバイスに通知を転送します。

![][0]

## <a name="the-challenges-of-push-notifications"></a>プッシュ通知の課題
PNS は高度な機能を備えていますが、ブロードキャストやセグメント化されたユーザーへのプッシュ通知の送信などの一般的なプッシュ通知シナリオを実装するだけでも、アプリケーション開発者は多くの課題を克服する必要があります。

プッシュ機能は、アプリの主要なビジネス ロジックとは無関係の複雑なインフラストラクチャを必要とするため、モバイル向けクラウド サービスの中でも最もリクエストの多い機能の&1; つです。 インフラストラクチャの課題には、次のようなものがあります。

* **プラットフォーム依存**: 

  * PNS が統一されていないので、さまざまなプラットフォームのデバイスに通知を送信するためには、複雑で保守が難しい、プラットフォームに依存するロジックをバックエンドで用意する必要があります。
* **スケール**:

  * PNS のガイドラインに従って、アプリが起動するたびにデバイス トークンを更新する必要があります。 そのため、トークンを最新の状態に維持するためだけに、バックエンドが大量のトラフィックとデータベース アクセスを扱うことになります。 デバイスの数が数億台から数十億台にまで増加すると、このインフラストラクチャの作成と保守にかかるコストも膨大になります。
  * ほとんどの PNS は、複数のデバイスに対するブロードキャストをサポートしていません。 そのため、100 万台のデバイスにブロードキャストするだけで、PNS への呼び出しが&100; 万回発生することになります。 この量のトラフィックを最小限の遅延でスケーリングするのは簡単なことではありません。
* **ルーティング**:
  
  * PNS はデバイスにメッセージを送信する機能を備えていますが、ほとんどのアプリの通知はユーザーや特定のグループを対象としています。 そのため、特定のグループやユーザー、プロパティなどにデバイスを関連付けるレジストリをバックエンドで維持する必要があります。この対策を行うことで、市場投入までの開発期間が長引き、アプリケーションの保守コストも増大します。

## <a name="why-use-notification-hubs"></a>Notification Hubs を使用する理由は?
Notification Hubs を使用すると、プッシュ機能の独自運用に関わる複雑さをすべて排除することができます。 マルチプラットフォーム対応のスケール アウトされたプッシュ通知インフラストラクチャを利用できるので、プッシュ関連のコードを削減してバックエンドを簡略化できます。 Notification Hubs では、次の図に示すように、デバイス側の処理は PNS ハンドルをハブに登録するだけであり、バックエンドによってメッセージがユーザーまたは対象グループに送信されます。

![][1]

Notification Hubs はすぐに利用できるプッシュ エンジンであり、次のメリットがあります。

* **クロス プラットフォーム**

  * iOS、Android、Windows のほか、Kindle や Baidu を含むすべての主要なプッシュ プラットフォームをサポートします。
  * すべてのプラットフォームに通知を送信する共通のインターフェイスを備えており、通知をプラットフォームの独自形式で送信することも、プラットフォームに依存しない形式で送信することもできます。プラットフォーム固有の対応は必要ありません。
  * デバイス ハンドルの管理は&1; か所で行えます。
* **クロス バックエンド**
  
  * クラウドまたはオンプレミスの
  * .NET、Node.js、Java などに対応しています。
* **豊富な配信パターン**:

  * *1 つまたは複数のプラットフォームへのブロードキャスト*: プラットフォームをまたぐ数百万台のデバイスへのブロードキャストを単一の API 呼び出しで速やかに実行できます。
  * *デバイスへのプッシュ*: 個々のデバイスを対象に通知を送信できます。
  * *ユーザーへのプッシュ*: タグとテンプレートを使用して、ユーザーがどのプラットフォームのデバイスを使用していても通知を送信できます。
  * *動的タグによるセグメントへのプッシュ*: タグを使ってデバイスをセグメント化し、必要に応じて&1; つのセグメントや条件を満たすセグメント (たとえば、アクティブ ユーザーかつシアトル在住、ただし新規ユーザーは除く) にプッシュできます。 パブリッシャー/サブスクライバー間に制限されず、デバイス タグはいつでもどこでも更新することができます。
  * *プッシュのローカライズ*: テンプレートを使用して、バックエンド コードに影響を与えずにローカライズを実現します。
  * *サイレント プッシュ*: サイレント通知をデバイスに送信して特定のプル (アクション) の実行をトリガーすることで、push-to-pull パターンを有効にできます。
  * *プッシュのスケジュール*: 通知を送信する日時をスケジュールできます。
  * *ダイレクト プッシュ*: デバイスをサービスに登録することなく、直接デバイス ハンドルの一覧へのプッシュをバッチ処理できます。
  * *個人用に設定したプッシュ*: デバイス プッシュ変数により、カスタマイズしたキーと値のペアを使用して、デバイス固有の個人用に設定したプッシュ通知を送信できます。
* **豊富なテレメトリ**
  
  * プッシュ、デバイス、エラー、および操作に関する全般的なテレメトリを、Azure Portal とプログラムで使用できます。
  * メッセージごとのテレメトリは、サービスへの最初の要求呼び出しから各プッシュを追跡します。これにより、プッシュが正常にバッチ処理されて送信されたかどうかがわかります。
  * プラットフォーム通知システムのフィードバックは、プラットフォーム通知システムからのフィードバックをすべて伝達します。このフィードバックはデバッグに役立ちます。
* **拡張性** 
  
  * 再構築やデバイスのシャーディングをすることなく、何百万台ものデバイスにメッセージをすばやく送信します。
* **セキュリティ**

  * 共有アクセス シークレット (SAS) またはフェデレーション認証。

## <a name="integration-with-app-service-mobile-apps"></a>App Service Mobile Apps との統合
Azure のサービス間でシームレスかつ同一のエクスペリエンスを実現するために、 [App Service Mobile Apps] には Notification Hubs を使用したプッシュ通知のサポートが組み込まれています。 [App Service Mobile Apps] は、拡張性が高くグローバルに使用可能な、企業の開発者やシステム インテグレーター向けのモバイル アプリ開発プラットフォームで、モバイル開発者に豊富な機能のセットを提供します。

Mobile Apps の開発者は、次のワークフローで Notification Hubs を使用します。

1. デバイスの PNS ハンドルを取得する
2. 便利な Mobile Apps クライアント SDK 登録 API を介して Notification Hubs でデバイスを登録する
   * Mobile Apps ではセキュリティの目的で登録時にすべてのタグを除去することに注意してください。 タグをデバイスに関連付けるには、Notification Hubs をバックエンドから直接操作します。
3. Notification Hubs を使用してアプリのバックエンドから通知を送信する

この統合は、開発者にいくつかの便利な機能を提供します。

* **Mobile Apps クライアント SDK**: これらのマルチプラットフォーム SDK は登録用のシンプルな API を提供し、モバイル アプリにリンクされている通知ハブと自動的に対話します。 開発者は Notification Hubs の資格情報を掘り下げて追加のサービスを操作する必要はありません。

  * *ユーザーへのプッシュ*: SDK が自動的に指定のデバイスを Mobile Apps の認証済みユーザー ID にタグ付けして、ユーザーへのプッシュ シナリオを有効にします。
  * *デバイスへのプッシュ*: SDK が自動的に Mobile Apps のインストール ID を Notification Hubs に登録する GUID として使用するので、開発者が複数サービスの GUID を維持する手間が省けます。
* **インストール モデル**: Mobile Apps は Notification Hubs の最新のプッシュ モデルと簡単に連携し、プッシュ通知サービスと連携する JSON インストール内のデバイスに関連付けられたすべてのプッシュ プロパティを表します。
* **柔軟性**: 開発者は統合中でも常に Notification Hubs を直接操作することを選択できます。
* **[Azure Portal] の統合されたエクスペリエンス**: 機能としてのプッシュが Mobile Apps に視覚的に表示され、開発者は Mobile Apps を通じて関連付けられた通知ハブを簡単に操作できます。

## <a name="next-steps"></a>次のステップ
Notification Hubs の詳細については、次のトピックを参照してください。

* **[Notification Hubs の機能と特徴]**
* **[Notification Hubs のチュートリアルとガイド]**
* **Notification Hubs の使用チュートリアル** ([iOS]、[Android]、[Windows ユニバーサル]、[Windows Phone]、[Kindle]、[Xamarin.iOS]、[Xamarin.Android])

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png
[Notification Hubs の機能と特徴]: http://azure.microsoft.com/services/notification-hubs
[Notification Hubs のチュートリアルとガイド]: http://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows ユニバーサル]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure Portal]: https://portal.azure.com
[tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)



<!--HONumber=Feb17_HO1-->


