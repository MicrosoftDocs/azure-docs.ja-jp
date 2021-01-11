---
title: Language Understanding (LUIS) とは
description: Language Understanding (LUIS) - 機械学習を自然言語での会話に使用して、意味を予測し、情報を抽出するクラウドベースの API サービス。
keywords: Azure, 人工知能, ai, 自然言語処理, nlp, 自然言語理解, nlu, ai 会話, 会話型 ai, ai チャットボット, チャットボット メーカー, LUIS, nlp ai, luis ai, azure luis, 自然言語の理解
ms.topic: overview
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 98d801f6a34feb40d56215f8b6257a68ec628e10
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320054"
---
# <a name="what-is-language-understanding-luis"></a>Language Understanding (LUIS) とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) は、カスタムの機械学習インテリジェンスをユーザーの自然言語での会話に適用して、全体の意味を予測し、関連性のある詳細な情報を引き出すクラウド ベースの API サービスです。

LUIS のクライアント アプリケーションは、ユーザーと自然言語でコミュニケーションを行ってタスクを完了する会話型アプリケーションであれば、どれでもかまいません。 クライアント アプリケーションの例として、ソーシャル メディア アプリ、チャットボット、音声対応デスクトップ アプリケーションがあります。

![Cognitive Services Language Understanding (LUIS) と連携する 3 つのクライアント アプリケーションの概念図](./media/luis-overview/luis-entry-point.png "Cognitive Services Language Understanding (LUIS) と連携する 3 つのクライアント アプリケーションの概念図")

## <a name="use-luis-in-a-chat-bot"></a>チャット ボットでの LUIS の使用

<a name="Accessing-LUIS"></a>

LUIS アプリの発行後、クライアント アプリケーションは、LUIS 自然言語処理エンドポイント [API][endpoint-apis] に発話 (テキスト) を送信し、その結果を JSON 応答として受信します。 LUIS の一般的なクライアント アプリケーションはチャット ボットです。


![自然言語の理解 (NLP) でユーザー テキストを予測するためにチャット ボットと連携する LUIS の概念図](./media/luis-overview/LUIS-chat-bot-request-response.svg "自然言語の理解 (NLP) でユーザー テキストを予測するためにチャット ボットと連携する LUIS の概念図")

|手順|アクション|
|:--|:--|
|1|クライアント アプリケーションがユーザーの "_発話_" (自分の言葉で表現されたテキスト) を送信します。ここでは、"I want to call my HR rep." を LUIS エンドポイントに HTTP 要求として送信します。|
|2|LUIS を使うと、カスタム言語モデルを作成して、アプリケーションにインテリジェンスを追加できます。 機械学習言語モデルにより、ユーザーの非構造化入力テキストが取得され、最上位の意図 `HRContact` を含む JSON 形式の応答が返されます。 エンドポイントからの JSON 形式の応答には、少なくとも、クエリの発話と上位スコアの意図が含まれます。 _Contact Type_ エンティティなどのデータを抽出することもできます。|
|3|クライアント アプリケーションが、JSON 応答を使用して、ユーザーから受けた要求への対応方法を判断します。 これらの判断には、Bot Framework コードでのデシジョン ツリーや、他のサービスの呼び出しが含まれることがあります。 |

LUIS アプリは、クライアント アプリケーションが賢明な選択を行えるようにするためのインテリジェンスを提供します。 そうした選択を LUIS が提供するわけではありません。

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>自然言語理解 (NLU)

LUIS は、自然言語処理 (NLP) のサブセットである NLU の形式で[人工知能 (AI) を提供](artificial-intelligence.md)します。

LUIS アプリには、特定の領域 (ドメイン) に固有の自然言語モデルが含まれます。 LUIS アプリは、構築済みのドメイン モデルをひな形にできるほか、独自にモデルを構築したり、構築済みのドメインの一部に独自の情報を融合したりすることができます。

* **構築済みのモデル**: LUIS には、意図、発話、および構築済みのエンティティを含む構築済みのドメイン モデルが多数あります。 構築済みのモデルの意図と発話を使用せずに、構築済みのエンティティを使用できます。 [構築済みのドメイン モデル](luis-how-to-use-prebuilt-domains.md)には、必要な設計がすべて含まれているため、LUIS の使用をすぐに開始できる優れた方法です。

* **カスタム モデル**の LUIS を使うと、意図を含む独自のカスタムモデルやエンティティを複数の方法で識別できます。 エンティティには、機械学習エンティティ、特定のエンティティまたはリテラル エンティティ、機械学習エンティティとリテラル エンティティの組み合わせが含まれます。

[NLP](artificial-intelligence.md)、および NLU の LUIS 固有の領域の詳細を確認してください。

## <a name="step-1-design-and-build-your-model"></a>手順 1:モデルを設計して構築する

