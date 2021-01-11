---
title: 支払いの概要の概説 - Azure Marketplace
description: '[支払いの概要] には、プランで獲得した金額の詳細が表示されます。 また、支払いを受け取るタイミングと支払われる金額を確認することもできます。'
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 2e7658e965931dd78a6db0e2c9fee36edffff98b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479814"
---
# <a name="payout-summary-overview"></a>支払いの概要の概説

[[支払いの概要]](./payout-summary.md) には、Microsoft で獲得した金額の詳細が表示されます。 また、支払いを受け取るタイミングと支払われる金額を確認することもできます。

Azure Marketplace でオファリングを販売している場合は、[支払いの概要] に成功した支払いに関する情報も表示されます。 Azure Marketplace の支払いの詳細については、「[Microsoft Azure Marketplace 参加ポリシー](https://docs.microsoft.com/legal/marketplace/participation-policy)」と「[Microsoft Azure Marketplace 発行元契約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx)」を参照してください。

> [!NOTE]
> 支払いの対象となるには、収益が $50 の[支払いしきい値](./payment-thresholds-methods-timeframes.md)に達している必要があります。 支払いしきい値の詳細については、「[Microsoft Azure Marketplace 発行元契約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx)」を参照してください。

支払いの詳細を表示するには、[パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインし、画面の右上隅にある [支払い] アイコンを選択します。

![パートナー センター ポータルの右上隅にある支払いアイコンを示しています。](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>ロールとアクセス許可

支払いレポートにアクセスするためのロールとアクセス許可は次のとおりです。

| レポート/ページ | アカウント所有者 | Manager | Developer | 経営担当者 | 財務担当者 | マーケター |
| --- | --- | --- | --- | --- | --- | --- |
| 取得レポート (ほぼリアルタイムのデータを含む) | 表示可能 | 表示可能 | アクセス権なし | アクセス権なし | 表示可能 | アクセス権なし |
| フィードバック レポート/応答 | フィードバックの表示および送信可能 | フィードバックの表示および送信可能 | フィードバックの表示および送信可能 | アクセス権なし | アクセス権なし | フィードバックの表示および送信可能 |
| --- | --- | --- | --- | --- | --- | --- |
| 正常性レポート (ほぼリアルタイムのデータを含む) | 表示可能 | 表示可能 | 表示可能 | 表示可能 | アクセス権なし | アクセス権なし |
| 利用状況レポート | 表示可能 | 表示可能 | 表示可能 | 表示可能 | アクセス権なし | アクセス権なし |
| 支払い受取口座 | 更新可能 | アクセス権なし | アクセス権なし | アクセス権なし | 更新可能 | アクセス権なし |
| 税プロファイル | 更新可能 | アクセス権なし | アクセス権なし | アクセス権なし | 更新可能 | アクセス権なし |
| 支払いの概要 | 表示可能 | アクセス権なし | アクセス権なし | アクセス権なし | 表示可能 | アクセス権なし  |
| | | | | | | |

## <a name="payment-schedules"></a>支払いスケジュール

保持期間、パートナーの可視性、顧客がクレジット カードや請求書を使用する場合などの支払いスケジュールの詳細については、**支払いの詳細**に関するトピックの「[支払いスケジュール](./payout-policy-details.md#payment-schedules)」セクションを参照してください。

## <a name="transaction-history-download-export"></a>取引履歴のダウンロード エクスポート

このオプションにより、[取引履歴] ページに表示される各収益項目のダウンロードが提供されます。 これには、収益の種類、日付、関連する取引金額、顧客、製品、およびインセンティブ プログラムに関連するその他の取引詳細が含まれます。

| 列名 | 説明 |
| --- | --- |
| earningId | 各収益の一意識別子 |
| participantId | プログラムによるパートナー収益のプライマリ ID |
| participantIdType | インセンティブ プログラムの場合はプログラム ID、プログラムがストア プログラムおよび Azure Marketplace 用である場合は販売者 |
| participantName | 収益パートナーの名前 |
| partnerCountryCode | 収益パートナーの場所、国、地域 |
| programName | インセンティブ/ストア プログラム名 |
| transactionId | 取引の一意識別子 |
| transactionCurrency | 元の顧客取引が発生した通貨 (パートナーの場所の通貨ではありません) |
| transactionDate | 取引の日付。 多数の取引が 1 つの収益に寄与するプログラムに便利です |
| transactionExchangeRate | 対応する取引の米国ドル金額を示すために使用される為替レート |
| transactionAmount | 生成された収益に基づく元の取引通貨の取引金額 |
| transactionAmountUSD | 米国ドル (USD) での取引金額 |
| lever | 収益のビジネス ルール |
| earningRate | 収益を生成するために取引金額に適用されたインセンティブ率 |
| 数量 | 取引プログラムの課金数量。 プログラムによって異なります |
| quantityType | 数量の種類。例:課金数量、月間アクティブ ユーザー (MAU) |
| earningType | 料金、リベート、共同、販売などであるかどうかを示します |
| earningAmount | 元の取引通貨での収益金額 |
| earningAmountUSD | 米国ドルでの収益金額 |
| earningDate | 収益の日付 |
| calculationDate | 収益がシステムで計算された日付 |
| earningExchangeRate | 対応する米国ドル金額を示すために使用される為替レート |
| exchangeRateDate | earningAmountUSD の計算に使用される為替レートの日付 |
| paymentAmountWOTax | &quot;送信済み&quot; の支払いのみを対象とする支払先通貨での収益金額 (税別) |
| paymentCurrency | 支払いプロファイルでパートナーによって選択された支払先通貨。 送信済みの支払いにのみ表示されます |
| paymentExchangeRate | ExchangeRateDate を使用して支払通貨での paymentAmountWOTax を計算するために使用される為替レート |
| paymentId | 支払いの一意識別子。 この番号は、口座取引明細書に表示されます |
| paymentStatus | 支払いの状態 |
| paymentStatusDescription | 支払い状態の説明 |
| customerId | 常に空白になります |
| customerName | 常に空白になります |
| partNumber | 常に空白になります |
| productName | 取引にリンクされている製品名 |
| productId | 一意の製品識別子 |
| parentProductId | 一意の親製品識別子。 取引の親製品がない場合は、親製品 ID = 製品 ID です。 |
| parentProductName | 親製品の名前。 取引の親製品がない場合は、親製品名 = 製品名です。 |
| productType | 製品の種類 (アプリ、アドオン、ゲームなど) |
| invoiceNumber | 請求書番号 (Enterprise Agreement の場合のみ) |
| resellerId | リセラー識別子 |
| resellerName | リセラー名 |
| transactionType | 取引の種類 (購入、払戻、取消、配賦など) |
| localProviderSeller | レコードのローカル プロバイダー/販売者 |
| taxRemitted | 送金された税金額 (消費税、使用税、または VAT/GST 税)。 |
| taxRemitModel | 税の送金を担当する当事者 (消費税、使用税、または VAT/GST 税)。 |
| storeFee | コマーシャル マーケットプレースでアプリまたはアドオンを利用できるようにするための料金として、Microsoft によって保持される金額。 |
| transactionPaymentMethod | 取引に使用される顧客の支払い方法 (カード、携帯電話会社の請求、PayPal など) |
| tpan | サード パーティの広告ネットワーク |
| customerCountry | 顧客の国および地域 |
| customerCity | 顧客の市区町村 |
| customerState | 顧客の都道府県 |
| customerZip | 顧客の郵便番号 |
| TenantId | テナントの ID |
| externalReferenceId | プログラムの一意識別子 |
| externalReferenceIdLabel | 一意識別子のラベル |
| transactionCountryCode | 取引が発生した国および地域番号 |
| taxCountry | 顧客の国および地域 |
| taxState | 顧客の都道府県 |
| taxCity | 顧客の市区町村 |
| taxZipCode | 顧客の郵便番号 |
| LicensingProgramName | ライセンス プログラムの名前 |
| Program Code | プログラム名と共にマップする文字列 |
| earningAmountInLastPaymentCurrency | 前回の支払い通貨での収益金額 (前の支払いが行われていない場合、フィールドは空になります) |
| lastPaymentCurrency | 前回の支払い通貨 (前の支払いが行われていない場合、フィールドは空になります) |
| AssetId | Marketplace サービスの顧客注文の一意識別子。 これは購入品目を表します。 複数の資産が存在することもあります。 |
| OrderId | 顧客の請求書に関連しています |
| LineItemId | 顧客の請求書の個別行 |
| 顧客の国および地域 | 顧客が指定した国や地域の名前。 これは、顧客の Azure サブスクリプションの国や地域とは異なる場合があります。 |
| Customer EmailAddress | 顧客が指定したメール アドレス。 これは、顧客の Azure サブスクリプションのメール アドレスとは異なる場合があります。 |
| SkuId | 発行中に定義される SKU ID。 1 つのプランに多数の SKU を関連付けることは可能ですが、1 つの SKU に関連付けることのできるプランは 1 つのみです。 |

> [!NOTE]
> 取引の公開オプションに関するすべてのレポートと分析情報は、パートナー センターの [分析] セクションにあります。

## <a name="billing-questions-and-support"></a>課金に関する質問とサポート
課金サポートについては、コマーシャル マーケットプレースの[発行元のサポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

## <a name="next-step"></a>次の手順

- [支払いの概要](./payout-summary.md)
