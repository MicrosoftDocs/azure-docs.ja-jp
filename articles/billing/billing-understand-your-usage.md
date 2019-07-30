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
ms.openlocfilehash: 2eb9f8e19be2a7b6220bc34bf4ce0c72c4ac0b4f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275055"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Azure の利用状況と請求金額ファイル内の用語について

詳細な利用状況と請求金額ファイルには、交渉単価、購入履歴 (予約、Marketplace 利用料金など)、および指定された期間内の返金に基づいて評価された毎日の利用状況が含まれています。
利用料金には、クレジット、税、その他の課金や割引は含まれません。
次の表は、各アカウントの種類にどのような料金が含まれるかを示しています。

アカウントの種類 | Azure の利用状況 | Marketplace の利用状況 | 購入 | 返金
--- | --- | --- | --- | ---
Enterprise Agreement (EA) | はい | はい | はい | いいえ
Microsoft 顧客契約 (MCA) | はい | はい | はい | はい
従量課金制 (PAYG) | はい | いいえ | いいえ | いいえ

Marketplace 注文 (外部サービスとしても知られています) について詳しくは、「[外部サービスの課金に対する Azure での請求について](billing-understand-your-azure-marketplace-charges.md)」をご覧ください。

ダウンロードの手順については、[Azure の請求書と毎日の利用状況データを取得する方法](billing-download-azure-invoice-daily-usage-date.md)に関するページをご覧ください。
利用状況と請求金額ファイルは、コンマ区切り値 (.csv) のファイル形式で利用でき、スプレッドシートのアプリケーションで開くことができます。

## <a name="list-of-terms-and-descriptions"></a>用語と説明の一覧

次の表に、Azure の利用状況と請求金額ファイルの最新バージョンで使用されている重要な用語について説明します。
一覧では、従量課金制 (PAYG)、Enterprise Agreement (EA)、および Microsoft 顧客契約 (MCA) アカウントについて取り上げています。

