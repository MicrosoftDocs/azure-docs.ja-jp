---
title: 例:Bing Entity Search API を使用してカスタム コグニティブ スキルを作成する - Azure Search
description: Azure Search のコグニティブ検索インデックス作成パイプラインにマッピングされたカスタム スキルで Bing Entity Search サービスを使用する方法を紹介します。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7d90f46ada9b9453b4c1516a4a898456dc73b8e7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672147"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>例:Bing Entity Search API を使用してカスタム スキルを作成する

この例では、Web API カスタム スキルを作成する方法について説明します。 このスキルでは、場所、著名人、組織を受け入れ、それらの説明を返します。 この例では、[Azure 関数](https://azure.microsoft.com/services/functions/)を使用して、カスタム スキル インターフェイスを実装できるように [Bing Entity Search API](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) をラップします。

## <a name="prerequisites"></a>前提条件

+ カスタム スキルで実装する必要がある入出力インターフェイスがよくわからない場合は、[カスタム スキル インターフェイス](cognitive-search-custom-skill-interface.md)に関する記事を参照してください。

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Azure 開発のワークロードを備えた、[Visual Studio 2019](https://www.visualstudio.com/vs/) 以降をインストールします。

## <a name="create-an-azure-function"></a>Azure Function の作成

この例では Azure 関数を使用して Web API をホストしていますが、必須ではありません。  [コグニティブ スキルのインターフェイス要件](cognitive-search-custom-skill-interface.md)を満たしていれば、どのような方法を使用してもかまいません。 ただし、Azure Functions を使用すると、カスタム スキルを簡単に作成できます。

### <a name="create-a-function-app"></a>Function App を作成する

1. Visual Studio で、[ファイル] メニューから **[新規]**  >  **[プロジェクト]** の順に選択します。

1. [新しいプロジェクト] ダイアログで、 **[インストール済み]** を選択し、 **[Visual C#]**  >  **[クラウド]** の順に展開して **[Azure Functions]** を選択します。プロジェクトの名前を入力して、 **[OK]** を選択します。 関数アプリ名は、C# 名前空間として有効である必要があります。そのため、アンダースコア、ハイフン、その他の英数字以外の文字は使用しないでください。

1. **[Azure Functions v2 (.NET Core)]** を選択します。 バージョン 1 でも同様の手順を実行できますが、以下に記述したコードは、v2 テンプレートに基づいています。

1. 種類として **[HTTP Trigger]\(HTTP トリガー\)** を選択します。

1. ストレージ アカウントについては、この関数にはストレージが不要なので、 **[なし]** を選択できます。

1. **[OK]** を選択して、関数プロジェクトと、HTTP でトリガーされる関数を作成します。

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Bing Entity Search サービスを呼び出すコードを変更する

Visual Studio によってプロジェクトが作成されます。その中には、選択した関数の種類のスケルトン コードが含まれているクラスがあります。 メソッドの *FunctionName* 属性は、関数の名前を設定します。 *HttpTrigger* 属性は、関数が HTTP 要求によってトリガーされることを指定します。

次に、*Function1.cs* ファイルのすべての内容を次のコードに置き換えます。

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// cognitive search pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Bing Entity Search API にサインアップしたときに取得したキーに基づいて、`key` 定数の独自の *key* 値を確実に入力します。

このサンプルには、便宜上すべての必要なコードが 1 つのファイルに含まれています。 それと同じスキルでもう少し構造化されたバージョンが、[power skills リポジトリ](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)にあります。

もちろん、ファイル名を `Function1.cs` から `BingEntitySearch.cs` に変更できます。

## <a name="test-the-function-from-visual-studio"></a>Visual Studio から関数をテストする

**F5** キーを押して、プログラムを実行し、関数の動作をテストします。 ここでは、次の関数を使用して、2 つのエンティティを検索します。 Postman または Fiddler を使用して、以下に示すような呼び出しを発行します。

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>要求本文
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
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
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Azure に関数を発行する

関数の動作に満足したら、発行できます。

1. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[発行]** を選択します。 **[新規作成]**  >  **[発行]** の順に選択します。

1. まだ Visual Studio を Azure アカウントに接続していない場合は、 **[アカウントの追加]** を選択します。

1. 画面の指示に従います。 使用するアプリ サービス、Azure サブスクリプション、リソース グループ、ホスティング プラン、ストレージ アカウントに対して一意の名前を指定するように求められます。 リソース グループ、ホスティング プラン、ストレージ アカウントをまだ作成していない場合は新しく作成できます。 終わったら、 **[作成]** を選択します。

1. デプロイが完了したら、サイトの URL を書き留めておきます。 これが Azure における関数アプリのアドレスになります。 

1. [Azure portal](https://portal.azure.com) でリソース グループに移動し、発行した `EntitySearch` 関数を探します。 **[管理]** セクションに [ホスト キー] が表示されます。 "*既定*" のホスト キーの **[コピー]** アイコンを選択します。  

## <a name="test-the-function-in-azure"></a>Azure で関数をテストする

既定のホスト キーが用意できたので、次のように関数をテストします。

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>要求本文
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

この例では、以前にローカル環境で関数を実行したときと同じ結果が得られるはずです。

## <a name="connect-to-your-pipeline"></a>パイプラインに接続する
新しいカスタム スキルが作成できたので、スキルセットに追加できます。 次の例では、スキルを呼び出して、ドキュメント内の組織に説明を追加する方法を示しています (これは場所および人の操作にも拡張できる可能性があります)。 `[your-entity-search-app-name]` をご自分のアプリの名前に置き換えます。

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

ここでは、組み込みの[エンティティ認識スキル](cognitive-search-skill-entity-recognition.md)がスキルセットに存在すること、および組織のリストでエンリッチされたドキュメントがあることを想定しています。 参考のため、必要なデータを生成するために十分なエンティティ抽出スキルの構成を示します。

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>次の手順
お疲れさまでした。 最初のカスタム エンリッチャーが作成されました。 これで同じパターンに従って、独自のカスタム機能を追加できます。 

+ [コグニティブ検索パイプラインにカスタム スキルを追加する](cognitive-search-custom-skill-interface.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [スキルセットを作成する (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [エンリッチされたフィールドをマップする方法](cognitive-search-output-field-mapping.md)
