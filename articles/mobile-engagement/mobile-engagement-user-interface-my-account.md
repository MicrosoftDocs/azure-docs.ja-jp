---
title: "Azure Mobile Engagement ユーザー インターフェイス - マイ アカウント"
description: "Azure Mobile Engagement を利用してアカウント プロファイルとテスト デバイスを管理する方法について説明します。"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 22832678-3959-4b8c-9fb2-f2ff5974e5d1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: 4e463e973dcfa1faa7b08e4738192161980b3aa2
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-manage-your-account-profile-and-test-devices"></a>アカウント プロファイルとテスト デバイスを管理する方法
この記事では、**Mobile Engagement** ポータルの**ホーム** ページについて説明します。 **Mobile Engagement** ポータルはモバイル アプリの監視と管理に使用します。 

**[マイ アカウント]** ページにアクセスするには、ページ上部のアカウントをクリックします。

UI の [マイ アカウント] セクションでは、プロファイル設定やテスト デバイス ID などのアカウントに関連付けられている設定を表示し、変更できます。 これらの設定には、デバイス API からもアクセスできる項目が含まれています。

![MyAccount1][7]  

## <a name="profile"></a>プロファイル:
アカウント設定を次のように表示または変更できます。 [[ホーム]](mobile-engagement-user-interface-home.md)から、メール アドレスに基づいて別のユーザーに自分のアプリケーションを使用するアクセス許可を与えることもできます。

![MyAccount2][8]  

## <a name="devices"></a>デバイス:
**リーチ**または**プッシュ** キャンペーンのテストに使用できるテスト デバイスのテスト デバイス ID を表示、追加、削除できます。 各プラットフォーム用 (iOS、Android、Windows Phone など) のデバイスのデバイス ID を検索する方法のコンテキストは、[新しいデバイス] をクリックすると表示されます。 

![MyAccount3][9]  

プッシュ API またはデバイス API を使用するには、ユーザーの一意のデバイス ID (deviceid パラメーター) が必要です。 これを取得するには、次の方法があります。

1. バックエンドからは、デバイス API の "Get" 機能を使用してデバイス ID の完全な一覧を取得できます。
2. アプリからは、SDK を使用して取得できます。 (Android の場合は、エージェント クラスの getDeviceID() 関数を呼び出し、iOS の場合は、エージェント クラスの deviceid プロパティを読み取ります)。
3. リーチのお知らせからは、お知らせに関連付けられているアクションの URL に {deviceid} パターンが含まれている場合、このパターンは、アクションをトリガーするデバイスの ID に自動的に置き換えられます。
   http://<example>.com/registeruser?deviceid={deviceid}&otherparam=myparamdata は、http://<example>.com/registeruser?deviceid=XXXXXXXXXXXXXXXX&otherparam=myparamdata に置き換えられます。 
4. リーチの Web のお知らせからは、お知らせの HTML コードに {deviceid} パターンが含まれている場合は、Web のお知らせを表示するデバイスの ID に自動的に置き換えられます。
   自分のデバイス ID: {deviceid} は、次のデバイス ID に置き換えられます。自分のデバイス ID: XXXXXXXXXXXXXXXX
5. デバイスでアプリケーションを開き、タグが付けられているアプリでイベントを実行します。
   [UI] - [アプリ] - [監視] - [イベント] - [詳細] から、実行したイベントを一覧から見つけます。
   [監視] で、このイベントをクリックします。
   このイベントを実行したデバイスの一覧に、デバイス ID が見つかるはずです。
   次に、このデバイス ID をコピーし、[UI] - [マイ アカウント] - [デバイス] - [新しいデバイス] - [デバイスのプラットフォームを選択] で、これを登録できます。
   >(IDFA が iOS で無効になっている場合、アプリをアンインストールして再インストールすると、デバイス ID が経時変化する可能性があります)。

## <a name="troubleshooting-guide"></a>トラブルシューティング ガイド
* [トラブルシューティング ガイド - サービス][Link 24]

## <a name="see-also"></a>関連項目
* [UI ドキュメント - ホーム][Link 13]

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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md





