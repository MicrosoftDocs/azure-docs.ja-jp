---
title: Microsoft Bing Speech Service | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Microsoft Speech API を使用して、ユーザーとのリアルタイムでの対話など、音声駆動のアクションをアプリに追加します。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff9de2557583eecd5ddb2acd97b445a93abc5fb6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966649"
---
# <a name="what-is-bing-speech"></a>Bing Speech とは

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

クラウドベースの Microsoft Bing Speech API は、音声コマンド制御、自然な言葉での会話を使用したユーザー ダイアログ、文字起こしやディクテーションなど、強力な音声認識機能をアプリケーションで実現する簡単な方法を開発者に提供します。 Microsoft Speech API は、*Speech to Text* と *Text to Speech* の両方の変換をサポートします。

- **Speech to Text** API は、人間の音声を、アプリケーションを制御する入力またはコマンドとして使用できるテキストに変換します。
- **Text to Speech** API は、テキストを、アプリケーションのユーザーが再生できるオーディオ ストリームに変換します。

## <a name="speech-to-text-speech-recognition"></a>Speech to Text (音声認識)

Microsoft Speech Recognition API は、オーディオ ストリームを、アプリケーションでユーザーに表示できるテキストまたはコマンド入力として操作できるテキストに "*書き起こします*"。 開発者は、音声認識機能をアプリに追加するための手段として 2 つの方法を使用できます: REST API **または** Websocket ベースのクライアント ライブラリ。

- [REST API](GetStarted/GetStartedREST.md): アプリからサービスへの HTTP 呼び出しを使用して音声認識を行います。
- [クライアント ライブラリ](GetStarted/GetStartedClientLibraries.md):高度な機能を使用する場合は、Microsoft Speech クライアント ライブラリをダウンロードして、それらをアプリにリンクできます。  クライアント ライブラリは、各種言語 (C#、Java、JavaScript、ObjectiveC) を使用して、さまざまなプラットフォーム (Windows、Android、iOS) で使用できます。 REST API とは異なり、クライアント ライブラリは、Websocket ベースのプロトコルを利用します。

| ユース ケース | [REST API](GetStarted/GetStartedREST.md) | [クライアント ライブラリ](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| 短い発話音声を変換する。たとえば、中間結果を使用しないコマンド (15 秒以下の音声) など | はい | はい |
| 長い音声 (15 秒以上) を変換する | いいえ | はい |
| 中間結果を使って音声をストリーミングする | いいえ | はい |
| LUIS を使用して、音声から変換されたテキストの意味を解釈する | いいえ | はい |

いずれの手法 (REST API とクライアント ライブラリ) を使用する場合でも、Microsoft Speech Service は次をサポートします。

- Microsoft の高度な音声認識テクノロジ。Cortana、Office Dictation、Office Translator、およびその他の Microsoft 製品で使用されているものと同じです。
- リアルタイムの連続的認識。 Speech Recognition API は、リアルタイムでオーディオをテキストに起こし、途中までに認識されている中間結果の言葉を受け取ります。 Speech Service は、発話の終了の検出もサポートします。 また、大文字/小文字の設定、句読点、不適切な表現のマスキング、テキストの正規化など、追加の書式設定機能を選択することができます。
- *対話*、*会話*、および*ディクテーション*のシナリオでの音声認識結果の最適化をサポート。 カスタマイズされた言語モデルと音響モデルを必要とするユーザー シナリオにおいて、[Custom Speech Service](../custom-speech-service/cognitive-services-custom-speech-home.md) を使用すると、お使いのアプリケーションやそのユーザー向けにカスタマイズされた音声モデルを作成できます。
- 複数の方言で話される多くの言語をサポート。 各認識モードでサポートされている言語の完全な一覧については、[認識言語](api-reference-rest/supportedlanguages.md)に関するページをご覧ください。
- Language Understanding との統合。 入力オーディオをテキストに変換する以外に、*Speech to Text* はアプリケーションにテキストの意味を理解するための追加機能を提供します。 [Language Understanding Intelligent Service (LUIS)](../LUIS/what-is-luis.md) を使用して認識されたテキストから意図やエンティティを抽出します。

### <a name="next-steps"></a>次の手順

- [REST API](GetStarted/GetStartedREST.md) または[クライアント ライブラリ](GetStarted/GetStartedClientLibraries.md)を使用して、マイクロソフトの音声認識サービスの使用を開始します。
- 普段使用しているプログラミング言語の[サンプル アプリケーション](samples.md)を確認します。
- リファレンス セクションに移動して、[Microsoft Speech プロトコル](API-Reference-REST/websocketprotocol.md)の詳細と API のリファレンスを探します。

## <a name="text-to-speech-speech-synthesis"></a>Text to Speech (音声合成)

*Text to Speech* API は、REST を使用して構造化されたテキストをオーディオ ストリームに変換します。 この API は、テキストをさまざまな音声や言語にすばやく変換します。 さらに、SSML タグを使用して、発音、音量、ピッチなどの音声の特徴を 変更できます。

### <a name="next-steps"></a>次の手順

- Microsoft テキスト読み上げサービスの使用を開始する: [Text to Speech API リファレンス](api-reference-rest/bingvoiceoutput.md)。 Text to Speech でサポートされている言語と音声の全一覧については、[サポートされているロケールと音声フォント](api-reference-rest/bingvoiceoutput.md#SupLocales)に関するページをご覧ください。
