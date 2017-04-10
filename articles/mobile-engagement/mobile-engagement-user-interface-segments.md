---
title: "Azure Mobile Engagement のユーザーインターフェイス - Segments (セグメント)"
description: "Azure Mobile Engagement を利用してユーザーのセグメントを作成して管理し、使用パターンを特定する方法について説明します。"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6a4f2205-4a3c-406e-a04f-5e6f2a36653f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b4f1df6f46b7b8349fcea649ebcdf34fc363491d
ms.lasthandoff: 12/08/2016


---
# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>ユーザーのセグメントを作成して管理し、使用パターンを特定する方法
この記事では、**Mobile Engagement** ポータルの **[セグメント]** タブについて説明します。 **Mobile Engagement** ポータルはモバイル アプリの監視と管理に使用します。

UI の [Segment (セグメント)] セクションでは、異なる動作に基づいてユーザーを区分し、アプリケーションから取得したり Segments API 経由でアクセスできる分析作業を行うことができます。 セグメントの計算はまず作成から 24 時間後に行われ、その後最新の分析情報に基づいて 24 時間毎に再計算されます。 セグメントが計算されると、毎日 [Day to day history (毎日の履歴)] チャートが表示されるようになります。

> [!NOTE]
> **Mobile Engagement** ポータル UI の多くのセクションには、**[ヘルプの表示]** ボタンが含まれています。 このボタンを押すと、セクションに関する文脈上の情報が表示されます。
> 
> 

## <a name="create-segments"></a>セグメントの作成
セグメントは、Analytics セクションにある過去 60 日までの特定の期間に対して最大 10 件の条件に基づいて作成できます。 たとえば、過去 10 日間でアプリ内の特定のページを閲覧したり、特定のコンテンツを検索したユーザーに基づいたセグメントを作成できます。 この情報は、Analytics セクションにあります。 その情報を使用してセグメントを作成し、そのユーザーを対象としたプッシュ通知を設定してアプリケーションの再使用を促すことができます。 

> [!NOTE]
> 一度計算されたセグメントは編集できません。複製の作成 (コピー) や破棄のみが可能です。 セグメントは同じアプリケーション (同一の AppID) 内で複製したり、他のアプリケーション (別の AppID) 内に複製したりできます。 

 ![segments1][35] 

## <a name="examples-segments"></a>セグメントの例
 ![segments2][36]

セグメントでは、アプリケーションのユーザーを区分できます。
ユーザーの区分は、マーケティング戦略に重要な要素です。 Azure Mobile Engagement では、履歴データを取得してカスタムのセグメントを作成できます。 お客様のアプリケーションの使用状況を把握できるパワフルなツールです。 セグメントを簡単に分析でき、プッシュ通知の対象としてセグメントを使用できます。
一般的な使用例は、ストアでアプリを評価していただくようエンド ユーザーにプッシュ通知を送信する場合です。 すべてのエンド ユーザーに通知を送る代わりに、過去 1 か月間に毎日アプリケーションを使用し、大いに活用しているユーザーのみを指定してセグメントを作成できます。 数を減らして対象を絞り込んだプッシュ通知を送信することで、より良い ROI を得ることができます。

 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>Azure Mobile Engagement の主な要素に基に作成できるセグメント:
* Event: アプリケーションの特定のイベントで 1 週間に 2 回以上発生したものを対象とするセグメントを作成します。 
* Session: 過去 1 週間の アプリケーションの使用頻度が 6 回以上のユーザーのセグメントを作成します。
* Activity: 過去 1 か月の 1 つのページまたはコンテンツの使用頻度が 11 回以上または 10 回未満のユーザーのセグメントを作成します。
* Job: 1 日に 3 回以上ジョブを完了したユーザーのセグメントを作成します。
* Crash: 過去 1 週間に 11 回以上クラッシュしたすべてのユーザーのセグメントを作成します (このセグメントへのプッシュ通知に謝罪やクーポンを付けることもできます)。
* Error: 過去 3 日間に 101 回以上のエラーが発生したすべてのユーザーのセグメントを作成します。
* App Info: 過去 25 日間に発生したカスタムの App Info を対象としたセグメントを作成します。
  
  ![segments4][38]

セグメントを最適な方法で使用するには、「App Info」タグのタグ付けプランを使用してアプリケーションに SDK をカスタム統合する必要があります。
その後、インターフェイスのホームページに移動して、対象のアプリケーションを選択して [Segment (セグメント)] セクションをクリックします。

1. [Segments (セグメント)] セクションをクリックします。
2. [New segment (新しいセグメント)] ボタンをクリックして新しいセグメントを作成します。

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>実生活の例: "Session" 情報に基づいた簡単なセグメントを作成する。
過去 1 週間に 51 回以上アプリを使用したすべてのエンド ユーザーのセグメントを作成します。 そこから、1 回のセッションに費やした時間が 30 秒未満のエンド ユーザーのみに絞ります。 これで、アプリを快適に操作できたエンド ユーザーが表示されます。 次に、作成したセグメントを使用して対象のエンド ユーザーにアプリをストアで評価していただくよう依頼するプッシュ通知を送信できます。

 ![segments5][39]

1. セグメントの一覧で見つけやすいよう、セグメントに名前を付けます。
2. [作成] ボタンをクリックします。
   
   ![segments6][40]

セッションを選択します。

 ![segments7][41]

1. 「過去 1 週間」の期間を選択します。
2. [次へ] をクリックします。
   
   ![segments8][42]
3. 一覧から関連する演算子を選択します: =、≥、≤
4. 対象の数を入力します。
5. 対象の回数を選択します。 
6. [次へ] をクリックします。
   この例では、過去 1 週間に 50 回以上のセッションを実行したユーザーに一致するよう設定しています。
   
   ![segments9][43]

セッションのセグメンテーションでは、セッションあたりの長さを条件として選ぶことができます。

1. 一覧から演算子を選択します。
2. セッションあたりの長さを指定します。
3. [次へ] をクリックします。
   この例では、回数セクションで区分されたセッションで、1 回のセッションあたりに費やした時間が 30 秒未満のユーザーのみを選択しています。
   
   ![segments10][44]

すべてのフィルターで取得できるよう条件に名前を付けて、[完了] をクリックします。

 ![segments11][45]

条件の設定が終わると、セグメント フィルターに表示されるようになります。
セグメントは分析データに基づいているため、1 日 1 回計算されます。
この例では、合計のエンド ユーザー数の 47.7% が条件に一致します。 これらのユーザーはアプリを快適に操作できた可能性があり、ストアでアプリを評価するようプッシュ通知を送信すると高評価を付けてもらえる見込みがあります。

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


