---
title: システム クエリのリスト
description: Microsoft コマーシャル マーケットプレースにおいて、プランに関する分析データをプログラムで取得する際に使用できるシステム クエリについて説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f2b5f7eb559e349947f88067a3d2ea53d99b7cbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583578"
---
# <a name="list-of-system-queries"></a>システム クエリのリスト

次のシステム クエリは、`QueryId` と共に[作成レポート API](analytics-programmatic-access.md#create-report-api) で直接使用できます。 システム クエリは、パートナーセンターにおける、6 か月の計算期間のエクスポート レポートに似ています。

列の名前、属性、説明などの詳細については、次の記事を参照してください。

- [コマーシャル マーケットプレース分析での [顧客] ダッシュボード](customer-dashboard.md#customer-details-table)
- [コマーシャル マーケットプレース分析での [注文] ダッシュボード](orders-dashboard.md#orders-details-table)
- [コマーシャル マーケットプレース分析の [使用量] ダッシュボード](usage-dashboard.md#usage-details-table)
- [コマーシャル マーケットプレース分析の [Marketplace の分析情報] ダッシュボード](insights-dashboard.md#marketplace-insights-details-table)

以下のセクションでは、さまざまなレポートのレポート クエリについて説明します。

## <a name="customers-report-query"></a>顧客レポートのクエリ

**レポートの説明**: 直近 6 か月間の顧客レポート

**QueryID**: `b9df4929-073f-4795-b0cb-a2c81b11e28d`

**レポート クエリ**:

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>注文レポートのクエリ

**レポートの説明**: 直近 6 か月間の注文レポート

**QueryID**: `fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**レポート クエリ**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>使用状況レポートのクエリ

**レポートの説明**: 直近 6 か月間の、VM の正規化された使用状況レポート

**QueryID**: `2c6f384b-ad52-4aed-965f-32bfa09b3778`

**レポート クエリ**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**レポートの説明**: 直近 6 か月間の、VM の未加工の使用状況レポート

**QueryID**: `3f19fb95-5bc4-4ee0-872e-cedd22578512`

**レポート クエリ**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**レポートの説明**: 直近 6 か月間の従量制使用状況レポート

**QueryID**: `f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**レポート クエリ**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>マーケットプレースの分析情報レポート クエリ

**レポートの説明**: 直近 6 か月間のマーケットプレースの分析情報レポート

**QueryID**: `6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**レポート クエリ**:

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>次のステップ

- [コマーシャル マーケットプレースの分析データにアクセスするための API](analytics-available-apis.md)
- [コマーシャル マーケットプレースの分析データにアクセスするためのサンプル アプリケーション](analytics-sample-application.md)
