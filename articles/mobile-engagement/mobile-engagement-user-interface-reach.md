---
title: "Azure Mobile Engagement ユーザー インターフェイス - Reach (リーチ)"
description: "Azure Mobile Engagement でプッシュ通知を利用してアプリケーションのユーザーに通知する方法について説明します。"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: d96e2590-08dd-4481-a352-2c18f26a1643
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 0f09a4118c3cfbd014e1c53fd22da06eb7ede260
ms.lasthandoff: 12/08/2016


---
# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>プッシュ通知を利用してアプリケーションのユーザーに通知する方法
この記事では、**Mobile Engagement** ポータルの **[リーチ]** タブについて説明します。 **Mobile Engagement** ポータルはモバイル アプリの監視と管理に使用します。 ポータルを使い始めるには、最初に **Azure Mobile Engagement** アカウントを作成する必要があることに注意してください。 詳細については、「 [Azure Mobile Engagement アカウントの作成](mobile-engagement-create.md)」を参照してください。

UI の Reach (リーチ) セクションは、プッシュ キャンペーン管理ツールで、プッシュ通知キャンペーンや機能を作成、編集、アクティブ化、完了、モニターしたり、統計を取得したりできます。また、Reach API (と低レベルのプッシュ API の一部の要素) 経由でアクセスすることも可能です。 API と UI のいずれを使用する場合でも、リーチ キャンペーンを使用する前に、Azure Mobile Engagement と Reach の両方を、SDK を使用する各プラットフォーム用のアプリケーションに統合する必要があります。

> [!NOTE]
> **Mobile Engagement** ポータル UI の多くのセクションには、**[ヘルプの表示]** ボタンが含まれています。 このボタンを押すと、セクションに関する文脈上の情報が表示されます。
> 
> 

## <a name="four-types-of-push-notifications"></a>4 種類のプッシュ通知
1. アナウンス - ユーザーに広告メッセージを送信してアプリ内の他の場所へリダイレクトしたり、アプリ外の Web ページやストアに誘導したりできます。 
2. アンケート - ユーザーに質問を投げかけることでエンド ユーザーからの情報を収集できます。
3. データ プッシュ- バイナリのデータ ファイルや base 64 のデータ ファイルを送信できます。 データ プッシュに含まれる情報はアプリケーションに送信され、ユーザーの現在のアプリの使用状況を修正できます。 アプリケーション側でデータ プッシュのデータを処理する機能を備えている必要があります。

## <a name="campaign-details"></a>キャンペーンの詳細
キャンペーンの名前の上にカーソルを合わせるか、クリックして開くとキャンペーンを編集、複製、削除したり、まだアクティブ化されていないキャンペーンをアクティブ化したりできます。 すでにアクティブなキャンペーンの名前の上にカーソルを合わせるか、クリックして開くとそのキャンペーンの複製を作成できますが、 一度アクティブ化したキャンペーンを変更することはできません。

![Reach1][18]

## <a name="reach-feedback"></a>Reach のフィードバック
リーチ キャンペーンの詳細を表示するには、 **[統計]** をクリックします。 **[簡易]** ビューには、キャンペーンのアクティブ化後に起きたことが、棒グラフの形式で視覚的に表現されます。 **[詳細]** ビューには、プッシュ キャンペーンに関するさらに詳しい情報が表示されます。 これらの詳細情報は、テスト キャンペーン (テスト デバイスに送信されるプッシュ) を送信している場合には表示されません。 これらの詳細情報には、次のような意味があります。

