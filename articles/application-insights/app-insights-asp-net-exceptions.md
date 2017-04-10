---
title: "Azure Application Insights を利用して Web アプリの障害と例外を診断する | Microsoft Docs"
description: "要求テレメトリと共に ASP.NET アプリから例外を取り込みます。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 2f046ff687985a5c4f83ca7236ce832b4c81ea6e
ms.lasthandoff: 03/18/2017


---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Application Insights を利用し、Web アプリの例外を診断する
ライブ Web アプリの例外は、[Application Insights](app-insights-overview.md) によって報告されます。 要求の失敗をクライアントとサーバーの両方の例外やその他のイベントに相互に関連付け、原因をすばやく診断できます。

## <a name="set-up-exception-reporting"></a>例外のレポートを設定する
* 例外がサーバー アプリから報告されるようにするには、次のいずれかを実行します。
  * [Application Insights SDK](app-insights-asp-net.md) をアプリ コードにインストールする
  * IIS Web サーバー: [Application Insights エージェント](app-insights-monitor-performance-live-website-now.md)を実行する
  * Azure Web アプリ: [Application Insights Extension](app-insights-azure-web-apps.md) を追加する
* ブラウザー例外をキャッチする [JavaScript スニペット](app-insights-javascript.md)を Web ページにインストールします。
* 一部のアプリケーション フレームワークまたは一部の設定では、より多くの例外をキャッチするために余分の手順を実行する必要があります。
  * [Web フォーム](#web-forms)
  * [MVC](#mvc)
  * [Web API 1.*](#web-api-1)
  * [Web API 2.*](#web-api-2)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Visual Studio を使用して例外を診断する
デバッグに役立てるため、Visual Studio でアプリ ソリューションを開きます。

サーバー上または開発用コンピューターで、F5 キーを使用してアプリケーションを実行します。

Visual Studio で Application Insights の [検索] ウィンドウを開き、アプリからのイベントを表示するように設定します。 これは、デバッグ時に [Application Insights] ボタンをクリックするだけで実行できます。

![プロジェクトを右クリックし、[Application Insights] を選択して開きます。](./media/app-insights-asp-net-exceptions/34.png)

レポートをフィルター処理して例外だけを表示することができます。

*例外が表示されませんか?[例外のキャプチャ](#exceptions)に関するセクションをご覧ください。*

例外レポートをクリックしてスタック トレースを表示します。
関連コード ファイルを開くには、スタック トレース内の明細行参照をクリックします。  

コードでは、CodeLens により例外に関するデータが示されています。

![例外の CodeLens 通知。](./media/app-insights-asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Azure ポータルを使用して障害を診断する
アプリの Application Insights の概要の [障害] タイルには、例外と失敗した HTTP 要求のグラフ、最も頻繁に発生する障害の原因となる要求 URL の一覧が表示されます。

![[設定]、[障害] を選択する](./media/app-insights-asp-net-exceptions/012-start.png)

一覧で失敗したいずれかの例外の種類をクリックして、例外の個々の発生の情報を取得します。これにより、詳細とスタック トレースを確認できます。

![失敗した要求のインスタンスを選択し、例外の詳細で、例外のインスタンスを表示します。](./media/app-insights-asp-net-exceptions/030-req-drill.png)

**または**、要求の一覧から開始して、それに関連する例外を検索することもできます。

*例外が表示されませんか?[例外のキャプチャ](#exceptions)に関するセクションをご覧ください。*


## <a name="custom-tracing-and-log-data"></a>カスタムのトレースとログ データ
アプリに固有の診断データを取得するには、独自のテレメトリ データを送信するコードを挿入します。 これは、要求、ページ ビュー、およびその他の自動収集されたデータとともに、診断検索に表示されます。

いくつかのオプションがあります。

* [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) は通常、使用パターンを監視するために使用されますが、送信されるデータは診断検索のカスタム イベントの下にも表示されます。 イベントには名前が付けられるほか、文字列のプロパティや数値のメトリックが付与され、それらを元に[診断検索の結果をフィルター処理](app-insights-diagnostic-search.md)できます。
* [TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) は、POST 情報などの長いデータを送信できます。
* [TrackException()](#exceptions) は、スタック トレースを送信します。 [例外に関する詳細](#exceptions)をご覧ください。
* 既に Log4Net、NLog などのログ記録フレームワークを使用している場合は、[これらのログのキャプチャ](app-insights-asp-net-trace-logs.md)して、診断検索の要求や例外データの横に表示できます。

これらのイベントを表示するには [[検索]](app-insights-diagnostic-search.md)、[フィルター] と開き、[カスタム イベント]、[トレース]、または [例外] を選択します。

![ドリル スルー](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> アプリが大量のテレメトリを生成する場合は、アダプティブ サンプリング モジュールが、代表的な一部のイベントのみを送信することによって、ポータルに送信される量を自動的に削減します。 同じ操作に含まれるイベントは、グループ単位で選択または選択解除されるので、関連するイベントごとに操作できます。 [サンプリングについてはこちらを参照してください。](app-insights-sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>要求の POST データを表示する方法
要求詳細では、POST 呼び出しでアプリに送信されたデータは含まれません。 このデータを報告するには:

* アプリケーション プロジェクトに [SDK をインストールします](app-insights-asp-net.md)。
* アプリケーションにコードを挿入し、[Microsoft.ApplicationInsights.TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) を呼び出します。 メッセージ パラメーターで POST データを送信します。 許可されるサイズには制限があります。そのため、必要不可欠なデータだけを送信するように努めてください。
* 失敗した要求を調査するときは、関連付けられているトレースを検索します。  

![ドリル スルー](./media/app-insights-asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a> 例外と関連する診断データをキャプチャする
最初、ポータルにはアプリの障害の原因となる例外の一部しか表示されません。 ブラウザーの例外はすべて表示されます (Web ページで [JavaScript SDK](app-insights-javascript.md) を使用している場合)。 ただし、ほとんどのサーバー例外は IIS によりキャッチされます。それを確認するには、簡単なコードを記述する必要があります。

そのための方法は次のとおりです。

* **例外を明示的に記録します** 。
* **例外を自動的にキャプチャします** 。 追加しなければならないものはフレームワークの種類によって異なります。

## <a name="reporting-exceptions-explicitly"></a>例外を明示的に報告する
最も簡単な方法として、例外ハンドラーに TrackException() の呼び出しを挿入します。

JavaScript

    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

プロパティと測定値のパラメーターは省略可能ですが、[フィルター処理と、特別な情報を追加する](app-insights-diagnostic-search.md)のに便利です。 たとえば、複数のゲームを実行できるアプリケーションを使用している場合、1 つのゲームに関連する例外レポートをすべて検索できます。 必要な数だけ項目を各辞書に追加できます。

## <a name="browser-exceptions"></a>ブラウザーの例外
ほとんどのブラウザー例外が報告されます。

Web ページにコンテンツ配信ネットワークまたは他のドメインのスクリプト ファイルが含まれている場合、スクリプト タグに ```crossorigin="anonymous"``` 属性があり、サーバーが [CORS ヘッダー](http://enable-cors.org/)を送信することを確認します。 これで、これらのリソースから、未処理の JavaScript 例外のスタック トレースと詳細が取得できます。

## <a name="web-forms"></a>Web フォーム
Web フォームの場合、HTTP モジュールは、CustomErrors で構成されたリダイレクトがないとき、例外を回収できます。

ただし、アクティブなリダイレクトがある場合、次の行を Global.asax.cs の Application_Error 関数に追加します。 (Global.asax ファイルがない場合、それを追加します。)

*C#*

    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }


## <a name="mvc"></a>MVC
[CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) 構成が `Off` の場合、[HTTP モジュール](https://msdn.microsoft.com/library/ms178468.aspx)で例外を収集できます。 ただし、`RemoteOnly` (既定) または `On` の場合、例外は消去され、Application Insights が自動回収する例外はなくなります。 これを解決するには、[System.Web.Mvc.HandleErrorAttribute クラス](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)をオーバーライドし、次のようにオーバーライドしたクラスを異なる MVC バージョンに適用します ([github ソース](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs))。

    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above  
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }

#### <a name="mvc-2"></a>MVC 2
HandleError 属性をコントローラーの新しい属性で置換します。

    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...

[サンプル](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Global.asax.cs で `AiHandleErrorAttribute` をグローバル フィルターとして登録します。

    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...

[サンプル](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4、MVC5
FilterConfig.cs で AiHandleErrorAttribute をグローバル フィルターとして登録します。

    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }

[サンプル](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api-1x"></a>Web API 1.x
System.Web.Http.Filters.ExceptionFilterAttribute を上書きします。

    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);    
            }
            base.OnException(actionExecutedContext);
        }
      }
    }

この上書きされた属性を特定のコントローラーに追加するか、WebApiConfig クラスのグローバル フィルター構成に追加できます。

    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }

[サンプル](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

例外フィルターが処理できないケースがあります。 For example:

* コントローラー コンストラクターからスローされる例外。
* メッセージ ハンドラーからスローされる例外。
* ルーティング中にスローされる例外。
* 応答コンテンツのシリアル化中にスローされる例外。

## <a name="web-api-2x"></a>Web API 2.x
IExceptionLogger の実装を追加します。

    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }

これを WebApiConfig のサービスに追加します。

    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
  }

[サンプル](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

代替として、次のように操作できます。

1. 唯一の ExceptionHandler を IExceptionHandler のカスタム実装で置換します。 これはフレームワークが送信する応答メッセージを選択できるときにのみ呼び出されます (たとえば、接続が中止されるときではなく)。
2. 例外フィルター (上の Web API 1.x コントローラーのセクション参照) - 場合によっては呼び出されません。

## <a name="wcf"></a>WCF
Attribute を拡張し、IErrorHandler と IServiceBehavior を実装するクラスを追加します。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

サービス実装に属性を追加します。

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...

[サンプル](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>例外パフォーマンス カウンター
サーバーに [Application Insights エージェントがインストール](app-insights-monitor-performance-live-website-now.md)されている場合は、.NET によって測定された例外レートのグラフを取得できます。 これには、処理済みの .NET 例外と未処理の .NET 例外の両方が含まれます。

メトリックス エクスプローラー ブレードを開き、新しいグラフを追加します。パフォーマンス カウンターの下に表示されている **[例外レート]** を選択します。

.NET フレームワークでは、特定の時間間隔で例外数をカウントし、それを時間間隔の長さで割り算することで、例外レートを算出します。

この値は、TrackException レポートをカウントすることにより、Application Insights ポータルで算出される「例外」数とは異なります。 サンプリングの時間間隔が異なります。さらに、SDK では、すべての処理済みの例外と未処理の例外について TrackException レポートを送信するわけではありません。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="next-steps"></a>次のステップ
* [REST、SQL、および依存関係へのその他の呼び出しを監視する](app-insights-asp-net-dependencies.md)
* [ページの読み込み時間、ブラウザー例外、および AJAX 呼び出しを監視する](app-insights-javascript.md)
* [パフォーマンス カウンターを監視する](app-insights-performance-counters.md)

