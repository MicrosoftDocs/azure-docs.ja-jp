---
title: Speech Devices SDK - 音声サービス
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK を使ってみましょう。 音声サービスは、さまざまなデバイスやオーディオ ソースと連携します。 Speech Devices SDK は、専用マイク配列開発キットとの組み合わせで事前に調整されたライブラリです。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: 0309329b37436022dc88f8951853c4bb09a2c080
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015275"
---
# <a name="what-is-the-speech-devices-sdk"></a>Speech Devices SDK とは

[Speech Service](overview.md) は、さまざまなデバイスやオーディオ ソースと連携します。 適合するハードウェアやソフトウェアで音声アプリケーションを次のレベルに引き上げることができます。 Speech Devices SDK は、専用マイク配列開発キットとの組み合わせで事前に調整されたライブラリです。

Speech Devices SDK は、次の場合に役立ちます。

- 新しい音声シナリオをすばやくテストする。
- クラウド ベースの Speech サービスをより簡単にデバイスに統合する。
- 顧客向けに優れたユーザー エクスペリエンスを作成する。

Speech Devices SDK は、[Speech SDK](speech-sdk.md) を利用しています。 デバイスのマイク配列と共に高度な音声処理アルゴリズムを使用して、[Speech サービス](overview.md)に音声を送信します。 ノイズの抑制、エコー キャンセレーション、ビームフォーミング、残響除去によって、正確な遠距離[音声認識](speech-to-text.md)が提供されます。

また、Speech Devices SDK を使用して、独自の[カスタマイズされたキーワード](./custom-keyword-basics.md)を備えたアンビエント デバイスを構築することもできます。 カスタム キーワードによって、ブランドに固有のユーザー操作を開始するキューが提供されます。

Speech Devices SDK を使用すると、[音声アシスタント](./voice-assistants.md)、ドライブスルー注文システム、[会話の文字起こし](./conversation-transcription.md)、スマート スピーカーなど、さまざまな音声対応シナリオが可能になります。 ユーザーへのテキストでの応答、既定の音声または[カスタム音声](./how-to-custom-voice-create-voice.md)での応答、検索結果の提供、他の言語への[翻訳](speech-translation.md)などを行うことができます。 皆様の開発の成果を楽しみにしております。

## <a name="get-the-speech-devices-sdk"></a>Speech Devices SDK を取得する

### <a name="android"></a>Android

Android 用の Speech Devices SDK は、[Roobo v1](speech-devices-sdk-roobo-v1.md) と同等のデバイスをサポートしています。使用するには、[Android Speech Devices SDK](https://aka.ms/sdsdk-download-android) の最新バージョンをダウンロードします。


別の Android デバイス (電話やモバイルなど) がある場合は、[Android Speech SDK](speech-sdk.md) から開始します


### <a name="windows"></a>Windows

Windows の場合、サンプル アプリケーションは、クロスプラットフォーム Java アプリケーションとして提供されます。 [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) の最新バージョンをダウンロードします。
アプリケーションは、Speech SDK パッケージと、64 ビット Windows 上の Eclipse Java IDE (v4) で構築されます。 これは、64 ビットの Java 8 のランタイム環境 (JRE) で実行されます。

### <a name="linux"></a>Linux

Linux の場合、サンプル アプリケーションは、クロスプラットフォーム Java アプリケーションとして提供されます。 [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) の最新バージョンをダウンロードします。
アプリケーションは、Speech SDK パッケージと、64 ビット Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 8、CentOS 8) 上の Eclipse Java IDE (v4) によって構築されます。 これは、64 ビットの Java 8 のランタイム環境 (JRE) で実行されます。

今後公開されるデバイス ([Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2)、[Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)、および [GGEC Speaker](https://aka.ms/sdsdk-download-speaker)、[Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32)、[Linux ARM64](https://aka.ms/sdsdk-download-linux-arm64)) をサポートするために、追加のバイナリが提供されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech Device を選択する](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Speech サービスのサブスクリプション キーを無料で取得する](overview.md#try-the-speech-service-for-free)