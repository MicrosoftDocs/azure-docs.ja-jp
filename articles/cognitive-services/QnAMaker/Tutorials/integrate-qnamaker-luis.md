---
title: LUIS と QnAMaker - ボットの統合
titleSuffix: Azure Cognitive Services
description: QnA Maker ナレッジ ベースは、大きくなるにつれて、単一のモノリシックなセットとして維持することが難しくなり、より小さな論理的なチャンクにナレッジ ベースを分割する必要があります。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/11/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1792cf2359caef3211b4ce1ac86928eeb85d682b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053167"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>QnA Maker と LUIS にボットを組み合わせてナレッジ ベースを配信する
QnA Maker ナレッジ ベースは、大きくなるにつれて、単一のモノリシックなセットとして維持することが難しくなり、より小さな論理的なチャンクにナレッジ ベースを分割する必要があります。

QnA Maker には複数のナレッジ ベースを簡単に作成できますが、入力された質問を適切なナレッジ ベースにルーティングするためには、何らかのロジックが必要となります。 これは LUIS を使用して実行できます。

この記事では、Bot Framework v3 SDK を使用します。 この情報の Bot Framework v4 SDK バージョンに関心がある場合は、こちらの [Bot Framework に関する記事](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)をご覧ください。

## <a name="architecture"></a>アーキテクチャ

![QnA Maker と Language Understanding アーキテクチャ](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

上記のシナリオでは、QnA Maker はまず、入力された質問の意図を LUIS モデルから取得し、それを使って正しい QnA Maker ナレッジ ベースにルーティングします。

## <a name="create-a-luis-app"></a>LUIS アプリの作成

1. [LUIS](https://www.luis.ai/) ポータルにサインインします。
1. [アプリを作成します](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app)。
1. それぞれの QnA Maker ナレッジ ベースの[意図を追加](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents)します。 発話例は、QnA Maker ナレッジ ベースの質問に対応している必要があります。
1. [LUIS アプリをトレーニング](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train)し、[LUIS アプリを公開](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp)します。
1. **管理**セクションで、LUIS アプリ ID、LUIS エンドポイント キー、およびホスト リージョンをメモします。 これらの値は後で必要になります。 

## <a name="create-qna-maker-knowledge-bases"></a>QnA Maker ナレッジ ベースの作成

1. [QnA Maker](https://qnamaker.ai) にサインインします。
1. LUIS アプリのそれぞれの意図に対してナレッジ ベースを[作成](https://www.qnamaker.ai/Create)します。
1. ナレッジ ベースをテストして発行します。 それぞれの KB を公開するとき、KB ID、ホスト ( _.azurewebsites.net/qnamaker_ の前のサブドメイン)、および承認エンドポイント キーをメモします。 これらの値は後で必要になります。 

    この記事では、KB はすべて同じ Azure QnA Maker サブスクリプションで作成されることを想定しています。

    ![QnA Maker の HTTP 要求](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web アプリ ボット

1. LUIS アプリを自動的にインクルードする ["Basic" Web アプリ ボットを作成](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0)します。 4\.x の SDK および C# プログラミング言語を選択します。

1. Azure portal に Web アプリ ボットを作成したら、その Web アプリ ボットを選択します。
1. Web アプリ ボット サービス ナビゲーションで **[アプリケーションの設定]** を選択し、使用可能な設定の **[アプリケーションの設定]** セクションにスクロールします。
1. **LuisAppId** を前のセクションで作成した LUIS アプリの値に変更し、 **[保存]** を選択します。


## <a name="change-code-in-basicluisdialogcs"></a>BasicLuisDialog.cs のコードの変更
1. Azure portal の Web アプリ ボット ナビゲーションの **[Bot Management]\(ボットの管理\)** セクションの **[ビルド]** を選択します。
2. **[Open online code editor]\(オンライン コード エディターを開く\)** を選択します。 オンライン編集環境で新しいブラウザー タブが開きます。 
3. **[WWWROOT]** セクションで、**Dialogs** ディレクトリを選択し、**BasicLuisDialog.cs** を開きます。
4. **BasicLuisDialog.cs** ファイルの先頭に依存関係を追加します。

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. 以下のクラスを追加し、QnA Maker の応答を逆シリアル化します。

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. 次のクラスを追加し、QnA Maker サービスへの HTTP 要求を実行します。 **Authorization** ヘッダーの値に `EndpointKey` という単語が含まれていて、その単語に続いてスペースがあることがわかります。 JSON の結果が上記のクラスに逆シリアル化され、最初の応答が返されます。

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceHostName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string hostName, string kbId, string endpointkey)
        {
            qnaServiceHostName = hostName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. BasicLuisDialog クラスを変更します。 それぞれの LUIS の意図には、**LuisIntent** で装飾されたメソッドがあるはずです。 装飾のパラメーターは、実際の LUIS の意図の名前です。 装飾されたメソッド名は、読みやすさと保守性を確保するために LUIS の意図の名前である必要 _が_ ありますが、設計時と実行時には同じである必要はありません。  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_endpointDomain = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>ボットのビルド
1. コード エディターで `build.cmd` を右クリックし、 **[Run from Console]\(コンソールから実行\)** を選択します。

    ![コンソールから実行](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. コード ビューがターミナル ウィンドウに置き換わり、ビルドの進行状況と結果が表示されます。

    ![コンソール ビルド](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>ボットをテストする
Azure portal で、 **[Test in Web Chat]\(Web チャットでのテスト\)** を選択してボットをテストします。 異なる意図のメッセージを入力して、対応するナレッジ ベースからの応答を得ます。

![Web チャット テスト](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker のビジネス継続性計画の作成](../How-To/business-continuity-plan.md)
