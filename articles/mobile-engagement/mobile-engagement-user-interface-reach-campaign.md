---
title: "Azure モバイル エンゲージメント ユーザー インターフェイス - リーチ キャンペーン"
description: "Azure モバイル エンゲージメントを使用して、プッシュ通知キャンペーンを作成し、管理する方法"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2fe124a2-a86f-4136-81ba-a9d298ec798a
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d5110a33e13802fd6e5e0f075c7d3ddd521a1ef9
ms.lasthandoff: 12/08/2016


---
# <a name="how-to-create-and-manage-push-notification-campaigns"></a>プッシュ通知キャンペーンを作成し、管理する方法
UI の [Reach (リーチ)] セクションを使用して、プッシュ通知の送信に必要なすべての情報を指定することで、複雑な式を用いた新しいプッシュ キャンペーンを作成できます。 プッシュ キャンペーンのオプションは、次の4 つの種類のキャンペーンによって少し異なります。アナウンス、アンケート、データ プッシュ、タイル (Windows Phone の場合のみ)。

### <a name="option-applies-to"></a>オプションの適用対象:
* 言語： すべて (アナウンス、アンケート、データ プッシュ、タイル)
* キャンペーン： すべて (アナウンス、アンケート、データ プッシュ、タイル)
* 通知： アナウンス、アンケート
* コンテンツ： キャンペーンの種類ごとに異なります
* 対象ユーザー： すべて (アナウンス、アンケート、データ プッシュ、タイル)
* 期間： アナウンス、アンケート、タイル
* テスト： すべて (アナウンス、アンケート、データ プッシュ、タイル)

![リーチ - キャンペーン 1][20]

## <a name="languages"></a>言語
[Language (言語)] ドロップダウン メニューを使用して、異なる言語に設定されたデバイスに、異なるバージョンのプッシュを送信できます。 既定では、デバイスに設定された言語に関係なくすべてのデバイスに同じプッシュが送信されます。 異なる言語のデバイスを使用しているユーザーは、既定の言語バージョンのプッシュを受信します。 プッシュ キャンペーンには、選択した追加言語ごとに別のコンテンツを指定できるオプションがたくさんあります。 

![リーチ - キャンペーン 2][21]

### <a name="language-differences-apply-to"></a>言語ごとの違い:
* 言語： 既定の言語に加えて固有の言語を選択できます
* キャンペーン： すべての言語で同一
* 通知： 既定の言語に加えて言語ごとに固有
* コンテンツ： 既定の言語に加えて言語ごとに固有
* 対象ユーザー： 言語ごとの条件でフィルター処理できます
* 期間： すべての言語で同一
* テスト： 言語ごとに一度に送信できます

### <a name="supported-languages"></a>サポートされている言語:
* アラビア語 (ar) 
* ブルガリア語 (bg) 
* カタルニア語 (ca) 
* 中国語 (zh) 
* クロアチア語 (hr) 
* チェコ語 (cs) 
* デンマーク語 (da) 
* オランダ語 (nl) 
* 英語 (en) 
* フィンランド語 (fi) 
* フランス語 (fr) 
* ドイツ語 (de) 
* ギリシャ語 (el) 
* ヘブライ語 (he) 
* ヒンディー語 (hi) 
* ハンガリー語 (hu) 
* インドネシア語 (id) 
* イタリア語 (it) 
* 日本語 (ja) 
* 韓国語 (ko) 
* ラトビア語 (lv) 
* リトアニア語 (lt) 
* マレー語 (マクロ言語) (ms) 
* ノルウェー ブークモール (nb) 
* ポーランド語 (pl) 
* ポルトガル語 (pt) 
* ルーマニア語 (ro) 
* ロシア語 (ru) 
* セルビア語 (sr) 
* スロバキア語 (sk) 
* スロベニア語 (sl) 
* スペイン語 (es) 
* スウェーデン語 (sv) 
* タガログ語 (tl) 
* タイ語 (th) 
* トルコ語 (tr) 
* ウクライナ語 (uk) 
* ベトナム語 (vi) 

## <a name="campaign"></a>キャンペーン
[Campaign (キャンペーン)] セクションを使用して、キャンペーンの名前とカテゴリを設定したり、プッシュ キャンペーンの対象ユーザーを無視してこのキャンペーンを Reach API (と低レベル Push API の一部の要素) 経由で送信するよう選択したりできます。 カテゴリをカスタムの通知テンプレートと共に使用すると、定義済みの設定に基づいてアプリ内の通知を管理できます。 既存の "カテゴリ" の一覧は、Reach API 経由で取得できます。

