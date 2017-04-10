---
title: "Azure Mobile Engagement ユーザー インターフェイス - リーチ コンテンツ"
description: "Azure Mobile Engagement でさまざまな種類のプッシュ通知キャンペーンの一意のコンテンツを管理する方法について説明します"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: add64f06-43c9-475c-8722-51cd00bb844b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: 3741a43b74af5846e95e42d8a7b533621e780f2d
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>さまざまな種類のプッシュ通知キャンペーンの一意のコンテンツを管理する方法
新しいキャンペーンの [Contents (コンテンツ)] セクションを使用して、アナウンス、アンケート、データ プッシュ、タイル (Windows Phone のみ) のコンテンツを修正できます。 プッシュ キャンペーンの [Contents (コンテンツ)] 設定は、キャンペーンの種類に固有です。 

### <a name="content-types"></a>コンテンツの種類：
* アナウンス
* アンケート
* データ プッシュ
* タイル (Windows Phone のみ)

## <a name="content-of-announcements"></a>アナウンスのコンテンツ
 ![リーチ - コンテンツ&1;][30] 

### <a name="choose-the-type-of-your-announcement"></a>アナウンスの種類を選択します:
* 通知のみ: シンプルな標準的な通知です。 ユーザーがクリックすると、他のビューが表示されず、操作に関連付けられた内容のみが発生します。
* テキストのアナウンス: ユーザーにテキスト ビューを閲覧するよう促す通知です。
* Web のアナウンス:  ユーザーに Web ビューを閲覧するよう促す通知です。

### <a name="see-also"></a>関連項目
* [リーチ - 方法 - アナウンス][Link 3] 

### <a name="about-web-view-announcements"></a>Web ビューのアナウンスについて:
ここで指定する HTML コードや JavaScript コードの発生パターン "{deviceid}" は、アナウンスを表示するデバイスの識別子に自動的に置き換えられます。 この方法では、Azure Mobile Engagement デバイスの識別子を、バック オフィスでホストする 外部 Web サービスで簡単に取得できます。
全画面表示の Web ビューを作成する場合は (既定の Action (アクション) と Exit (終了) ボタンなし)、Web ビューのアナウンスの JavaScript コードから次の関数を使用できます: 

* アナウンスの操作を実行する: ReachContent.actionContent()
* アナウンスを終了する: ReachContent.actionContent()

### <a name="choose-your-action"></a>アクションを選択する:
### <a name="about-action-urls"></a>アクションの URL について:
対象デバイスのオペレーティング システムで解釈できる URL はすべて、アクション URL として使用できます。
アプリケーションがサポートする専用の URL (ユーザーを特定の画面にジャンプさせるなど) をアクション URL として使用することも可能です。
発生する {deviceid} パターンはすべて、アクションを実行するデバイスの識別子に自動的に置き換えられます。 これを使用して、Azure Mobile Engagement のデバイス識別子を、バック オフィスでホストする外部 Web サービス経由で簡単に取得できます。

* **Android + iOS のアクション**
  * Web ページを開く
  * http://\[web-site-domain\] 
  * 例: http://www.azure.com
  * メールを送信する
  * mailto:\[e-mail-recipient\]?subject=\[subject\]&body=\[message\] 
  * Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
  * SMS を送信する
  * sms:\[phone-number\] 
  * 例: sms:2125551212
  * 電話をかける
  * tel:\[phone-number\] 
  * 例: tel:2125551212
* **Android のみのアクション**
  * Play ストアでアプリケーションをダウンロードする
  * market://details?id=\[app package\] 
  * 例: market://details?id=com.microsoft.office.word
  * 地理的にローカライズされた検索を開始する
  * geo:0,0?q=\[search query\] 
  * 例: geo:0,0?q=starbucks,paris
