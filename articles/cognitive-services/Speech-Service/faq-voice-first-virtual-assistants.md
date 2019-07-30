---
title: Direct Line Speech に関してよく寄せられる質問
titleSuffix: Azure Cognitive Services
description: Direct Line Speech チャネルを使用した音声優先仮想アシスタントに関してよく寄せられる質問の回答を紹介します。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: bd7e1f87fea03d0aac7fd9f746b777e3b15e917e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606635"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>音声優先仮想アシスタントのプレビュー:よく寄せられる質問

疑問点への回答がこのドキュメントで見つからない場合は、[その他のサポート オプション](support.md)を確認してください。

## <a name="general"></a>全般

**Q:Direct Line Speech とは何ですか?**

**A:** Speech SDK の `DialogServiceConnector` は、Bot Framework の Direct Line Speech チャネルに接続されているボットに双方向の非同期通信を提供します。 このチャネルは、Azure Speech Services から音声変換とテキスト読み上げへの調整されたアクセスを提供します。これにより、ボットが音声入力、音声出力の会話エクスペリエンスに完全に対応します。 ウェイク ワードとウェイク ワード検証がサポートされているため、このソリューションを使用すると、高度にカスタマイズ可能な音声優先仮想アシスタントを自社のブランドや製品用に構築できます。

**Q:始めにどうすればよいですか。**

**A:** 音声優先仮想アシスタントの作成から始めるには、[基本的な Bot Framework ボットの作成](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)から始めることをお勧めします。 次に、ボットを [Direct Line Speech チャネル](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)に接続します。

## <a name="debugging"></a>デバッグ

**Q:接続時に 401 エラーが表示され、何も機能しません。音声サブスクリプション キーが有効であることはわかっています。どうなっているのでしょうか?**

**A:** プレビュー段階の Direct Line Speech には、使用できるサブスクリプションに特定の制限があります。 **Cognitive Services** リソース (Microsoft.CognitiveServicesAllInOne、"All Cognitive Services") "*ではなく*"、**Speech** リソース (Microsoft.CognitiveServicesSpeechServices、"Speech") を使用していることを確認してください。 Direct Line Speech では、現在、[Speech Services のリージョンのサブセット](regions.md#voice-first-virtual-assistants)のみサポートされています。

![Direct Line Speech の正しいサブスクリプション](media/voice-first-virtual-assistants/faq-supported-subscription.png "互換性のある音声サブスクリプションの例")

**Q:Direct Line Speech から戻された認識テキストを受け取りますが、'1011' エラーが表示され、ボットからは何も表示されません。なぜですか?**

**A:** このエラーは、ボットと Direct Line Speech 間の通信に問題があることを示しています。 [Direct Line Speech チャネルを接続し](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)、(関連する Web ソケット サポートと共に) [ストリーミング プロトコル サポートをボットに追加し](https://aka.ms/botframework/addstreamingprotocolsupport)ていることを確認してから、ボットがチャネルからの受信要求に応答していることを確認します。

**Q:このコードがまだ動作しないか、DialogServiceConnector を使用すると別のエラーが発生するか、あるいはこの両方が発生します。どうすればよいですか。**

**A:** ファイルベースのログではより詳細な情報が提供され、サポート リクエストを迅速に行うことができます。 この機能を有効にするには、[ファイル ログの使用方法](how-to-use-logging.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* [トラブルシューティング](troubleshooting.md)
* [リリース ノート](releasenotes.md)
