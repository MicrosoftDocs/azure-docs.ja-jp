---
title: インク データを Ink Recognizer API に送信する
titleSuffix: Azure Cognitive Services
description: さまざまなアプリケーション用の Ink Analyzer API の呼び出しについて学習します
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ad961495d44f13522a3c02224a5612aaedaf076
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274488"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>インク データを Ink Recognizer API に送信する 

デジタル インキングとは、手書きや図面などの入力のデジタル表現を可能にするテクノロジを指します。 これは通常、スタイラスなどの入力デバイスの動きをキャプチャするデジタイザーを使用して実現されます。 デバイスが多彩なデジタル インキング エクスペリエンスを可能にし続けるのに伴い、人工知能および機械学習によって、任意のコンテキストで書かれたシェイプおよびテキストの認識が可能になりました。 Ink Recognizer API を使用すると、インク ストロークを送信して、それらに関する詳細な情報を取得できます。 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Ink Recognizer API と OCR サービス

Ink Recognizer API では、光学式文字認識 (OCR) を使用しません。 OCR サービスは、画像のピクセル データを処理して、手書きおよびテキスト認識を提供します。 これは、オフライン認識とも呼ばれます。 これに対し、Ink Recognizer API では、入力デバイスの使用時にキャプチャされたデジタル インク ストローク データが必要になります。 この方法でデジタル インク データを処理すると、OCR サービスと比較してより正確な認識結果が得られます。 

## <a name="sending-ink-data"></a>インク データの送信

Ink Recognizer API では、入力デバイスによって作成された、それが検出面に触れた時点から持ち上げられた時点までのインク ストロークを表す X 座標と Y 座標が必要です。 各ストロークのポイントは、コンマ区切りの値の文字列である必要があり、次の例のような JSON 形式になっている必要があります。 さらに、各インク ストロークは、各要求で一意の ID を備える必要があります。 同じ要求内で ID が繰り返されると、API からエラーが返されます。 最も正確な認識結果を得るには、小数点以下の桁数を少なくとも 8 桁にしてください。 キャンバスの原点 (0, 0) は、インキング キャンバスの左上隅と見なされます。

> [!NOTE]
> 次の例は有効な JSON ではありません。 Ink Recognizer の完全な JSON 要求を [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909) で確認できます。
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Ink Recognizer の応答

Ink Recognizer API は、インク コンテンツから認識したオブジェクトに関する分析応答を返します。 応答には、異なるインク ストローク間の関係を記述する認識ユニットが含まれます。 たとえば、異なる別個のシェイプを作成するストロークは、異なるユニットに含まれます。 各ユニットには、認識されたオブジェクト、その座標、その他の描画属性など、そのインク ストロークに関する詳細情報が含まれます。

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Ink Recognizer API によって認識されるシェイプ

Ink Recognizer API では、メモを取る際に最も一般的に使用されるシェイプを識別できます。 次の画像はいくつかの基本的な例を示しています。 API によって認識されるシェイプおよびその他のインク コンテンツの完全な一覧については、[API リファレンスの記事](https://go.microsoft.com/fwlink/?linkid=2089907)を参照してください。 

![Ink Recognizer API によって認識されるシェイプの一覧](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>推奨される呼び出しパターン

Ink Recognizer REST API は、アプリケーションに応じてさまざまなパターンで呼び出すことができます。 

### <a name="user-initiated-api-calls"></a>ユーザーによって開始される API 呼び出し

ユーザー入力を受け取るアプリ (たとえば、メモ取りアプリや注釈アプリ) を作成している場合、いつどのインクを Ink Recognizer API に送信するかをユーザーが制御できるようにすることをお勧めします。 この機能は、テキストとシェイプの両方がキャンバスに存在し、ユーザーがそれぞれに対して異なるアクションを実行したい場合に特に便利です。 API に送信される内容をユーザーが選択できるように選択機能 (なげなわ、その他の幾何学的選択ツールなど) を追加することを検討してください。  

### <a name="app-initiated-api-calls"></a>アプリによって開始される API 呼び出し

タイムアウト後にアプリが Ink Recognizer API を呼び出すようにすることもできます。 現在のインク ストロークを定期的に API に送信することで、API の応答時間を向上させながら、作成された認識結果を保存できます。 たとえば、ユーザーが入力を完了したことを検出した後に、一連の手書きのテキストを API に送信できます。 

認識結果を事前に得ることで、互いに関連しているインク ストロークの特性に関する情報が得られます。 たとえば、どのストロークがグループ化されて同じ単語、行、リスト、段落、またはシェイプが形成されるか、などです。 たとえば、この情報に基づいてストロークのグループを一度に選択できるようにすることで、アプリのインク選択機能を充実させることができます。

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Ink Recognizer API と Windows Ink を統合する

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) は、さまざまなデバイスでデジタル インキング エクスペリエンスを可能にするツールとテクノロジを提供します。 Windows Ink プラットフォームと Ink Recognizer API を組み合わせて、デジタル インク ストロークを表示および解釈するアプリケーションを作成できます。

## <a name="next-steps"></a>次の手順

* [Ink Recognizer API とは](../overview.md)
* [Ink Recognizer REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2089907)

* 以下を使用してデジタル インク ストローク データの送信を開始します。
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)
