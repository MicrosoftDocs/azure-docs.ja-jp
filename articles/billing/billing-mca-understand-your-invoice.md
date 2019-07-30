---
title: Azure での Microsoft 顧客契約の請求書を理解する
description: Azure での Microsoft 顧客契約の請求書を読み取って理解する方法を説明します
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fed658d3f672d6116d7c2b0f3e2e9ad989dd67c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490640"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Microsoft 顧客契約の請求書の用語

この記事は、Microsoft 顧客契約の Azure 課金アカウントを対象としています。 [Microsoft 顧客契約にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-customer-agreement)。

請求書は、料金の概要と支払いの手順を提示しています。 請求書は、[Azure Portal](https://portal.azure.com/) から Portable Document Format (.pdf) のダウンロードで入手するか、電子メール経由で受信することができます。 詳細については、[Microsoft Azure の請求書の表示とダウンロード](billing-download-azure-invoice.md)を参照してください。

## <a name="billing-period"></a>請求期間

ユーザーへの請求は月単位で行われます。 [Azure portal](https://portal.azure.com/) の課金プロファイルのプロパティで*請求日*を調べることで、その月の何日に請求書を受け取るかを確認できます。 請求期間の終わりと請求日の間に発生する料金は、次の請求期間内にあるため、次月の請求書に含められます。 各請求書の請求期間開始日と終了日は、上記の **[課金の要約]** の請求書 PDF に記載されています。

## <a name="invoice-terms-and-descriptions"></a>請求書の用語と説明

以降のセクションでは、請求書で見られる重要な用語を示し、各用語について説明します。

### <a name="invoice-summary"></a>請求書の要約

最初のページの最上部にある **[請求書の要約]** には、課金プロファイルと支払い方法に関する情報が表示されます。

![請求書の要約セクション](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| 期間 | 説明 |
| --- | --- |
| 販売先 |請求先アカウントのプロパティに含まれる、法人の住所|
| 請求先 |課金プロファイルのプロパティに含まれる、請求書を受け取る課金プロファイルの請求先住所|
| 課金プロファイル |請求書を受け取る課金プロファイルの名前 |
| 発注 number |追跡のために割り当てられる、省略可能な発注書番号 |
| 請求書番号 |追跡のために使用される、Microsoft で生成した一意の請求書番号 |
| 請求日 |請求書が生成された日付。通常は、請求期間が終了してから 5 から 12 日後。 請求書の日付は、課金プロファイルのプロパティで確認できます。|
| 支払い条件 |Microsoft の請求に対する支払い方法。 *30 日払い*とは、請求書の日付から 30 日以内に支払うことを意味します。 |

### <a name="billing-summary"></a>課金の要約

**[課金の要約]** には、課金プロファイルに対する前回の請求期間以降の請求金額、適用されるクレジット、税、合計支払金額が表示されます。

![[課金の要約] セクション](./media/billing-understand-your-invoice-mca/billingsummary.png)

| 期間 | 説明 |
| --- | --- |
| Charges|この課金プロファイルに対する前回の請求期間以降の Microsoft の料金の合計額 |
| 謝辞 |払い戻しで受け取ったクレジット |
| 適用された Azure クレジット | 請求期間ごとに Azure の料金に自動的に適用される Azure クレジット |
| 小計 |課税前の金額 |
| 税 |支払う税金のタイプと額。課金プロファイルの国や地域によって異なります。 税金を支払う必要がない場合、請求書に税は表示されません。 |
| 推定合計節約額 |有効な割引によって節約される推定の合計金額。 該当する場合は、請求書セクションごとの詳細の購入品目の下に、有効な割引率が表示されます。 |

### <a name="invoice-sections"></a>請求書セクション

課金プロファイルの各請求書セクションには、料金、適用される Azure クレジットの金額、税、合計支払金額が表示されます。

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>請求書セクションごとの詳細

詳細には、製品の注文ごとに分けられた各請求書セクションのコストが表示されます。 各製品の注文では、コストがサービスのタイプごとに分けられています。 製品およびサービスの日単位の料金は、Azure portal と、Azure の利用状況および請求金額 CSV で確認できます。 詳細については、[Microsoft 顧客契約の請求書の料金を理解する](billing-mca-understand-your-bill.md)ことに関する記事をご覧ください。

各サービス ファミリの合計支払金額は、*クレジット/料金*から *Azure クレジット*を差し引き、*税*を加算して計算されます。


![請求書セクションごとの詳細](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| 期間 |説明 |
| --- | --- |
| 単価 | 使用量の評価に使用される (価格設定通貨による) サービスの実効単価。 これは、製品、サービス ファミリ、測定、オファーによって異なります。 |
| 数量 | 請求期間中に購入または使用された数量 |
| 料金/クレジット | クレジットまたは返金が適用された後の料金の正味金額 |
| Azure クレジット | 料金/クレジットに適用される Azure クレジットの金額|
| 課税率 | 国や地域によって異なる課税率 |
| 税額 | 課税率に基づいて購入に適用される税金の額 |
| 合計 | 購入の合計支払金額 |

### <a name="how-to-pay"></a>支払い方法

請求書の下部に、請求額の支払い手順が表示されます。 小切手、電信送金、オンライン決済による支払いが可能です。 オンライン決済の場合は、クレジット/デビット カードまたは Azure クレジット (該当する場合) を使用できます。

### <a name="publisher-information"></a>パブリッシャー情報

請求にサード パーティのサービスが含まれている場合は、請求書の下部に各発行元の名前と住所が表示されます。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

- [課金プロファイルの請求書の料金を理解する](billing-mca-understand-your-bill.md)
- [Azure の請求書と毎日の使用状況データを取得する方法](billing-download-azure-invoice-daily-usage-date.md)
- [組織の Azure の価格を表示する](billing-ea-pricing.md)
- [課金プロファイルの税務書類を表示する](billing-mca-download-tax-document.md)
