---
title: "Azure モバイル エンゲージメント ユーザー インターフェイス - Settings (設定)"
description: "Azure モバイル エンゲージメントを使用してアプリケーションのグローバル設定を管理する方法について説明します。"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 858f4cb4-14de-4bb5-826f-28cadbfc928b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f3c20ee0d5abb40d7650eeaa722e87142275448d
ms.lasthandoff: 12/08/2016


---
# <a name="how-to-manage-the-global-settings-of-your-application"></a>アプリケーションのグローバル設定を管理する方法
アプリケーションで利用可能な **[設定]** メニューのオプションは、アプリケーションのプラットフォームやアプリケーションで付与されているユーザーの権限によって異なります。 設定には、詳細、プロジェクト、ネイティブのプッシュ、プッシュの速度、タグ (アプリ情報)、コマーシャル プレッシャーの項目があります。 [設定] セクションの [タグ (アプリ情報)] メニュー オプションはアプリケーション (SDK 使用) またはバックエンド (デバイス API 使用) で管理できます。 

> [!NOTE]
> **Mobile Engagement** ポータル UI の多くのセクションには、**[ヘルプの表示]** ボタンが含まれています。 このボタンを押すと、セクションに関する文脈上の情報が表示されます。
> 
> 

## <a name="details"></a>詳細
アプリケーションの名前と説明を変更できます。アプリケーションの所有者とユーザーのロール権限を表示します。 

分析構成: 週の開始日、保管日数を表示、変更できます。

  ![settings1][46]

## <a name="projects"></a>プロジェクト
アプリケーションを表示するすべてのプロジェクトを選択できます。 

また、プロジェクトを検索したり、アプリケーションが包括されているプロジェクトの名前、説明、所有者、ユーザーの権限を表示したりすることもできます。

詳細については、[UI ドキュメント - ホーム][Link 13]に関するページをご覧ください

  ![settings3][48]

## <a name="native-push"></a>Native Push (ネイティブのプッシュ)
ネイティブ プッシュで使用する新しい証明書を登録したり、既存の証明書を削除したりできます。 ネイティブのプッシュでは、Azure モバイル エンゲージメントが実行していないときでもいつでもアプリケーションをプッシュできるようになります。 

少なくとも 1 つ以上のネイティブ プッシュ サービスの資格情報や証明書を指定すると、リーチ キャンペーンの作成時に [Any time (いつでも)] を選択できるようになり、また PUSH API で "notifier" パラメーターを使用できるようになります。

### <a name="apple-push-notification-service-apns"></a>Apple プッシュ通知サービス (APNS)
Apple プッシュ通知サービスを使用してネイティブのプッシュを有効にするには、証明書の登録が必要です。 証明書の種類を、開発 (DEV) または実稼働 (PROD) のいずれかに指定する必要があります。 その後、証明書とパスワードをアップロードします。

詳細については、[SDK ドキュメント - iOS - Apple プッシュ通知に対するアプリケーション準備][Link 5]に関するページをご覧ください

![settings4][49]

### <a name="windows-push-notification-service-wpns"></a>Windows プッシュ通知サービス (WPNS)
Windows 通知サービスを使用してネイティブのプッシュを有効にするには、アプリケーションの資格情報を指定する必要があります。 パッケージ セキュリティ ID (SID) とシークレット キーが必要です。

![settings5][50]

### <a name="google-cloud-messaging-for-android-gcm"></a>Google Cloud Messaging for Android (GCM)
GCM を使用してネイティブのプッシュを有効にするには、Google の指示に従う必要があります。 その後、IP の制限なしで構成された、サーバーの SimpleAPI キーを貼り付ける必要があります。 Android v1.12.0 以上では、SDK との統合が必要です。

詳細については、次を参照してください。 

* [SDK ドキュメント - Android - GCM の統合方法][Link 5]
* [Google 開発者 GCM ガイド](http://developer.android.com/guide/google/gcm/gs.html)

### <a name="amazon-device-messaging-for-android-adm"></a>「Amazon Device Messaging for Android (ADM)
ADM を使用してネイティブのプッシュを有効にするには、クライアント ID とクライアント シークレットで構成される Amazon <OAuth credentials> を指定する必要があります (Android v2.1.0 以上では SDK との統合が必要)。

詳細については、次を参照してください。 

* [SDK ドキュメント - Android - ADM の統合方法][Link 5]
* [Amazon 開発者 ADM ドキュメント](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## <a name="push-speed"></a>Push Speed (プッシュの速度)
アプリケーションの現在のプッシュ速度を表示したり、アプリケーションのプッシュ速度を定義したりできます。

  ![settings7][52]

## <a name="tag-app-info"></a>タグ (アプリ情報)
![settings11][56]

## <a name="commercial-pressure"></a>Commercial Pressure (コマーシャル プレッシャー)
![settings12][57]

## <a name="see-also"></a>関連項目
* [概念][Link 6]
* [トラブルシューティング ガイド - サービス][Link 24]

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


