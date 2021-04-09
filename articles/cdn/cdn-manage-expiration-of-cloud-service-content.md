---
title: Azure CDN での Web コンテンツ有効期限の管理 | Microsoft Docs
description: Azure CDN で Azure Web Apps/Cloud Services、ASP.NET、または IIS コンテンツの有効期限を管理する方法について説明します。
services: cdn
documentationcenter: .NET
author: asudbring
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: how-to
ms.date: 02/15/2018
ms.author: allensu
ms.openlocfilehash: d8eb450d2010bf2a525a26f1c5ff48f59732ce43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240972"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Azure CDN で Web コンテンツ有効期限を管理する
> [!div class="op_single_selector"]
> * [Azure Web コンテンツ](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure BLOB Storage](cdn-manage-expiration-of-blob-content.md)
> 

パブリックにアクセス可能な配信元 Web サーバーからのファイルは、それらの有効期間 (TTL) が経過するまで Azure Content Delivery Network (CDN) でキャッシュできます。 TTL は、配信元サーバーからの HTTP 応答の `Cache-Control` ヘッダーによって決まります。 この記事では、Microsoft Azure App Service、Azure Cloud Services、ASP.NET アプリケーション、およびインターネット インフォメーション サービス (IIS) サイトでの Web Apps 機能の `Cache-Control` ヘッダーを設定する方法について説明します。これらはすべて同様に構成されます。 `Cache-Control` ヘッダーを設定するには、構成ファイルを使用するか、プログラムを使用します。 

[CDN キャッシュ規則](cdn-caching-rules.md)を設定して、Azure Portal からキャッシュの設定を制御することもです。 1 つ以上のキャッシュ規則を作成し、それらのキャッシュ動作を **[オーバーライド]** または **[キャッシュのバイパス]** に設定すると、この記事で説明されている最初に示されたキャッシュ設定は無視されます。 全般的なキャッシュの概念については、「[キャッシュのしくみ](cdn-how-caching-works.md)」をご覧ください。

> [!TIP]
> ファイルに TTL を設定しないことも選択できます。 この場合は、Azure Portal でキャッシュ規則を設定していない限り、Azure CDN によって既定の 7 日間の TTL が自動的に適用されます。 この既定の TTL は、一般的な Web 配信の最適化に対してのみ適用されます。 大きなファイルの最適化に対する既定の TTL は 1 日、メディア ストリーミングの最適化に対する既定の TTL は 1 年です。
> 
> ファイルとその他のリソースへのアクセスを高速化する Azure CDN のしくみについて詳しくは、「[Azure Content Delivery Network (CDN) の概要](cdn-overview.md)」をご覧ください。
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>CDN キャッシュ規則を使用した Cache-Control ヘッダーの設定
Web サーバーの `Cache-Control` ヘッダーを設定するための推奨される方法は、Azure Portal でのキャッシュ規則の使用です。 CDN キャッシュ規則の詳細については、[キャッシュ規則による Azure CDN キャッシュ動作の制御](cdn-caching-rules.md)に関するページを参照してください。

> [!NOTE] 
> キャッシュ規則は、**Azure CDN Standard from Verizon** および **Azure CDN Standard from Akamai** プロファイルでのみ使用できます。 **Azure CDN Premium from Verizon** プロファイルの場合は、同様の機能に対して **管理** ポータルで [Azure CDN ルール エンジン](./cdn-verizon-premium-rules-engine.md)を使用する必要があります。

**[CDN キャッシュ規則] ページに移動するには**:

1. Azure Portal で、CDN プロファイルを選択してから、Web サーバーのエンドポイントを選択します。

