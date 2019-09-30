---
title: Azure 請求書の確認 | Microsoft Docs
description: Azure サブスクリプションの使用状況と課金内容を確認し、理解する方法について説明します。
services: ''
documentationcenter: ''
author: bandersmsft
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 77c1a85136b2117af7396b8eec2d8b92b335d61d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "60369968"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Microsoft Azure の請求書の用語を確認します。

請求書は、料金の概要と支払いの手順を提示しています。 請求書は、[Azure Portal](https://portal.azure.com/) から Portable Document Format (.pdf) のダウンロードで入手するか、電子メール経由で受信することができます。 詳しくは、「[Azure の請求書と毎日の使用状況データを取得する方法](billing-download-azure-invoice-daily-usage-date.md)」をご覧ください。

注意点を次に示します。

-   無料試用版のサブスクリプションをご利用の場合は、Azure Portal から毎日の使用状況に関する情報は取得できますが、請求書は発行されません。

-   前回の請求期間末の最大 24 時間分の使用状況が、現在の請求書に表示される場合があります。

-   米国以外のお客様向けの課金明細書に記載されている料金は、見積もりとしての使用を目的として提供されています。 銀行ではさまざまな費用に通貨換算率を適用している場合があります。

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>請求書の用語と説明の詳細
次のセクションでは、請求書に表示される主要な用語と、各用語の説明を一覧に示します。

### <a name="account-information"></a>アカウント情報

請求書のアカウント情報のセクションは最初のページの上部に表示され、プロファイルおよびサブスクリプションに関する情報を示します。

![請求書のアカウント情報セクション](./media/billing-understand-your-invoice/1.png)

| 期間 | 説明 |
| --- | --- |
| 顧客発注番号 |追跡のために割り当てられる、省略可能な発注書番号 |
| 請求書番号 |追跡のために使用される、Microsoft で生成した一意の請求書番号 |
| Billing cycle |この請求書に含まれる日付の範囲 |
| 請求日 |請求書が生成された日付。通常は、請求サイクルの終了の翌日 |
| 支払い方法 |アカウントで使用される支払いの種類 (請求書またはクレジット カード) |
| 請求先 |一覧表示される、アカウントに対応した請求先住所 |
| サブスクリプション オファー (従量課金制) |購入したサブスクリプション オファーの種類 (従量課金制、BizSpark Plus、Azure Pass など)。 詳細については、[Azure のオファーの種類](https://azure.microsoft.com/support/legal/offer-details/)に関する記事をご覧ください。 |
| アカウント所有者メール アドレス | Microsoft Azure アカウントが登録されたアカウントのメール アドレス。 <br /><br />メール アドレスを変更するには、「[Azure アカウントのプロファイル情報 (連絡先メール アドレス、住所、電話番号など) を変更する方法](billing-how-to-change-azure-account-profile.md)」を参照してください。 |

### <a name="understand-the-invoice-summary"></a>請求書の要約について
**請求書の要約**セクションには、直近の請求期間以降の合計取引金額と、現在の使用料金が一覧表示されます。

![請求書の要約セクション](./media/billing-understand-your-invoice/2.png)

サブスクリプションの名前 (「運用環境のストレージ」) は、この請求書のサブスクリプションの名前です。

#### <a name="understand-the-previous-charges"></a>繰越した料金について
請求書の繰越残高、入金額、および未払い残高のセクションでは、直近の請求書期間以降の取引が要約されています。

| 期間 | 説明 |
| --- | --- |
| 繰越残高 |直近の請求期間以降の合計請求額 |
| 入金額 |直近の請求期間に適用した支払いおよびクレジットの合計金額 |
| (前の請求サイクルからの) 未払い残高 |直近の請求期間以降のアカウントのクレジットまたは支払い残高 |

#### <a name="understand-the-current-charges"></a>現在の料金について
請求書の現在の料金セクションは、現在の請求期間の月額料金に関する詳細を示しています。

| 期間 | 説明 |
| --- | --- |
| 利用料金 |利用料金は、現在の請求期間のサブスクリプションに対する合計月額料金です。|
| 割引 |現在の請求期間に適用されるサービス割引|
| 調整 |現在の請求期間に適用されるその他のクレジット (割引額、控除など) または未処理料金<br/><br/>たとえば、Visual Studio Enterprise with MSDN のプランをご利用の場合は、毎月のクレジット額が表示されます。 サブスクリプションをキャンセルすると、サブスクリプション オファーで提供される毎月のクレジット額を超えた月額利用料金が表示されます。 料金は、現在の請求期間の開始日からサブスクリプションのキャンセル日まで発生します。 |

#### <a name="sold-to-and-payment-instructions"></a>販売先と支払い指示

次の表では、請求書の 2 ページ目に示される販売先と支払い指示について説明します。

| 期間 |説明 |
| --- | --- |
| 販売先 |アカウントにあるプロファイルの住所。 <br/><br/>住所を変更する必要がある場合は、「[Azure アカウントのプロファイル情報 (連絡先メール アドレス、住所、電話番号など) を変更する方法](billing-how-to-change-azure-account-profile.md)」を参照してください。|
| 支払い指示 |支払い方法 (クレジット カードや請求書など) に応じた支払い方法に関する指示 |

#### <a name="usage-charges"></a>利用料金

請求書の利用料金セクションには、料金のメーター レベルの情報が表示されます。

![利用料金セクション](./media/billing-understand-your-invoice/3.png)

次の表では、請求書に表示される利用料金の列のヘッダーについて説明します。

| 期間 |説明 |
| --- | --- |
| 名前 |使用状況の最上位レベルのサービスを示します。 |
| 種類 |料金に影響する Azure サービスの種類を定義します。 |
| リソース |消費しているメーターの測定単位を示します。 |
| リージョン |データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します。 |
| 使用量 |請求期間中に使用されたメーターの量 |
| Included |現在の請求期間内に無料で含まれるメーターの量 |
| 課金対象 |消費量と含まれる量の差が表示されます。 この差異について課金されます。 プランに一定量が含まれない従量課金制では、この合計は消費量と同じになります。 |
| 料金 |課金対象項目ごとに請求される価格 |
| 値 |[超過量] 列に [単価] 列を掛けた料金を示します。 消費量が含まれる量を超過していない場合、この列の料金は発生しません。 |
| 小計 |この請求期間の課税前の合計料金 |
| 総計 |この請求期間の課税後の合計料金 |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>請求書の料金が正しいことを確認する方法
請求書に詳細を確認したい料金がある場合は、[Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)に関する記事をご覧ください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
