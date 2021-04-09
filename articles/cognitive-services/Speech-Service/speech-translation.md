---
title: 音声翻訳の概要 - Speech Service
titleSuffix: Azure Cognitive Services
description: 音声翻訳を使用すると、音声のエンド ツー エンドでリアルタイムの多言語翻訳機能を、アプリケーション、ツール、デバイスに追加することができます。 同じ API を、音声間の翻訳と、音声テキスト変換の両方に使用できます。 この記事は、音声翻訳サービスの利点と機能の概要になっています。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: 音声翻訳
ms.openlocfilehash: 94ddd06068513261b5b73b313877e273c7251d62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954962"
---
# <a name="what-is-speech-translation"></a>音声翻訳とは何か

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

この概要では、[多言語のオーディオ ストリームをリアルタイムで音声間の](language-support.md#speech-translation)翻訳または音声テキスト変換できる音声翻訳サービスの利点と機能について説明します。 Speech SDK を使用すると、アプリケーション、ツール、およびデバイスから、提供されたオーディオのソース トランスクリプションや翻訳出力にアクセスすることができます。 音声の検出中には中間トランスクリプションと翻訳結果が返され、最終的な結果は、合成された音声に変換することができます。

## <a name="core-features"></a>コア機能

* 認識結果を使用した音声テキスト翻訳。
* 音声間翻訳。
* 複数のターゲット言語への翻訳をサポート。
* 中間認識と翻訳結果。

## <a name="get-started"></a>はじめに 

音声翻訳の使用を開始するには、[クイックスタート](get-started-speech-translation.md)を参照してください。 音声翻訳サービスは、[Speech SDK](speech-sdk.md) と [Speech CLI](spx-overview.md) を介して利用できます。

## <a name="sample-code"></a>サンプル コード

Speech SDK のサンプル コードは、GitHub 上で入手できます。 これらのサンプルでは、ファイルやストリームからの音声の読み取り、連続的および単発の認識/翻訳、カスタム モデルの使用など、一般的なシナリオについて説明されています。

* [音声テキスト変換と翻訳のサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>移行ガイド

アプリケーション、ツール、または製品で [Translator Speech API](./how-to-migrate-from-translator-speech-api.md) をご使用の方に向けて、Speech Service への移行に役立つガイドを作成しました。

* [Translator Speech API から Speech Service に移行する](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](./speech-sdk.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 音声テキスト変換](rest-speech-to-text.md)
* [REST API: テキスト読み上げ](rest-text-to-speech.md)
* [REST API: 一括文字起こしとカスタマイズ](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>次のステップ

* 音声翻訳の[クイックスタート](get-started-speech-translation.md)を完了する
* [Speech サービスのサブスクリプション キーを無料で取得する](overview.md#try-the-speech-service-for-free)
* [Speech SDK を取得する](speech-sdk.md)