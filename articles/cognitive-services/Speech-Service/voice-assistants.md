---
title: 音声アシスタント - 音声サービス
titleSuffix: Azure Cognitive Services
description: Speech SDK (ソフトウェア開発キット) を使用した音声アシスタントの特徴、機能、制限の概要。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: fc85eec008ef099d63d538e4871a1a84573f5a18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98790447"
---
# <a name="what-is-a-voice-assistant"></a>音声アシスタントとは

音声サービスを使用した音声アシスタントにより、開発者は、そのアプリケーションとエクスペリエンスのための自然で人間のような会話型インターフェイスを作成できます。

音声アシスタント サービスを使用すると、(1) ボットに音声機能を追加するための [Direct Line Speech](direct-line-speech.md) (Azure Bot Service 経由) または (2) 音声コマンド実行シナリオのためのカスタム コマンドのいずれかを使用するアシスタント実装とデバイスとの間で、高速で信頼性の高い対話を実現できます。

## <a name="choosing-an-assistant-solution"></a>アシスタント ソリューションを選択する

音声アシスタントを作成する際に最初に行うことは、その要件を明らかにすることです。 音声サービスには、アシスタントの対話を精巧に作り上げるためのさまざまな補完的ソリューションが用意されています。 音声入力および音声出力機能を、Azure Bot Service を使用して構築された柔軟で使いやすいボットに [Direct Line Speech](direct-line-speech.md) チャネルを使用して追加できます。または、[カスタム コマンド](custom-commands.md) アプリが簡単に作成できることを利用して、単純な音声コマンド処理シナリオを実現できます。

| 目的 | 候補となるソリューション | 例 |
|-------------------|------------------|----------------|
|堅牢なスキルの連携と完全なデプロイ制御を使用した制約のない会話 | [Direct Line Speech](direct-line-speech.md) チャネルを使用する Azure Bot Service ボット | <ul><li>"I need to go to Seattle"</li><li>"What kind of pizza can I order?"</li></ul>
|作成とホスティングが簡素化された音声コマンド処理またはシンプルなタスク指向の会話 | [カスタム コマンド](custom-commands.md) | <ul><li>"Turn on the overhead light"</li><li>"Make it 5 degrees warmer"</li><li>その他のサンプルを[こちらで入手できます](https://speech.microsoft.com/customcommands)</li></ul>

アシスタントで扱う処理がまだ決まっていない場合には、既定の最適な選択肢として [Direct Line Speech](direct-line-speech.md) をお勧めします。 [仮想アシスタント ソリューションとエンタープライズ テンプレート](/azure/bot-service/bot-builder-enterprise-template-overview)や [QnA Maker サービス](../qnamaker/overview/overview.md)を使用して、一般的なパターンを作成したり既存のナレッジ ソースを使用したりするなど、豊富なツール群と作成支援機能が統合されています。

[カスタム コマンド](custom-commands.md)を使用すると、音声優先の対話操作を行うために最適化された、リッチな音声コマンド処理アプリを簡単に構築できます。 これにより、統一された作成エクスペリエンス、自動ホスティング モデル、比較的低い複雑性が実現し、音声コマンドのシナリオに最適なソリューションの構築に専念できるようになります。

   ![アシスタント ソリューションの比較](media/voice-assistants/assistant-solution-comparison.png "アシスタント ソリューションの比較")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>Speech SDK を使用して音声アシスタントを構築するための参照アーキテクチャ

   ![音声アシスタントのオーケストレーション サービス フローの概念図](media/voice-assistants/overview.png "音声アシスタントのフロー")

## <a name="core-features"></a>コア機能

アシスタントの対話を作成する手段として [Direct Line Speech](direct-line-speech.md) と[カスタム コマンド](custom-commands.md)のどちらを選んだとしても、その豊富なカスタマイズ機能を使用して、ブランド、製品、パーソナリティに合わせてアシスタントをカスタマイズできます。

| カテゴリ | 特徴 |
|----------|----------|
|[カスタム キーワード](./custom-keyword-basics.md) | ユーザーは、"Hey Contoso" などのカスタム キーワードを使用してアシスタントとの会話を始めることができます。 これは、アプリが Speech SDK のカスタム キーワード エンジンを使用して行います。カスタム キーワード エンジンは、[ここで生成できる](./custom-keyword-basics.md)カスタム キーワードを使用して構成できます。 音声アシスタントは、サービス側のキーワード検証を使用することで、(デバイス単体の場合と比べて) キーワード アクティブ化の正確性を高めることができます。
|[音声テキスト変換](speech-to-text.md) | 音声アシスタントは、音声サービスの[音声変換](speech-to-text.md)を使用してリアルタイム オーディオを認識されたテキストに変換します。 このテキストは文字起こしされているため、アシスタントの実装とクライアント アプリケーションのどちらからでも利用できます。
|[テキスト読み上げ](text-to-speech.md) | アシスタントからのテキスト応答は、音声サービスの[テキスト読み上げ](text-to-speech.md)を使用して合成されます。 この合成は、クライアント アプリケーションでオーディオ ストリームとして利用できるようになります。 Microsoft では、独自のカスタム高品質ニューラル TTS 音声を構築してブランドに音声を加える機能を提供しています。 詳細については、[こちらからお問い合わせください](mailto:mstts@microsoft.com)。

## <a name="getting-started-with-voice-assistants"></a>音声アシスタントの使用を開始する

10 分もかからずにコードを実行できるように設計されたクイック スタートが用意されています。 以下の表は、音声アシスタントのクイックスタートの一覧を言語別に整理して示しています。

* [クイック スタート: Direct Line Speech を使用してカスタム音声アシスタントを作成する](quickstarts/voice-assistants.md)
* [クイック スタート: カスタム コマンドを使用して音声コマンド処理アプリを構築する](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>サンプル コードとチュートリアル

音声アシスタントを作成するためのサンプル コードは GitHub から入手できます。 これらのサンプルは、いくつかの一般的なプログラミング言語でアシスタントに接続するためのクライアント アプリケーションに対応しています。

* [GitHub 上の音声アシスタントのサンプル](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [チュートリアル:C# Speech SDK で、Azure Bot Service を使用して構築したアシスタントを音声対応にする](tutorial-voice-enable-your-bot-speech-sdk.md)
* [チュートリアル:単純な音声コマンドを使用してカスタム コマンド アプリケーションを作成する](./how-to-develop-custom-commands-application.md)

## <a name="customization"></a>カスタマイズ

Azure Speech Services を使用して構築された音声アシスタントでは、すべてのカスタム オプションを使用できます。

* [カスタム音声](./custom-speech-overview.md)
* [Custom Voice](how-to-custom-voice.md)
* [カスタム キーワード](custom-keyword-overview.md)

> [!NOTE]
> カスタマイズのオプションは、言語やロケールによって異なります ([サポートされる言語](language-support.md)に関するページを参照してください)。

## <a name="next-steps"></a>次のステップ

* [Speech サービスのサブスクリプション キーを無料で取得する](overview.md#try-the-speech-service-for-free)
* [カスタム コマンドについて詳しく学習する](custom-commands.md)
* [Direct Line Speech について詳しく学習する](direct-line-speech.md)
* [Speech SDK を取得する](speech-sdk.md)