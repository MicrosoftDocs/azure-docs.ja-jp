---
title: Azure Cognitive Services の Text Analytics を使用した感情分析 | Microsoft Docs
description: Text Analytics REST API を使用したセンチメントの検出方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 7287a9ddbd84960dcde790d813a6204e9e790094
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887422"
---
# <a name="example-how-to-detect-sentiment-with-text-analytics"></a>例:Text Analytics でセンチメントを検出する方法

[Sentiment Analysis API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) はテキスト入力を評価し、ドキュメントごとに 0 (負) から 1 (正) のセンチメント スコアを返します。

この機能は、ソーシャル メディア、顧客レビューおよびディスカッション フォーラムで肯定的および否定的センチメントを検出するために役立ちます。 コンテンツはユーザーが提供します。モデルとトレーニング データは、サービスによって提供されます。

現時点では、感情分析は、英語、ドイツ語、スペイン語、およびフランス語をサポートします。 その他の言語はプレビュー段階です。 詳細については、[サポートされている言語](../text-analytics-supported-languages.md)に関するページを参照してください。

> [!TIP]
> Text Analytics には感情分析用の Linux ベースの Docker コンテナー イメージも用意されているため、データの近くに [Text Analytics コンテナーをインストールして実行](text-analytics-how-to-install-containers.md)することができます。

## <a name="concepts"></a>概念

Text Analytics では、機械学習分類アルゴリズムを使用して、0 ～ 1 のセンチメント スコアを生成します。 1 に近いスコアは肯定的センチメント、0 に近いスコアは否定的センチメントを示します。 モデルは、センチメント関連付けの広範なテキスト本文を使って事前トレーニングされています。 現時点では、ユーザー独自のトレーニング データを提供することはできません。 モデルでは、テキスト分析中に、テキスト処理、品詞の分析、単語の配置、および単語の関連付けを含む手法を組み合わせて使用します。 アルゴリズムの詳細については、[Text Analytics の概要](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/)に関する記事を参照してください。

感情分析は、テキスト内の特定のエンティティのセンチメントを抽出するのではなく、ドキュメント全体に対し実行されます。 実際には、ドキュメントに大きなテキスト ブロックではなく、1 つまたは 2 つの文が含まれている場合に、スコア付けの正確性が向上する傾向があります。 客観性評価段階では、モデルは、ドキュメント全体として客観的か、またはセンチメントが含まれているかどうかを決定します。 大部分が客観的なドキュメントはセンチメント検出段階には進まず、.50 スコアが付けられ、それ以上は処理されません。 パイプラインを先に進むドキュメントの次の段階では、ドキュメントで検出されたセンチメントの程度にしたがって、 .50 より上または下のスコアが生成されます。

## <a name="preparation"></a>準備

感情分析では、作業するテキストが小さなチャンクである場合に、高い品質の結果を生成します。 これは、テキスト ブロックが大きい方がパフォーマンスが向上するキー フレーズ抽出とは反対です。 両方の操作から最善の結果を得るには、両方の操作に応じて入力を再構成することを検討してください。

JSON ドキュメントは、id、text、language の形式である必要があります。

ドキュメントのサイズは、ドキュメントあたり 5,120 文字未満である必要があり、コレクションあたり最大 1,000 の項目 (ID) を含めることができます。 コレクションは、要求の本文で送信されます。 感情分析のために送信するコンテンツの例を次に示します。

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>手順 1:要求を構造化する

要求定義の詳細については、[Text Analytics API を呼び出す方法](text-analytics-how-to-call-api.md)に関するページを参照してください。 確認に便利なように、以下に再度、要点を示します。

+ **POST** 要求を作成します。 この要求については次の API ドキュメントを確認してください。[感情分析 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)

+ Azure 上の Text Analytics リソースまたはインスタンス化された [Text Analytics コンテナー](text-analytics-how-to-install-containers.md)を使用して、感情分析用の HTTP エンドポイントを設定します。 そこには、`/sentiment` リソースが含まれている必要があります: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`

+ Text Analytics 操作用のアクセス キーが含まれるように要求ヘッダーを設定します。 詳細については、[エンドポイントを見つけてアクセス キーにアクセスする方法](text-analytics-how-to-access-key.md)に関するページを参照してください。

+ 要求本文で、この分析のために準備した JSON ドキュメントのコレクションを提供します。

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md) を使用するか、[ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)に記載されている **API テスト コンソール**を開き、要求を構造化して POST でサービスに投稿します。

## <a name="step-2-post-the-request"></a>手順 2:要求を投稿する

要求が受信されると分析が実行されます。 サービスは、1 分あたり最大 100 個の要求を受け付けます。 各要求の最大サイズは 1 MB です。

サービスはステートレスであることを思い出してください。 ユーザーのアカウントに保存されるデータはありません。 結果はすぐに、応答で返されます。


## <a name="step-3-view-results"></a>手順 3:結果の表示

センチメント アナライザーは、0 ～ 1 の範囲のスコアを割り当てて、テキストを主に正または負に分類します。 0.5 に近い値は、中立または不確定です。 0.5 のスコアは、中立性を示します。 文字列の感情分析をすることができない場合、またはセンチメントがない場合、スコアは必ず正確に 0.5 になります。 たとえば、英語の言語コードでスペイン語の文字列を渡した場合、スコアは 0.5 になります。

出力はすぐに返されます。 結果は、JSON を受け付けるアプリケーションにストリームするか、ローカル システム上のファイルに出力を保存してから、そのファイルを、データの並べ替え、検索、および操作が可能なアプリケーションにインポートすることができます。

次の例では、この記事のドキュメント コレクションへの応答を示します。

```
{
    "documents": [
        {
            "score": 0.9999237060546875,
            "id": "1"
        },
        {
            "score": 0.0000540316104888916,
            "id": "2"
        },
        {
            "score": 0.99990355968475342,
            "id": "3"
        },
        {
            "score": 0.980544924736023,
            "id": "4"
        },
        {
            "score": 0.99996328353881836,
            "id": "5"
        }
    ],
    "errors": []
}
```

## <a name="summary"></a>まとめ

この記事では、Cognitive Services の Text Analytics を使用する感情分析の概念とワークフローについて説明しました。 要旨:

+ 選択した言語に対して、[Sentiment analysis API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) を使用できます。
+ 要求本文内の JSON ドキュメントには、id、text、および language のコードが含まれます。
+ POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](text-analytics-how-to-access-key.md)を使用して `/sentiment` エンドポイントに対して行われます。
+ ドキュメント ID ごとのセンチメント スコアで構成される応答出力は、Excel や Power BI を含む JSON を受け取るすべてのアプリにストリーミングすることができます。

## <a name="see-also"></a>関連項目 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics 製品ページ](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [キー フレーズを抽出する](text-analytics-how-to-keyword-extraction.md)
