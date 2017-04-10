---
title: "Azure Marketplace の使用量ベース レポートおよび Seller Insights レポートの概要 | Microsoft Docs"
description: "Azure Marketplace の販売者は、Seller Insights レポートとも呼ばれる使用量ベース レポートを理解してください"
services: Azure Marketplace
documentationcenter: na
author: v-jeana
manager: lakoch
editor: 
ms.assetid: f1ffde66-98f0-4c3e-ad94-fee1f97cae03
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/13/2017
ms.author: v-jeana; hascipio; v-dabosl
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 408394e529c262c0a47aae680c8f6ea1bc8eb7c6
ms.lasthandoff: 12/08/2016


---
# <a name="understand-your-seller-insights-report"></a>Seller Insights レポートの概要
**Seller Insights とは**

提供品で使用が発生した場合、すべての VM および使用量ベース課金制開発者サービスの公開元は、Microsoft から毎月レポートを受け取ります。

**受け取るレポート**

* **ウェルカム メール:** 新しい公開元は、Seller Insights マンスリー レポートの提供が始まることを通知するウェルカム メールを受け取ります。
* **マンスリー売上レポート:** 使用実績がある場合は、レポートとパスワードにアクセスする方法が含まれる 2 番目の電子メールを受け取ります。

  * VM がある場合、または使用量ベース課金制開発者サービス SKU の使用実績ある場合は、提供品の注文、使用量、市場、顧客に関する詳細がマンスリー レポートで示されます。
  * レポートは、顧客データを保護するため、パブリッシャーと Microsoft だけが知っているパスワードによってロックされています。
  * 該当月にどの提供品にも使用実績がない場合、レポートは送られません。

## <a name="understand-your-seller-insights-report"></a>Seller Insights レポートの概要
**SKU およびライセンス タイプ別の注文数: [Marketplace 注文] タブ**

![readingreportbyorders][2]

* スライサーを使用して各要素でレポートをフィルター処理できます。
* グラフには、Azure ライセンス タイプごとの毎月の注文が示されます。 各バーには、その月の注文合計が Azure ライセンス タイプ別に表示されます。
* グラフに毎月の注文が SKU 別に表示されます。 各バーには、すべての SKU の毎月の注文合計が SKU 別に表示されます。
* グラフに、注文数の月ごとの傾向が Azure ライセンス タイプ別と Azure Marketplace ライセンス タイプ別に表示されます。
* 円グラフには、注文数が Azure ライセンス タイプ別と Marketplace ライセンス タイプ別に表示されます。
* 表には、Marketplace ライセンス タイプ別の月ごとの合計注文数、月ごとの合計、すべての月の総計が表示されます。

**SKU およびライセンス タイプ別の注文数: [Marketplace 利用状況] タブ**

![readingreportbyusage][3]

* スライサーを使用して各要素でレポートをフィルター処理できます。
* 正規化された VM の利用状況情報または処理されていない利用状況データのいずれかを選択する必要があります。
* グラフには、Azure ライセンス タイプごとの毎月の利用状況が示されます。 各バーには、その月の利用合計が Azure ライセンス タイプ別に表示されます。
* グラフに毎月の利用状況が SKU 別に表示されます。 各バーには、すべての SKU の毎月の利用合計が SKU 別に表示されます。
* グラフには、利用状況の月ごとの傾向が Azure ライセンス タイプ別と Marketplace ライセンス タイプ別に表示されます。
* 円グラフには、利用状況が Azure ライセンス タイプ別と Marketplace ライセンス タイプ別に表示されます。
* 表には、Marketplace ライセンス タイプ別の月ごとの利用合計、月ごとの合計、すべての月の総計が表示されます。

**[注文データ] タブおよび [利用状況データ] タブ**

これらのタブでは、レポートの生成に使われた詳細なデータが提供されます。

![orderdata][4]

![usagedata][5]

**SKU およびライセンス タイプ別の利用状況: [顧客] タブ**

![customerstab][6]

* 機密性に関する記述に注意してください。
* このタブには、SKU 別の顧客のリスト、プロファイル情報、取引の日付、プロモーションの連絡に関するオプトインが含まれます。
* レポートには、SKU 別の注文数および合計が含まれます。

**免責:**

![legal][1]

法的免責事項を注意深くお読みください。 質問や意見がある場合は、免責事項の下部にあるリンクをクリックすると、Marketplace のサポート ページに移動します。

## <a name="request-a-password-reminder"></a>パスワードのリマインダーを要求する
https://publish.windowsazure.com/ にアクセスし、Microsoft アカウントの資格情報でサインインします。
![passwordreminder][7]

**[公開元]** タブを選択します。 
![selectpublisherstab][8]

URL で公開元 ID を探します。

* この ID をパスワードとして使用して、Seller Insights の Excel ファイルを開きます。
  通知があるまで、これがパスワードとして使用されます。
* ブックを読むときは Windows で MS Office 2013 を使うことをお勧めします。  Microsoft Office for Mac を使うと問題があることが報告されています。

![publisherid][9]

## <a name="next-steps"></a>次のステップ
レポートおよび Insights に関して質問がある場合は、サポート チームにお問い合わせください。

1. https://publish.windowsazure.com/ にあるサポート ページに移動します。
2. **[問題の種類]** ボックスで、**[レポートおよびインサイト]** を選択します。
3. **[カテゴリ]** ボックスで、**[レポートに関する質問]** を選択します。
4. **[要求の開始]**をクリックします。
   ![sellerinsightsquestions][10]

[1]: ./media/marketplace-publishing-report-seller-insights/legal.png
[2]: ./media/marketplace-publishing-report-seller-insights/readingreportbyorders.png
[3]: ./media/marketplace-publishing-report-seller-insights/readingreportbyusage.png
[4]: ./media/marketplace-publishing-report-seller-insights/orderdata.png
[5]: ./media/marketplace-publishing-report-seller-insights/usagedata.png
[6]: ./media/marketplace-publishing-report-seller-insights/customerstab.png
[7]: ./media/marketplace-publishing-report-seller-insights/passwordreminder.png
[8]: ./media/marketplace-publishing-report-seller-insights/selectpublisherstab.png
[9]: ./media/marketplace-publishing-report-seller-insights/publisherid.png
[10]: ./media/marketplace-publishing-report-seller-insights/sellerinsightsquestions.png

