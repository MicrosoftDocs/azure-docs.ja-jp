---
title: Web アプリの使用 - Personalizer
description: Personalizer ループを使用して C# .NET Web アプリをカスタマイズし、アクション (特徴を伴う) およびコンテキストの特徴に基づいてユーザーに適切なコンテンツを提供します。
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e9ce4c433a038008b1ffd75dc6c4b2f9d0b57fde
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935621"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>チュートリアル:.NET Web アプリに Personalizer を追加する

Personalizer ループを使用して C# .NET Web アプリをカスタマイズし、アクション (特徴を伴う) およびコンテキストの特徴に基づいてユーザーに適切なコンテンツを提供します。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * Personalizer のキーとエンドポイントの設定
> * 特徴の収集
> * Rank API と Reward API の呼び出し
> * _rewardActionId_ として指定された最上位アクションの表示



## <a name="select-the-best-content-for-a-web-app"></a>Web アプリに最適なコンテンツの選択

表示する 1 つの最上位項目 (rewardActionId) にパーソナライズする必要がある Web ページ上に "_アクション_" (特定の種類のコンテンツ) のリストがある場合、Web アプリで Personalizer を使用する必要があります。 アクション リストの例としては、ニュース記事、ボタンの配置位置、製品名用の単語の選択肢などがあります。

アクションのリストを、コンテキストの特徴と一緒に Personalizer ループに送信します。 Personalizer によって最適なアクションが 1 つ選択され、そのアクションが Web アプリに表示されます。

このチュートリアルでは、次の種類の食べ物をアクションとします。

* パスタ
* アイスクリーム
* ジュース
* サラダ
* ポップコーン
* コーヒー
* スープ

Personalizer によるアクションについての学習を補助するには、各 Rank API 要求で "_アクションと特徴_" および "_コンテキストの特徴_" の両方を送信します。

モデルの**特徴**は、Web アプリのユーザーベースのメンバー間で集約 (グループ化) できるアクションまたはコンテキストに関する情報です。 特徴は、個別に特定されたもの (ユーザー ID など) でも、詳細に特定されたもの (正確な時刻など) でも "_ありません_"。

### <a name="actions-with-features"></a>アクションと特徴

各アクション (コンテンツ項目) には、食品項目を区別するのに役立つ特徴があります。

これらの特徴は、Azure portal のループ構成の一部として構成されません。 代わりに、各 Rank API 呼び出しを使用して JSON オブジェクトとして送信されます。 これにより、アクションとその特徴を時間の経過と共に拡張、変更、縮小できる柔軟性が得られ、Personalizer は傾向を追跡できるようになります。

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>コンテキストの特徴

コンテキストの特徴は、Personalizer がアクションのコンテキストを理解するのに役立ちます。 このサンプル アプリケーションのコンテキストには次のものが含まれます。

* 時間帯 - 朝、午後、夕方、夜
* ユーザーの味の好み - 塩味、甘味、苦味、酸味、またはうま味
* ブラウザーのコンテキスト - ユーザー エージェント、地理的な場所、参照元

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Web アプリで Personalizer を使用する方法

Web アプリで、食べ物の選択肢のリストから最適なアクションを選択するために Personalizer を使用します。 これは、Rank API 呼び出しで次の情報が送信されることで行われます。
* `taste` や `spiceLevel` などの**アクション**とその特徴
* 1 日の `time`、ユーザーの `taste` の好み、ブラウザーのユーザー エージェント情報、コンテキストの特徴などの**コンテキスト**の特徴
* ジュースなどの**除外するアクション**
* Rank API の呼び出しごとに異なる **eventid**

## <a name="personalizer-model-features-in-a-web-app"></a>Web アプリの Personalizer モデルの特徴

Personalizer には、アクション (コンテンツ) と現在のコンテキスト (ユーザーと環境) の特徴が必要です。 特徴は、アクションをモデル内の現在のコンテキストに合わせるために使用されます。 モデルは、アクション、コンテキスト、それらの特徴に関する Personalizer の過去の知識を表現したものであり、知識や経験に基づいた決定を可能にします。

特徴を含むモデルは、Azure portal の **[モデルの更新頻度]** 設定に基づいたスケジュールで更新されます。

> [!CAUTION]
> このアプリケーションの特徴は、特徴と特徴の値を示すことを目的としていますが、必ずしも Web アプリで使用するのに最適な特徴であるとは限りません。

### <a name="plan-for-features-and-their-values"></a>特徴とその値の計画

特徴は、お客様の技術アーキテクチャのスキーマまたはモデルに適用するのと同じ計画と設計で選択しなければなりません。 特徴の値は、ビジネス ロジックまたはサードパーティのシステムで設定できます。 特徴の値は、特徴のグループまたはクラス全体に当てはまらなくなるほど詳細に特定しすぎてはなりません。

### <a name="generalize-feature-values"></a>特徴の値の一般化

