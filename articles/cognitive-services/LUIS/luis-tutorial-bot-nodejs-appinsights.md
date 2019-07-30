---
title: Application Insights (Node.js)
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
ms.openlocfilehash: cfed5477df75350f24e77786117e85b9c728c49a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657753"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Node.js でボットから Application Insights に LUIS の結果を追加する
このチュートリアルでは、ボットおよび Language Understanding の情報を [Application Insights](https://azure.microsoft.com/services/application-insights/) テレメトリ データ ストレージに追加します。 そのデータを用意したら、Kusto 言語または Power BI でそれのクエリを実行し、意図および発話のエンティティについてリアルタイムで分析、集計、およびレポートすることができます。 この分析は、LUIS アプリの意図およびエンティティを追加または編集する必要があるかどうかの判断に役立ちます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ボットおよび Language Understanding のデータを Application Insights でキャプチャする
> * Application Insights に Language Understanding のデータを照会する

## <a name="prerequisites"></a>前提条件

* Application Insights を有効にして作成された Azure Bot Service ボット
* 前のボット **[チュートリアル](luis-nodejs-tutorial-bf-v4.md)** からダウンロードしたボット コード 
* [ボット エミュレーター](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

このチュートリアルのコードはすべて、[Azure-Samples Language Understanding GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry)で入手できます。 

## <a name="add-application-insights-to-web-app-bot-project"></a>Application Insights を Web アプリ ボット プロジェクトに追加する
現在、この Web アプリ ボットで使用されている Application Insights サービスは、ボット用の一般的な状態テレメトリを収集します。 LUIS 情報は収集されません。 

Web アプリ ボットで LUIS 情報をキャプチャするためには、 **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM パッケージがインストールされ、構成されている必要があります。  

1. VSCode 統合ターミナルで次のコマンドを使用し、ボット プロジェクトのルートに次の NPM パッケージを追加します。 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    LUIS JSON 構造を見やすくし、また Application Insights で扱いやすくするために、**underscore** パッケージを使用して LUIS JSON 構造をフラット化します。
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>LUIS クエリの結果をキャプチャし、Application Insights に送信する

1. VSCode で、新しいファイル **appInsightsLog.js** を作成し、次のコードを追加します。

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    このファイルはボット コンテキストと LUIS 応答を受け取り、両方のオブジェクトをフラット化して、Application Insights の**トレース** イベントに挿入します。 イベントの名前は **LUIS** です。 

1. **dialogs** フォルダーを開いて、**luisHelper.js** ファイルを開きます。 新しい **appInsightsLog.js** を必須ファイルとしてインクルードし、ボット コンテキストと LUIS 応答をキャプチャします。 このファイルのコード全体を次に示します。 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>Application Insights のインストルメンテーション キーを追加する 

Application Insights にデータを追加するには、インストルメンテーション キーが必要です。

1. ブラウザーで [Azure portal](https://portal.azure.com) にアクセスし、ボットの **Application Insights** リソースを探します。 その名前は、大部分がボットの名前で、その末尾にランダムな文字列が付きます (例: `luis-nodejs-bot-johnsmithxqowom`)。 
1. Application Insights リソースの **[概要]** ページで、 **[インストルメンテーション キー]** をコピーします。
1. VSCode で、ボット プロジェクトのルートにある **.env** ファイルを開きます。 すべての環境変数は、このファイルに保持されています。  
1. 新しい変数 `MicrosoftApplicationInsightsInstrumentationKey` を、実際のインストルメンテーション キーの値と共に追加します。 値を引用符で囲まないでください。 

## <a name="start-the-bot"></a>ボットを起動する

1. VSCode 統合ターミナルからボットを起動します。
    
    ```console
    npm start
    ```

1. ボット エミュレーターを起動してボットを開きます。 その[手順](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot)については、前のチュートリアルを参照してください。

1. ボットに質問します。 その[手順](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent)については、前のチュートリアルを参照してください。

## <a name="view-luis-entries-in-application-insights"></a>Application Insights の LUIS エントリの表示

Application Insights を開いて、LUIS エントリを表示します。 Application Insights にデータが表示されるまで数分かかる場合があります。

1. [Azure portal](https://portal.azure.com) で、ボットの Application Insights リソースを開きます。 
1. リソースを開いたら、 **[検索]** を選択し、イベントの種類が**トレース**である直近 **30 分**のデータをすべて検索します。 **LUIS** という名前のトレースを選択します。 
1. ボットと LUIS の情報は、 **[カスタム プロパティ]** に表示されます。 

    ![Application Insights に格納されている LUIS のカスタム プロパティを確認する](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights で意図、スコア、および発話のクエリを実行する
Application Insights を使用すると、[Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) 言語を使用してデータのクエリを実行したり、[Power BI](https://powerbi.microsoft.com) にデータをエクスポートしたりできます。 

1. **[Log (Analytics)]** を選択します。 新しいウィンドウが開きます。このウィンドウは、上部にクエリ ウィンドウがあり、その下にデータ テーブル ウィンドウがあります。 データベースを使用したことがある方は、よくご存じの配置でしょう。 このクエリは、以前のフィルター選択されたデータを表します。 ボットと LUIS の情報は、**CustomDimensions** 列にあります。
1. 最上位の意図、スコア、および発話を引き出すには、クエリ ウィンドウで、最後の行 (`|top...` 行) のすぐ上に次を追加します。

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. クエリを実行します。 topIntent、score、および utterance の新しい列を利用できます。 並べ替える topIntent 列を選択します。

[Kusto クエリ言語](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)または [Power BI へのデータのエクスポート](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)の詳細を確認してください。 

## <a name="next-steps"></a>次の手順

Application Insights データに追加する必要があるその他の情報としては、アプリ ID、バージョン ID、モデルの最終変更日、前回のトレーニング日、最後の発行日などがあります。 これらの値は、エンドポイントの URL (アプリ ID とバージョン ID) またはオーサリング API 呼び出しから取得し、Web アプリ ボットの設定で指定して、そこからプルすることができます。  

複数の LUIS アプリで同じエンドポイント サブスクリプションを使用している場合は、サブスクリプション ID と、それが共有キーであることを示すプロパティも含める必要があります。 

> [!div class="nextstepaction"]
> [発話の例の詳細](luis-how-to-add-example-utterances.md)
