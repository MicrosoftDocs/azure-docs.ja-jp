---
title: 例:コグニティブ検索パイプラインにカスタム スキルを作成する - Azure Search
description: Azure Search のコグニティブ検索インデックス作成パイプラインにマッピングされたカスタム スキルで Text Translate API を使用する方法を紹介します。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f3b4e6cd18a362775443bb296560a076aaa1497d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57883629"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>例:Text Translate API を使用してカスタム スキルを作成する

この例では、任意の言語のテキストを受け取って英語に翻訳する Web API カスタム スキルを作成する方法について説明します。 この例では、[Azure 関数](https://azure.microsoft.com/services/functions/)を使用して、[Translate Text API](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) をラップし、カスタム スキル インターフェイスを実装します。

## <a name="prerequisites"></a>前提条件

+ カスタム スキルに実装する必要がある入出力インターフェイスがよくわからない場合は、[カスタム スキル インターフェイス](cognitive-search-custom-skill-interface.md)に関する記事を参照してください。

+ [Translator Text API にサインアップ](../cognitive-services/translator/translator-text-how-to-signup.md)し、その API を利用するための API キーを取得します。

+ Azure 開発のワークロードを備えた、[Visual Studio 2017 バージョン 15.5](https://www.visualstudio.com/vs/) 以降をインストールします。

## <a name="create-an-azure-function"></a>Azure Function の作成

この例では Azure 関数を使用して Web API をホストしていますが、必須ではありません。  [コグニティブ スキルのインターフェイス要件](cognitive-search-custom-skill-interface.md)を満たしていれば、どのような方法を使用してもかまいません。 ただし、Azure Functions を使用すると、カスタム スキルを簡単に作成できます。

### <a name="create-a-function-app"></a>Function App を作成する

1. Visual Studio で、[ファイル] メニューから **[新規]** > **[プロジェクト]** の順に選択します。

1. [新しいプロジェクト] ダイアログで、**[インストール済み]** を選択し、**[Visual C#]** > **[クラウド]** の順に展開して **[Azure Functions]** を選択します。プロジェクトの名前を入力して、**[OK]** を選択します。 関数アプリ名は、C# 名前空間として有効である必要があります。そのため、アンダースコア、ハイフン、その他の英数字以外の文字は使用しないでください。

1. **[Azure Functions v2 (.NET Core)]** を選択します。 バージョン 1 でも同様の手順を実行できますが、以下に記述したコードは、v2 テンプレートに基づいています。

1. 種類として **[HTTP Trigger]\(HTTP トリガー\)** を選択します。

1. ストレージ アカウントについては、この関数にはストレージが不要なので、**[なし]** を選択できます。

1. **[OK]** を選択して、関数プロジェクトと、HTTP でトリガーされる関数を作成します。

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Translate Cognitive Service を呼び出すようにコードを変更する

Visual Studio によってプロジェクトが作成されます。その中には、選択した関数の種類のスケルトン コードが含まれているクラスがあります。 メソッドの *FunctionName* 属性は、関数の名前を設定します。 *HttpTrigger* 属性は、関数が HTTP 要求によってトリガーされることを指定します。

次に、*Function1.cs* ファイルのすべての内容を次のコードに置き換えます。

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to another.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

Translate Text API にサインアップしたときに取得したキーに基づいて、*TranslateText* メソッドの *key* 値を確実に入力します。

この例は、一度に 1 つのレコードに対してのみ機能する単純なエンリッチャーです。 この点が、後でスキルセットのバッチ サイズを設定する際に重要になります。

## <a name="test-the-function-from-visual-studio"></a>Visual Studio から関数をテストする

**F5** キーを押して、プログラムを実行し、関数の動作をテストします。 このケースでは、下の関数を使用してスペイン語のテキストを英語に翻訳します。 Postman または Fiddler を使用して、以下に示すような呼び出しを発行します。

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>要求本文
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>Response
次の例のような応答が表示されます。

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Azure に関数を発行する

関数の動作に満足したら、発行できます。

1. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[発行]** を選択します。 **[新規作成]** > **[発行]** の順に選択します。

1. まだ Visual Studio を Azure アカウントに接続していない場合は、**[アカウントの追加]** を選択します。

1. 画面の指示に従います。 使用する Azure アカウント、リソース グループ、ホスティング プラン、ストレージ アカウントを指定するように求められます。 リソース グループ、ホスティング プラン、ストレージ アカウントをまだ作成していない場合は新しく作成できます。 終わったら、**[作成]** を選択します。

1. デプロイが完了したら、サイトの URL を書き留めておきます。 これが Azure における関数アプリのアドレスになります。 

1. [Azure portal](https://portal.azure.com) でリソース グループに移動し、発行した Translate 関数を探します。 **[管理]** セクションに [ホスト キー] が表示されます。 "*既定*" のホスト キーの **[コピー]** アイコンを選択します。  

## <a name="test-the-function-in-azure"></a>Azure で関数をテストする

既定のホスト キーが用意できたので、次のように関数をテストします。

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>要求本文
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

これにより、以前にローカル環境で関数を実行したときと同じような結果が得られます。

## <a name="connect-to-your-pipeline"></a>パイプラインに接続する
新しいカスタム スキルが作成できたので、スキルセットに追加できます。 下の例では、スキルを呼び出す方法を示しています。 スキルはバッチに対応していないので、一度に 1 つずつドキュメントを送信するために、最大バッチ サイズを ```1``` にする命令を追加します。

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "http://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>次の手順
お疲れさまでした。 最初のカスタム エンリッチャーが作成されました。 これで同じパターンに従って、独自のカスタム機能を追加できます。 

+ [コグニティブ検索パイプラインにカスタム スキルを追加する](cognitive-search-custom-skill-interface.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [スキルセットを作成する (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [エンリッチされたフィールドをマップする方法](cognitive-search-output-field-mapping.md)
