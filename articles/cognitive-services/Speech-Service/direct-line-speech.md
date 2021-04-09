---
title: Direct Line Speech - 音声サービス
titleSuffix: Azure Cognitive Services
description: Direct Line Speech と Speech SDK (ソフトウェア開発キット) を使用した音声アシスタントの特徴、機能、制限の概要。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 6eb689c2e93b5a9cac932e428245410ba35e77d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611795"
---
# <a name="what-is-direct-line-speech"></a>Direct Line Speech とは

**Direct Line Speech** は、柔軟で拡張可能な音声アシスタントを作成するための堅牢なエンドツーエンドのソリューションです。 これは、Bot Framework とその Direct Line Speech チャネル (音声によるボットとの対話のために最適化されています) を利用しています。

[音声アシスタント](voice-assistants.md)はユーザーの音声を聞き取り、応答としてアクションを実行します (多くの場合は音声で応答します)。 これは、[音声テキスト変換](speech-to-text.md)を使用してユーザーの音声を文字に変換し、そのテキストの自然言語理解に対してアクションを実行します。 このアクションには、多くの場合、[テキスト読み上げ](text-to-speech.md)で生成されたアシスタントからの音声出力が含まれます。

Direct Line Speech は、音声アシスタントのための最高レベルのカスタマイズと精緻化を提供します。 これは、タスクの完了または指示と制御が目的の、自由形式な、自然な、またはその 2 つの性質を持った会話のシナリオ向けに設計されています。 このような高度の柔軟性はさらなる複雑化を伴うため、自然言語入力を使用して十分に定義されたタスクを扱うシナリオでは、ソリューションのエクスペリエンスを簡素化するために、[Custom Commands](custom-commands.md) を検討することができます。

## <a name="getting-started-with-direct-line-speech"></a>Direct Line Speech の使用を開始する

Direct Line Speech を使用して音声アシスタントを作成する最初の手順では、[Speech のサブスクリプション キーを取得](overview.md#try-the-speech-service-for-free)し、そのサブスクリプションに関連付けられた新しいボットを作成し、そのボットを Direct Line Speech チャネルに接続します。

   ![Direct Line Speech のオーケストレーション サービス フローの概念図](media/voice-assistants/overview-directlinespeech.png "Speech チャネルのフロー")

Direct Line Speech を使用して簡単な音声アシスタントを作成するための完全なステップ形式ガイドについては、[Speech SDK と Direct Line Speech チャネルを使用してボットを音声対応にするチュートリアル](tutorial-voice-enable-your-bot-speech-sdk.md)を参照してください。

また、コードを実行し、API を迅速に学習できるように設計されたクイックスタートも用意しています。 以下の表は、音声アシスタントのクイックスタートの一覧を言語とプラットフォーム別に整理して示しています。

| クイック スタート | プラットフォーム | API リファレンス |
|------------|----------|---------------|
| C#、UWP | Windows | [[参照]](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows、macOS、Linux | [[参照]](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [[参照]](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>サンプル コード

音声アシスタントを作成するためのサンプル コードは GitHub から入手できます。 これらのサンプルは、いくつかの一般的なプログラミング言語でアシスタントに接続するためのクライアント アプリケーションに対応しています。

* [音声アシスタントのサンプル (SDK)](https://aka.ms/csspeech/samples/#voice-assistants-quickstarts)
* [チュートリアル:Speech SDK を使用してアシスタントを音声対応にする (C#)](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>カスタマイズ

音声サービスを使用して構築された音声アシスタントでは、[音声変換](speech-to-text.md)、[テキスト読み上げ](text-to-speech.md)、および[カスタム キーワードの選択](./custom-keyword-basics.md)に利用できるさまざまなカスタム オプションを使用できます。

> [!NOTE]
> カスタマイズのオプションは、言語やロケールによって異なります ([サポートされる言語](./language-support.md)に関するページを参照してください)。

Direct Line Speech と音声アシスタントのための関連する機能は、[仮想アシスタント ソリューションとエンタープライズ テンプレート](/azure/bot-service/bot-builder-enterprise-template-overview)に最適な補足機能です。 Direct Line Speech は任意の互換性のあるボットで機能しますが、これらのリソースは、高品質の会話エクスペリエンスだけでなく、すぐに開始できる一般的なサポート スキルおよびモデルに再利用できるベースラインとして役立ちます。

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](./speech-sdk.md)
* [Azure Bot Service](/azure/bot-service/)

## <a name="next-steps"></a>次のステップ

* [Speech サービスのサブスクリプション キーを無料で取得する](overview.md#try-the-speech-service-for-free)
* [Speech SDK を取得する](speech-sdk.md)
* [基本ボットの作成とデプロイ](/azure/bot-service/bot-builder-tutorial-basic-deploy)
* [仮想アシスタント ソリューションとエンタープライズ テンプレートの入手](https://github.com/Microsoft/AI)