1. 左側のウィンドウの [設定] で、 **[キャッシュ規則]** を選択します。

   ![[CDN キャッシュ規則] ボタン](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   **[キャッシュ規則]** ページが表示されます。

   ![[CDN キャッシュ] ページ](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**グローバル キャッシュ規則を使用して Web サーバーの Cache-Control ヘッダーを設定するには:**

1. **[グローバル キャッシュ規則]** で、 **[クエリ文字列のキャッシュ動作]** を **[クエリ文字列を無視]** に設定し、 **[キャッシュ動作]** を **[オーバーライド]** に設定します。
      
1. **[キャッシュの有効期間]** として、 **[Seconds] (秒)** ボックスに「3600」と入力するか、または **[時間]** ボックスに「1」と入力します。 

   ![CDN グローバル キャッシュ規則の例](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   このグローバル キャッシュ規則は 1 時間のキャッシュ有効期間を設定し、エンドポイントへのすべての要求に影響を与えます。 これは、エンドポイントで指定された配信元サーバーによって送信されるすべての `Cache-Control` または `Expires` HTTP ヘッダーをオーバーライドします。   

1. **[保存]** を選択します。

**カスタム キャッシュ規則を使用して Web サーバー ファイルの Cache-Control ヘッダーを設定するには:**

1. **[Custom caching rules] (カスタム キャッシュ規則)** で、次の 2 つの一致条件を作成します。

     a. 最初の一致条件では、 **[一致条件]** を **[パス]** に設定し、 **[一致する値]** として `/webfolder1/*` を入力します。 **[キャッシュ動作]** を **[オーバーライド]** に設定し、 **[日]** ボックスに「4」と入力します。

     b. 2 番目の一致条件では、 **[一致条件]** を **[パス]** に設定し、 **[一致する値]** として `/webfolder1/file1.txt` を入力します。 **[キャッシュ動作]** を **[オーバーライド]** に設定し、 **[日]** ボックスに「2」と入力します。

    ![CDN カスタム キャッシュ規則の例](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    最初のカスタム キャッシュ規則は、エンドポイントで指定された配信元サーバー上の `/webfolder1` フォルダー内のすべてのファイルに対して 4 時間のキャッシュ有効期間を設定します。 2 番目の規則は `file1.txt` ファイルについてのみ最初の規則をオーバーライドし、そのファイルに対して 2 時間のキャッシュ有効期間を設定します。

1. **[保存]** を選択します。


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>構成ファイルを使用した Cache-Control ヘッダーの設定
画像やスタイル シートなどの静的コンテンツの場合は、Web アプリケーションの **applicationHost.config** ファイルか **Web.config** 構成ファイルを変更することで、更新頻度を制御できます。 コンテンツの `Cache-Control` ヘッダーを設定するには、いずれかのファイル内の `<system.webServer>/<staticContent>/<clientCache>` 要素を使用します。

### <a name="using-applicationhostconfig-files"></a>ApplicationHost.config ファイルの使用
**ApplicationHost.config** ファイルは、IIS 構成システムのルート ファイルです。 **ApplicationHost.config** ファイルの構成設定は、サイト上のすべてのアプリケーションに影響を与えますが、Web アプリケーション用の既存の **Web.config** ファイルの設定によってオーバーライドされます。

### <a name="using-webconfig-files"></a>Web.config ファイルの使用
**Web.config** ファイルの使用では、Web アプリケーション全体、または Web アプリケーション上の特定のディレクトリの動作をカスタマイズできます。 通常、Web アプリケーションのルート フォルダーには、少なくとも 1 つの **Web.config** ファイルがあります。 特定のフォルダー内の各 **Web.config** ファイルについて、これらの構成設定は、別の **Web.config** ファイルによってサブフォルダー レベルでオーバーライドされない限り、そのフォルダーとそのサブフォルダー内のすべてに影響を与えます。 

たとえば、Web アプリケーションのルート フォルダー内の **Web.config** ファイルに `<clientCache>` 要素を設定して、Web アプリケーション上のすべての静的なコンテンツを 3 日間キャッシュできます。 また、可変コンテンツ (`\frequent` など) が含まれるサブフォルダーに **Web.config** ファイルを追加して、サブフォルダーのコンテンツを 6 時間キャッシュするようにその `<clientCache>` 要素を設定することもできます。 最終結果として、6 時間だけキャッシュされる `\frequent` ディレクトリ内のすべてのコンテンツを除き、Web サイト全体にあるそのコンテンツは 3 日間キャッシュされます。  

次の XML 構成ファイルの例は、3 日間の最大有効時間を指定するように `<clientCache>` 要素を設定する方法を示しています。  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

**cacheControlMaxAge** 属性を使用するには、**cacheControlMode** 属性の値を `UseMaxAge` に設定する必要があります。 この設定により、HTTP ヘッダーとディレクティブ、`Cache-Control: max-age=<nnn>` が応答に追加されます。 **cacheControlMaxAge** 属性の期間の値の形式は `<days>.<hours>:<min>:<sec>` です。 その値は秒に変換され、`Cache-Control``max-age` ディレクティブの値として使用されます。 `<clientCache>` 要素の詳細については、[クライアント キャッシュ\<clientCache>](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)に関するページをご覧ください。  

## <a name="setting-cache-control-headers-programmatically"></a>プログラムによる Cache-Control ヘッダーの設定
ASP.NET アプリケーションでは、.NET API の **HttpResponse.Cache** プロパティを設定することによって、CDN のキャッシュ動作をプログラムで制御します。 **HttpResponse.Cache** プロパティの詳細については、[HttpResponse.Cache プロパティ](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache)に関するページと [HttpCachePolicy クラス](/dotnet/api/system.web.httpcachepolicy)に関するページをご覧ください。  

アプリケーションのコンテンツを ASP.NET でプログラムでキャッシュするには、次の手順に従います。
   1. `HttpCacheability` を `Public` に設定することで、コンテンツをキャッシュ可能とマークします。 
   1. 次のいずれかの `HttpCachePolicy` メソッドを呼び出して、キャッシュ検証を設定します。
      - `SetLastModified` を呼び出して、`Last-Modified` ヘッダーのタイムスタンプの値を設定します。
      - `SetETag` を呼び出して、`ETag` ヘッダーの値を設定します。
   1. 必要に応じて、`SetExpires` を呼び出して `Expires` ヘッダーの値を設定して、キャッシュの有効期間を指定します。 それ以外の場合は、このドキュメントで既に説明した既定のキャッシュのヒューリスティックが適用されます。

たとえば、コンテンツを 1 時間キャッシュするには、次の C# コードを追加します。  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Cache-Control ヘッダーのテスト
Web コンテンツの TTL 設定を簡単に確認できます。 ブラウザーの[開発者ツール](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)を使って、Web コンテンツに `Cache-Control` 応答ヘッダーが含まれているかどうかをテストします。 **wget**、[Postman](https://www.getpostman.com/)、[Fiddler](https://www.telerik.com/fiddler) などのツールを使って応答ヘッダーを確認することもできます。

## <a name="next-steps"></a>次の手順
* [**clientCache** 要素の詳細を確認する](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [**HttpResponse.Cache** プロパティのドキュメントを参照する](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [**HttpCachePolicy クラス** のドキュメントを参照する](/dotnet/api/system.web.httpcachepolicy)  
* [キャッシュの概念を学習する](cdn-how-caching-works.md)