**[意図](luis-concept-intent.md)** と呼ばれる、ユーザーの意図のカテゴリを使用してモデルを設計します。 意図にはそれぞれ、ユーザー **[発話](luis-concept-utterance.md)** の例が必要です。 それぞれの発話からデータが得られますが、それらのデータは[機械学習エンティティ](luis-concept-entity-types.md#effective-machine-learned-entities)を使って抽出する必要があります。

|ユーザーの発話例|Intent|抽出された日付|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|open|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|1pm、Bob (午後 1 時、Bob)|

モデルの構築には、[オーサリング](https://go.microsoft.com/fwlink/?linkid=2092087) API シリーズ、[**LUIS ポータル**](https://www.luis.ai)、またはその両方を使用します。 [ポータル](get-started-portal-build-app.md)と [SDK クライアント ライブラリ](azure-sdk-quickstart.md)を使用して構築する方法について確認してください。

## <a name="step-2-get-the-query-prediction"></a>手順 2:クエリ予測を取得する

アプリのモデルがトレーニングされてエンドポイントに発行された後、クライアント アプリケーション (チャットボットなど) によって、予測[エンドポイント](https://go.microsoft.com/fwlink/?linkid=2092356) API に発話が送信されます。 API によって、分析のために発話にモデルが適用され、予測結果が JSON 形式で返されます。

エンドポイントからの JSON 形式の応答には、少なくとも、クエリの発話と上位スコアの意図が含まれます。 以下の **Contact Type** エンティティや全体的なセンチメントなどのデータを抽出することもできます。

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>手順 3:モデルの予測の改善

LUIS アプリが公開され、実際のユーザーの発話を受け取ると、予測精度向上のためにエンドポイント発話の[アクティブな学習](luis-concept-review-endpoint-utterances.md)が LUIS によって提供されます。 これらの提案は、開発ライフサイクルの定期的なメンテナンス作業の一部として確認してください。

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>開発ライフサイクルとツール
LUIS を使うと、完全な[開発ライフ サイクル](luis-concept-app-iteration.md)に統合するためのツール、バージョン管理、および他の LUIS 作成者とのコラボレーションが提供されます。

Language Understanding (LUIS) は、REST API として、HTTP 要求を行う任意の製品、サービス、フレームワークで使用できます。 LUIS には、いくつかの主要なプログラミング言語用のクライアント ライブラリ (SDK) も用意されています。 提供されている[開発者向けリソース](developer-reference-resource.md)の詳細を確認してください。

LUIS をボットですばやく簡単に使用するためのツール:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS): この NPM パッケージは、スタンドアロンのコマンドライン ツールまたはインポートとして、作成と予測を提供します。
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen): LUISGen は、エクスポートされた LUIS モデルから厳密に型指定された C# および typescript のソース コードを生成するためのツールです。
* [Dispatch](https://aka.ms/dispatch-tool): ディスパッチャー モデルを使うことで、いくつかの LUIS アプリや QnA Maker アプリを親アプリから使用できます。
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown): LUDown は、ボットの言語モデルを管理するのに役立つコマンドライン ツールです。

## <a name="integrate-with-a-bot"></a>ボットとの統合

[Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) を [Microsoft Bot Framework](https://dev.botframework.com/) と共に使用して、チャットボットを構築してデプロイします。 設計と開発には、グラフィカル インターフェイス ツールの [Composer](https://docs.microsoft.com/composer/) または主要なボット シナリオ用に設計された[実用的なボット サンプル](https://github.com/microsoft/BotBuilder-Samples)を使用します。

## <a name="integrate-with-other-cognitive-services"></a>他の Cognitive Services との統合

LUIS で使用されるその他の Cognitive Services:
* [QnA Maker][qnamaker] は、多種多様なテキストを質問と回答のナレッジ ベースに統合できるようにします。
* [Speech サービス](../Speech-Service/overview.md)は、話し言葉による要求をテキストに変換します。

LUIS は、既存の LUIS リソースの一部として Text Analytics の機能を提供します。 この機能には、あらかじめ構築された keyPhrase エンティティを使用した[感情分析](luis-how-to-publish-app.md#configuring-publish-settings)と[キー フレーズ抽出](luis-reference-prebuilt-keyphrase.md)が含まれます。

## <a name="learn-with-the-quickstarts"></a>クイックスタートで学習する

[ポータル](get-started-portal-build-app.md)と [SDK クライアント ライブラリ](azure-sdk-quickstart.md)を使用したハンズオン クイックスタートで LUIS について学習します。


## <a name="next-steps"></a>次のステップ

* サービスとドキュメントの[最新情報](whats-new.md)
* [意図](luis-concept-intent.md)と[エンティティ](luis-concept-entity-types.md)を使用する[アプリを計画する](luis-how-plan-your-app.md)。
* [予測エンドポイントに対してクエリを実行する](luis-get-started-get-intent-from-browser.md)。
* LUIS の[開発者向けリソース](developer-reference-resource.md)。

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