期間 | アカウントの種類 | 説明
--- | --- | ---
AccountName | EA | 登録アカウントの表示名。
AccountOwnerId | EA | 登録アカウントの一意識別子。
AdditionalInfo: | All | サービス固有のメタデータ。 たとえば、仮想マシンのイメージの種類です。
BillingAccountId | EA、MCA | ルート請求先アカウントの一意識別子。
BillingAccountName | EA、MCA | 請求先アカウントの名前。
BillingCurrency | EA、MCA | 請求先アカウントに関連付けられている通貨。
BillingPeriod | EA | 料金の請求期間。
BillingPeriodEndDate | EA、MCA | 請求期間の終了日。
BillingPeriodStartDate | EA、MCA | 請求期間の開始日。
BillingProfileId | EA、MCA | EA 登録または MCA 課金プロファイルの一意識別子。
BillingProfileName | EA、MCA | EA 登録または MCA 課金プロファイルの名前。
ChargeType | EA、MCA | 請求金額が利用状況 (**Usage**)、購入 (**Purchase**)、または返金 (**Refund**) のどれを表しているかを示します。
ConsumedQuantity | PAYG | Quantity を参照。
ConsumedService | All | 料金に関連付けられているサービスの名前。
コスト | EA | CostInBillingCurrency を参照。
CostCenter | EA、MCA | コストを追跡するためのサブスクリプション用に定義されているコスト センター (MCA アカウント用のオープンの請求期間のみで使用できます)。
CostInBillingCurrency | MCA | 請求通貨での、クレジットまたは税込みでの料金のコスト。
CostInPricingCurrency | MCA | 価格通貨での、クレジットまたは税込みでの料金のコスト。
通貨 | PAYG | BillingCurrency を参照。
Date | EA、MCA | 料金の利用状況または購入日。
ExchangeRateDate | MCA | 為替レートが確立された日付。
ExchangeRatePricingToBilling | MCA | 価格通貨でのコストを請求通貨に変換するために使用される為替レート。
頻度 | EA、MCA | 料金が繰り返される予定かどうかを示す。 課金は、1 回限り (**OneTime**) か、月ごとまたは年ごとの繰り返し (**Recurring**) か、または利用状況に基づいて (**UsageBased**) 行われる場合があります。
IncludedQuantity | PAYG | 現在の請求期間内に無料で含まれるメーターの量。
InstanceId | PAGY | ResourceId を参照。
InvoiceId | EA、MCA | 請求書の PDF に記載されている一意のドキュメント ID。
InvoiceSection | MCA | InvoiceSectionName を参照。
InvoiceSectionId | EA、MCA | EA 部門または MCA 請求書セクションの一意識別子。
InvoiceSectionName | EA、MCA | EA 部門または MCA 請求書セクションの名前。
IsAzureCreditEligible | EA、MCA | Azure クレジットを使用して料金を支払うことができるかどうかを示す (値：True、False)。
IsEstimated | All | 請求期間がクローズ/ファイナライズされているかどうかを示します。 請求書が生成されるまでは、請求期間の使用状況データが変更される可能性があります。 これらのレコードは、「推定」とマークされます。
Location | EA、MCA | リソースが実行されているデータセンターの場所。
MeterCategory | All | メーターの分類カテゴリの名前。 たとえば、*Cloud services*、*Networking* などです。
MeterId | All | メーターの一意識別子。
MeterName | All | メーターの名前。
MeterRegion | All | 場所に基づいて価格設定されたサービスに対する、データセンターの場所の名前。 Location を参照。
MeterSubCategory | All | メーターのサブ分類カテゴリの名前。
OfferId | EA、MCA | 購入したオファーの名前。
PartNumber | EA | 特定のメーター価格の取得に使用される識別子。
PlanName | EA | Marketplace プランの名前。
PreviousInvoiceId | MCA | この明細項目が返金の場合、元の請求書への参照。
PricingCurrency | MCA | 交渉済みの価格に基づいて評価する場合に使用される通貨。
Product | MCA | ProductName を参照。
ProductId | EA、MCA | 製品の一意識別子。
ProductName | EA | 製品の名前。
ProductOrderId | EA、MCA | 製品注文の一意識別子。
ProductOrderName | EA、MCA | 製品注文の一意の名前。
発行元 | EA、MCA | Marketplace サービスの発行元。
PublisherType | EA、MCA | 発行元の種類 (値: firstParty、thirdPartyReseller、thirdPartyAgency)。
Quantity | EA、MCA | 購入または消費されるユニットの数。
料金 | PAYG | UnitPrice を参照。
ReservationId | EA、MCA | 購入した予約インスタンスの一意識別子。
ReservationName | EA、MCA | 購入した予約インスタンスの名前。
ResourceGroupId | EA、MCA | リソースが属している[リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)の一意識別子。
ResourceGroupName | EA、MCA | リソースが属している[リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)の名前。
ResourceId | EA、MCA | [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) のリソースの一意識別子。
ResourceLocation | EA、MCA | リソースが実行されているデータセンターの場所。 Location を参照。
ResourceName | EA | リソースの名前。
ResourceType | MCA | リソース インスタンスの種類。
ServiceFamily | EA、MCA | サービスが属するサービス ファミリ。
ServiceInfo1 | All | サービス固有のメタデータ。
ServiceInfo2 | All | サービス固有の省略可能なメタデータを設定する、以前から使用されているフィールド。
ServicePeriodEndDate | MCA | 消費または購入したサービスに対して価格を定義およびロックしている評価期間の終了日。
ServicePeriodStartDate | MCA | 消費または購入したサービスに対して価格を定義およびロックしている評価期間の開始日。
SubscriptionId | All | サブスクリプションの一意識別子。
SubscriptionName | All | サブスクリプションの名前。
Tags | All | リソースに割り当てられたタグ。 リソース グループのタグは含みません。 内部チャージバックのコストをグループ化または分散するために使用できます。 詳細については、[タグを使用した Azure リソースの整理](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)に関するページを参照してください。
単位 | PAYG | UnitOfMeasure を参照。
UnitOfMeasure | All | サービス課金の測定単位。 たとえば、コンピューティング サービスは時間単位で課金されます。
UnitPrice | EA | 請求金額の単位あたりの価格。
UsageDate | PAYG | Date を参照。

一部のフィールドでは、アカウントの種類間で大文字小文字とスペースに違いが生じる場合があることに注意してください。
以前のバージョンの従量課金制の利用状況ファイルには、明細と毎日の利用状況のための個別のセクションがあります。

## <a name="ensure-that-your-charges-are-correct"></a>請求金額が正しいことを確認する

利用状況と請求金額の詳細について詳しくは、[従量課金制](./billing-understand-your-bill.md)または [Microsoft 顧客契約](billing-mca-understand-your-bill.md)の請求書に関するページをご覧ください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

- [Microsoft Azure の請求書の表示とダウンロード](billing-download-azure-invoice.md)
- [Microsoft Azure の利用状況と料金の表示とダウンロード](billing-download-azure-daily-usage.md)
