---
title: Speech コンテナーをインストールする
titleSuffix: Azure Cognitive Services
description: 音声アンブレラ helm チャートの構成オプションについて説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "80874351"
---
### <a name="speech-umbrella-chart"></a>音声 (アンブレラ チャート)

最上位レベル「アンブレラ」チャートの値は、対応するサブチャートの値を上書きします。 そのため、オンプレミスでカスタマイズされたすべての値をここで追加する必要があります。

|パラメーター|説明|Default|
| -- | -- | -- | -- |
| `speechToText.enabled` | **音声テキスト変換**サービスが有効かどうか。 | `true` |
| `speechToText.verification.enabled` | **音声テキスト変換**サービス用の `helm test` 機能が有効かどうか。 | `true` |
| `speechToText.verification.image.registry` | `helm test` が**音声テキスト変換**サービスをテストするために使用する Docker イメージ リポジトリ。 helm は、テストおよびこのレジストリから *test-use* イメージをプルするため、クラスター内に個別のポッドを作成します。 | `docker.io` |
| `speechToText.verification.image.repository` | `helm test` が**音声テキスト変換**サービスをテストするために使用する Docker イメージ リポジトリ。 helm テスト ポッドは、*test-use* イメージをプルするため、このリポジトリを使用します。 | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | **音声テキスト変換**サービスで `helm test` とともに使用される Docker イメージ タグ。 helm テスト ポッドは、*test-use* イメージをプルするため、このタグを使用します。 | `latest` |
| `speechToText.verification.image.pullByHash` | *test-use* Docker イメージをハッシュでプルするかどうか。 `true` の場合、`speechToText.verification.image.hash` を有効なイメージ ハッシュ値とともに追加する必要があります。 | `false` |
| `speechToText.verification.image.arguments` | *test-use* Docker イメージを実行するために使用される引数。 helm テスト ポッドは、`helm test` を実行するときにコンテナーにこれらの引数を渡します。 | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | **テキスト読み上げ**サービスが有効かどうか。 | `true` |
| `textToSpeech.verification.enabled` | **音声テキスト変換**サービス用の `helm test` 機能が有効かどうか。 | `true` |
| `textToSpeech.verification.image.registry` | `helm test` が**音声テキスト変換**サービスをテストするために使用する Docker イメージ リポジトリ。 helm は、テストおよびこのレジストリから *test-use* イメージをプルするため、クラスター内に個別のポッドを作成します。 | `docker.io` |
| `textToSpeech.verification.image.repository` | `helm test` が**音声テキスト変換**サービスをテストするために使用する Docker イメージ リポジトリ。 helm テスト ポッドは、*test-use* イメージをプルするため、このリポジトリを使用します。 | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | **音声テキスト変換**サービスで `helm test` とともに使用される Docker イメージ タグ。 helm テスト ポッドは、*test-use* イメージをプルするため、このタグを使用します。 | `latest` |
| `textToSpeech.verification.image.pullByHash` | *test-use* Docker イメージをハッシュでプルするかどうか。 `true` の場合、`textToSpeech.verification.image.hash` を有効なイメージ ハッシュ値とともに追加する必要があります。 | `false` |
| `textToSpeech.verification.image.arguments` | *test-use* Docker イメージを実行する引数。 helm テスト ポッドは、`helm test` を実行するときにコンテナーにこれらの引数を渡します。 | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |