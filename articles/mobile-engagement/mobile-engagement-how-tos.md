---
title: "Azure モバイル エンゲージメント ユーザー インターフェイス - Reach (リーチ) 方法"
description: "Azure モバイル エンゲージメントのユーザー インターフェイスの概要"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 30af87e6-c816-4cce-8609-6cbd3e83de14
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 3a423e42441c6b3b2f1917ba02f80078c759ec69
ms.openlocfilehash: 4c114f470631ae6b06968c3cc8601bc1c76bc1da
ms.lasthandoff: 02/02/2017


---
# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>エンドユーザーに通知するためのプッシュの利用と管理を始める方法
SDK とアプリを完全統合したら、UI のリーチ セクションでアプリのユーザーにプッシュ通知を実行できます。  

## <a name="do-your-first-push-notification-campaign"></a>最初のプッシュ通知キャンペーンの実行
* リーチが SDK でアプリに統合されていることを確認します。 
* アプリケーションを選択します。

![First1][1]

* [リーチ] セクションに移動し、[新しいアナウンスメント] をクリックします。

![First2][2]

* 新しいキャンペーンを作成し、名前を付けます。
  
![First3][3]

* 通知の配信方法として、[アプリ内のみ] を選択します。

![First4][4]

* プッシュするメッセージを作成します。

![First5][5]

* 通知のタイトルを記述できます (省略可能)。
* プッシュ メッセージの内容を記述します。
* イメージをアップロードすることができます。 ファイルのサイズは 32,768 バイトを超えないようにしてください。
* 他のオプションを選択することもできますが、このチュートリアルの焦点を合わせるために、後で参照します。
* コンテンツの種類として [通知のみ] を選択します。

![First6][6]

* プッシュ キャンペーンを作成すると、キャンペーンの一覧に表示されます。

![First7][7]

## <a name="test-your-push-notification-campaign"></a>プッシュ通知キャンペーンのテスト
![Test1][8]

* デバイスを登録します。
* プッシュするデバイスのチェック ボックスをオンにします。
* [テスト] ボタンをクリックしてデバイスにプッシュを送信します。

![Test2][9]

* キャンペーンをアクティブ化します。

![Test3][10]

* これでキャンペーンが作成されたので、ユーザーに通知をプッシュするにはキャンペーンのアクティブ化のみ必要です。

## <a name="send-personalized-pushes"></a>個人用に設定されたプッシュの送信
* この例では、カスタム リベート コードがプッシュ通知に入力されるプッシュを作成します。

![Personalize1][11]

個人用設定は、アプリ情報タグからマーカーを置き換えることで動作するため、最初にユーザーが適切なアプリ情報を定義したことを確認する必要があります。 この例では、対象とするユーザーには、rebate_code という名前のアプリ情報タグが定義されています。
上記のように、プッシュ通知の内容には、マーカー ${rebate_code} が含まれます。これは、アプリ情報タグの実際の内容で置き換えられることを示します。

> [!WARNING]
> アプリ情報タグがユーザーに対して定義されていない場合、ユーザーはプッシュを受け取りません。

* 結果

