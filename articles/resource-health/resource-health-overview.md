---
title: "Azure Resource Health の概要 | Microsoft Docs"
description: "Azure Resource Health の概要"
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: resource-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 06/01/2016
ms.author: BernardoAMunoz
translationtype: Human Translation
ms.sourcegitcommit: d777bc6bd477c5b6645fc8bd7b6d57a5d2f89e22
ms.openlocfilehash: e465e2c1503add186a4b134e85bd9aab61d5c0ad
ms.lasthandoff: 01/12/2017


---
# <a name="azure-resource-health-overview"></a>Azure Resource Health の概要
Azure Resource Health は個々の Azure リソースの正常性を明らかにし、問題のトラブルシューティングのために実行できるガイダンスを提供するサービスです。 Resource Health の目標は、サーバーやインフラストラクチャの要素に直接アクセスすることができないクラウド環境において、お客様がトラブルシューティングに費やす時間を短縮し、特に、問題の根本原因がアプリケーション内にあるか、Azure プラットフォーム内のイベントによるものかを判別する時間を短縮することです。

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-the-resource-is-healthy-or-not"></a>どのようなものがリソースと認識されるのか、またリソースが正常であるかどうかはどのようにして判断されるか
リソースとは、特定のサービスで提供されているリソース タイプ (仮想マシン、Web アプリ、SQL データベースなど) からユーザーが作成したインスタンスです。 

Resource Health は、リソースやサービスから生成される信号を基にリソースが正常であるかどうかを判断します。 現在 Resource Health で考慮されるのは、決まった&1; つのリソース タイプの正常性だけであることに注意してください。その他の要素については、全体的な正常性を左右する可能性があっても考慮されません。 たとえば仮想マシンの状態を報告するときは、インフラストラクチャのコンピューティング要素のみが考慮されます。このときネットワークの問題は Resource Health に表示されません。ただしサービスの機能が停止していることが明らかである場合は、ブレード上部のバナーで表示されます。 サービスの機能停止については、後でこの記事の中で詳しく説明します。 

## <a name="how-is-resource-health-different-from-service-health-dashboard"></a>Resource Health はサービス正常性ダッシュボードと何が違うのでしょうか
Resource Health の方が、サービス正常性ダッシュボードと比べて粒度の細かい情報が得られます。 サービス正常性ダッシュボードで報告されるのが、リージョン内のサービスの利用の可否に影響を及ぼすイベントであるのに対し、Resource Health によって報告されるのは、特定のリソースに関連した情報です。たとえば、仮想マシン、Web アプリ、SQL データベースの利用の可否に影響するイベントが報告されます。 たとえばノードが予期せず再起動した場合、そのノードで仮想マシンを実行しているユーザーは、自分の VM がしばらく利用できなかった理由を知ることができます。   

## <a name="how-to-access-resource-health"></a>Resource Health へのアクセス方法
Resource Health で監視できるサービスのリソース正常性には、2 とおりの方法でアクセスできます。

### <a name="azure-portal"></a>Azure ポータル
リソースの正常性のほか、その現在の状態に応じて推奨される対策が、Azure ポータルの [リソース正常性] ブレードに詳しく表示されます。 リソースの状態を照会するときは、このブレードが最適です。ポータル内の他のリソースに簡単にアクセスすることができます。 既に述べたように、[リソース正常性] ブレードに表示される推奨される対策は、現在の状態によって異なります。

* リソースが正常であるとき: リソースの正常性に影響する問題が検出されていないため、提示される対策はトラブルシューティング プロセスの支援が中心となります。 たとえば [トラブルシューティング] ブレードに直接アクセスし、ごく一般的にユーザーが遭遇する問題の解決方法についての指針を得ることができます。
* リソースが異常であるとき: 問題の原因が Azure 側にある場合、リソースを復旧するためにマイクロソフトが講じている (または講じた) 対策がブレードに表示されます。 ユーザーによって開始された操作に問題の原因がある場合は、その問題を解決してリソースを復旧するうえでユーザーが講じることのできる一連の対策がブレードに表示されます。  

Azure ポータルへのログイン後、[リソース正常性] ブレードには次の&2; とおりの方法でアクセスできます。 

### <a name="open-the-resource-blade"></a>[リソース] ブレードを開く
特定のリソースの [リソース] ブレードを開きます。 [リソース] ブレードの左隣に開いたブレードで、[サポート + トラブルシューティング] の下の [リソース正常性] をクリックして、[リソース正常性] ブレードを開きます。 

