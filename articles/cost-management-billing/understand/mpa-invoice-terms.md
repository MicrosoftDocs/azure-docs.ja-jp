---
title: Azure での Microsoft Partner Agreement の請求書を理解する
description: Azure での Microsoft Partner Agreement の請求書を読み取って理解する方法を説明します
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 9ff151071d42308137bdb8e9064f9b27ed194cae
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688846"
---
# <a name="terms-in-your-microsoft-partner-agreement-invoice"></a>Microsoft Partner Agreement の請求書の用語

この記事は、Microsoft Partner Agreement の Azure 課金アカウントを対象としています。 [Microsoft Partner Agreement にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-partner-agreement)。

請求書は、料金の概要と支払いの手順を提示しています。 請求書は、[Azure Portal](https://portal.azure.com/) から Portable Document Format (.pdf) のダウンロードで入手するか、電子メール経由で受信することができます。 詳細については、[Microsoft Azure の請求書の表示とダウンロード](download-azure-invoice.md)を参照してください。

以降のセクションでは、請求書で見られる重要な用語を示し、各用語について説明します。

## <a name="billing-period"></a>請求期間

ユーザーへの請求は月単位で行われます。 請求期間の終わりと請求日の間に発生する料金は、次の請求期間内にあるため、次月の請求書に含められます。 各請求書の請求期間開始日と終了日は、上記の **[課金の要約]** の請求書 PDF に記載されています。

## <a name="invoice-summary"></a>請求書の要約

最初のページの最上部にある **[請求書の要約]** には、課金プロファイルと支払い方法に関する情報が表示されます。

<!-- add screenshot -->

| 期間 | 説明 |
| --- | --- |
| 販売先 |請求先アカウントのプロパティに含まれる、法人の住所|
| 請求先 |課金プロファイルのプロパティに含まれる、請求書を受け取る課金プロファイルの請求先住所|
| 課金プロファイル |請求書を受け取る課金プロファイルの名前 |
| 発注 number |追跡のために割り当てられる、省略可能な発注書番号 |
| 請求書番号 |追跡のために使用される、Microsoft で生成した一意の請求書番号 |
| 請求日 |請求書が生成された日付。通常は、請求期間が終了してから 5 から 12 日後。 請求書の日付は、課金プロファイルのプロパティで確認できます。|
| 支払い条件 |Microsoft の請求に対する支払い方法。 "*60 日払い*" とは、請求書の日付から 60 日以内に支払うことを意味します。 |

## <a name="billing-summary"></a>課金の要約

**[課金の要約]** には、課金プロファイルに対する前回の請求期間以降の請求金額、適用されるクレジット、税、合計支払金額が表示されます。

<!-- add screenshot -->

| 期間 | 説明 |
| --- | --- |
| Charges|この課金プロファイルに対する前回の請求期間以降の Microsoft の料金の合計額 |
| 謝辞 |払い戻しで受け取ったクレジット |
| 適用された Azure クレジット | 請求期間ごとに Azure の料金に自動的に適用される Azure クレジット |
| 小計 |課税前の金額 |
| 税 |支払う税金のタイプと額。課金プロファイルの国や地域によって異なります。 税金を支払う必要がない場合、請求書に税は表示されません。 |
| 推定合計節約額 |有効な割引によって節約される推定の合計金額。 該当する場合は、請求書セクションごとの詳細の購入品目の下に、有効な割引率が表示されます。 |


## <a name="billing-details-by-product"></a>製品ごとの請求の詳細

**[製品ごとの請求の詳細]** セクションには、この課金プロファイルに関連付けられた製品ごとに請求金額の合計が一覧表示されます。 Azure の利用状況と請求金額を含む CSV で、各製品の請求金額に対する日単位の内訳を確認できます。 Azure の利用状況と請求金額を含む CSV と照らして請求書の PDF を調整する方法については、[Microsoft Partner Agreement の請求書について](review-partner-agreement-bill.md)のページを参照してください。

## <a name="how-to-pay"></a>支払い方法

請求書の下部に、請求額の支払い手順が表示されます。 請求書の日付から 60 日以内に小切手または電信送金で支払うことができます。

## <a name="publisher-information"></a>パブリッシャー情報

請求にサード パーティのサービスが含まれている場合は、請求書の下部に各発行元の名前と住所が表示されます。

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft Partner Agreement へのアクセスの確認
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [課金プロファイルの請求書の料金を理解する](review-customer-agreement-bill.md)
- [Azure の請求書と毎日の使用状況データを取得する方法](../manage/download-azure-invoice-daily-usage-date.md)
- [組織の Azure の価格を表示する](../manage/ea-pricing.md)
- [課金プロファイルの税務書類を表示する](mca-download-tax-document.md)