![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>通知のテキストのさらなるカスタマイズが可能
![Personalize3][13]

* 通知のタイトルの挿入
* メッセージの内容
* アナウンスメントの種類の選択 (テキスト ビューまたは Web ビュー)

![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>次の情報でアナウンスメントの本文もカスタマイズ可能
* ランディング ページをカスタマイズする場合は実際の URL
* タイトル
* メッセージの本文

## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>プッシュ通知の区別 (アプリの内部または外部)
* プッシュする通知の種類を選択し、アプリケーションを選択し、[リーチ] セクションに移動し、プッシュ キャンペーンを選択または作成して、[通知] セクションに移動します。
* 目的の「配信モード」をクリックします。
* 特定のアクティビティ (画面) に対して通知を行う場合は、[アクティビティの制限] チェック ボックスをオンにします。

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>[アプリ外のみ] 配信モード
![Differentiate2][16]

[アプリ外のみ] 配信モードは、アプリケーションが閉じている場合にプッシュ通知を提供します。 これは標準のプッシュ通知です。
[アプリ外のみ] を選択する場合は、アプリケーションをビルドしているプラットフォーム (APNS または GCM) から証明書を既に提供している必要があります。

### <a name="see-also"></a>関連項目
* [Apple Push Notification Service – Certificates (Apple プッシュ通知サービス – 証明書)](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9)、[Google Cloud Messaging – Certificate (Google クラウド メッセージング - 証明書)](http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>[アプリ内のみ] 配信モード
![Differentiate3][17]

[アプリ内のみ] 配信モードは、アプリケーションが実行中の場合にプッシュ通知を提供します。
この通知では、APNS および GCM のシステムを実行する必要はありません。
アプリ内配信システムを使用して、エンドユーザーに到達できます。
通知を完全にカスタマイズし、通知が表示されるアクティビティ (画面) を決定できます。

### <a name="anytime-delivery-mode"></a>[随時] 配信モード
[随時] 配信モードを選択すると、アプリケーションが実行されているかどうかにかかわらずエンドユーザーに到達することができます。
[常時] を選択する場合は、アプリケーションをビルドしているプラットフォーム (APNS または GCM) から証明書を既に提供している必要があります。 

## <a name="schedule-a-push-campaign"></a>プッシュ キャンペーンのスケジュール
### <a name="plan-to-start-a-campaign"></a>キャンペーン開始の計画
![Shedule1][18]

今日が 3 月 21 日で、アナウンスメントを 3 月 22 日の午前 0 時に行うことを計画しています。 プッシュを行うために、インターフェイスの前にとどまっている必要はありません。 通知を送信する正確な時刻 (分単位) を事前に計画することができます。

* [なし] チェック ボックスをオフにし、開始時刻を選択します。 
* プッシュ キャンペーンを開始する日付と時刻を選択します。

### <a name="plan-to-end-a-campaign"></a>キャンペーン終了の計画
![Shedule2][19]

キャンペーンを 3 月 25 日の午後 3時 00 分に終了しますが、その時刻には不在になることがわかっています。
プッシュを行うために、インターフェイスの前にとどまっている必要はありません。 キャンペーンを終了する正確な時刻 (分単位) を事前に計画することができます。

* [なし] チェック ボックスをオンにするか、終了時刻を選択します。
* プッシュ キャンペーンを終了する日付と時刻を選択します。

### <a name="end-a-campaign-manually"></a>手動でのキャンペーンの終了
![Shedule3][20]

既定では、[なし] チェック ボックスがオンになっています。
つまり、キャンペーンは [リーチ] セクションでアクティブにするとすぐに開始し、[リーチ] セクションで終了したときに終了します。

> [!NOTE]
> 終了日を設定せずに作成されたキャンペーンは、プッシュをデバイス上にローカルに保存するため、手動でキャンペーンを終了させても、次回アプリを開いたときにそのプッシュを表示します。

## <a name="enhance-a-push-notification-with-a-text-view"></a>テキスト ビューでのプッシュ通知の強化
### <a name="what-is-a-text-view"></a>テキスト ビューとは
![TextView1][21]

テキスト ビューとは、テキスト コンテンツを含むポップアップです。 このポップアップは、エンドユーザーがプッシュ通知をクリックした後に表示されます。
テキスト ビューでは、エンドユーザーにより多くの内容を表示することができます。 アプリのページへのジャンプ、ストアへのリダイレクト、Web ページを開く、電子メールの送信、地理的にローカライズされた検索の開始などのアクションの呼び出しを提示することもできます。

### <a name="example-text-view"></a>例: テキスト ビュー
* [リーチ] セクションでプッシュ通知キャンペーンを作成し、キャンペーンに名前を付けます。

![TextView2][22]

* 通知に表示されるメッセージを記述します。
* アナウンスメントの内容の種類として「テキスト」を選択します。

![TextView3][23]

> [!NOTE]
> テキスト ビューをプッシュした場合は、常に通知が最初に表示されます。 

* テキストを定義します (テキスト アナウンスメントの内容を選択した後、サブ セクションが表示され、表示するテキストを定義することができます)。

![TextView4][24]

* メッセージの上部に表示されるタイトルを記述します。
* テキスト ビューの主な内容を記述します。
* アクション ボタンに表示する内容を記述します (アクション ボタンにより、アプリケーションはアプリケーションのページを開く、アプリ ストアまたは提供できる任意の種類のソースへのリダイレクトなどの特定のアクションを実行できます)。
* 終了ボタンに表示される内容を記述します (終了ボタンをクリックすると、テキスト ビューが表示されなくなります)。
* プッシュ通知キャンペーンを作成すると、キャンペーンの一覧に表示されます。

![TextView5][25]

* テキスト ビューをユーザーに送信するには、プッシュ通知キャンペーンをアクティブ化します。

![TextView6][26]

* 結果

![TextView7][27]

* ユーザーは通知を受信し、クリックします。
* テキスト ビューは、ユーザーが対話できるポップアップとして表示されます。

## <a name="enhance-a-push-notification-with-a-web-view"></a>Web ビューでのプッシュ通知の強化
### <a name="what-is-a-web-view"></a>Web ビューとは
![WebView1][28]

Web ビューとは、Web コンテンツを含むポップアップです。 このポップアップは、エンドユーザーがプッシュ通知をクリックしたときに表示されます。
Web ビューでは、エンドユーザーとの対話を増やすことができます。
アプリ ストアへのリダイレクト、Web ページを開く、電子メールの送信、地理的にローカライズされた検索の開始などのアクションの呼び出しを提示することもできます。

### <a name="example-web-view"></a>例: Web ビュー
* [リーチ] セクションでプッシュ キャンペーンを作成し、キャンペーンに名前を付けます。

![WebView2][29]

* 通知に表示されるメッセージを記述します。
* アナウンスメントの内容の種類として「Web」を選択します。

![WebView3][30]

### <a name="about-announcement-types"></a>アナウンスメントの種類について:
* 通知のみ: シンプルな標準的な通知です。 ユーザーがクリックすると、他のビューが表示されず、操作に関連付けられた内容のみが発生します。
* テキストのアナウンス: ユーザーにテキスト ビューを閲覧するよう促す通知です。
* Web のアナウンス:  ユーザーに Web ビューを閲覧するよう促す通知です。
  「Web アナウンスメント」の内容を選択します。

> [!NOTE]
> Web ビューをプッシュした場合は、常に通知が最初に表示されます。

* Web コンテンツを定義します (Web アナウンスメントの内容を選択した後、サブ セクションが表示され、表示する Web ビューのコンテンツを定義することができます)。

![WebView4][31]

* メッセージの上部に表示されるタイトルを記述します (省略可能)。
* ここで HTML コードを記述します。
* ソース編集モード ボタンをクリックして編集を切り替え、その外観を参照します。
* アクション ボタンに表示する内容を記述します (アクション ボタンにより、アプリケーションはアプリケーションのページを開く、ストアまたは提供できる任意の種類のソースへのリダイレクトなどの特定のアクションを実行できます)。
* 終了ボタンに表示される内容を記述します (終了ボタンをクリックすると、Web ビューが表示されなくなります)。
* 結果

![WebView5][32]

* ユーザーは通知を受信し、クリックします。
* テキスト ビューは、ユーザーが対話できるポップアップとして表示されます。

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md


