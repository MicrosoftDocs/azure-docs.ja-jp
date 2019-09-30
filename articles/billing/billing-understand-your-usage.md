---
title: 詳細な利用状況と請求金額について | Microsoft Docs
description: 詳細な利用状況と請求金額について、どのように読んで理解すればよいかを説明します
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 1842d32a838470d9b2af3a778c44c37464d32294
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "68954336"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Azure の利用状況と請求金額ファイル内の用語について

詳細な利用状況と請求金額ファイルには、交渉単価、購入履歴 (予約、Marketplace 利用料金など)、および指定された期間内の返金に基づいて評価された毎日の利用状況が含まれています。
利用料金には、クレジット、税、その他の課金や割引は含まれません。
次の表は、各アカウントの種類にどのような料金が含まれるかを示しています。

アカウントの種類 | Azure の利用状況 | Marketplace の利用状況 | 購入 | 返金
--- | --- | --- | --- | ---
Enterprise Agreement (EA) | はい | はい | はい | いいえ
Microsoft 顧客契約 (MCA) | はい | はい | はい | はい
従量課金制 (PAYG) | はい | はい | いいえ | いいえ

Marketplace 注文 (外部サービスとしても知られています) について詳しくは、「[外部サービスの課金に対する Azure での請求について](billing-understand-your-azure-marketplace-charges.md)」をご覧ください。

ダウンロードの手順については、[Azure の請求書と毎日の利用状況データを取得する方法](billing-download-azure-invoice-daily-usage-date.md)に関するページを参照してください。
利用状況と請求金額の CSV ファイルは、Microsoft Excel またはその他のスプレッドシート アプリケーションで開くことができます。

## <a name="list-of-terms-and-descriptions"></a>用語と説明の一覧

次の表に、Azure の利用状況と請求金額ファイルの最新バージョンで使用されている重要な用語について説明します。
一覧では、従量課金制 (PAYG)、Enterprise Agreement (EA)、および Microsoft 顧客契約 (MCA) アカウントについて取り上げています。

