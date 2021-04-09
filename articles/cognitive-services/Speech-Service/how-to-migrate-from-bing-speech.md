---
title: Bing Speech から Speech Service に移行する
titleSuffix: Azure Cognitive Services
description: 既存の Bing Speech サブスクリプションから Azure Cognitive Services の Speech Service に移行する方法について説明します。
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: d0ffd786d3fb6bb5f0d70095d947c81caa070518
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499137"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Bing Speech から Speech Service に移行する

アプリケーションを Bing Speech API から Speech Service に移行するにあたっては､この記事を参考にしてください｡

この記事では、Bing Speech API と Speech Service の違いを簡単に説明し、アプリケーションの移行方法を提案します。 Bing Speech API サブスクリプション キーは Speech Service では機能しません。新たに Speech Service サブスクリプションが必要です。

1 つの Speech Service サブスクリプション キーで、次の機能にアクセスできます。 それぞれが個別に測定されるため、課金は使用した機能に対するものだけです。

* [音声テキスト変換](speech-to-text.md)
* [カスタム音声テキスト変換](https://cris.ai)
* [テキスト読み上げ](text-to-speech.md)
* [カスタム テキスト音声読み上げ](./how-to-custom-voice-create-voice.md)
* [音声翻訳](speech-translation.md) ([テキスト翻訳](../translator/translator-info-overview.md)は含まれません)

[Speech SDK](speech-sdk.md) は Bing Speech クライアント ライブラリの機能を代替するものですが､使用している API は異なります｡

## <a name="comparison-of-features"></a>機能の比較

Speech Service と Bing Speech はよく似ていますが、次のような違いがあります。

| 機能 | Bing Speech | Speech サービス | 詳細 |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | Speech Service は、Windows 10、Universal Windows Platform (UWP)、および .NET Standard 2.0 に対応します。 |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | Speech Service は Windows および Linux に対応します。 |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | Speech Service は Android および Speech Devices に対応します。 |
| 連続音声認識 | 10 分 | 無制限 | Speech SDK は、無制限の連続認識をサポートし、タイムアウトまたは切断時には自動的に再接続します。 |
| 部分的または中間結果 | :heavy_check_mark: | :heavy_check_mark: | Speech SDK でサポートされています。 |
| カスタム音声モデル | :heavy_check_mark: | :heavy_check_mark: | Bing Speech には別個の Custom Speech サブスクリプションが必要です。 |
| カスタム音声フォント | :heavy_check_mark: | :heavy_check_mark: | Bing Speech には別個の Custom Voice サブスクリプションが必要です。 |
| 24 KHz の音声 | :heavy_minus_sign: | :heavy_check_mark: |
| 音声意図認識 | 別途 LUIS API 呼び出しが必要 | 統合済み (SDK) | Speech Service では LUIS キーを利用できます。 |
| 簡単な意図認識 | :heavy_minus_sign: | :heavy_check_mark: |
| 長い音声ファイルの一括文字起こし | :heavy_minus_sign: | :heavy_check_mark: |
| 認識モード | エンドポイント URI 経由での手動 | 自動 | Speech Service には認識モードはありません。 |
| エンドポイントの地域性 | グローバル | 地域 | 地域のエンドポイントによって､待機時間が改善されます｡ |
| REST API | :heavy_check_mark: | :heavy_check_mark: | Speech Service REST API は Bing Speech と互換性があります (エンドポイントは異なる)。 REST API はテキスト読み上げ機能と音声テキスト変換機能(限定的) をサポートしています｡ |
| WebSockets プロトコル | :heavy_check_mark: | :heavy_minus_sign: | サービスへの常時接続を必要とする機能に向けた Web ソケット接続が Speech SDK によって抽象化されるため、手動でのサブスクライブはサポートされなくなりました。 |
| Service-to-service API 呼び出し | :heavy_check_mark: | :heavy_minus_sign: | Bing Speech では､C# サービス ライブラリで提供｡ |
| オープンソース SDK | :heavy_check_mark: | :heavy_minus_sign: |

Speech Service では､トランザクション ベースではなく時間ベースの価格モデルが採用されています｡ 詳しくは、[Speech Service の価格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)に関するページをご覧ください。

## <a name="migration-strategies"></a>移行方法

Bing Speech API を使用しているアプリケーションを開発中の場合､あるいはそうしたアプリケーションを運用している場合は､できるかぎり速やかに Speech Service を使用するように更新することをお勧めします｡ 使用できる SDK やサンプル コード､チュートリアルについては､[Speech Service](index.yml) のドキュメントをご覧ください。

Speech Service の [REST API](./overview.md#reference-docs) Bing Speech の API 互換性があります｡ 現在 Bing Speech REST API をご利用の場合は、REST エンドポイントのみを変更し、Speech Service サブスクリプション キーに切り替えるだけで済みます。

ただし、特定のプログラミング言語で Bing Speech クライアント ライブラリを使用している場合、[Speech SDK](speech-sdk.md) に移行するには、API が異なるため、アプリケーションそのものを変更する必要があります。 Speech SDK により、新しい機能を利用できる一方でコードを簡略化できるようになります。 Speech SDK は、さまざまなプログラミング言語で使用できます。 どのプラットフォームの API も似ており､マルチプラットフォーム開発が容易になります｡

Speech Service では、グローバル エンドポイントは提供されません。 アプリケーションのすべてのトラフィックに単一のリージョン エンドポイントを使用しているときにアプリケーションが効率的に機能するかどうかを判断します。 効率的に機能しない場合は､geolocation を使って最も効率的なエンドポイントを探してください｡ 使用するリージョンごとに Speech Service サブスクリプションが必要になります。

Speech SDK を使ってみる

1. [Speech SDK](speech-sdk.md) をダウンロードします｡
1. Speech Service [クイック スタート ガイド](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)と[チュートリアル](how-to-recognize-intents-from-speech-csharp.md)に従って作業します。 また、[コード サンプル](./speech-sdk.md#sample-source-code)を見て、新しい API に関する経験を得ます。
1. Speech Service を使用するようにアプリケーションを更新します。

## <a name="support"></a>サポート

Bing Speech のお客様は[サポート チケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開いて､カスタマー サービスに問い合わせることをお勧めします｡ サポートで[テクニカル サポート プラン](https://azure.microsoft.com/support/plans/)が必要な場合もお問い合わせください。

Speech Service､SDK､および API サポートについては､Speech Service の[サポート ページ](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)をご覧ください｡

## <a name="next-steps"></a>次のステップ

* [Speech Service を無料で試す](overview.md#try-the-speech-service-for-free)
* [音声変換の概要](get-started-speech-to-text.md)
* [テキスト読み上げの概要](get-started-text-to-speech.md)

## <a name="see-also"></a>関連項目

* [Speech Service リリース ノート](releasenotes.md)
* [Speech Service とは](overview.md)
* [Speech Service と Speech SDK のドキュメント](speech-sdk.md#get-the-speech-sdk)