#### <a name="generalize-into-categories"></a>カテゴリへの一般化

このアプリでは、特徴として `time` を使用しますが、時間を `morning`、`afternoon`、`evening`、`night` などのカテゴリにグループ化します。 これは、時間の情報を使用する例ですが、`10:05:01 UTC+2` のような詳細に特定する方法ではありません。

#### <a name="generalize-into-parts"></a>パーツへの一般化

このアプリでは、ブラウザーからの HTTP 要求の特徴を使用します。 これは、すべてのデータを含む非常に特定された文字列で始まります。次に例を示します。

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

**HttpRequestFeatures** クラス ライブラリは、個々の値を使用して、この文字列を **userAgentInfo** オブジェクトに一般化します。 特定されすぎた値は空の文字列に設定されます。 要求のコンテキストの特徴が送信されるときは、次の JSON 形式が使用されます。

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>サンプル Web アプリの使用

サンプルのブラウザーベースの Web アプリ (すべてのコードが提供されています) では、アプリを実行するために次のアプリケーションがインストールされている必要があります。

次のソフトウェアをインストールします。

* [.NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) - サンプルのバックエンド サーバーで .NET Core を使用する
* [Node.js](https://nodejs.org/) - クライアントまたはフロント エンドがこのアプリケーションに依存している
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)、または [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/) - Visual Studio 2019 の開発者環境または .NET Core CLI を使用してアプリをビルドおよび実行する

### <a name="set-up-the-sample"></a>サンプルのセットアップ
1. Azure Personalizer のサンプルのリポジトリを複製します。

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. _samples/HttpRequestFeatures_ に移動して、ソリューション `HttpRequestFeaturesExample.sln` を開きます。

    要求に応じて、Visual Studio に対して Personalizer の .NET パッケージの更新を許可します。

### <a name="set-up-azure-personalizer-service"></a>Azure Personalizer サービスの設定