![Resource health blade](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### <a name="help-and-support-blade"></a>[ヘルプとサポート] ブレード
[ヘルプとサポート] ブレードは、右上隅の疑問符をクリックし、[ヘルプとサポート] を選択して表示します。 

**上部のナビゲーション バーから**

![[ヘルプとサポート]](./media/resource-health-overview/HelpAndSupport.png)

[ヘルプとサポート] ブレードの左隣に開いたブレードで、[サポート + トラブルシューティング] の下の [リソース正常性] をクリックします。 これをクリックすると、該当するサブスクリプションの [リソース正常性] ブレードが開き、そのサブスクリプションに含まれるすべてのリソースが一覧表示されます。 各リソースの横に、その正常性を示すアイコンが表示されます。 各リソースをクリックすると、[リソース正常性] ブレードが開きます。

**[リソース正常性] タイル**

![[リソース正常性] タイル](./media/resource-health-overview/resourceHealthTile.png)

## <a name="what-does-my-resource-health-status-mean"></a>[リソース正常性] に表示されるステータスの意味
リソースの正常性に関して表示されるステータスは 4 種類あります。

### <a name="available"></a>使用可能
リソースの利用の可否に影響する可能性のある問題はプラットフォームに検出されませんでした。 これは緑色のチェック マーク アイコンで表されます。 

![Resource is available](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>使用不可
この場合、リソースの利用の可否に影響する可能性のある現在進行中の問題がプラットフォームに検出されました (VM を実行しているノードが予期せず再起動されたなど)。 これは赤色の警告アイコンで表されます。 問題に関する詳しい情報 (下記) は、ブレードの中央のセクションに表示されます。 

1. リソースを復旧するためにマイクロソフトが講じている対策 
2. 詳細な問題のタイムライン (解決までの予想時間)
3. ユーザーに推奨される対策 

![Resource is unavailable](./media/resource-health-overview/Unavailable.png)

### <a name="unavailable--customer-initiated"></a>使用不可 (ユーザーによって開始された操作に原因がある場合)
ユーザーからの要求 (リソースの停止、再起動要求など) が原因で、リソースが使用できない状態になっています。 これは青色の情報アイコンで表されます。 

![Resource is unavailable due to user an initiated action](./media/resource-health-overview/userInitiated.png)

### <a name="unknown"></a>Unknown
リソースに関する情報が途絶えてから 5 分が経過しました。 これは灰色の疑問符アイコンで表されます。 

リソースに何か問題があるという確実な証拠を示すものではないので注意してください。ユーザーは次の推奨事項に従う必要があります。

* リソースが正常に実行されているにもかかわらず、リソース正常性が不明として表示されている場合、特に気にする必要はありません。数分後にはリソースが正常ステータスに更新されます。
* リソースへのアクセスに問題があり、リソース正常性が不明として表示されている場合は、問題の徴候と考えられます。状態が正常または異常に変わるまでは、詳しい調査を実行する必要があります。

![Resource health is unknown](./media/resource-health-overview/unknown.png)

## <a name="service-impacting-events"></a>リソースに影響するサービス イベント
リソースに影響するサービス イベントが発生中である場合、[リソース正常性] ブレードの上部にバナーが表示されます。 このバナーをクリックすると、[監査イベント] ブレードが開き、その障害に関する詳しい情報が表示されます。

![Resource health may be impacted by a SIE](./media/resource-health-overview/serviceImpactingEvent.png)

## <a name="what-else-do-i-need-to-know-about-resource-health"></a>その他リソースの正常性について知っておくべきこと
### <a name="signal-latency"></a>信号の時間差
Resource Health に情報を提供する信号には、最大 15 分の遅れが生じる可能性があり、それが原因で、リソースの最新の正常性ステータスが、実際の利用の可否と食い違う可能性があります。 不確かな問題の調査に無駄な時間を費やさないためにも、この点に注意してください。 

### <a name="special-case-for-sql"></a>SQL に関する特記事項
Resource Health から報告されるのは、SQL Server のステータスではなく、SQL データベースのステータスです。 その方が、より実態に近い正常性情報が得られる反面、データベースの正常性を判断するためには、複数のコンポーネントとサービスを考慮に入れる必要があります。 現在の信号の基になっているのはデータベースへのログインです。つまり、定期的にログイン (クエリの実行要求が届いていることも含む) されているデータベースの場合、正常性ステータスが定期的に表示されます。 アクセスが途絶えて 10 分以上経過すると、データベースが不明状態に移行します。 かといってデータベースが使用不可というわけではありません。ログインが実行されていないために信号が生成されていないというだけです。 データベースに接続してクエリを実行すると、その正常性ステータスの判断と更新に必要な信号が生成されます。

## <a name="feedback"></a>フィードバック
ご意見とご提案をお待ちしております。 あなたの [提案](https://feedback.azure.com/forums/266794-support-feedback)をお寄せください。 また、[Twitter](https://twitter.com/azuresupport) や [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure)でマイクロソフトの最新情報をご覧いただけます。


