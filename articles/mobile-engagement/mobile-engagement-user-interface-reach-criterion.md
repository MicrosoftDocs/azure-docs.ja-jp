---
title: "Azure モバイル エンゲージメント ユーザー インターフェイス - Reach の条件"
description: "Azure モバイル エンゲージメントを使用して、ユーザーの選択したサブセットにプッシュ キャンペーンを送信する方法について説明します"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: a4ed03a0-55b1-4dd8-b0bd-c475005afb66
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: afa6625e4c3ebe2a408b4bd956c8d2eb9ac6a34b
ms.lasthandoff: 12/08/2016


---
# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>対象条件を使用して、ユーザーの選択したサブセットにプッシュ キャンペーンを送信する方法
[New Criteria (新しい条件)] ボタンを使用して特定の条件を指定することで対象ユーザーを絞り込む方法は、Azure モバイル エンゲージメントの最も重要な概念の 1 つであり、すべてのユーザーにプッシュ通知を送る代わりに、ユーザーに関連するプッシュ通知を送ることで反応を得やすくなります。 標準的な条件に基づいて対象ユーザーを限定し、プッシュをシミュレートすることで、通知を受信するユーザー数を確認できます。

**関連項目:**

* [UI ドキュメント - リーチ - 新しいのプッシュ キャンペーン][Link 27]

## <a name="audience-criteria-can-include"></a>対象ユーザーの条件には次のようなものを使用できます:
* **テクニカル: ** [分析] セクションおよび [監視] セクションに表示されるのと同一の技術情報に基づいて対象を絞り込むことができます。 **関連項目:** [UI ドキュメント - 分析][Link 15]、[UI ドキュメント- 監視][Link 16]
* **位置:** Geo-Fencing を用いた "Real time location reporting (リアルタイムの位置報告)" を使用するアプリケーションでは、Geo-Location を条件として使用して GPS の位置から対象ユーザーを設定できます。 "大まかなエリアの位置報告" の呼び出しを使用して携帯電話の位置から対象ユーザーを決定することもできます ("Real time location reporting" と "大まかなエリアの位置報告" は、SDK からアクティブ化する必要があります)。 **関連項目:** [SDK ドキュメント - iOS - 統合][Link 5]、[SDK ドキュメント - Android - 統合][Link 5]
* **Reach のフィードバック:** アナウンス、アンケート、データ プッシュからのリーチ フィードバックを通じて、過去のリーチ通知へのユーザーのフィードバックに基づいて対象ユーザーを設定できます。 この方法は、初めてのキャンペーンよりも数回リーチ キャンペーンを行った後に使用することでより的確な対象ユーザーを絞り込むことができます。 また、特定の過去のキャンペーンをすでに受信したユーザーにキャンペーンを送信しないよう設定すると、すでに同様のコンテンツの通知を受け取ったユーザーを除外できます。 さらに、現在アクティブな特定のキャンペーンに含まれているユーザーを新しいプッシュから除外することもできます。 **関連項目:** [UI ドキュメント - リーチ - プッシュのコンテンツ][Link 29]
* **インストールの追跡:** ユーザーがアプリをインストールした場所に基づいて情報を追跡できます。 **関連項目:** [UI ドキュメント - 設定][Link 20]
* **ユーザー プロファイル:** 標準のユーザー情報に基づいたターゲティングや、作成したカスタムのアプリ情報に基づいたターゲティングもできます。 これには、過去のキャンペーンへの反応だけでなく、現在ログインしているユーザーと、特定の質問に応答したユーザーが含まれます。 アプリで定義したすべてのアプリ情報はこの一覧に表示されます。
* セグメント： 複数の条件を含む特定のユーザー操作に基づいて作成したセグメントから対象ユーザーを設定することもできます。 アプリで定義したすべてのセグメントはこの一覧に表示されます。 **関連項目**: [UI ドキュメント - セグメント][Link 18]
* **アプリ情報:** カスタムのアプリ情報タグを [Settings (設定)] で作成すると、ユーザーの挙動を追跡できます。 **関連項目:** [UI ドキュメント - 設定][Link 20]

## <a name="example"></a>例:
アプリ内購入の操作を行ったユーザーのみにアナウンスをプッシュする場合。

1. アプリケーションの設定ページに移動し、[App Info (アプリ情報)] メニューを選択して [New app info (新しいアプリ情報)] を選択します。
2. "inAppPurchase" という新しいブール型のアプリ情報を登録します。
3. ユーザーが正常にアプリ内購入を実行したときにアプリケーションがこのアプリ情報を "true" に設定するようにします (sendAppInfo("inAppPurchase", ...) 関数を使用)。
4. この操作をアプリケーションで行いたくない場合は、device API を使用してバックエンドで行うことができます。
5. その後、アナウンスを作成して、"inAppPurchase" が "true" になっているユーザーのみを対象とする条件を付けます。

> [!NOTE]
> アプリ情報のタグ以外の条件に基づいたターゲティングを行う場合は、プッシュの送信前に Azure モバイル エンゲージメントがユーザーのデバイスから情報を収集する必要があるため、送信に遅延が生じる場合があります。 複雑なプッシュ構成のオプション (バッジのアップデートなど) の場合も、プッシュに遅延が生じる可能性があります。 Push API から "ワンショット" キャンペーンを使用する方法が、Azure モバイル エンゲージメントで最もすばやくプッシュを送信できる方法です。 その次にすばやく送信できる方法は、アプリ情報のタグのみをリーチ キャンペーンのプッシュ条件として使用する方法です。これは、アプリ情報のタグはサーバー側に保存されるためです。 プッシュ キャンペーンにその他のターゲティング条件を使用する場合は、柔軟性は非常に高くなりますが、キャンペーンの送信に Azure モバイル エンゲージメントからデバイスをクエリする必要があるため、送信までに最も時間がかかる方法になります。

![リーチ - Criterion1][29] 

## <a name="criterion-options-apply-to"></a>条件オプションの適用対象:
* **テクニカル**     
* ファームウェア名: ファームウェアの名
* ファームウェアのバージョン： ファームウェアのバージョン
* デバイス モデル: デバイス モデル
* デバイスの製造元: デバイスの製造元
* アプリケーションのバージョン: アプリケーションのバージョン
* 通信会社名： 通信会社名、未定義
* 通信会社の国: 通信会社の国、未定義
* ネットワークの種類: ネットワークの種類
* ロケール: ロケール
* 画面サイズ: 画面サイズ
* **場所**      
* 最後に検出された場所: 国、リージョン、市区町村
* リアルタイム ジオフェンシング： POI の一覧 (名前、操作内容)、円形 POI (名前、緯度、経度、半径メートル)
* **Reach のフィードバック**     
* アナウンスのフィードバック: アナウンス、フィードバック
* アンケートのフィードバック: アンケート、フィードバック
* アンケートの回答のフィードバック：アンケートの回答のフィードバック、質問、選択
* データ プッシュのフィードバック: データ プッシュ、フィードバック
* **インストールの追跡**     
* ストア: ストア、未定義
* ソース: ソース、未定義
* **ユーザー プロファイル**     
* 性別: 男性、女性、未定義
* 誕生日: 演算子、日付、未定義
* オプトイン: true または false、未定義
* **アプリ情報**      
* 文字列: 文字列、未定義
* 日付： 演算子、日付、未定義
* 整数: 演算子、数字、未定義
* ブール値： true または false、未定義
* **セグメント**    
* セグメントの名前 (ドロップダウン リストから)、除外リスト (セグメントに含まれない対象ユーザー)。

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


