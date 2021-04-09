---
title: プログラムによる分析のサンプル クエリ
description: 次のサンプル クエリを使用して、Microsoft コマーシャル マーケットプレースの分析データにプログラムによってアクセスします。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7d788448fb3f8a849f79e43fcb0737898f4c9e15
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583579"
---
# <a name="sample-queries-for-programmatic-analytics"></a>プログラムによる分析のサンプル クエリ

この記事では、Microsoft コマーシャル マーケットプレースの注文、使用状況、および顧客のレポートのサンプル クエリを提供します。 これらのクエリは、[Create Report Query](analytics-programmatic-access.md#create-report-query-api) API エンドポイントを呼び出すことによって使用できます。 必要に応じて、[Create Report Query](analytics-programmatic-access.md#create-report-query-api) API 呼び出しを変更して、列の追加、計算期間の調整、およびフィルター条件の追加を行うことができます。 サポートされている期間は、6 か月 (6M)、12 か月 (12M)、およびカスタム期間です。

列名、属性、および説明の詳細については、次の表を参照してください。

- [顧客詳細テーブル](customer-dashboard.md#customer-details-table)
- [[注文の詳細] テーブル](orders-dashboard.md#orders-details-table)
- [使用状況の詳細テーブル](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>顧客レポートのクエリ

これらのサンプル クエリは顧客レポートに適用されます。

| **クエリの説明** | **サンプル クエリ** |
| --- | --- |
| 選択した日付までのパートナーのアクティブな顧客 | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| 選択した日付までのパートナーのチャーン顧客 | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| 過去 6 か月間の、特定の地理的な場所からの新しい顧客の一覧 | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>使用状況レポートのクエリ

これらのサンプル クエリは使用状況レポートに適用されます。

| **クエリの説明** | **サンプル クエリ** |
| --- | --- |
| 過去 6M における、 "Azure から請求" Marketplace ライセンス タイプに対する仮想マシン (VM) の正規化された使用状況 | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| 過去 12M における、 "Azure から請求" Marketplace ライセンス タイプに対する VM の未調整の使用状況 | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| 過去 6M における、 "ライセンス持ち込み" Marketplace ライセンス タイプに対する VM の正規化された使用状況 | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| 過去 6M における、 "ライセンス持ち込み" Marketplace ライセンス タイプに対する VM の未調整の使用状況 | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| 使用日に基づく、過去 1 か月における有料プランに対する正規化された使用状況の毎日の合計および "見積拡張価格 (PC/CC)" | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| 使用日に基づく、過去 1 か月における有料プランに対する未調整の使用状況の毎日の合計および "見積拡張価格 (PC/CC)" | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| 特定のプラン名についての、過去 6M における "Azure から請求" Marketplace ライセンス タイプに対する仮想マシン (VM) の正規化された使用状況 | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| 特定のプラン名についての、過去 6M における従量制課金使用量 | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>注文レポートのクエリ

これらのサンプル クエリは注文レポートに適用されます。

| **クエリの説明** | **サンプル クエリ** |
| --- | --- |
| 過去 6M における、Azure ライセンス タイプが "エンタープライズ" の注文レポート | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| 過去 6M における、Azure ライセンス タイプが "従量課金制" の注文レポート | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| 過去 6M における、特定のプラン名の注文レポート | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| 過去 6M における、アクティブな注文の注文レポート | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| 過去 6M における、キャンセルされた注文の注文レポート | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>次のステップ

- [コマーシャル マーケットプレースの分析データにアクセスするための API](analytics-available-apis.md)
