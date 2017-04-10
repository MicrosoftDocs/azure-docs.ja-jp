---
title: "Azure Marketplace 支払いレポートについて | Microsoft Docs"
description: "Azure Marketplace 支払いレポートを確認および取り込む方法について説明します。"
services: marketplace-publishing
documentationcenter: na
author: v-jeana
manager: lakoch
editor: 
ms.assetid: 3e99aefe-abeb-414c-8689-15352d25aefd
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: v-jeana; hascipio; v-dabosl
translationtype: Human Translation
ms.sourcegitcommit: e86e4dcc39f9d34dc04afbdccb017d89969f548f
ms.openlocfilehash: fade348a599c41a7f099e7bbd8b45a3b4ccbf910


---
# <a name="understand-your-azure-marketplace-payout-reports"></a>Azure Marketplace 支払いレポートについて
## <a name="access-and-view-your-payout-reports"></a>支払いレポートへのアクセスと表示
デベロッパー センターに移行中に、一部の支払いレポートは https://dev.windows.com/ja-jp のデベロッパー センターで利用できる場合がありますが、他の支払いレポートは、https://publish.windowsazure.com の公開ポータルに表示されたままになる可能性があります。

支払いレポートは、最新の支払いに関連付けられているすべての Marketplace サービスについて、**デベロッパー センター**で利用できるようになります。現在は以下が対象となります。

* VM
* B+C プラン
* EA で提供される Data Services と Dev Services

支払いレポートは、次の **発行ポータル** に引き続き存在します。

* Web Direct (従来の支払いシステムを引き続き使用) で提供される Data Services と Dev Services

レポートは四半期の終了後 45 日間入手可能であり、返金後に計算されます。

### <a name="access-payout-reports-in-dev-center"></a>デベロッパー センターでの支払いレポートへのアクセス
1. デベロッパー センター (https://dev.windows.com/ja-jp) に移動します。
2. **[ダッシュボード]**をクリックします。

    ![LandingPageDashboardHighlight][1]
3. **[入金状況]**をクリックします。

    ![DashboardPayoutSummary][2]

## <a name="view-your-payout-reports-in-dev-center"></a>デベロッパー センターでの支払いレポートの表示
四半期ごとの支払いレポートには、その四半期内で発生したすべてのトランザクションが記録されます。

* 予約済みの額は、今後の入金サイクルの他に生じる支払いを示します (たとえば、この額は翌月の今後の入金に移動されます)。  (事前に顧客の給料が多い場合を除き)、通常、この額は&0; ドルになります。
* 今後の入金または最近の入金の **[詳細の表示]** リンクをクリックし、これらの支払いに関する注意を確認します。
* **[入金明細書]** をクリックして、アプリまたは製品の収益の詳細を表示します。
* **[表示]** リンクをクリックして、個々の明細書を表示します。

    ![PayoutSummaryUpcomingMostRecentLinksStatement][3]
* 個々の明細書の下部にある **[収益の内訳]** フィルターを使用して、複数のアプリまたは製品を表示します (存在する場合)。

    ![PayoutSummaryPaymentStatementsFilterControl][4]

## <a name="view-your-payout-reports-in-publishing-portal"></a>発行ポータルでの支払いレポートの表示
四半期ごとの支払いレポートには、その四半期内で発生したすべてのトランザクションが記録されます。

1. 発行ポータル (https://publish.windowsazure.com) に移動します。
2. **[発行元]** セクションで **[支払いレポート]** をクリックします。
3. ドロップダウン リストをクリックすると、使用可能なすべての四半期支払いレポートが表示されます。

    ![accessingpayoutreport][5]

### <a name="read-your-payout-reports"></a>支払いレポートの読み取り
四半期ごとの支払いレポートには、その四半期内で発生したすべてのトランザクションが記録されます。

* 特定の四半期に関連する台帳項目を探すには、ドロップダウン リストからその四半期の支払いレポートを選択します。 たとえば、2015 年 4 月から 6 月までの台帳項目を確認するには、ドロップダウン リストからその日付範囲を選択します。
* 特定の四半期に関連する支払いの詳細を探すには、ドロップダウン リストからその四半期の後続の支払いレポートを選択します。 たとえば、2015 年 4 月から 6 月までの支払額は、後続の 2015 年 7 月から 9 月までの支払いレポートで確認できます。
  ![readingpayoutreport][6]
* [財務の概要] パネルには、残高、貸方、および借方が分類別に表示されます。
* 元帳項目は個々のトランザクションを示します。

## <a name="definitions"></a>定義
**[財務の概要] パネル:**

![financialdefinitions][7]

**元帳項目:**

![ledgerdefinitions][8]

## <a name="payout-questions"></a>支払いの質問
支払いに関連する質問がある場合は、サポート チームにお問い合わせください。

![payoutquestions][9]

1. サポート ページに移動します。
2. **[支払い]**を選択します。
3. **[支払いに関連する質問]**を選択します。
4. **[要求の開始]**をクリックします。

## <a name="next-steps"></a>次のステップ
その他のサポートの問い合わせについては、<https://portal.azure.com> に問題を記録してください。

[1]: ./media/marketplace-publishing-report-payout/LandingPage-DashboardHighlight.png
[2]: ./media/marketplace-publishing-report-payout/Dashboard-PayoutSummary.png
[3]: ./media/marketplace-publishing-report-payout/PayoutSummary-UpcomingOrMostRecentPaymentLinksSingleStatementLink.png
[4]: ./media/marketplace-publishing-report-payout/PayoutSummary-PaymentStatements-SingleStatement-FilterControl.png
[5]: ./media/marketplace-publishing-report-payout/accessingpayoutreport.png
[6]: ./media/marketplace-publishing-report-payout/readingpayoutreport.png
[7]: ./media/marketplace-publishing-report-payout/financialdefinitions.png
[8]: ./media/marketplace-publishing-report-payout/ledgerdefinitions.png
[9]: ./media/marketplace-publishing-report-payout/payoutquestions.png



<!--HONumber=Feb17_HO2-->


