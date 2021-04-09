---
title: カスタム コマンド アプリをテストする
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンド アプリケーションをテストするためのさまざまな方法について説明します。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c0bd21f55fee4d8487826deae23093ede293c8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95021815"
---
# <a name="test-your-custom-commands-application"></a>カスタム コマンド アプリケーションをテストする

この記事では、カスタム コマンド アプリケーションをテストするためのさまざまな方法について説明します。

## <a name="test-in-the-portal"></a>ポータルでのテスト

ポータルでのテストは、カスタム コマンド アプリケーションが期待どおりに動作するかどうかを確認する最もシンプルかつ迅速な方法です。 アプリが正常にトレーニングされたら、[`Test`] ボタンをクリックしてテストを開始します。

> [!div class="mx-imgBorder"]
> ![ポータルでのテスト](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>Windows 音声アシスタント クライアントを使用してテストする

Windows 音声アシスタント クライアントとは、C# で記述された Windows Presentation Foundation (WPF) アプリケーションです。これを使用すれば、カスタム クライアント アプリケーションを作成する前に、ご利用のボットとのやりとりを簡単にテストできます。

このツールでは、カスタム コマンド アプリケーション ID を受け入れることができます。 これにより、カスタム コマンド サービスでホストされているタスクの完了または指示と制御のシナリオをテストすることができます。

クライアントを設定するには、[Windows 音声アシスタント クライアント](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf)をチェックアウトします。

> [!div class="mx-imgBorder"]
> ![WVAC でのプロファイルの作成](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Speech SDK 対応のクライアント アプリケーションを使用してテストする 
Speech ソフトウェア開発キット (SDK) では Speech サービスのさまざまな機能が公開されています。これにより、音声認識対応アプリケーションを開発できます。 また、これは多くのプログラミング言語と、あらゆるプラットフォームで使用できます。

Speech SDK を使用してユニバーサル Windows プラットフォーム (UWP) クライアント アプリケーションを設定し、それをカスタム コマンド アプリケーションと統合するには、次のようにします。  
- [方法: Speech SDK を使用してクライアント アプリケーションと統合する](./how-to-custom-commands-setup-speech-sdk.md)
- [方法: クライアント アプリケーションにアクティビティを送信する](./how-to-custom-commands-send-activity-to-client.md)
- [方法: Web エンドポイントを設定する](./how-to-custom-commands-setup-web-endpoints.md)

その他のプログラミング言語およびプラットフォームの場合:
- [Speech SDK のプログラミング言語、プラットフォーム、シナリオの容量](./speech-sdk.md)
- [音声アシスタントのサンプル コード](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [GitHub のサンプルを参照](https://aka.ms/speech/cc-samples)