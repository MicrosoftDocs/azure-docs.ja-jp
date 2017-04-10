---
title: "Azure Mobile Engagement ユーザー インターフェイス - ホーム"
description: "Azure Mobile Engagement を利用し、既存のアプリケーションとプロジェクトを管理する方法について説明します。"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: aff578d2-40f6-43e4-b0ea-7d2674cb28a1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1e3e4825d06486e6e8dfa874271ec82d203f7a7b
ms.lasthandoff: 12/08/2016


---
# <a name="how-to-manage-your-existing-application-and-projects"></a>既存のアプリケーションとプロジェクトを管理する方法
この記事では、**Mobile Engagement** ポータルの**ホーム** ページについて説明します。 **Mobile Engagement** ポータルはモバイル アプリの監視と管理に使用します。 ポータルを使い始めるには、最初に **Azure Mobile Engagement** アカウントを作成する必要があることに注意してください。 

ホーム ページに移動するには、ページの左上にある **[ホーム]** をクリックします。 ここには、選択されたコレクションの一部であるすべてのアプリケーションの一覧が含まれます。 このページには、アプリケーションの簡単な概要だけが表示されます。

また、ホーム ページには、アカウント内にあって任意のアプリケーションを含むことができるすべてのプロジェクトも表示されます。 アカウントを作成すると、だれでも UI のホーム ページにアクセスできますが、他のユーザーが **[マイ プロジェクト]**の自分のカスタム アプリケーションにアクセスするには、そのユーザーにアクセス許可を与える必要があります。

選択したアプリケーションの比較チャートを見ることもできます。 または、プロジェクトで選択したアプリケーションの比較チャートの表示を選択します。

![Home1][0]

## <a name="my-applications"></a>マイ アプリケーション
アプリケーションの概要を使用すると、開きたいアプリケーションを選択して、詳細なリボン オプションを表示させることができます。 アプリケーションの名前をクリックしてアプリケーションで最近表示したリボンの場所に戻ったり、ギア アイコンをクリックしてアプリケーションの [設定] ページに直接移動したりできます。 アプリケーション テーブルに表示される情報は、検索、フィルター処理、並べ替えができます。 列ヘッダーをドラッグ アンド ドロップして、順序を変更することもできます。

アプリケーションの概要には、次の内容が表示されます。

* **新しいユーザーの傾向**: 過去 2 週間の新しいユーザーの増加率。
* **アクティブ ユーザー**: 過去 30 日間のアクティブ ユーザー数。
* **アクティブ ユーザーの傾向**: 過去 2 週間のアクティブ ユーザーの増加率。
* **セッション**: セッションは、ユーザーが使用を開始してから終了するまでにユーザーによって実行されるアプリケーションの 1 回の使用です。
* **セッションの傾向**: 過去 2 週間のセッションの増加率。

アプリケーションをクリックすると、UI を使用してアプリの監視と管理を開始できます。 次に例を示します。    

* [アプリケーションに関するデータをリアルタイムに監視する](mobile-engagement-user-interface-monitor.md)
* [アプリケーションに関する履歴データを分析する](mobile-engagement-user-interface-analytics.md)
* [ユーザーのセグメントを作成して管理し、使用パターンを特定する](mobile-engagement-user-interface-segments.md)
* [プッシュ通知を利用してアプリケーションのユーザーに通知する](mobile-engagement-user-interface-reach.md)

## <a name="my-projects"></a>[マイ プロジェクト]
プロジェクトを使用してアプリケーションをグループ化し、他のユーザーにアプリケーションへのアクセス許可を与えます。 他のユーザーにアクセス許可を与えるには、電子メール アドレスを提供します。 **[新しいプロジェクト]** ボタンを使用すると、「名前」と「説明」を入力するだけで新しいプロジェクトを作成できます。 プロジェクトが作成されたら、プロジェクト名をクリックして製品の名前と説明を編集したり、このプロジェクトで表示したいすべてのアプリケーションを選択したりできます。

![Home6][60]

ロールの種類:

* **ビューアー**: ビューアーは、プロジェクトに関連付けられているアプリケーションのみを表示できるユーザーです。 ビューアーは、分析にアクセスしてデータを監視し、リーチの結果を確認できます。 ビューアーは、情報を変更できません。また、アプリケーションやユーザーを管理することもできません。 ビューアーは、リーチ キャンペーンの状態を作成または変更できません。
* **開発者**: 開発者は、アプリケーションの管理のほかに、ビューアーができるすべてのことを実行できるユーザーです。 開発者は、アプリケーションの有効化と無効化、アプリケーションの情報の変更 (パッケージや署名など)、リーチ キャンペーンの作成を行うことができます。 開発者は、ユーザーを管理できません。
* **管理者**: 管理者は、ユーザーの管理のほかに、開発者ができるすべてのことを実行できるユーザーです。 管理者は、プロジェクトに参加するユーザーの招待、ユーザー ロールの変更、プロジェクトの情報の変更を行うことができます。 アプリケーション レベルのアクセス許可は、[設定] でも設定できます。

プロジェクトをクリックすると、そのプロジェクトの一部であるすべてのアプリケーションが表示されます。 次の図は、選択したアプリケーションの比較チャートの例です。

![Home2][3]

## <a name="see-also"></a>関連項目
* [概念][Link 6]
* [トラブルシューティング ガイド - サービス][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
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