1. **プッシュ済み** - デバイスにプッシュされたメッセージの数を示します。 この数は、プッシュ キャンペーンを作成するときに指定した対象ユーザーによって異なります。 対象ユーザーを指定しない場合、このプッシュは登録されているすべてのデバイスに送信されます。 他のすべてのプッシュ サービスと同じように、通知はデバイスに直接プッシュされませんが、それぞれのプラットフォーム固有のプッシュ通知サービス (PNS - APNS/GCM/WNS) にプッシュされ、デバイスに配信されます。 
2. **配信済み** - PNS によってデバイスに正常に配信され、Mobile Engagement SDK によって受信済みと認められたメッセージの数を示します。 
   
   *配信済み数がプッシュ済み数よりも少ない理由:*
   
   1. ユーザーがデバイスからアプリをアンインストールしたのに、PNS にプッシュが送信された時点でそのことを PNS が認識していない場合、メッセージはドロップされます。
   2. デバイスにアプリがあっても、デバイス自体が長期間オフラインになっている場合、PNS からデバイスへのメッセージの配信は失敗します。 
   3. メッセージがデバイスに配信されても、アプリの Mobile Engagement SDK がメッセージの内容を認識しない場合、そのメッセージはドロップされます。 この問題が起きるのは、アプリでの通知のカスタマイズによって例外が生成され、それが SDK でキャッチされるとメッセージがドロップされる場合などです。 また、プラットフォームから送信されるより新しいバージョンのプッシュ メッセージを理解できないバージョンの Mobile Engagement SDK をデバイスのアプリが使用している場合にも、この問題が発生することがあります。ただし、それは、サービス プラットフォームから通知がディスパッチされた後にアプリがアップグレードされた場合だけです。 **[詳細]** タブには、ドロップされたメッセージの数が表示されます。 
   4. iOS デバイスでは、デバイスのバッテリ残量が少ない場合、またはリモート通知を処理するときにアプリで大量の電力が消費されている場合、メッセージが配信されないことがあります。 これは iOS デバイスの制限です。   
3. **表示済み** - デバイスのアプリ ユーザーに正常に表示されたメッセージの数を示します。メッセージの形式は、通知センターではシステム プッシュ/アプリ外通知、モバイル アプリではアプリ内通知です。  **[詳細]** タブには、システム通知の数と、アプリ内通知の数が表示されます。 
   
   *表示済みの数が配信済みの数 (表示されるのを待機中) より少なくなる理由*
   
   1. 通知キャンペーンに終了日があった場合、通知が配信されても、アプリ ユーザーがそれを開いて読もうとしたときに、既に有効期限が切れていて表示されない場合があります。   
   2. 通知がアプリ内通知の場合、通知はアプリのユーザーがそのアプリを開いたときにのみ表示されます。 アプリのユーザーがアプリを開いていない場合、SDK は、通知を配信済みとして報告しますが、アプリが開かれるまでは表示済みとして報告しません。 
   3. 通知がアプリ内通知であり、特定のアクティビティ/画面で表示されるように構成されている場合も、通知は配信済みとして報告されますが、ユーザーが特定の画面でアプリを開くまでは表示済みになりません。 
4. **ユーザー操作** - アプリ ユーザーが操作したメッセージの数を示します。アクションがあったメッセージと終了されたメッセージの両方が含まれます。 
   
   * *アプリのユーザーは、次のような方法で通知を操作できます。*
     
     1. 通知がシステム/アプリ外通知、または通知専用として送信されたアプリ内通知である場合、アプリのユーザーは通知をクリックします。
     2. 通知がテキストまたは Web ビューかアンケートを持つアプリ内通知である場合、アプリのユーザーは通知のアクション ボタンをクリックします。
     3. 通知が Web ビューを持つアプリ内通知である場合、アプリのユーザーは Web ビュー内の URL をクリックします (Android のみ)。
   * *アプリのユーザーは、次のような方法で通知を終了できます。*
     
     1. 通知の閉じるボタンを直接クリックします。 
     2. 通知をスワイプまたは削除します。 
     3. テキスト/Web コンテンツとアンケートを持つアプリ内通知は、通常、2 段階のプロセスでアプリのユーザーに表示されます。 まず通知が表示され、ユーザーがそれをクリックすると、続けてテキスト/Web/アンケート コンテンツが表示されます。 アプリのユーザーは、どちらの段階でも通知を終了することができ、詳細ビューの詳細情報にはそのことが反映されます。 
5. **処理済み** - アプリのユーザーによって明示的に操作されたメッセージの数を示します。 これは、通知でプッシュしたメッセージに何人のアプリ ユーザーが関心を持ったかを示すため、最も注目される数です。 

> [!NOTE]
> iOS および Windows プラットフォームでは、ユーザーがアプリを開いていて、キャンペーンが "随時" キャンペーンであった場合、アプリ外通知とアプリ内通知の両方が同時に表示されることがありえます。 この場合、表示済みの数が配信済みの数よりも多くなる可能性があります。 ユーザーが通知を操作したり処理したりした場合は、ユーザー操作/処理済みの数が配信済みの数より多くなることも考えられます。 
> 
> 

![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md


