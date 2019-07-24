---
title: Azure Application Insights にユーザー コンテキスト ID を送信して使用状況を把握できるようにする | Microsoft Docs
description: Application Insights で各ユーザーに一意の永続 ID 文字列を割り当てて、サービスにおけるユーザーの行動を追跡します。
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: conceptual
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 7c458867b89a76a2f19bbd632c8a884c629f5765
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078764"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>ユーザー コンテキスト ID を送信して Azure Application Insights で使用状況を把握できるようにする

## <a name="tracking-users"></a>ユーザーの追跡

Application Insights を使用すると、次に示す一連の製品使用状況ツールでユーザーを監視したり追跡したりすることができます。

- [ユーザー、セッション、イベント](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [ファネル](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [リテンション期間](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)コーホート
- [ブック](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Application Insights で一定期間にわたってユーザーの行動を追跡するためには、ユーザーごと、またはセッションごとの ID が必要となります。 すべてのカスタム イベントまたはページ ビューに次の ID を追加します。

- ユーザー、ファネル、リテンション期間、コーホート:ユーザー ID を含みます。
- セッション:セッション ID を含みます。

> [!NOTE]
> これは、Application Insights でユーザー アクティビティを追跡するための手動の手順を概説した高度な記事です。 多くの Web アプリケーションでは、**これらの手順は必要ありません**。ユーザー アクティビティを自動的に追跡するには、既定のサーバー側 SDK と[クライアント/ブラウザー側 JavaScript SDK](../../azure-monitor/app/website-monitoring.md ) で十分であるためです。 サーバー側 SDK に加えて[クライアント側監視](../../azure-monitor/app/website-monitoring.md )を構成していない場合は、まずそれを構成し、ユーザー行動分析ツールが期待どおりに機能しているかどうかをテストして確認します。

## <a name="choosing-user-ids"></a>ユーザー ID の選択

一定期間にわたってユーザーの行動を追跡するためには、ユーザー セッションの終了後もユーザー ID を維持する必要があります。 ID を維持する方法としては、さまざまなアプローチが存在します。

- 既にサービスに存在するユーザーの定義。
- サービスがブラウザーにアクセスできる場合、ID を Cookie に含めてブラウザーに渡すことができます。 ユーザーのブラウザーに Cookie が存在する限り、ID は維持されます。
- 必要に応じてセッションごとに新しい ID を使用することもできますが、ユーザーに関する結果が制限されます。 たとえば一定期間にわたるユーザーの行動の変化を把握できなくなります。

ID には、Guid のほか、個々のユーザーを一意に識別できるだけの複合文字列を使用する必要があります。 たとえば、長い乱数を使用することが考えられます。

ユーザーについて個人を特定できる情報を含んだ ID は、ユーザー ID として Application Insights に送信する値としては不適切です。 [認証されたユーザーの ID](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users) を送信することもできますが、そのような ID は、使用状況を把握するために必要なユーザー ID の要件を満たしません。

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET アプリ:ITelemetryInitializer でのユーザー コンテキストの設定

[ここ](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer)で詳しく説明されているように、テレメトリ初期化子を作成します。 要求テレメトリを通じてセッション ID を渡し、Context.User.Id と Context.Session.Id を設定します。

この例でユーザー ID に設定している識別子は、セッション後に有効期限が切れます。 可能であれば、セッションの終了後も維持されるユーザー ID を使用してください。

### <a name="telemetry-initializer"></a>テレメトリ初期化子

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>次の手順

- 使用状況を把握できるようにするには、[カスタム イベント](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)または[ページ ビュー](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)の送信を開始します。
- カスタム イベントまたはページ ビューを既に送信した場合は、使用状況ツールを見て、自分のサービスがユーザーにどのように使用されているかを把握します。
    - [利用状況の概要](usage-overview.md)
    - [ユーザー、セッション、およびイベント](usage-segmentation.md)
    - [ファネル](usage-funnels.md)
    - [保持](usage-retention.md)
    - [ブック](../../azure-monitor/app/usage-workbooks.md)
