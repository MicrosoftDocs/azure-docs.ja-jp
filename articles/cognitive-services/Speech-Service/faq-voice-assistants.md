---
title: 音声アシスタントについてよく寄せられる質問
titleSuffix: Azure Cognitive Services
description: Custom Commands または Direct Line Speech チャネルを使用した音声アシスタントに関してよく寄せられる質問への回答を紹介します。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 511eb12df511fd037fc0b5bec701c0cc5c29bad2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617779"
---
# <a name="voice-assistants-frequently-asked-questions"></a>音声アシスタントについてよく寄せられる質問

疑問点への回答がこのドキュメントで見つからない場合は、[その他のサポート オプション](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)を確認してください。

## <a name="general"></a>全般

**Q:音声アシスタントとは**

**A:** 音声アシスタントは、Cortana と同様に、ユーザーの発話を聞き取り、その発話の内容を分析して意味を取り、発話の意図に応答して 1 つ以上のアクションを実行し、多くの場合に読み上げコンポーネントを含む応答をユーザーに提供するソリューションです。 これは、システムと対話するための "音声入力、音声出力" のエクスペリエンスです。 音声アシスタントの作成者は、Speech SDK の `DialogServiceConnector` を使用してデバイス上のアプリケーションを作成し、[Custom Commands](custom-commands.md) またはボット フレームワークの [Direct Line Speech](direct-line-speech.md) チャネルを使用して作成されたアシスタントと通信します。 これらのアシスタントは、カスタム キーワードおよびカスタム音声を使用して、お客様のブランドや製品に合わせたエクスペリエンスを提供できます。

**Q:Custom Commands または Direct Line Speech のどちらを使用すればよいですか。違いは何でしょうか。**

**A:** [Custom Commands](custom-commands.md) は、タスク実行シナリオに適したアシスタントを簡単に作成してホストするための、複雑さを抑えたツール セットです。 [Direct Line Speech](direct-line-speech.md) は、信頼性の高い会話シナリオを可能にする、高度でリッチな機能を提供します。 詳細については、[アシスタント ソリューションの比較](voice-assistants.md#choosing-an-assistant-solution)に関する記事を参照してください。

**Q:始めにどうすればよいですか。**

**A:** カスタム コマンド (プレビュー) アプリケーションまたは基本的な Bot Framework ボットの作成から始めるのが最適な方法です。

- [カスタム コマンド (プレビュー) アプリケーションを作成する](./quickstart-custom-commands-application.md)
- [基本的な Bot Framework ボットを作成する](/azure/bot-service/bot-builder-tutorial-basic-deploy)
- [ボットを Direct Line Speech チャネルに接続する](/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>デバッグ

**Q:チャンネル シークレットはどこにありますか。**

**A:** プレビュー バージョンの Direct Line Speech を使用したことがある場合、または関連するドキュメントを読んでいる場合、シークレット キーは、Direct Line Speech チャネルの登録ページで確認できます。 この値は、Speech SDK の v1.7 `DialogServiceConfig` ファクトリ メソッド `FromBotSecret` でも必要となります。

最新バージョンの Direct Line Speech では、デバイスからボットとコンタクトを取るプロセスが簡素化されています。 チャンネル登録ページの上部にあるドロップダウンで、ご利用の Direct Line Speech チャネルの登録を音声リソースに関連付けてください。 関連付けが完了すると、サブスクリプションに関連付けたボットとコンタクトを取るように `DialogServiceConnector` を構成する `BotFrameworkConfig::FromSubscription` ファクトリ メソッドが v1.8 Speech SDK に追加されます。

まだクライアント アプリケーションを v1.7 から v1.8 に移行している段階の場合、`DialogServiceConfig::FromBotSecret` は、そのチャンネル シークレット パラメーターについて、空ではない非 null 値 (以前使用していたシークレットなど) で引き続き動作します。 それよりも新しいチャンネル登録に関連付けられている Speech サブスクリプションをご利用の場合は、単に無視されます。 値は null 以外で、かつ空ではないことが "_必要_" です。デバイス上でこれらの点がチェックされた後で、サービス側の関連付けが有効となります。

詳細なガイドについては、チャンネルの登録手順を示した[チュートリアル セクション](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel)を参照してください。

**Q:接続時に 401 エラーが表示され、何も機能しません。音声サブスクリプション キーが有効であることはわかっています。どうなっているのでしょうか?**

**A:** Azure portal でサブスクリプションを管理する場合、**Cognitive Services** リソース (Microsoft.CognitiveServicesAllInOne、"All Cognitive Services") "_ではなく_"、**Speech** リソース (Microsoft.CognitiveServicesSpeechServices、"Speech") を使用していることを確認してください。 また、[音声アシスタントについての Speech サービス リージョンのサポート](regions.md#voice-assistants)を確認してください。

![Direct Line Speech のサブスクリプションを修正する](media/voice-assistants/faq-supported-subscription.png "互換性のある Speech サブスクリプションの例")

**Q:`DialogServiceConnector` から戻された認識テキストを受け取りますが、'1011' エラーが表示され、ボットからは何も表示されません。なぜですか?**

**A:** このエラーは、アシスタントと音声アシスタント サービスの間の通信の問題を示しています。

- Custom Commands の場合は、Custom Commands アプリケーションが発行されていることを確認します
- Direct Line Speech の場合、[Direct Line Speech チャネルにボットを接続し](/azure/bot-service/bot-service-channel-connect-directlinespeech)、(関連する Web ソケット サポートと共に) [ストリーミング プロトコル サポートをボットに追加し](/azure/bot-service/directline-speech-bot)ていることを確認してから、ボットがチャネルからの受信要求に応答していることを確認します。

**Q:このコードがまだ動作しないか、`DialogServiceConnector` を使用すると別のエラーが発生するか、あるいはこの両方が発生します。どうすればよいですか。**

**A:** ファイルベースのログではより詳細な情報が提供され、サポート リクエストを迅速に行うことができます。 この機能を有効にするには、[ファイル ログの使用方法](how-to-use-logging.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [トラブルシューティング](troubleshooting.md)
- [リリース ノート](releasenotes.md)