期間 | アカウントの種類 | 説明
--- | --- | ---
AccountName | EA、PAYG | EA 登録アカウントまたは PAYG 課金アカウントの表示名。
AccountOwnerId<sup>1</sup> | EA、PAYG | EA 登録アカウントまたは PAYG 課金アカウントの一意識別子。
AdditionalInfo: | All | サービス固有のメタデータ。 たとえば、仮想マシンのイメージの種類です。
BillingAccountId<sup>1</sup> | All | ルート請求先アカウントの一意識別子。
BillingAccountName | All | 請求先アカウントの名前。
BillingCurrency | All | 請求先アカウントに関連付けられている通貨。
BillingPeriod | EA、PAYG | 料金の請求期間。
BillingPeriodEndDate | All | 請求期間の終了日。
BillingPeriodStartDate | All | 請求期間の開始日。
BillingProfileId<sup>1</sup> | All | EA 登録、PAYG サブスクリプション、MCA 課金プロファイル、AWS 統合アカウントの一意識別子。
BillingProfileName | All | EA 登録、PAYG サブスクリプション、MCA 課金プロファイル、AWS 統合アカウントの名前。
ChargeType | All | 請求金額が利用状況 (**Usage**)、購入 (**Purchase**)、または返金 (**Refund**) のどれを表しているかを示します。
ConsumedService | All | 料金に関連付けられているサービスの名前。
CostCenter<sup>1</sup> | EA、MCA | コストを追跡するためのサブスクリプション用に定義されているコスト センター (MCA アカウント用のオープンの請求期間のみで使用できます)。
コスト | EA、PAYG | CostInBillingCurrency を参照。
CostInBillingCurrency | MCA | 請求通貨での、クレジットまたは税込みでの料金のコスト。
CostInPricingCurrency | MCA | 価格通貨での、クレジットまたは税込みでの料金のコスト。
Currency | EA、PAYG | BillingCurrency を参照。
Date<sup>1</sup> | All | 料金の利用状況または購入日。
EffectivePrice | All | 期間のブレンド単価。 ブレンド価格では、数量が増えるにつれて徐々に価格が下がる単価 (段階的階層化など) の変動が平均されます。
ExchangeRateDate | MCA | 為替レートが確立された日付。
ExchangeRatePricingToBilling | MCA | 価格通貨でのコストを請求通貨に変換するために使用される為替レート。
頻度 | All | 料金が繰り返される予定かどうかを示す。 課金は、1 回限り (**OneTime**) か、月ごとまたは年ごとの繰り返し (**Recurring**) か、または利用状況に基づいて (**UsageBased**) 行われる場合があります。
InvoiceId | PAYG、MCA | 請求書の PDF に記載されている一意のドキュメント ID。
InvoiceSection | MCA | InvoiceSectionName を参照。
InvoiceSectionId<sup>1</sup> | EA、MCA | EA 部門または MCA 請求書セクションの一意識別子。
InvoiceSectionName | EA、MCA | EA 部門または MCA 請求書セクションの名前。
IsAzureCreditEligible | All | Azure クレジットを使用して料金を支払うことができるかどうかを示す (値：True、False)。
Location | MCA | リソースが実行されているデータセンターの場所。
MeterCategory | All | メーターの分類カテゴリの名前。 たとえば、*Cloud services*、*Networking* などです。
MeterId<sup>1</sup> | All | メーターの一意識別子。
MeterName | All | メーターの名前。
MeterRegion | All | 場所に基づいて価格設定されたサービスに対する、データセンターの場所の名前。 Location を参照。
MeterSubCategory | All | メーターのサブ分類カテゴリの名前。
OfferId<sup>1</sup> | All | 購入したオファーの名前。
PartNumber<sup>1</sup> | EA、PAYG | 特定のメーター価格の取得に使用される識別子。
PlanName | EA、PAYG | Marketplace プランの名前。
PreviousInvoiceId | MCA | この明細項目が返金の場合、元の請求書への参照。
PricingCurrency | MCA | 交渉済みの価格に基づいて評価する場合に使用される通貨。
Product | All | 製品の名前。
ProductId<sup>1</sup> | MCA | 製品の一意識別子。
ProductOrderId | All | 製品注文の一意識別子。
ProductOrderName | All | 製品注文の一意の名前。
発行元 | All | Marketplace サービスの発行元。
PublisherType | All | パブリッシャーの種類 (値: **Azure**、**AWS**、**Marketplace**)。
Quantity | All | 購入または消費されるユニットの数。
ReservationId | EA、MCA | 購入した予約インスタンスの一意識別子。
ReservationName | EA、MCA | 購入した予約インスタンスの名前。
ResourceGroup | All | リソースが属している[リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)の名前。
ResourceId<sup>1</sup> | All | [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) のリソースの一意識別子。
ResourceLocation | All | リソースが実行されているデータセンターの場所。 Location を参照。
ResourceName | EA、PAYG | リソースの名前。
ResourceType | MCA | リソース インスタンスの種類。
ServiceFamily | MCA | サービスが属するサービス ファミリ。
ServiceInfo1 | All | サービス固有のメタデータ。
ServiceInfo2 | All | サービス固有の省略可能なメタデータを設定する、以前から使用されているフィールド。
ServicePeriodEndDate | MCA | 消費または購入したサービスに対して価格を定義およびロックしている評価期間の終了日。
ServicePeriodStartDate | MCA | 消費または購入したサービスに対して価格を定義およびロックしている評価期間の開始日。
SubscriptionId<sup>1</sup> | All | Azure サブスクリプションの一意識別子。
SubscriptionName | All | Azure サブスクリプションの名前。
Tags<sup>1</sup> | All | リソースに割り当てられたタグ。 リソース グループのタグは含みません。 内部チャージバックのコストをグループ化または分散するために使用できます。 詳細については、[タグを使用した Azure リソースの整理](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)に関するページを参照してください。
期間 | All | プランの有効期間を表示します。 例: 予約インスタンスの場合は、期間として 12 か月が表示されます。 1 回限りの購入または定期的な購入の場合、期間は 1 か月です (SaaS、Marketplace サポート)。 これは、Azure の消費には適用されません。
UnitOfMeasure | All | サービス課金の測定単位。 たとえば、コンピューティング サービスは時間単位で課金されます。
UnitPrice | EA、PAYG | 請求金額の単位あたりの価格。

_<sup>**1**</sup> 1 つのコスト レコードの一意の ID を作成するために使用されるフィールド。_

一部のフィールドでは、アカウントの種類間で大文字小文字とスペースに違いが生じる場合があることに注意してください。
以前のバージョンの従量課金制の利用状況ファイルには、明細と毎日の利用状況のための個別のセクションがあります。

### <a name="list-of-terms-from-older-apis"></a>以前の API の用語一覧
以下に示したのは、以前の API で用いられていた用語と新しい用語の対応表です。 それらの説明については、前出の表を参照してください。

以前の用語 | 新しい用語
--- | ---
ConsumedQuantity | Quantity
IncludedQuantity | 該当なし
InstanceId | ResourceId
料金 | EffectivePrice
単位 | UnitOfMeasure
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>請求金額が正しいことを確認する

利用状況と請求金額の詳細について詳しくは、[従量課金制](./billing-understand-your-bill.md)または [Microsoft 顧客契約](billing-mca-understand-your-bill.md)の請求書に関するページをご覧ください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

- [Microsoft Azure の請求書の表示とダウンロード](billing-download-azure-invoice.md)
- [Microsoft Azure の利用状況と料金の表示とダウンロード](billing-download-azure-daily-usage.md)
