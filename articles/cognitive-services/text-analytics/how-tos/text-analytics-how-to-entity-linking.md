---
title: Text Analytics API でエンティティ認識を利用する
titleSuffix: Azure Cognitive Services
description: Text Analytics REST API を使用してエンティティを認識する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: d1f4c72ec0a67baa6f523c1ab632903b0580f3a7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339382"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics-preview"></a>Text Analytics (プレビュー段階) で名前付きエンティティの認識を使用する方法

[Entity Linking API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) は、構造化されていないテキストを受け取り、JSON ドキュメントごとに、あいまいさを解消したエンティティの一覧を返します。一覧には、Web 上にある、より多くの情報 (Wikipedia と Bing) へのリンクが含まれます。 

## <a name="entity-linking-and-named-entity-recognition"></a>Entity Linking と名前付きエンティティ認識

Text Analytics の `entities` エンドポイントは、名前付きエンティティの認識 (NER) とエンティティ リンクの両方をサポートしています。

### <a name="entity-linking"></a>Entity Linking
エンティティ リンク設定は、テキスト内で見つかったエンティティの個性を識別してあいまいさを解消する機能です (例: "Mars" が惑星として使用されているか、古代ローマの戦争の神様として使用されているかを判定する)。 このプロセスのためには、認識されたエンティティがリンクされているナレッジ ベースが存在している必要があります。`entities` エンドポイントの Text Analytics には、Wikipedia がナレッジ ベースとして使用されます。

Text Analytics [バージョン 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) では、エンティティ リンク設定のみ利用できます。

### <a name="named-entity-recognition-ner"></a>名前付きエンティティの認識 (NER)
名前付きエンティティの認識 (NER) は、テキスト形式のさまざまなエンティティを識別して、事前に定義したクラスに分類する機能です。 サポートされているエンティティのクラスを以下に示します。

Text Analytics [バージョン 2.1 プレビュー](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)では､エンティティ リンク設定と名前付きエンティティ認識 (NER) の両方を利用できます。

### <a name="language-support"></a>言語のサポート

さまざまな言語でエンティティ リンク設定を使用するには、各言語で、対応するナレッジ ベースを使用する必要があります。 Text Analytics でのエンティティ リンク設定の場合、これは、`entities` エンドポイントによってサポートされている各言語は、その言語の対応する Wikipedia コーパスにリンクすることを意味します。 コーパスのサイズは言語によって異なるため、エンティティ リンク設定の機能の再現度もさまざまであると考えられています。

## <a name="supported-types-for-named-entity-recognition"></a>名前付きエンティティ認識でサポートされている型

| Type  | SubType | 例 |
|:-----------   |:------------- |:---------|
| Person        | 該当なし\*         | "Jeff", "Bill Gates"     |
| Location      | 該当なし\*         | "Redmond, Washington", "Paris"  |
| Organization  | 該当なし\*         | "Microsoft"   |
| Quantity      | Number        | "6", "six"     | 
| Quantity      | 割合    | "50%"､"fifty percent"| 
| Quantity      | Ordinal       | "2nd"､"second"     | 
| Quantity      | NumberRange   | "4 to 8"     | 
| Quantity      | Age           | "90 day old"､"30 years old"    | 
| Quantity      | 通貨      | "$10.99"     | 
| Quantity      | Dimension     | "10 miles"､"40 cm"     | 
| Quantity      | 気温   | "32 degrees"    |
| DateTime      | 該当なし\*         | "6:30PM February 4, 2012"      | 
| DateTime      | Date          | "May 2nd, 2017", "05/02/2017"   | 
| Date Time     | Time          | "8am", "8:00"  | 
| DateTime      | DateRange     | "May 2nd to May 5th"    | 
| DateTime      | TimeRange     | "6pm to 7pm"     | 
| DateTime      | Duration      | "1 minute and 45 seconds"   | 
| DateTime      | Set           | "every Tuesday"     | 
| DateTime      | TimeZone      |    | 
| URL           | 該当なし\*         | "https:\//www.bing.com"    |
| Email         | 該当なし\*         | "support@contoso.com" |

\* 入力および抽出されたエンティティによっては、一部エンティティで `SubType` が省略されることがあります。



## <a name="preparation"></a>準備

JSON ドキュメントは、id、text、language の形式である必要があります。

現在サポートされている言語については、[この一覧](../text-analytics-supported-languages.md)を参照してください。

