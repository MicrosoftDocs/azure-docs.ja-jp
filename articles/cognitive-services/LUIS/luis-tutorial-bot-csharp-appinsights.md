---
title: Application Insights、C#
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、ボットおよび Language Understanding の情報を Application Insights テレメトリ データ ストレージに追加します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: 720352403fd5f5937669f9838f3974cb0d3f8797
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657802"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>C# でボットから Application Insights に LUIS の結果を追加する

このチュートリアルでは、ボットおよび Language Understanding の情報を [Application Insights](https://azure.microsoft.com/services/application-insights/) テレメトリ データ ストレージに追加します。 そのデータを用意したら、Kusto 言語または Power BI でそれのクエリを実行し、意図および発話のエンティティについてリアルタイムで分析、集計、およびレポートすることができます。 この分析は、LUIS アプリの意図およびエンティティを追加または編集する必要があるかどうかの判断に役立ちます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ボットおよび Language Understanding のデータを Application Insights でキャプチャする
> * Application Insights に Language Understanding のデータを照会する

## <a name="prerequisites"></a>前提条件

* Application Insights を有効にして作成された Azure Bot Service ボット。
* 前のボットの **[チュートリアル](luis-csharp-tutorial-bf-v4.md)** からダウンロードしたボット コード。 
* [ボット エミュレーター](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

このチュートリアルのコードはすべて、[Azure-Samples Language Understanding GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry)で入手できます。 

## <a name="add-application-insights-to-web-app-bot-project"></a>Application Insights を Web アプリ ボット プロジェクトに追加する

現在、この Web アプリ ボットで使用されている Application Insights サービスは、ボット用の一般的な状態テレメトリを収集します。 LUIS 情報は収集されません。 

Web アプリ ボットで LUIS 情報をキャプチャするには、 **[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** NuGet パッケージがインストールされ、構成されている必要があります。  

1. Visual Studio から、ソリューションに依存関係を追加します。 **ソリューション エクスプローラー**でプロジェクト名を右クリックし、 **[NuGet パッケージの管理]** を選択します。NuGet パッケージ マネージャーに、インストールされているパッケージの一覧が表示されます。 
1. **[参照]** を選択し、**Microsoft.ApplicationInsights** を検索します。
1. パッケージをインストールします。 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>LUIS クエリの結果をキャプチャし、Application Insights に送信する

1. `LuisHelper.cs` ファイルを開き、その内容を次のコードに置き換えます。 **LogToApplicationInsights** メソッドは、ボットと LUIS のデータのキャプチャし、それを `LUIS` という名前のトレース イベントとして Application Insights に送信します。

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>Application Insights のインストルメンテーション キーを追加する 

Application Insights にデータを追加するには、インストルメンテーション キーが必要です。

1. ブラウザーで [Azure portal](https://portal.azure.com) にアクセスし、ボットの **Application Insights** リソースを探します。 その名前は、大部分がボットの名前で、その末尾にランダムな文字列があります (例: `luis-csharp-bot-johnsmithxqowom`)。 
1. Application Insights リソースの **[概要]** ページで、 **[インストルメンテーション キー]** をコピーします。
1. Visual Studio で、ボット プロジェクトのルートにある **appsettings.json** ファイルを開きます。 このファイルに、すべての環境変数が保持されています。
1. 新しい変数 `BotDevAppInsightsKey` を、実際のインストルメンテーション キーの値とともに追加します。 この値は引用符で囲む必要があります。 

## <a name="build-and-start-the-bot"></a>ボットをビルドして起動する

1. Visual Studio でボットをビルドし、実行します。 
1. ボット エミュレーターを起動し、ボットを開きます。 この[手順](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot)については、前のチュートリアルを参照してください。

1. ボットに質問します。 この[手順](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent)については、前のチュートリアルを参照してください。

## <a name="view-luis-entries-in-application-insights"></a>Application Insights の LUIS エントリの表示

Application Insights を開いて、LUIS エントリを表示します。 Application Insights にデータが表示されるまで数分かかる場合があります。

1. [Azure portal](https://portal.azure.com) で、ボットの Application Insights リソースを開きます。 
1. リソースを開いたら、 **[検索]** を選択し、イベントの種類が**トレース**である直近 **30 分**のデータをすべて検索します。 **LUIS** という名前のトレースを選択します。 
1. ボットと LUIS の情報は、 **[カスタム プロパティ]** に表示されます。 

    ![Application Insights に格納されている LUIS のカスタム プロパティを確認する](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights で意図、スコア、および発話のクエリを実行する
Application Insights を使用すると、[Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) 言語を使用してデータのクエリを実行したり、[Power BI](https://powerbi.microsoft.com) にデータをエクスポートしたりできます。 

1. **[Log (Analytics)]** を選択します。 新しいウィンドウが開きます。このウィンドウは、上部にクエリ ウィンドウがあり、その下にデータ テーブル ウィンドウがあります。 データベースを使用したことがある方は、よくご存じの配置でしょう。 このクエリは、以前のフィルター選択されたデータを表します。 ボットと LUIS の情報は、**CustomDimensions** 列にあります。
1. 最上位の意図、スコア、および発話を引き出すには、クエリ ウィンドウで、最後の行 (`|top...` 行) のすぐ上に次を追加します。

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. クエリを実行します。 topIntent、score、および utterance の新しい列を利用できます。 並べ替える topIntent 列を選択します。

[Kusto クエリ言語](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)または [Power BI へのデータのエクスポート](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)の詳細を確認してください。 


## <a name="learn-more-about-bot-framework"></a>Bot Framework の詳細

[Bot Framework](https://dev.botframework.com/) の詳細を確認してください。

## <a name="next-steps"></a>次の手順

Application Insights データに追加する必要があるその他の情報としては、アプリ ID、バージョン ID、モデルの最終変更日、前回のトレーニング日、最後の発行日などがあります。 これらの値は、エンドポイントの URL (アプリ ID とバージョン ID) またはオーサリング API 呼び出しから取得し、Web アプリ ボットの設定で指定して、そこからプルすることができます。  

複数の LUIS アプリで同じエンドポイント サブスクリプションを使用している場合は、サブスクリプション ID と、それが共有キーであることを示すプロパティも含める必要があります。

> [!div class="nextstepaction"]
> [発話の例の詳細](luis-how-to-add-example-utterances.md)