1. Azure portal で、[Personalizer リソースを作成します](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。

1. Azure portal の **[キーとエンドポイント]** タブで、`Endpoint` と、`Key1` または `Key2` (いずれかが動作します) を見つけます。これらが `PersonalizerServiceEndpoint` と `PersonalizerApiKey` です。
1. **appsettings.json** に `PersonalizerServiceEndpoint` を入力します。
1. 次のいずれかの方法で、`PersonalizerApiKey` を [アプリ シークレット](https://docs.microsoft.com/aspnet/core/security/app-secrets)として構成します。

    * .NET Core CLI を使用している場合は、`dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` コマンドを使用できます。
    * Visual Studio を使用している場合は、プロジェクトを右クリックし、 **[ユーザー シークレットの管理]** メニュー オプションを選択すると、Personalizer のキーを構成することができます。 これにより、Visual Studio によって `secrets.json` ファイルが開かれ、次のようにキーを追加できます。

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>サンプルを実行する

次のいずれかの方法を使用して、HttpRequestFeaturesExample をビルドして実行します。

* Visual Studio 2019:**F5** キーを押す
* .NET Core CLI: `dotnet build`、`dotnet run` の順に指定する

Web ブラウザーで Rank 要求と Reward 要求を送信し、その応答と、環境から抽出された HTTP 要求の特徴を確認することができます。

> [!div class="mx-imgBorder"]
> ![HTTPRequestFeaturesExample プロジェクトをビルドして実行します。 ブラウザー ウィンドウが開き、シングル ページ アプリケーションが表示されます。](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Personalizer ループのデモンストレーション

1. **[新しい Rank 要求の生成]** ボタンを選択して、Rank API 呼び出し用の新しい JSON オブジェクトを作成します。 これにより、アクション (特徴を伴う) およびコンテキストの特徴が作成され、それらの値が表示されるので、JSON がどのようになるかを確認できます。

    今後のアプリケーションでは、アクションと特徴の生成は、クライアント上、サーバー上、その 2 つの組み合わせ、または他のサービスへの呼び出しによって行われる可能性があります。

1. JSON オブジェクトをサーバーに送信するには **[Rank 要求の送信]** を選択します。 サーバーは、Personalizer Rank API を呼び出します。 サーバーは応答を受信し、表示するクライアントに対して再上位ランクのアクションを返します。

1. 報酬の値を設定し、 **[報酬要求の送信]** ボタンを選択します。 報酬の値を変更しない場合、クライアント アプリケーションは常に `1` の値を Personalizer に送信します。

    > [!div class="mx-imgBorder"]
    > ![HTTPRequestFeaturesExample プロジェクトをビルドして実行します。 ブラウザー ウィンドウが開き、シングル ページ アプリケーションが表示されます。](./media/tutorial-web-app/reward-score-api-call.png)

    今後のアプリケーションでは、サーバー上のビジネス ロジックと共に、クライアント上のユーザーの行動から情報を収集した後に、報酬スコアが生成される可能性があります。

## <a name="understand-the-sample-web-app"></a>サンプル Web アプリを理解する

サンプル Web アプリには **C# .NET** サーバーがあります。このサーバーは、特徴のコレクションを管理し、Personalizer のエンドポイントに対する HTTP 呼び出しの送受信を管理します。

サンプル Web アプリでは、**Knockout フロントエンド クライアント アプリケーション**を使用して特徴をキャプチャし、ボタンのクリックや .NET サーバーへのデータ送信などのユーザー インターフェイス アクションを処理します。

以下のセクションでは、開発者が Personalizer を使用するために理解しておく必要があるサーバーとクライアントの部分について説明します。

## <a name="rank-api-client-application-sends-context-to-server"></a>Rank API:クライアント アプリケーションがコンテキストをサーバーに送信する

クライアント アプリケーションは、ユーザーのブラウザー "_ユーザー エージェント_" を収集します。

> [!div class="mx-imgBorder"]
> ![HTTPRequestFeaturesExample プロジェクトをビルドして実行します。 ブラウザー ウィンドウが開き、シングル ページ アプリケーションが表示されます。](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>Rank API:サーバー アプリケーションが Personalizer を呼び出す

これは、クライアント アプリケーションを使用した一般的な .NET Web アプリであり、定型コードの多くが用意されています。 Personalizer に固有ではないコードは次のコード スニペットから削除されるため、Personalizer 固有のコードに集中することができます。

### <a name="create-personalizer-client"></a>Personalizer クライアントを作成する

サーバーの **Startup.cs** では、Personalizer のエンドポイントとキーを使用して Personalizer クライアントが作成されます。 クライアント アプリケーションは、このアプリの Personalizer と通信する必要はなく、代わりにサーバーに依存して SDK 呼び出しを行います。

Web サーバーの .NET スタートアップ コードは次のとおりです。

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>最適なアクションを選択する

サーバーの **PersonalizerController.cs** で、**GenerateRank** サーバー API が Rank API を呼び出すための準備についてまとめます

* Rank 呼び出し用の新しい `eventId` を作成する
* アクションのリストを取得する
* ユーザーから特徴のリストを取得し、コンテキストの特徴を作成する
* 必要に応じて、除外するアクションを設定する
* Rank API を呼び出し、結果をクライアントに返す

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

アクション (特徴を伴う) と現在のコンテキストの特徴の両方を含む、次のような JSON が Personalizer に送信される

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Personalizer の rewardActionId をクライアントに返す

Rank API で、選択された最適なアクションの **rewardActionId** をサーバーに返します。

**rewardActionId** で返されたアクションを表示します。

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>クライアントで rewardActionId アクションを表示する

このチュートリアルでは、`rewardActionId` の値が表示されます。

今後のアプリケーションでは、テキスト、ボタン、または強調表示された Web ページのセクションになる可能性があります。 リストは、コンテンツの順序付けではなく、スコアのポスト分析のために返されます。 `rewardActionId` のコンテンツのみが表示されます。

## <a name="reward-api-collect-information-for-reward"></a>Reward API: 報酬に関する情報を収集する

特徴を計画するのと同様に、[報酬スコア](concept-rewards.md)は慎重に計画しなければなりません。 通常、報酬スコアは 0 から 1 の値にする必要があります。 値は、ユーザーの行動に基づいてクライアント アプリケーションで部分的に計算したり、ビジネス ロジックと目標に基づいてサーバー上で部分的に計算したり "_できます_"。

Azure portal で Personalizer リソース用に構成された**報酬の待機時間**内に、サーバーが Reward API を呼び出さない場合、そのイベントには**既定の報酬** (これも Azure portal で構成されます) が使用されます。

このサンプル アプリケーションでは、値を選択して、報酬が選択内容にどのような影響を与えるかを確認できます。

## <a name="additional-ways-to-learn-from-this-sample"></a>このサンプルから学習するその他の方法

このサンプルでは、Personalizer リソース用の Azure portal で構成される時間ベースのイベントをいくつか使用します。 それらの値を使用してから、このサンプル Web アプリに戻り、変更が Rank と Reward の呼び出しにどのように影響するかを確認します。

* Reward wait time (報酬の待機時間)
* モデルの更新頻度

次のような追加の設定を行うことができます。
* Default reward (既定の報酬)
* 探索の割合


## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了したら、次のリソースをクリーンアップします。

* サンプル プロジェクト ディレクトリを削除します。
* Personalizer リソースを削除します。Personalizer リソースはアクションとコンテキスト専用であるとお考えください。まだ食べ物をアクションのサブジェクト ドメインとして使用している場合にのみ、リソースを再利用します。


## <a name="next-steps"></a>次のステップ
* [Personalizer のしくみ](how-personalizer-works.md)
* [特徴](concepts-features.md): アクションおよびコンテキストで使用する特徴に関する概念を学習する
* [報酬](concept-rewards.md): 報酬の計算について学習する
