---
title: "クイック スタート ガイド: Machine Learning テキスト分析 API | Microsoft Docs"
description: "Azure Machine Learning テキスト分析 - クイック スタート ガイド"
services: cognitive-services
documentationcenter: 
author: onewth
manager: jhubbard
editor: cgronlun
ms.assetid: e8b9e98c-40e7-4425-ae16-d1eaa7d2f837
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: onewth
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 663b99c2491bebf49f950605152604b040ade070


---
# <a name="getting-started-with-the-text-analytics-apis-to-detect-sentiment-key-phrases-topics-and-language"></a>テキスト分析 API を使ってセンチメント、キー フレーズ、トピック、および言語を検出してみる
<a name="HOLTop"></a>

このドキュメントでは、 [テキスト分析 API](//go.microsoft.com/fwlink/?LinkID=759711)を使うためにサービスまたはアプリケーションをオンボードする方法について説明します。
この API を使用すると、テキストからセンチメント、キー フレーズ、トピック、および言語を検出することができます。 [対話型のエクスペリエンス デモをご覧になる場合は、ここをクリックしてください。](//go.microsoft.com/fwlink/?LinkID=759712)

API の技術ドキュメントについては、 [API 定義](//go.microsoft.com/fwlink/?LinkID=759346) を参照してください。

このガイドはバージョン 2 の API 用です。 バージョン 1 の API の詳細については、 [こちらのドキュメントを参照してください](../machine-learning/machine-learning-apps-text-analytics.md)。

このチュートリアルを完了すると、以下をプログラムで検出できるようになります。

* **センチメント** - テキストが肯定的か否定的か。
* **キー フレーズ** - 1 つの記事の話題。
* **トピック** - 多くの記事の話題。
* **言語** - テキストがどの言語で記述されているか。

この API では、送信されるドキュメントあたり 1 トランザクションが請求されることに注意してください。 たとえば、1 回の呼び出しで 1000 個のドキュメントのセンチメントを要求する場合、1000 のトランザクションが推論されます。

<a name="Overview"></a>

## <a name="general-overview"></a>概要
このドキュメントはステップ バイ ステップ ガイドです。 モデルのトレーニングに必要な手順を説明し、運用環境でモデルを使用できるようにするためのリソースを示すことを目的としています。 この演習の所要時間は約 30 分です。

以下のタスクでは、エディターを使用し、任意の言語で RESTful エンドポイントを呼び出す必要があります。

それでは作業を始めましょう。

## <a name="task-1---signing-up-for-the-text-analytics-apis"></a>タスク 1 - テキスト分析 API にサインアップする
このタスクでは、テキスト分析サービスにサインアップします。

1. [Azure Portal](//go.microsoft.com/fwlink/?LinkId=761108) で **Cognitive Services** に移動し、**テキスト分析**が "API の種類" として選択されていることを確認します。
2. プランを選択します。 1 か月あたり 5,000 トランザクションの **Free レベル**を選択してもかまいません。 無料プランと同様に、サービスの使用に対しては課金されません。 Azure サブスクリプションにログインする必要があります。 
3. その他のフィールドに入力し、自分のアカウントを作成します。
4. テキスト分析にサインアップしたら、 **API キー**を見つけます。 プライマリ キーは API サービスを使用するときに必要になるため、コピーしておいてください。

## <a name="task-2---detect-sentiment-key-phrases-and-languages"></a>タスク 2 - センチメント、キー フレーズおよび言語を検出する
テキストのセンチメント、キー フレーズおよび言語を検出するのは簡単です。 プログラムで、 [デモ エクスペリエンス](//go.microsoft.com/fwlink/?LinkID=759712) で返されるものと同じ結果が得られます。

> [!TIP]
> センチメント分析の場合は、テキストをセンチメントに分割することをお勧めします。 通常は、これでセンチメントの予測精度がより高くなります。
> 
> 

サポートされている言語は次のとおりです。

| 機能 | サポートされている言語コード |
|:--- |:--- |
| センチメント |`en` (英語)、`es` (スペイン語)、`fr` (フランス語)、`pt` (ポルトガル語) |
| キー フレーズ |`en` (英語)、`es` (スペイン語)、`de` (ドイツ語)、`ja` (日本語) |

1. ヘッダーを次のように設定する必要があります。 現在、API で受け入れられる入力形式は JSON のみであることに注意してください。 XML はサポートされていません。
   
        Ocp-Apim-Subscription-Key: <your API key>
        Content-Type: application/json
        Accept: application/json
2. 次に、JSON で入力行の書式を設定します。 センチメント、キー フレーズおよび言語の形式は同じです。 各 ID は一意である必要があり、システムによって返される ID であることに注意してください。 送信可能な 1 つのドキュメントの最大サイズは 10 KB であり、送信された入力の最大サイズの合計は 1 MB です。 1 回の呼び出しで 1,000 を超えるドキュメントを送信することはできません。 1 分あたりの呼び出しは 100 回に制限されています。そのため、1 回の呼び出しで大量のドキュメントを送信することをお勧めします。 language はオプションのパラメーターです。英語以外のテキストを分析対象とする場合は指定する必要があります。 以下に、入力の例を示しました。センチメント分析またはキー フレーズ抽出のオプション パラメーター `language` を指定しています。
   
        {
            "documents": [
                {
                    "language": "en",
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "language": "en",
                    "id": "100",
                    "text": "Final document"
                }
            ]
        }
3. センチメント、キー フレーズおよび言語の入力を使用して、システムへの **POST** 呼び出しを行います。 URL は次のようになります。
   
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages
4. この呼び出しでは、ID と検出されたプロパティを含む JSON 形式の応答が返されます。 センチメントの出力例を以下に示します (エラーの詳細を除く)。 センチメントの場合、次のようにドキュメントごとに 0 ～ 1 のスコアが返されます。
   
        // Sentiment response
        {
              "documents": [
                {
                    "id": "1",
                    "score": "0.934"
                },
                ...
                {
                    "id": "100",
                    "score": "0.002"
                },
            ]
        }
   
        // Key phrases response
        {
              "documents": [
                {
                    "id": "1",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
                ...
                {
                    "id": "100",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
            ]
        }
   
        // Languages response
        {
              "documents": [
                {
                    "id": "1",
                    "detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
                },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
                }
            ]
        }

## <a name="task-3---detect-topics-in-a-corpus-of-text"></a>タスク 3 - テキストのコーパスでトピックを検出する
これは新しくリリースされた API であり、送信されたテキスト レコードの一覧に基づき検出されたトピックの上位を返します。 トピックはキー フレーズ、つまり、1 つまたは複数の関連単語で特定されます。 この API は、レビューやユーザー フィードバックなど、人間が書いた短いテキストで効果的に機能するように設計されています。

この API を利用するには、 **100 件以上のテキスト レコード** を送信する必要がありますが、数百から数千単位のレコードからトピックを検出するように設計されています。 英語以外のレコードや 3 単語未満のレコードは破棄されるため、トピックには割り当てられません。 トピック検出の場合、送信可能な 1 つのドキュメントの最大サイズは 30 KB であり、送信された入力の最大サイズの合計は 30 MB です。 トピック検出では、送信は 5 分ごとに 5 回に制限されています。

結果の品質を向上させるのに役立つ、次のような 2 つの追加の **省略可能な** 入力パラメーターがあります。

* **ストップ ワード -**   この単語とそれに近い形のもの (複数形など) は、トピック検出パイプライン全体から除外されます。 よく使われる単語にはこれを使用します (たとえば、ソフトウェアに関する顧客からの苦情の場合、"問題"、"エラー"、"ユーザー" などを選択するのが妥当です)。 各文字列は 1 つの単語にする必要があります。
* **ストップ フレーズ** - このフレーズは、返されるトピックのリストから除外されます。 結果に表示しない一般的なトピックを除外する場合は、これを使用します。 たとえば、"Microsoft" や "Azure" を選択してトピックを除外するのが妥当です。 文字列には複数の単語を含めることができます。

テキスト内のトピックを検出するには、以下の手順に従います。

1. JSON で入力の書式を設定します。 この時点で、ストップ ワードとストップ フレーズを定義できます。
   
        {
            "documents": [
                {
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "id": "100",
                    "text": "Final document"
                }
            ],
            "stopWords": [
                "issue", "error", "user"
            ],
            "stopPhrases": [
                "Microsoft", "Azure"
            ]
        }
2. タスク 2 で定義したものと同じヘッダーを使用して、次のようにトピック エンドポイントへの **POST** 呼び出しを行います。
   
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics
3. これに対する応答では、次のようにヘッダーとして `operation-location` が返され、値は結果として得られるトピックをクエリする URL となります。
   
        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'
4. 返された `operation-location` を **GET** 要求を使用して定期的にクエリします。 1 分に 1 回行うことをお勧めします。
   
        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>
5. エンドポイントから応答が返されます。この応答には、`{"status": "notstarted"}` (処理前の場合)、`{"status": "running"}` (処理中の場合) および `{"status": "succeeded"}` (出力が完了している場合) があります。 以下のような形式の出力を使用できます (この例では、エラーの形式や日付などの詳細が除外されていることに注意してください)。
   
        {
            "status": "succeeded",
            "operationProcessingResult": {
                  "topics": [
                    {
                        "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
                        "score": "5"
                        "keyPhrase": "first topic name"
                    },
                    ...
                    {
                        "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
                        "score": "3"
                        "keyPhrase": "final topic name"
                    }
                ],
                  "topicAssignments": [
                    {
                        "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
                        "documentId": "1",
                        "distance": "0.354"
                    },
                    ...
                    {
                        "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
                        "documentId": "55",
                        "distance": "0.758"
                    },            
                ]
            }
        }

`operations` エンドポイントから返されるトピックの成功応答は、次のようなスキーマです。

    {
            "topics" : [{
                "id" : "string",
                "score" : "number",
                "keyPhrase" : "string"
            }],
            "topicAssignments" : [{
                "documentId" : "string",
                "topicId" : "string",
                "distance" : "number"
            }],
            "errors" : [{
                "id" : "string",
                "message" : "string"
            }]
        }

次に、この応答の各部分について説明します。

**topics**

| キー | Description |
|:--- |:--- |
| id |各トピックの一意の識別子。 |
| スコア |トピックに割り当てられているドキュメントの数。 |
| keyPhrase |トピックの要約となる単語または語句。 |

**topicAssignments**

| キー | Description |
|:--- |:--- |
| documentId |ドキュメントの識別子。 入力に含まれる ID と同じです。 |
| topicId |ドキュメントが割り当てられているトピック ID。 |
| distance |ドキュメントからトピックへのアフィリエーション スコア (0 ～ 1)。 distance スコアが低くなるほど、トピックのアフィリエーションは強くなります。 |

**errors**

| キー | Description |
|:--- |:--- |
| id |エラーが参照するドキュメントの一意の識別子を入力します。 |
| message |エラー メッセージ。 |

## <a name="next-steps"></a>次のステップ
お疲れさまでした。 これで、データでのテキスト分析の使用は完了です。 次は、 [Power BI](//powerbi.microsoft.com) などのツールを使用してデータを視覚化する方法だけでなく、自動的にテキスト データのリアルタイム ビューを表示して洞察を得る方法を確認することができます。

センチメントなどのテキスト分析機能をボットの一部として使用する方法については、Bot Framework サイトの[感情分析ボット](http://docs.botframework.com/en-us/bot-intelligence/language/#example-emotional-bot)の例を参照してください。




<!--HONumber=Nov16_HO3-->


