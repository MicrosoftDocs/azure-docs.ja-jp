---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.openlocfilehash: b2b405e7f6babf84ef8297310a5a7a34c3f2dc11
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102623521"
---
Cognitive Services には、[Language Understanding](../luis/what-is-luis.md) と [QnA Maker](../qnamaker/overview/overview.md) という 2 つの自然言語処理サービスが用意されており、それぞれ異なる目的があります。 各サービスをどのようなときに使用し、それぞれのサービスがどのように補完されるかについて説明します。

自然言語処理 (NLP) により、チャット ボットなどのクライアント アプリケーションは、自然言語を使用してユーザーと連携できるようになります。 ユーザーは、文または語句を入力します。 ユーザーのテキストは、文法、スペル、および句読点が不適切である可能性があります。 Cognitive Services はユーザーが入力した文を処理して、ユーザーを支援するためにチャット ボットが必要とする情報を返すことができます。

## <a name="cognitive-services-with-nlp"></a>NLP での Cognitive Services

Language Understanding (LUIS) と QnA Maker では、NLP が提供されます。 クライアント アプリケーションでは、自然言語のテキストが送信されます。 サービスでは、テキストが取得されて処理され、結果が返されます。

## <a name="when-to-use-each-service"></a>どのようなときに各サービスを使用するか

Language Understanding (LUIS) と QnA Maker では、さまざまな問題が解決されます。 LUIS ではユーザーのテキスト (発話と呼ばれます) の意図が特定され、QnA Maker ではユーザーのテキスト (クエリと呼ばれます) への回答が決定されます。

正しいサービスを選択するには、クライアント アプリケーションから送られてくるユーザー テキストと、クライアント アプリケーションで Cognitive Services から取得する必要がある情報を、理解する必要があります。

チャット ボットで `How do I get to the Human Resources building on the Seattle North campus?` というテキストを受け取る場合は、次のグラフを使用して、各サービスでテキストがどのように処理されるかを理解します。

|サービス|クライアント アプリケーションの決定|
|--|--|
|LUIS|テキストの **ユーザーの意図を決定します** - サービスでは質問に対する回答は返されません。 たとえば、このテキストは `FindLocation` という意図と一致するものとして分類されます。<br>|
|QnA Maker|カスタム ナレッジ ベースから **質問に対する回答が返されます**。 たとえば、このテキストは、`Get on the #9 bus and get off at Franklin street` という静的なテキストが回答になる質問として特定されます。|
|||

> [!div class="mx-imgBorder"]
> ![LUIS と QnA Maker をそれぞれいつ使用するかについてのインフォグラフィック](./luis-qna-maker-together-decision.png)

## <a name="when-do-you-use-luis"></a>LUIS を使用する場合

チャット ボットのプロセスの一部として発話の意図を知る必要があるときは、LUIS を使用します。 テキスト `How do I get to the Human Resources building on the Seattle North campus?` の例を続けると、ユーザーが場所を探そうとしていることがわかったら、(エンティティで抽出された) 発話に関する詳細をトランスポート サーバーなどの別のサービスに渡して回答を取得できます。

意図を特定するために、LUIS と QnA Maker を組み合わせる必要はありません。

チャット ボットで意図とエンティティに基づいてテキストを処理し (LUIS を使用)、特定の静的なテキストの回答を検索する (QnA Maker を使用) 必要がある場合は、この発話に対して 2 つのサービスを組み合わせることができます。

## <a name="when-do-you-use-qna-maker"></a>QnA Maker を使用する場合

回答の静的なナレッジ ベースがある場合は、QnA Maker を使用します。 このナレッジ ベースはニーズに合わせてカスタマイズされており、PDF や URL などのドキュメントで作成します。

引き続き発話 `How do I get to the Human Resources building on the Seattle North campus?` の例を使うと、公開されている QnA Maker サービスにテキストをクエリとして送信し、最適な回答を受け取ります。

質問に対する回答を決定するために、LUIS と QnA Maker を組み合わせる必要はありません。

チャット ボットで意図とエンティティに基づいてテキストを処理し (LUIS を使用)、回答を検索する (QnA Maker を使用) 必要がある場合は、この発話に対して 2 つのサービスを組み合わせることができます。

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>ナレッジ ベースが不完全な場合に両方のサービスを使用する

QnA Maker のナレッジ ベースを構築しているのに、対象ドメインが変化していることがわかった場合は (タイムリーな情報など)、LUIS と QnA Maker サービスを組み合わせることができます。 これにより、ナレッジ ベースの情報を使用する一方で、LUIS を使用してユーザーの意図を判断することもできます。 クライアント アプリケーションで意図がわかったら、別のソースに関連情報を要求することができます。

クライアント アプリケーションでは、スコアに対する LUIS と QnA Maker の両方の応答を監視する必要があります。 QnA Maker からのスコアが任意のしきい値を下回っている場合は、LUIS から返された意図とエンティティの情報を使用して、情報をサードパーティのサービスに渡します。

例のテキスト `How do I get to the Human Resources building on the Seattle North campus?` で、QnA Maker から信頼度の低いスコアが返されるものとします。 LUIS から返された意図 `FindLocation` と、抽出されたエンティティ (`Human Resources building` や `Seattle North campus` など) を使用し、この情報をマッピング サービスまたは検索サービスに送信して、別の回答を取得します。

このサードパーティの回答を、検証のためにユーザーに提示できます。 ユーザーの承認が得られたら、QnA Maker に戻って、ナレッジを拡大するための情報を追加できます。

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>チャット ボットで詳細情報が必要な場合に両方のサービスを使用する

いずれかのサービスで提供されるものより多くの情報が必要な場合、デシジョン ツリーを続けるには、クライアント アプリケーションで両方のサービスを使用して両方の応答を処理します。

両方のサービスを使用するプロセスを構築するには、Bot Framework の **[Dispatch CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** ツールを使用します。 このツールでは、子アプリとしての LUIS と QnA Maker の間でディスパッチを行う、意図の上位 LUIS アプリが構築されます。 LUIS、QnA Maker、Bot Framework との統合について詳しくは、[こちら](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs)を参照してください。

この種のチャット ボットを実装するには、Bot Builder のサンプル「**NLP とディスパッチ**」を使用してください ([C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) または [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch))。

## <a name="best-practices"></a>ベスト プラクティス

各サービスのベスト プラクティスを実装します。

* [LUIS](../luis/luis-concept-best-practices.md) のベスト プラクティス
* [QnA Maker](../qnamaker/concepts/best-practices.md) のベスト プラクティス

## <a name="see-also"></a>関連項目

* [Language Understanding (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Dispatch CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Bot Framework のサンプル](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure Bot Service](/azure/bot-service/bot-service-overview-introduction)
* [Azure Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot Framework Web チャット](https://github.com/microsoft/BotFramework-WebChat)