---
title: 音声サービスとは
titleSuffix: Azure Cognitive Services
description: Speech Service は、音声テキスト変換、テキスト読み上げ、音声翻訳が 1 つの Azure サブスクリプションにまとめられたものです。 Speech SDK、Speech Devices SDK、または REST API を使用して、アプリケーション、ツール、デバイスに音声を追加します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 08/20/2020
ms.author: trbye
ms.openlocfilehash: cc29eb959876a0c9c6f8c8e5dee2d18aaa5443ac
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88825360"
---
# <a name="what-is-the-speech-service"></a>音声サービスとは

音声サービスは、音声テキスト変換、テキスト読み上げ、音声翻訳が 1 つの Azure サブスクリプションにまとめられたものです。 [Speech CLI](spx-overview.md)、[Speech SDK](speech-sdk-reference.md)、[Speech Devices SDK](https://aka.ms/sdsdk-quickstart)、[Speech Studio](https://speech.microsoft.com/)、または [REST API](rest-apis.md) を使用して、アプリケーション、ツール、デバイスを簡単に音声対応にできます。

> [!IMPORTANT]
> Speech Service は、Bing Speech API と Translator Speech に代わるものです。 移行の手順については、" _「ハウツー ガイド」の「移行」_ " をご覧ください。

以下の機能は、Speech Service に属しています。 各機能の一般的なユース ケースの詳細を学習したり、API リファレンスを参照したりするには、次の表のリンクを使用してください。

| サービス | 特徴量 | 説明 | SDK | REST |
|---------|---------|-------------|-----|------|
| [音声テキスト変換](speech-to-text.md) | リアルタイムの音声テキスト変換 | 音声テキスト変換では、オーディオ ストリームまたはローカル ファイルからテキストへの文字起こしまたは翻訳がリアルタイムで行われ、結果のテキストをアプリケーション、ツール、またはデバイスで使用したり表示したりできます。 音声テキスト変換を [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) で使用し、文字起こしされた音声からユーザーの意図を抽出して、音声コマンドで対応します。 | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [一括音声テキスト変換](batch-transcription.md) | 一括音声テキスト変換では、Azure Blob Storage に格納された大量の音声データを対象に、音声からテキストへの文字起こしを非同期で行うことができます。 音声からテキストへの変換に加え、一括音声テキスト変換は、ダイアライゼーションや感情分析にも対応します。 | いいえ | [はい](https://westus.cris.ai/swagger/ui/index) |
| | [マルチデバイスの会話](multi-device-conversation.md) | 会話の中で複数のデバイスまたはクライアントを接続して、音声ベースまたはテキストベースのメッセージを送信し、文字起こしや翻訳を容易に実現します。| はい | いいえ |
| | [会話の文字起こし](conversation-transcription-service.md) | リアルタイムの音声認識、話者識別、およびダイアライゼーションが有効になります。 話者を区別することができるため、対面会議の文字起こしに最適です。 | はい | いいえ |
| | [カスタム音声モデルの作成](#customize-your-speech-experience) | 独自環境での認識と文字起こしに音声テキスト変換を使用している場合は、カスタムの音響、言語、発音モデルを作成してトレーニングし、周囲の雑音や業界固有の語彙に対応できます。 | いいえ | [はい](https://westus.cris.ai/swagger/ui/index) |
| [音声合成](text-to-speech.md) | テキスト読み上げ | テキスト読み上げでは、[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) が使用され、入力テキストが人間のような合成音声に変換されます。 標準音声とニューラル音声から選択できます (「[言語のサポート](language-support.md)」を参照)。 | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Custom Voice の作成](#customize-your-speech-experience) | ブランドや製品に固有のカスタム音声フォントを作成します。 | いいえ | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [音声翻訳](speech-translation.md) | 音声翻訳 | 音声翻訳を使用すると、音声のリアルタイムの多言語翻訳がアプリケーション、ツール、デバイスで可能になります。 音声間翻訳や音声テキスト翻訳にはこのサービスを使用します。 | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | いいえ |
| [音声アシスタント](voice-assistants.md) | 音声アシスタント | Speech Service を使用した音声アシスタントにより、開発者は、そのアプリケーションとエクスペリエンスのための自然で人間のような会話型インターフェイスを作成できます。 音声アシスタント サービスは、Bot Framework の Direct Line Speech チャネルまたは統合カスタム コマンド (プレビュー) サービスを使用してタスクを遂行するアシスタントの実装とデバイスとの間に、高速で信頼性の高い対話を実現します。 | [はい](voice-assistants.md) | いいえ |
| [Speaker Recognition](speaker-recognition-overview.md) | 話者の検証と識別 | Speaker Recognition サービスは、固有の音声特性によって話者を検証および識別するアルゴリズムを提供します。 Speaker Recognition は、"だれが話しているのか" という質問に回答するために使用されます。 | はい | [はい](https://docs.microsoft.com/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Speech Service を無料で試す

次の手順では、Microsoft アカウントと Azure アカウントの両方が必要です。 Microsoft アカウントがない場合は、[Microsoft アカウント ポータル](https://account.microsoft.com/account)で無料のものにサインアップすることができます。 **[Microsoft アカウントでサインイン]** を選択し、サインインを求められたら **[Microsoft アカウントの作成]** を選択します。 新しい Microsoft アカウントを作成して確認する手順に従います。

Azure アカウントを作成したら、[Azure サインアップ ページ](https://azure.microsoft.com/free/ai/)に移動し、 **[無料で始める]** を選択し、Microsoft アカウントを使用して新しい Azure アカウントを作成します。

> [!NOTE]
> 音声サービスには、無料とサブスクリプションの 2 つのサービス レベルがあります。これには、異なる制限事項と特典があります。 無料の Azure アカウントにサインアップすると、最大 30 日間有効な、有料の音声サービス サブスクリプションに充当できる 200 ドルのサービス クレジットが付属しています。
>
> 無料の小規模の音声サービス レベルを使用する場合は、無料試用版またはサービス クレジットの有効期限が切れた後でも、この無料のサブスクリプションを保持できます。
>
> 詳細については、「[Cognitive Services の価格 - Speech Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)」を参照してください。

### <a name="create-the-azure-resource"></a>Azure リソースを作成する

Speech Service リソース (無料または有料レベル) を Azure アカウントに追加するには:

1. お使いの Microsoft アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

1. ポータルの左上にある **[Create a resource]\(リソースの作成\)** を選択します。 **[リソースの作成]** が表示されない場合は、画面左上の折りたたまれたメニューを選択することで、いつでも見つけることができます。

1. **新規**ウィンドウで、検索ボックスに「speech」と入力し、Enter キーを押します。

1. 検索結果で、 **[Speech]** を選択します。

   ![speech の検索結果](media/index/speech-search.png)

1. **[作成]** を選択して、次のことを行います。

   - 新しいリソースに一意の名前を指定します。 この名前は、同じサービスに関連付けられた複数のサブスクリプションを区別するのに役立ちます。
   - 新しいリソースが関連付けられている Azure サブスクリプションを選択して、料金の課金方法を決定します。
   - リソースが使用される[リージョン](regions.md)を選択します。
   - 無料 (F0) または有料 (S0) の価格レベルのどちらかを選択します。 各レベルの価格と使用量クォータの完全な情報については、 **[価格の詳細を表示]** を選択します。
   - この Speech サブスクリプションの新しいリソース グループを作成するか、既存のリソース グループにサブスクリプションを割り当てます。 リソース グループは、さまざまな Azure サブスクリプションを整理しておくのに役立ちます。
   - **［作成］** を選択します これでデプロイの概要に移動し、デプロイの進行状況を示すメッセージが表示されます。

> [!NOTE]
> Standard レベルのサブスクリプションは、1 つまたは複数のリージョンにいくつでも作成できます。 ただし、Free レベルのサブスクリプションは 1 つしか作成できません。 7 日間使用されなかった無料レベルのモデル デプロイは、自動的に破棄されます。

新しい音声リソースを展開するまでに少し時間がかかります。 デプロイが完了したら、 **[リソースに移動]** を選択し、左側のナビゲーション ウィンドウで **[キー]** を選択して、音声サービスのサブスクリプション キーを表示します。 各サブスクリプションには 2 つのキーがあります。アプリケーションでどちらのキーを使用しても構いません。 キーをコード エディターやその他の場所にすばやくコピーして貼り付けるには、各キーの横にあるコピー ボタンを選択し、ウィンドウを切り替えてクリップボードの内容を目的の場所に貼り付けます。

> [!IMPORTANT]
> これらのサブスクリプション キーは、Cognitive Service API にアクセスするために使用されます。 キーを共有しないでください。 Azure Key Vault を使用するなどして、安全に保管してください。 これらのキーを定期的に再生成することもお勧めします。 API 呼び出しを行うために必要なキーは 1 つだけです。 最初のキーを再生成するときに、2 番目のキーを使用してサービスに継続的にアクセスすることができます。

## <a name="complete-a-quickstart"></a>クイックスタートに取り組む

ほとんどの一般的なプログラミング言語向けのクイックスタートが提供されています。それぞれ基本的な設計パターンを学び、いずれも 10 分もかからずにコードを実行できるように作られています。 それぞれの機能のクイックスタートについては、次の各記事を参照してください。

* [音声テキスト変換のクイックスタート](speech-to-text-basics.md)
* [テキスト読み上げのクイックスタート](get-started-text-to-speech.md)
* [音声翻訳のクイックスタート](speech-translation-basics.md)
* [意図認識のクイックスタート](quickstarts/intent-recognition.md)
* [話者認識のクイックスタート](speaker-recognition-basics.md)

Speech Service を使用する機会が得られたら、さまざまなシナリオを解決する方法を説明するチュートリアルを試してみてください。

- [チュートリアル:Speech SDK、LUIS、C# を使用して音声の意図を認識する](how-to-recognize-intents-from-speech-csharp.md)
- [チュートリアル:Speech SDK を使用してボットを音声対応にする (C#)](tutorial-voice-enable-your-bot-speech-sdk.md)
- [チュートリアル:テキストを翻訳してセンチメントを分析し、翻訳済みテキストから音声を合成する Flask アプリを作成する (REST)](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>サンプル コードを取得する

Speech Service のサンプル コードは、GitHub 上で入手できます。 これらのサンプルでは、ファイルやストリームからの音声の読み取り、連続的な認識と単発の認識、カスタム モデルの使用など、一般的なシナリオについて説明されています。 SDK と REST のサンプルを見るには、次のリンクを使用してください。

- [音声変換、テキスト読み上げ、および音声翻訳のサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [バッチ文字起こしのサンプル (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [テキスト読み上げのサンプル (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [音声アシスタントのサンプル (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>音声エクスペリエンスをカスタマイズする

Speech Service は組み込みのモデルで問題なく動作しますが、製品や環境に合わせてエクスペリエンスをさらにカスタマイズおよび調整したいことがあります。 カスタマイズ オプションは、音響モデルのチューニングから、独自ブランドに固有の音声フォントにまで及びます。

その他の製品では、医療や保険など特定の目的に合わせてチューニングされた音声モデルが用意されていますが、それらは、すべてのユーザーに対して一律に提供されるものです。 Azure Speech のカスタマイズは、他のユーザーやカスタマーが手にすることのできない "*独自*" の競争優位をもたらします。 つまり、カスタマイズしたモデルは自分だけのものであり、ユースケースに合わせて独自にチューニングされています。

| Speech Service | プラットフォーム | 説明 |
| -------------- | -------- | ----------- |
| 音声テキスト変換 | [カスタム音声](https://aka.ms/customspeech) | 音声認識モデルをニーズと使用可能なデータに合わせてカスタマイズします。 話し方、ボキャブラリ、背景ノイズといった音声認識の障壁を克服可能です。 |
| テキスト読み上げ | [Custom Voice](https://aka.ms/customvoice) | お客様のスピーチ データを使用して、テキスト読み上げアプリ用の認識可能な独自の音声を作成します。 音声パラメーターのセットを調整することで、音声出力をさらに細かく調整できます。 |

## <a name="reference-docs"></a>リファレンス ドキュメント

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: 音声テキスト変換](rest-speech-to-text.md)
- [REST API: テキスト読み上げ](rest-text-to-speech.md)
- [REST API: 一括文字起こしとカスタマイズ](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [音声テキスト変換の概要](speech-to-text-basics.md)
> [テキスト読み上げの概要](get-started-text-to-speech.md)
