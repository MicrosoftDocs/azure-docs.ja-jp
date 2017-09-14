---
title: "Azure Application Insights にユーザー コンテキストを送信して使用状況を把握できるようにする | Microsoft Docs"
description: "Application Insights で各ユーザーに一意の永続 ID 文字列を割り当てた後、サービスにおけるユーザーの行動を追跡します。"
services: application-insights
documentationcenter: 
author: abgreg
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: article
ms.date: 08/02/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 7d0da5fb0b2c59764b36becd826d8c4cc6efc4ad
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---
#  <a name="sending-user-context-to-enable-usage-experiences-in-azure-application-insights"></a>Azure Application Insights にユーザー コンテキストを送信して使用状況を把握できるようにする

## <a name="tracking-users"></a>ユーザーの追跡

Application Insights を使用すると、次に示す一連の製品使用状況ツールでユーザーを監視したり追跡したりすることができます。 
* [ユーザー、セッション、イベント](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [ファネル](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [保持](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* コーホート
* [ブック](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Application Insights で一定期間にわたってユーザーの行動を追跡するためには、ユーザーごと、またはセッションごとの ID が必要となります。 すべてのカスタム イベントまたはページ ビューに次の ID を追加します。
- ユーザー、ファネル、リテンション期間、コーホート: ユーザー ID を追加します。
- セッション: セッション ID を追加します。

アプリが [JavaScript SDK](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page) と統合されている場合、ユーザー ID は自動的に追跡されます。

## <a name="choosing-user-ids"></a>ユーザー ID の選択

一定期間にわたってユーザーの行動を追跡するためには、ユーザー セッションの終了後もユーザー ID を維持する必要があります。 ID を維持する方法としては、さまざまなアプローチが存在します。
- 既にサービスに存在するユーザーの定義。
- サービスがブラウザーにアクセスできる場合、ID を Cookie に含めてブラウザーに渡すことができます。 ユーザーのブラウザーに Cookie が存在する限り、ID は維持されます。
- 必要に応じてセッションごとに新しい ID を使用することもできますが、ユーザーに関する結果が制限されます。 たとえば一定期間にわたるユーザーの行動の変化を把握できなくなります。

ID には、Guid のほか、個々のユーザーを一意に識別できるだけの複合文字列を使用する必要があります。 たとえば、長い乱数を使用することが考えられます。

ユーザーについて個人を特定できる情報を含んだ ID は、ユーザー ID として Application Insights に送信する値としては不適切です。 [認証されたユーザーの ID](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users) を送信することもできますが、そのような ID は、使用状況を把握するために必要なユーザー ID の要件を満たしません。

## <a name="aspnet-apps-set-user-context-in-an-itelemetryinitializer"></a>ASP.NET アプリ: ITelemetryInitializer でのユーザー コンテキストの設定

テレメトリ初期化子を作成し (詳細については[こちら](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer)を参照)、Context.User.Id と Context.Session.Id を設定します。

この例でユーザー ID に設定している識別子は、セッション後に有効期限が切れます。 可能であれば、セッションの終了後も維持されるユーザー ID を使用してください。

*C#*

```C#

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
            // For a full experience, track each user across sessions. For an incomplete view of user 
            // behavior within a session, store user ID on the HttpContext Session.
            // Set the user ID if we haven't done so yet.
            if (HttpContext.Current.Session["UserId"] == null)
            {
                HttpContext.Current.Session["UserId"] = Guid.NewGuid();
            }

            // Set the user id on the Application Insights telemetry item.
            telemetry.Context.User.Id = (string)HttpContext.Current.Session["UserId"];

            // Set the session id on the Application Insights telemetry item.
            telemetry.Context.Session.Id = HttpContext.Current.Session.SessionID;
        }
      }
    }
```

## <a name="next-steps"></a>次のステップ
- 使用状況を把握できるようにするには、[カスタム イベント](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)または[ページ ビュー](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)の送信を開始します。
- カスタム イベントまたはページ ビューを既に送信した場合は、使用状況ツールを見て、自分のサービスがユーザーにどのように使用されているかを把握します。
    * [利用状況の概要](app-insights-usage-overview.md)
    * [ユーザー、セッション、およびイベント](app-insights-usage-segmentation.md)
    * [ファネル](usage-funnels.md)
    * [保持](app-insights-usage-retention.md)
    * [ブック](app-insights-usage-workbooks.md)

