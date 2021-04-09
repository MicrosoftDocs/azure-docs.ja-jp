---
title: Azure Application Insights を使用して要求を追跡するコードを記述する | Microsoft Docs
description: 要求のプロファイルを取得できるように、Application Insights を使用して要求を追跡するコードを記述します。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.custom: devx-track-csharp
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: aaa1d6df9faa20b1a561bfccdfea682af7645c18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88930249"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Application Insights を使用して要求を追跡するコードを記述する

[パフォーマンス] ページでアプリケーションのプロファイルを表示するには、Azure Application Insights によってアプリケーションの要求が追跡されている必要があります。 Application Insights では、既にインストルメント化されているフレームワーク上に構築されているアプリケーションの要求を自動的に追跡することができます。 2 つの例として、ASP.NET と ASP.NET Core があります。 

Azure Cloud Service の worker ロールや Service Fabric ステートレス API など、その他のアプリケーションでは、Application Insights に要求の開始位置と終了位置を伝えるコードを記述する必要があります。 このコードを記述すると、要求テレメトリが Application Insights に送信されます。 [パフォーマンス] ページにテレメトリが表示され、それらの要求のプロファイルが収集されます。 

要求を手動で追跡するには、次の操作を行います。

  1. アプリケーションの有効期間の初期段階に次のコードを追加します。  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      このグローバル インストルメンテーション キーの構成については、「[Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)」(Service Fabric と Application Insights を使用する) を参照してください。  

  1. インストルメント化するすべてのコードについて、次の例のように、その近くに `StartOperation<RequestTelemetry>` **using** ステートメントを追加します。

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        別の `StartOperation<RequestTelemetry>` スコープ内で `StartOperation<RequestTelemetry>` を呼び出すことはサポートされていません。 代わりに、入れ子にしたスコープで `StartOperation<DependencyTelemetry>` を使用できます。 次に例を示します。  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