> [!WARNING]
> リーチ キャンペーンの [Campaign (キャンペーン)] セクションで [Ignore Audience, push will be sent to users via the API (対象ユーザーを無視して API 経由でユーザーにプッシュを送信する)] オプションを使用すると、キャンペーンは自動的に送信されず、Reach API 経由で手動で送信する必要があります。

![リーチ - キャンペーン 3][22]

### <a name="option-applies-to"></a>オプションの適用対象:
* 名前: すべて
* カテゴリ： アナウンス、アンケート
* 対象ユーザーを無視すると、プッシュは API 経由でユーザーに送信されます: すべて

## <a name="notification"></a>通知
通知のセクションを使用して、プッシュ、メッセージ、アプリ内の画像、消去可能かどうかについてプッシュの基本的な設定を設定できます。 ほとんどの通知設定は、デバイスのプラットフォーム固有です。 プッシュを "アプリ内" に送るか、"アプリ外" に送るか、その両方に送るかを選択できます (ユーザーはデバイスのオペレーティング システム レベルで "アプリ外" プッシュを "オプトイン" するか "オプトアウト" にするかを選択でき、Azure モバイル エンゲージメントではこの設定をオーバーライドできません。 また、Reach API では "アプリ内" プッシュと "アプリ外" プッシュを処理します。 Push API を使用して "アプリ外" プッシュを処理することも可能です)。プッシュは画像や、アプリ外へのリンクやアプリ内の他の場所を示すディープ リンクなどの HTML コンテンツを使用してカスタマイズできます (Android SDK 2.1.0 以降のインテント カテゴリが必要)。 アイコンや iOS バッジを変更したり、テキストまたは Web コンテンツ (html コンテンツ付きのポップアップ、アプリ内外の他の場所への URL リンク) を送信したりできます。 また、Android デバイスでプッシュの受信時に着信音を鳴らしたりバイブレートさせることもできます (デバイスの着信音を鳴らしたりバイブレートさせたりするには、Android マニフェスト ファイルに対する正しい SDK の権限が必要です)。端末ごとに画面サイズが異なるため、現在 Android の「全体像」サイズの業界標準はありませんが、400ｘ100 の画像がほぼすべての画面サイズに適していると言われています。

### <a name="delivery-types"></a>配信の種類:
* Out of app only (アプリ外のみ): ユーザーがアプリケーションを使用していないときに通知が配信されます。
* Out of app only の通知の場合、Apple や Google からの証明書 (APNS や GCM 証明書) が必要です。
* アプリ内のみ： アプリケーションを実行中にのみ通知が表示されます。
* 通知は Capptain の配信システムを使用します。 プッシュの視覚的なレイアウトや表示は自由にカスタマイズできます。
* いつでも： このオプションでは、アプリケーションが実行されているかどうかにかかわらず通知が送信されます。

![リーチ - キャンペーン 4][23]

### <a name="option-applies-to"></a>オプションの適用対象:
* 通知： アナウンス、アンケート

## <a name="content"></a>コンテンツ
[Content (コンテンツ)] セクションを使用して、アナウンス、アンケート、データ プッシュ、タイル (Windows Phone のみ) のコンテンツを変更できます。 プッシュ キャンペーンの [Contents (コンテンツ)] 設定は、キャンペーンの種類に固有です。 

### <a name="see-also"></a>関連項目
* [UI ドキュメント - Reach - プッシュのコンテンツ][Link 29]

![リーチ - キャンペーン 5][24]

## <a name="audience"></a>対象となる読者
[Audience (対象ユーザー)] セクションを使用して、カスタマイズした条件に基づいてキャンペーンを制限する項目の標準リストを定義できます。 対象ユーザーを制限するオプションでは、プッシュの送信先を、新規ユーザー、既存ユーザー、ネイティブ プッシュのユーザーのみ、のいずれかに設定できます。 また、プッシュを受信するユーザー数を制限するクォータを設定することも可能です。 キャンペーンの式を手動で編集して、対象ユーザーを指定する 1 つ以上の条件を追加してキャンペーンのフィルター方法を決めることができます。 対象ユーザーの式は手動で入力できます。 この式では、条件間のリレーションシップを明示的に定義する必要があります。 条件は、大文字で始まる識別子を使用する必要があり、スペースを含めることはできません。 条件間のリレーションシップは、and、or、not 演算子と '(', ')' を使用して記述します。 例： "Criterion1 or (Criterion1 and not Criterion2)"

