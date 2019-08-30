---
title: クイック スタート:Python 用 Text Analytics クライアント ライブラリ | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: このクイックスタートを使用して、Azure Cognitive Services から Text Analytics API を使ってみましょう。
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: aahi
ms.openlocfilehash: 1d7ad19a58327ba508ccb4e47d12d3d0f50465f4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884004"
---
# <a name="quickstart-text-analytics-client-library-for-python"></a>クイック スタート:Python 用 Text Analytics クライアント ライブラリ
<a name="HOLTop"></a>

Python 用 Text Analytics クライアント ライブラリの概要を紹介します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 

Python 用 Text Analytics クライアント ライブラリを使って次のことを実行します。

* センチメント分析
* 言語検出
* エンティティの認識
* キー フレーズの抽出


[リファレンス ドキュメント](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [パッケージ (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>設定

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](../../cognitive-services-apis-create-account.md) または [Azure CLI](../../cognitive-services-apis-create-account-cli.md) を使用して、ローカル コンピューター上に Text Analytics のリソースを作成します。 さらに、以下を実行できます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services/#decision)を無料で入手する。 これは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。  
* お使いのリソースを [Azure portal](https://portal.azure.com/) で表示する

試用版のサブスクリプションまたはリソースからキーを取得した後、`TEXT_ANALYTICS_SUBSCRIPTION_KEY` という名前のキーの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python をインストールしたら、次を使用してクライアント ライブラリをインストールすることができます。

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

お気に入りのエディターまたは IDE で、新しい Python アプリケーションを作成します。 次に、次のライブラリをインポートします。

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```python
# replace this endpoint with the correct one for your Azure resource. 
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
# This sample assumes you have created an environment variable for your key
key = os.environ["TEXT_ANALYTICS_SUBSCRIPTION_KEY"]
credentials = CognitiveServicesCredentials(key)
```

## <a name="object-model"></a>オブジェクト モデル

Text Analytics クライアントは、ご利用のキーを使用して Azure に対して認証を行う [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) オブジェクトです。 このクライアントには、テキストを単一の文字列として、またはバッチとして分析するためのメソッドがいくつか備わっています。 

テキストは、`documents` (使用したメソッドに応じて `id`、`text`、`language` の各属性の組み合わせを保持する `dictionary` オブジェクト) のリストとして API に送信されます。 `text` 属性には、分析対象のテキストが元の `language` で格納され、`id` には任意の値を指定できます。 

応答オブジェクトは、各ドキュメントの分析情報を格納するリストです。 

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python 用 Text Analytics クライアント ライブラリを使用して次のことを実行する方法が示されています。

* [クライアントを認証する](#authenticate-the-client)
* [感情分析](#sentiment-analysis)
* [言語検出](#language-detection)
* [エンティティの認識](#entity-recognition)
* [キー フレーズ抽出](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>クライアントを認証する

`credentials` および `text_analytics_url` をパラメーターとして新しい [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) オブジェクトを作成します。 Text Analytics サブスクリプションには、必ず適切な Azure リージョンを使用してください (たとえば `westcentralus`)。

```python
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>センチメント分析

[sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 関数を呼び出し、結果を取得します。 結果を反復処理し、各ドキュメントの ID とセンチメント スコアを印刷します。 0 に近いスコアは否定的センチメント、1 に近いスコアは肯定的センチメントを示します。

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "I had the best day of my life."
    }
]
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Sentiment Score: ",
          "{:.2f}".format(document.score))
```

### <a name="output"></a>Output

```console
Document Id:  1 , Sentiment Score:  0.87
```

## <a name="language-detection"></a>言語検出

以前に作成したクライアントを使用して、[detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) を呼び出して結果を取得します。 結果を反復処理し、各ドキュメントの ID と最初に返された言語を印刷します。

```python
documents = [
    {
        'id': '1',
        'text': 'This is a document written in English.'
    }
]
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Language: ",
          document.detected_languages[0].name)
```

### <a name="output"></a>Output

```console
Document Id:  1 , Language:  English
```

## <a name="entity-recognition"></a>エンティティの認識

以前に作成したクライアントを使用して、[entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 関数を呼び出して結果を取得します。 続いて、結果を反復処理し、各ドキュメントの ID とそれに含まれているエンティティを印刷します。

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    }
]
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ", entity.name, "\tType: ",
              entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```

### <a name="output"></a>Output

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
```

## <a name="key-phrase-extraction"></a>キー フレーズの抽出

以前に作成したクライアントを使用して、[key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 関数を呼び出して結果を取得します。 続いて、結果を反復処理し、各ドキュメントの ID とそれに含まれているキー フレーズを印刷します。

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    }
]
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t", phrase)
```


### <a name="output"></a>Output

```console
Document Id:  3
    Phrases:
         cat
         veterinarian
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、そのリソース グループに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics と Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics の概要](../overview.md)
* [感情分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [エンティティの認識](../how-tos/text-analytics-how-to-entity-linking.md)
* [言語を検出する](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [言語の認識](../how-tos/text-analytics-how-to-language-detection.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py) にあります。
