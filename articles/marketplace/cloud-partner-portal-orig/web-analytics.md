---
title: Web Analytics | Microsoft Docs
description: 。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 89cc8c4bffe910de0861d7f44925a10df3811fdb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097476"
---
<a name="web-analytics"></a>Web Analytics
=============

この記事では、Web Analytics について学習し、これを使用してビジネスを最大限に成長させる方法を説明します。 現在、この [Insights] タブは、AppSource のすべてのプランでご利用いただけます。

プランを作成して発行したら、次のステップは、その成果を追跡し、測定することです。 **Web Analytics** には、お客様がマーケットプレースで発行した各プランの実績を正確に把握する機能が追加されています。 利用を開始するには、Cloud パートナー ポータルの左側にある [Insights] ページに移動して、新しい [Analytics] タブを表示します。

![WebAnalytics ページ](./media/si-getting-started/WebAnalytics1.png)

お客様の発行元 ID について、Microsoft Power BI で作成された機能豊富なダッシュボードが表示され、各プランの日々更新されるデータを確認することができます。

<a name="microsoft-campaigns"></a>**Microsoft キャンペーン**
-----------------------

お客様がプランを促進し、その成果を追跡できるように、Microsoft は Cloud パートナー ポータルで **Microsoft キャンペーン**の提供を開始しました。 キャンペーンは、マーケットプレース向けに新たにサポートされた機能で、お客様のアプリの詳細ページに顧客を誘導しているさまざまなチャネルを追跡することができます。

### <a name="how-to-make-a-campaign"></a>**キャンペーンの作成方法**

キャンペーンを最も簡単に言い表すと、マーケットプレースで公開されているお客様のアプリの詳細ページにリンクされた URL に、独自の語句を追加することです。 Google や Bing、LinkedIn のほか、多くのサイトが広告 (つまり、それらのサイトから目的のサイトへのリンク) を作成するよう奨励しています。

一般に、こうした取り組みの目的は、製品に新しい顧客を効果的に呼び込むことであり、各チャネルの貢献度を測定することが欠かせません。 そのために役立つのがキャンペーンです。

お客様独自のキャンペーンは、2 とおりの方法で生成できます。

1. それが何のキャンペーンで、それらの顧客がどのページやイベントから誘導されてきているかを表すクエリ パラメーター **mktcmpid** を URL に追加する。

使用例: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (上級): サポートされているいずれかの汎用キャンペーン ID を URL 内で使用する。 必要な ref タグを追加できる柔軟な表記法がサポートされています。以下の追加タグが自動的に認識されます。
    
    1. **utm\_campaign**
    2. **utm\_source**
    3. **ref**
    4. **src**

使用例: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

これらのキャンペーン ID を複数組み合わせれば、顧客の流入元 (例: メール、ブログ、ソーシャル メディア) など、キャンペーンのトラフィックの源泉となっている複数のソースを特定することができます。

例: 

1. ニュースレターが参照元の場合:  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. LinkedIn が参照元の場合:  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**ページをすべて経由するようにキャンペーンを設定する**

キャンペーンのシナリオによっては、中間ページが存在することも考えられます。トラフィックをまず中間ページに誘導したうえで、顧客をマーケットプレースに送るような場合です。 マーケットプレースに送信する初期キャンペーン ID が最終的な URL に到達することが重要です。

たとえば次のようになります。

1. トラフィックを会社のランディング ページ <https://contoso.com> に誘導する広告をマーケティング担当者が Google から購入します。 このランディング ページには、\"製品を試用する\" というリンクがあります。リンク先は <https://appsource.com> です。
2. ユーザーが広告をクリックすると、会社のランディング ページに誘導されます。
    1.  参照 URL = google.com
    2.  ランディング ページ URL = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. \"製品を試用する\" リンクをクリックすると、ユーザーが AppSource に誘導されます。
    1. 参照 URL = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. ランディング ページ URL (**この URL には必ず utm\_campaign と utm\_source を追加する**) =  [https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername?**utm\_campaign=MyCampaignAdName&utm\_source=MySourceAdName**](https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>キャンペーンの成果を評価する方法
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**キャンペーンごとのページ アクセス数**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

これは、流入元のキャンペーンごとに 1 日あたりのページ アクセス数を示した内訳です。

### <a name="conversion-rate-by-campaign"></a>**キャンペーンごとのコンバージョン率**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

プラン全体のコンバージョン率表示と同様、このグラフでは、各種キャンペーンの貢献度内訳を確認できます。 このグラフは、コンバージョン率が相対的に高いキャンペーンの流入元を特定するのに役立ちます。

### <a name="distribution-by-campaign"></a> **キャンペーンごとの分布**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

顧客のドメインに注目する方法と同様、このグラフでは、マーケットプレースにユーザーを誘導しているキャンペーンごとにデータの分布を確認できます。 \_NoCampaign は、顧客がマーケットプレースに移動したときの URL にキャンペーン ID が含まれていなかったことを意味します。

<a name="next-steps"></a>**次のステップ**
--------------

これでプランの成果を追跡できるようになりました。今度は、ご自分のキャンペーンを作成してみてください。

質問や機能のご要望がありましたら、右上隅にあるフィードバック機能でお寄せください。

![Cloud パートナー ポータルのフィードバック機能](./media/si-getting-started/WebAnalytics5.png)
