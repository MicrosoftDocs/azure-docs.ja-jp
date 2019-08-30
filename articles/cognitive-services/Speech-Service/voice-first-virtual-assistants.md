---
title: カスタムの音声優先仮想アシスタント (プレビュー) - Speech Service
titleSuffix: Azure Cognitive Services
description: Bot Framework および Cognitive Services Speech Software Development Kit (SDK) 上の Direct Line Speech チャネルを使用した、カスタムの音声優先仮想アシスタントの特徴、機能、および制限の概要。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: f77a95af1edcd37043ea1e067560577140a81a04
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967607"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>音声優先仮想アシスタント プレビューの概要

開発者が Azure Speech Services を使用したカスタムの仮想アシスタントを使用すると、アプリケーションとエクスペリエンスに合わせて自然で人間のような会話型インターフェイスを作成できます。 Bot Framework の Direct Line Speech チャネルには、低待機時間と高信頼性で音声入力、音声出力の対話を可能にする、互換性のあるボットへの調整され、編成されたエントリ ポイントが用意されており、これらの機能が強化されます。 これらのボットは、自然言語の対話に Microsoft の Language Understanding (LUIS) を使用できます。 Direct Line Speech は、Speech Software Development Kit (SDK) を使用するデバイスからアクセスされます。

   ![Direct Line Speech のオーケストレーション サービス フローの概念図](media/voice-first-virtual-assistants/overview.png "音声チャネル フロー")


Direct Line Speech とカスタムの音声優先仮想アシスタントのための関連する機能は、[仮想アシスタント ソリューションとエンタープライズ テンプレート](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)に最適な補足機能です。 Direct Line Speech は任意の互換性のあるボットで機能しますが、これらのリソースは、高品質の会話エクスペリエンスだけでなく、すぐに開始できる一般的なサポート スキルおよびモデルに再利用できるベースラインとして役立ちます。


## <a name="core-features"></a>コア機能

| Category | 機能 |
|----------|----------|
|[カスタム ウェイク ワード](speech-devices-sdk-create-kws.md) | "ヘイ Contoso" などのカスタム キーワードを使用して、ユーザーがボットとの会話を始められるようにすることができます。 このタスクは、Speech SDK のカスタム ウェイク ワード エンジンを使用して実行されます。これは、[ここで生成できる](speech-devices-sdk-create-kws.md)カスタム ウェイク ワードを使用して構成できます。 Direct Line Speech チャネルには、デバイス単体よりもウェイク ワードのアクティブ化の正確性を向上するサービス側のウェイク ワード検証が含まれています。
|[音声テキスト変換](speech-to-text.md) | Direct Line Speech チャネルには、Azure Speech Services の[音声変換](speech-to-text.md)を使用する、オーディオから認識済みテキストへのリアルタイムの文字起こしが含まれています。 このテキストは、文字起こしされているため、ボットとクライアント アプリケーションの両方で利用できます。
|[テキスト読み上げ](text-to-speech.md) | ボットからのテキスト応答は、Azure Speech Services の[テキスト読み上げ](text-to-speech.md)を使用して合成されます。 この合成は、クライアント アプリケーションでオーディオ ストリームとして利用できるようになります。 Microsoft では、独自のカスタム高品質ニューラル TTS 音声を構築してブランドに音声を加える機能を提供しています。詳しくは、[こちらにお問い合わせください](mailto:mstts@microsoft.com)。
|[Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Bot Framework 内のチャネルとして Direct Line Speech を使用すると、クライアント アプリケーション、互換性のあるボット、および Azure Speech Services の機能間をスムーズかつシームレスに接続できます。 Direct Line Speech チャネルを使用するようにボットを構成する方法の詳細については、[Bot Framework ドキュメントの関連するページ](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)を参照してください。

## <a name="get-started-with-virtual-assistants"></a>仮想アシスタントの概要

10 分もかからずにコードを実行できるように設計されたクイック スタートが用意されています。 以下の表は、音声優先仮想アシスタントのクイックスタートの一覧を言語別に整理して示しています。

| クイック スタート | プラットフォーム | API リファレンス |
|------------|----------|---------------|
| C#、UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>サンプル コード

音声優先仮想アシスタントを作成するためのサンプル コードは GitHub から入手できます。 これらのサンプルは、いくつかの一般的なプログラミング言語でボットに接続するためのクライアント アプリケーションに対応しています。

* [音声優先仮想アシスタントのサンプル (SDK)](https://aka.ms/csspeech/samples)
* [チュートリアル:Speech SDK を使用して音声でボットを有効にする (C#)](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>チュートリアル
[Speech SDK および Direct Line Speech チャネルを使用して、ボットを音声対応にする](tutorial-voice-enable-your-bot-speech-sdk.md)方法についてのチュートリアルです。

## <a name="customization"></a>カスタマイズ

Azure Speech Services を使用して構築された音声優先仮想アシスタントでは、[音声変換](speech-to-text.md)、[テキスト読み上げ](text-to-speech.md)、および[カスタム キーワードの選択](speech-devices-sdk-create-kws.md)に利用できるさまざまなカスタム オプションを使用できます。

> [!NOTE]
> カスタマイズのオプションは、言語やロケールによって異なります ([サポートされる言語](supported-languages.md)に関するページを参照してください)。

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>次の手順

* [Speech Services のサブスクリプション キーを無料で取得する](get-started.md)
* [Speech SDK を取得する](speech-sdk.md)
* [基本ボットの作成とデプロイ](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [仮想アシスタント ソリューションとエンタープライズ テンプレートの入手](https://github.com/Microsoft/AI)