> [!NOTE]
> キャンペーンに多数の対象ユーザーが含まれる場合、特に同時に複数のキャンペーンを開始しようとした場合には、サーバー側のターゲティング スキャンの動作が遅くなることがあります。

* 可能な限り、一度に 1 つのキャンペーンのみを開始するようにしてください。
* 最大でも、一度に開始するキャンペーンは 4 つまでにしてください。
* アクティブなユーザーにのみプッシュを送信するようにすると ([Engage only users who can be reached using Native Push (ネイティブ プッシュでリーチできるユーザーのみを対象にする)] チェック ボックスと [Engage only active users (アクティブなユーザーのみを対象にする)] チェック ボックス)、現時点でアプリをインストール、使用しているユーザーのみをスキャンできます。
  対象ユーザーを定義したら、シミュレート ボタンを使用してこの通知の受信者数を確認できます。 この操作では、設定した対象ユーザーに当てはまる可能性のある既知のユーザー数が算出されます (ユーザーのランダム サンプルに基づいた概算です)。 アプリケーションをアンインストールしたユーザーもこの対象ユーザーに含まれますが、リーチはできませんのでご注意ください。

### <a name="see-also"></a>関連項目
* [UI ドキュメント - リーチ - 新しいプッシュの条件][Link 28]

![リーチ - キャンペーン 6][25]

### <a name="edit-expression"></a>式を編集する
![リーチ - キャンペーン 7][26]

### <a name="limit-your-audience-option-applies-to"></a>対象ユーザーの制限オプションの適用対象:
* ユーザーのサブセットのみを対象にする： すべて (お知らせ、アンケート、データ プッシュ、タイル)
* 既存のユーザーのみを対象にする： すべて (お知らせ、アンケート、データ プッシュ、タイル)
* 新規のユーザーのみを対象にする： すべて (お知らせ、アンケート、データ プッシュ、タイル)
* アイドル状態のユーザーのみを対象にする: お知らせ、アンケート、タイル
* アクティブなユーザーのみを対象にする： すべて (お知らせ、アンケート、データ プッシュ、タイル)
* ネイティブ プッシュでリーチできるユーザーのみを対象にする: お知らせ、アンケート

## <a name="time-frame"></a>期間
[Time Frame (期間)] セクションでは、プッシュの送信時期を設定したり、期間を空白にしてすぐにキャンペーンを開始したりできます。 エンド ユーザーのタイムゾーンを使用した場合、アジアのエンド ユーザーには予想よりも 1 日早く送信される場合があり、世界中の全タイムゾーンがキャンペーンで設定した期間に入るまで、プッシュは少しずつ送信されます。 また、エンド ユーザーのタイムゾーンに設定すると、プッシュの開始前に携帯端末に時刻を要求する必要があるため、キャンペーンに遅れが生じる可能性があります。

> [!NOTE]
> キャンペーンに終了日を設定しない場合、プッシュがローカルに保存され、キャンペーンを手動で終了したあとも表示される可能性があります。 このような動作を避けるには、キャンペーンの終了日を指定してください。

### <a name="see-also"></a>関連項目
* [リーチ - 方法 – スケジュール設定][Link 3] 

![リーチ - キャンペーン 8][27]

### <a name="settings-apply-to"></a>設定の適用対象:
* 期間： アナウンス、アンケート、タイル

## <a name="test"></a>テスト
[Test (テスト)] セクションを使用して、キャンペーンを保存する前にこのプッシュを独自のテスト デバイスに送信できます。 キャンペーンにカスタムの言語を構成している場合は、各言語でプッシュをテストできます。 テスト デバイスは、[My Account (マイ アカウント)] でセットアップできます。

> [!NOTE]
> プッシュを "テスト" するボタンを使用するときは、サーバー側のデータは記録されません。データの記録は、実際のプッシュ キャンペーンでのみ行われます。

### <a name="see-also"></a>関連項目
* [UI ドキュメント - マイ アカウント][Link 14]

![リーチ - キャンペーン 9][28]

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