ドキュメントのサイズは、ドキュメントあたり 5,120 文字未満である必要があり、コレクションあたり最大 1,000 の項目 (ID) を含めることができます。 コレクションは、要求の本文で送信されます。 次の例では、エンティティ リンク設定の末尾に付け加えるコンテンツを示しています。

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
                },
               {"id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>手順 1:要求を構造化する

要求定義の詳細については、[Text Analytics API を呼び出す方法](text-analytics-how-to-call-api.md)に関するページを参照してください。 確認に便利なように、以下に再度、要点を示します。

+ **POST** 要求を作成します。 この要求については次の API ドキュメントを確認してください。[Entity Linking API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ エンティティ抽出用の HTTP エンドポイントを設定します。 そこには、`/entities` リソースが含まれている必要があります: `https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

+ Text Analytics 操作用のアクセス キーが含まれるように要求ヘッダーを設定します。 詳細については、[エンドポイントを見つけてアクセス キーにアクセスする方法](text-analytics-how-to-access-key.md)についてのページを参照してください。

+ 要求本文で、この分析のために準備した JSON ドキュメントのコレクションを提供します。

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md) を使用するか、[ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)に記載されている **API テスト コンソール**を開き、要求を構造化して POST でサービスに投稿します。

## <a name="step-2-post-the-request"></a>手順 2:要求を投稿する

要求が受信されると分析が実行されます。 このサービスは、毎秒 100 件/毎分 1000 件の要求を受け取ります。 各要求の最大サイズは 1 MB です。

サービスはステートレスであることを思い出してください。 ユーザーのアカウントに保存されるデータはありません。 結果はすぐに、応答で返されます。

## <a name="step-3-view-results"></a>手順 3:結果の表示

すべての POST 要求で、ID と検出されたプロパティを含む JSON 形式の応答が返されます。

出力はすぐに返されます。 結果は、JSON を受け付けるアプリケーションにストリームするか、ローカル システム上のファイルに出力を保存してから、そのファイルを、データの並べ替え、検索、および操作が可能なアプリケーションにインポートすることができます。

次に、エンティティ リンク設定のための出力の例を示します。

```json
{
    "Documents": [
        {
            "Id": "1",
            "Entities": [
                {
                    "Name": "Jeff",
                    "Matches": [
                        {
                            "Text": "Jeff",
                            "Offset": 0,
                            "Length": 4
                        }
                    ],
                    "Type": "Person"
                },
                {
                    "Name": "three dozen",
                    "Matches": [
                        {
                            "Text": "three dozen",
                            "Offset": 12,
                            "Length": 11
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50",
                    "Matches": [
                        {
                            "Text": "50",
                            "Offset": 49,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50%",
                    "Matches": [
                        {
                            "Text": "50%",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        },
        {
            "Id": "2",
            "Entities": [
                {
                    "Name": "Great Depression",
                    "Matches": [
                        {
                            "Text": "The Great Depression",
                            "Offset": 0,
                            "Length": 20
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Great Depression",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                    "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                },
                {
                    "Name": "1929",
                    "Matches": [
                        {
                            "Text": "1929",
                            "Offset": 30,
                            "Length": 4
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "By 1933",
                    "Matches": [
                        {
                            "Text": "By 1933",
                            "Offset": 36,
                            "Length": 7
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "Gross domestic product",
                    "Matches": [
                        {
                            "Text": "GDP",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Gross domestic product",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                    "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                },
                {
                    "Name": "United States",
                    "Matches": [
                        {
                            "Text": "America",
                            "Offset": 56,
                            "Length": 7
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "United States",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                    "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                    "Type": "Location"
                },
                {
                    "Name": "25",
                    "Matches": [
                        {
                            "Text": "25",
                            "Offset": 72,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "25%",
                    "Matches": [
                        {
                            "Text": "25%",
                            "Offset": 72,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        }
    ],
    "Errors": []
}
```


## <a name="summary"></a>まとめ

この記事では、Cognitive Services の Text Analytics を使用するエンティティ リンク設定の概念とワークフローについて説明しました。 要約すると:

+ [Entity Linking API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) は選択した言語で利用できます。
+ 要求本文内の JSON ドキュメントには、id、text、および language のコードが含まれます。
+ POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](text-analytics-how-to-access-key.md)を使用して `/entities` エンドポイントに対して行われます。
+ リンクされているエンティティ (ドキュメント ID ごとに信頼度スコア、オフセット、Web リンクが含まれている) で構成される応答の出力は、どのアプリケーションでも使用できます

## <a name="see-also"></a>関連項目 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics 製品ページ](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)
