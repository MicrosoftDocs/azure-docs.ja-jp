---
title: 'Microsoft 顧客契約の Azure の利用状況と料金を含む CSV に関する用語について理解します | Microsoft Docs '
description: 課金プロファイルの Azure の利用状況と料金を含む CSV のセクションを読んで理解する方法について説明します。
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 8f71f42386ce49d4d7178cb03d28d74edacd7e39
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57836948"
---
# <a name="understand-terms-on-your-azure-usage-and-charges-csv-for-a-microsoft-customer-agreement"></a>Microsoft 顧客契約の Azure の利用状況と料金を含む CSV に関する用語について理解します。

この記事では、Microsoft 顧客契約の請求先アカウントについて説明します。 [Microsoft 顧客契約にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-customer-agreement)。

Azure の利用状況および料金を含む CSV ファイルには、現在の請求期間を対象に毎日測定された利用料金が含まれています。

Azure の利用状況と料金を含むファイルを入手する方法については、「[View and download Azure usage and charges for your Microsoft Customer Agreement (Microsoft 顧客契約の Azure の利用状況と料金の表示とダウンロード)](billing-download-azure-daily-usage.md)」を参照してください。
このファイルは、スプレッドシート アプリケーションで開くことができるコンマ区切り値 (.csv) ファイル形式で入手できます。

利用料金は、サブスクリプションの**月額**料金の合計です。 利用料金では、クレジットや割引は考慮されません。

## <a name="changes-in-the-enterprise-agreement-azure-usage-and-charges-csv"></a>Enterprise Agreement における Azure の利用状況と料金を含む CSV の変更

EA のお客様であれば、課金プロファイル Azure の利用状況を含む CSV ファイルの用語は、EA Azure の利用状況を含む CSV ファイルの用語と異なることに気付かれるでしょう。 これは、課金プロファイル使用条件への EA の使用条件のマッピングです。

| EA Azure の利用状況を含む CSV | Microsoft 顧客契約の Azure の利用状況と料金を含む CSV |
| --- | --- |
| Date | date |
| 月| date |
| 日 | date |
| 年 | date |
| Product | product |
| MeterId | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | 数量 |
| ResourceRate | effectivePrice | <!-- this was highlighted -->
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo: | additionalInfo |
| Tags | tags |
| StoreServiceIdentifier | 該当なし |
| DepartmentName | invoiceSection | <!-- this was highlighted -->
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible | <!-- this was highlighted -->

<!-- TO DO: Marketplace CSV? -->

## <a name="detailed-terms-and-descriptions-in-your-azure-usage-and-charges-file"></a>Azure の利用状況と料金のファイルに含まれる詳細な用語と説明

次のセクションでは、Azure の利用状況と料金のファイルにある重要な用語について説明します。

用語 | 説明
--- | ---
invoiceld | PDF 請求書に記載されている一意のドキュメント ID
previousInvoiceId | この明細項目が払い戻しの場合、元の請求書への参照
billingAccountName | 請求先アカウントの名前
billingAccountId | ルート請求先アカウントの一意の識別子
billingProfileId | 請求される金額が発生する課金プロファイルの名前
billingProfileName | 請求される金額が発生する課金プロファイルの一意の識別子
invoiceSectionId | 請求書セクションの一意の識別子
invoiceSectionName | 請求書セクションの名前
costCenter | コストを追跡するためのサブスクリプションで定義されているコスト センター (オープンの請求期間でのみ使用できます)
billingPeriodStartDate | 請求書を生成する請求期間の開始日
billingPeriodEndDate | 請求書を生成する請求期間の終了日
servicePeriodStartDate | 消費または購入したサービスに対して料金を定義およびロックしている評価期間の開始日
servicePeriodEndDate | 消費または購入したサービスに対して料金を定義およびロックしている評価期間の終了日
date | Azure や Marketplace 使用量ベースの料金の場合、評価日になります。 1 回限りの購入 (Reservations、Marketplace) あるいは毎月の固定請求金額 (サポート オファー) の場合、購入日になります。
serviceFamily | サービスが属するサービス ファミリ
productOrderId | 製品の注文の一意の識別子
productOrderName | 製品の注文の一意の名前
consumedService | 消費済みサービスの名前
meterId | メーターの一意の識別子
meterName | メーターの名前
meterCategory | メーターの分類カテゴリの名前。 たとえば、*Cloud services*、*Networking* などです。
meterSubCategory | メーターのサブ分類カテゴリの名前
meterRegion | サービスのメーターが使用可能な地域の名前。 データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します。
offer | 購入したオファーの名前
productId | 料金が発生する製品の一意の識別子
product | 料金が発生する製品の名前
サブスクリプション ID | 料金が発生するサブスクリプションの一意の識別子
subscriptionName | 料金が発生するサブスクリプションの名前
reservationId | 購入した予約インスタンスの一意の識別子
reservationName | 購入した予約インスタンスの名前
publisherType | 発行元の種類 (値: firstParty、thirdPartyReseller、thirdPartyAgency)
publisherName | Marketplace サービスの発行元
resourceGroupId | リソースに関連付けられているリソース グループの一意の識別子
resourceGroupName | リソースに関連付けられているリソース グループの名前
resourceId | リソース インスタンスの一意の識別子
resourceType | リソース インスタンスの種類
resourceLocation | リソースが実行されているデータ センターの場所を特定します。
location | 同じリージョンの別のリソースの場所が構成されている場合、リソースの正規化された場所
数量 | 購入または消費されるユニットの数
unitOfMeasure | サービス課金の測定単位。 たとえば、コンピューティング サービスは時間単位で課金されます。
chargeType | 料金の種類。 値: <ul><li>AsCharged-Usage:発生する料金は、Azure のサービスの利用状況をベースにしています。 これには、予約インスタンスのために課金されない VM の使用量が含まれます。</li><li>AsCharged PurchaseMarketplace:Marketplace での購入による 1 回限りのまたは毎月の固定料金</li><li>AsCharged-UsageMarketplace:ユニットの消費量ベースで課金される Marketplace のサービスの料金</li></ul>
isAzureCreditEligible | サービスの料金を Azure クレジットを使用して支払うことができるかどうかを示すフラグ (値：True、False)
serviceInfo1 | サービス固有のメタデータ
serviceInfo2 | これは、サービス固有の省略可能なメタデータをキャプチャする、以前から使用されているフィールドです
additionalInfo | その他のサービス固有のメタデータ。
tags | このリソースには、ユーザーが割り当てたタグが含まれます

### <a name="how-do-i-make-sure-that-the-charges-in-my-azure-usage-and-charges-file-are-correct"></a>Azure の利用状況と料金を含むファイルの料金が正しいことを確認する方法

詳細な利用状況を含むファイルに詳細を確認したい料金がある場合は、「[課金プロファイルの請求書の料金を理解する](billing-mca-understand-your-bill.md)」をご覧ください。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

- [Microsoft Azure の請求書の表示とダウンロード](billing-download-azure-invoice.md)
- [Microsoft Azure の利用状況と料金の表示とダウンロード](billing-download-azure-daily-usage.md)