* **iOS のみのアクション**
  * App ストアでアプリケーションをダウンロードする
  * http://itunes.apple.com/[国]/app/[アプリ名]/id[アプリ ID]?mt=8 
  * 例: http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
  * Windows のアクション
  * Web ページを開く
  * http://\[web-site-domain\] 
  * 例: http://www.azure.com
  * メールを送信する
  * mailto:\[e-mail-recipient\]?subject=\[subject\]&body=\[message\] 
  * Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
  * SMS を送信する (Skype ストア アプリが必要)
  * sms:\[phone-number\] 
  * 例: sms:2125551212
  * 電話をかける (Skype ストア アプリが必要)
  * tel:\[phone-number\] 
  * 例: tel:2125551212
  * Play ストアでアプリケーションをダウンロードする
  * ms-windows-store:PDP?PFN=\[app package ID\] 
  * 例: ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
  * Bingmaps 検索を開始する
  * bingmaps:?q=\[search query\] 
  * 例: bingmaps:?q=starbucks,paris
  * カスタムのスキームを使用する
  * \[custom scheme\]://\[custom scheme params\] 
  * 例: myCustomProtocol://myCustomParams
  * パッケージ データを使用する (拡張機能読み取りのためのストア アプリ が必要)
  * \[folder\]\[data\].\[extension\] 
  * 例: myfolderdata.txt

### <a name="build-a-tracking-url"></a>追跡用 URL を構築する:
* <UI Documentation> の「設定」セクションで、ユーザーが他のアプリケーションをダウンロードできるようにする追跡用の URL を構築する方法をご覧ください。

### <a name="define-the-texts-of-your-announcement"></a>アナウンスのテキストを定義する
アナウンスの件名、コンテンツ、ボタンのテキストを入力します。 このキャンペーンに対するユーザーの反応のリーチ フィードバックに基づいて、今後のキャンペーンの対象ユーザーを決めることができます。 対象ユーザーの設定は、このキャンペーンはプッシュされただけか、応答があったか、アクションはあったか、終了されたかといったフィードバックに基づいて行うことができます。

### <a name="see-also"></a>関連項目
* [UI ドキュメント - リーチ - 新しいプッシュの条件][Link 28]

## <a name="content-of-polls"></a>アンケートのコンテンツ
![リーチ - コンテンツ&2;][31] 

アナウンスの件名、説明、ボタンのテキストを入力します。 次に、質問と質問への回答の選択肢を追加します。
このキャンペーンに対するユーザーの反応のリーチ フィードバックに基づいて、今後のキャンペーンの対象ユーザーを決めることができます。 対象ユーザーの設定は、このキャンペーンはプッシュされただけか、応答があったか、アクションはあったか、終了されたかに基づいて行うことができます。 また、質問への回答の選択内容を条件として使用して、アンケートの回答フィードバックに基づいて対象ユーザーを設定することも可能です。

### <a name="see-also"></a>関連項目
* [UI ドキュメント - リーチ - 新しいプッシュの条件][Link 28]

## <a name="content-of-data-pushes"></a>データ プッシュのコンテンツ
![リーチ - コンテンツ&3;][32] 

### <a name="choose-the-type-of-your-data"></a>データの種類を選択します：
* テキスト
* バイナリ データ
* Base64 データ

### <a name="define-the-content-of-your-data"></a>データのコンテンツを定義します
* テキスト データのプッシュを選択した場合は、[Contents (コンテンツ)] ボックスにテキストをコピーして貼り付けます。
* バイナリ データや base64 データのプッシュを選択した場合は、[upload your file (ファイルをアップロード)] ボタンを使用してファイルをアップロードします。
* このキャンペーンに対するユーザーの反応のリーチ フィードバックに基づいて、今後のキャンペーンの対象ユーザーを決めることができます。 対象ユーザーの設定は、このキャンペーンはプッシュされただけか、応答があったか、アクションはあったか、終了されたかに基づいて行うことができます。

### <a name="see-also"></a>関連項目
* [UI ドキュメント - リーチ - 新しいプッシュの条件][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>タイルのコンテンツ (Windows Phone のみ)
![リーチ - コンテンツ&4;][33]

### <a name="define-the-content-of-your-tile"></a>タイルのコンテンツを定義します
タイルのペイロードは、Windows Phone デバイスでアプリのタイルに表示されるテキストです。
タイルのプッシュは、Windows Phone のネイティブ プッシュの Microsoft Push Notification Service (MPNS) バージョンです。 タイル プッシュの種類は唯一、応答のない種類であるため、タイル プッシュ キャンペーンの結果に基づいて今後のキャンペーンの対象ユーザーは設定できません。 

### <a name="see-also"></a>関連項目
* [API ドキュメント - リーチ API - ネイティブ プッシュ][Link 4]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md


