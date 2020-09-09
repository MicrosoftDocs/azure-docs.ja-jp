---
title: Cognitive Services コンテナー イメージ タグ
titleSuffix: Azure Cognitive Services
description: すべての Cognitive Service コンテナー イメージ タグの包括的一覧
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e0df3de5eadfd2cc5c00c52da5c4942b42a68b2b
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722570"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure Cognitive Services コンテナー イメージ タグ

Azure Cognitive Services からはさまざまなコンテナー イメージが提供されます。 コンテナー レジストリとそれに対応するリポジトリは、コンテナー イメージによって異なります。 各コンテナー イメージ名にからは複数のタグが与えられます。 コンテナー イメージ タグは、コンテナー イメージのバージョンを管理するためのメカニズムです。 この記事は、Cognitive Services コンテナー イメージと利用できるタグをすべてリストアップする包括的リファレンスとして使用されることを意図して作成されています。

> [!TIP]
> [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) を使用する場合、コンテナー レジストリ、リポジトリ、コンテナー イメージ名、それらに対応するタグの大文字/小文字の違いに注意してください。これらでは**大文字と小文字が区別されます**。

## <a name="anomaly-detector"></a>Anomaly Detector

[Anomaly Detector][ad-containers] コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートで見つけることができます。 `azure-cognitive-services` リポジトリ内にあり、`anomaly-detector` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |

## <a name="computer-vision"></a>Computer Vision

[Computer Vision][cv-containers] Read OCR コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-read` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-read` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest ( (2.0.013250001-amd64-preview)` | • コンテナーのメモリ使用量をさらに減らします。 |
|                                          | • マルチポッド セットアップには外部キャッシュが必要です。 たとえば、キャッシュ用に Redis を設定します。 |
|                                          | • Redis cache がセットアップされていて ResultExpirationPeriod=0 のとき、結果が失われる問題を解決します。  |
|                                          | • 要求本文サイズの 26MB 制限を削除します。 コンテナーでは 26MB を超えるファイルを受け取れるようになります。  |
|                                          | • タイム スタンプとビルド バージョンをコンソール ログに追加します。  |
| `1.1.013050001-amd64-preview`            | * 認識結果を消去するタイミングを指定する目的で ReadEngineConfig:ResultExpirationPeriod コンテナー初期化構成を追加しました。 |
|                                          | 設定は時間単位であり、既定値は 48 時間です。   |
|                                          |   この設定により、結果格納時のメモリ使用量を減らすことができます。特に、コンテナーのメモリ内ストレージが使用されるときに効果があります。  |
|                                          |    * 例 1. ReadEngineConfig:ResultExpirationPeriod=1、プロセスから 1 時間後に認識結果が消去されます。   |
|                                          |    * 例 2. ReadEngineConfig:ResultExpirationPeriod=0、結果取得後に認識結果が消去されます。  |
|                                          | 無効なイメージ形式がシステムに渡されたときの 500 内部サーバー エラーを解決しました。 400 エラーが返されるようになりました。   |
|                                          | `{`  |
|                                          | `"error": {`  |
|                                          |      `"code": "InvalidImageSize",`  |
|                                          |      `"message": "Image must be between 1024 and 209715200 bytes."`  |
|                                          |          `}`  |
|                                          | `}`  |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Face

[Face][fa-containers] コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-face` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-face` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Form Recognizer

[Form Recognizer][fr-containers] コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-form-recognizer` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

[LUIS][lu-containers] コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services` リポジトリ内にあり、`luis` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/luis` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>カスタム音声変換

[カスタム音声変換][sp-cstt]コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-custom-speech-to-text` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ            | Notes |
|-----------------------|:------|
| `latest`              |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>カスタム テキスト読み上げ

[カスタム テキスト読み上げ][sp-ctts]コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-custom-text-to-speech` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ            | Notes |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>音声テキスト変換

[音声テキスト変換][sp-stt]コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-speech-to-text` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ                  | Notes                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | `en-US` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-ar-ae-preview` | `ar-AE` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-ar-eg-preview` | `ar-EG` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-ar-kw-preview` | `ar-KW` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-ar-qa-preview` | `ar-QA` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-ar-sa-preview` | `ar-SA` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-ca-es-preview` | `ca-ES` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-da-dk-preview` | `da-DK` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-de-de-preview` | `de-DE` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-en-au-preview` | `en-AU` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-en-ca-preview` | `en-CA` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-en-gb-preview` | `en-GB` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-en-in-preview` | `en-IN` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-en-nz-preview` | `en-NZ` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-es-es-preview` | `es-ES` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-es-mx-preview` | `es-MX` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-fi-fi-preview` | `fi-FI` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-fr-ca-preview` | `fr-CA` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-fr-fr-preview` | `fr-FR` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-gu-in-preview` | `gu-IN` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-hi-in-preview` | `hi-IN` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-it-it-preview` | `it-IT` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-ko-kr-preview` | `ko-KR` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-mr-in-preview` | `mr-IN` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-nb-no-preview` | `nb-NO` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-nl-nl-preview` | `nl-NL` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-pl-pl-preview` | `pl-PL` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-pt-br-preview` | `pt-BR` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-pt-pt-preview` | `pt-PT` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-ru-ru-preview` | `ru-RU` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-sv-se-preview` | `sv-SE` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-ta-in-preview` | `ta-IN` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-te-in-preview` | `te-IN` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-th-th-preview` | `th-TH` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-tr-tr-preview` | `tr-TR` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-zh-cn-preview` | `zh-CN` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-zh-hk-preview` | `zh-HK` ロケールのコンテナー イメージ。 |
| `2.2.0-amd64-zh-tw-preview` | `zh-TW` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-ar-ae-preview` | `ar-AE` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-ar-eg-preview` | `ar-EG` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-ar-kw-preview` | `ar-KW` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-ar-qa-preview` | `ar-QA` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-ar-sa-preview` | `ar-SA` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-ca-es-preview` | `ca-ES` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-da-dk-preview` | `da-DK` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-de-de-preview` | `de-DE` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-en-au-preview` | `en-AU` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-en-ca-preview` | `en-CA` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-en-gb-preview` | `en-GB` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-en-in-preview` | `en-IN` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-en-nz-preview` | `en-NZ` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-es-es-preview` | `es-ES` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-es-mx-preview` | `es-MX` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-fi-fi-preview` | `fi-FI` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-fr-ca-preview` | `fr-CA` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-fr-fr-preview` | `fr-FR` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-gu-in-preview` | `gu-IN` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-hi-in-preview` | `hi-IN` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-it-it-preview` | `it-IT` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-ko-kr-preview` | `ko-KR` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-mr-in-preview` | `mr-IN` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-nb-no-preview` | `nb-NO` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-nl-nl-preview` | `nl-NL` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-pl-pl-preview` | `pl-PL` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-pt-br-preview` | `pt-BR` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-pt-pt-preview` | `pt-PT` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-ru-ru-preview` | `ru-RU` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-sv-se-preview` | `sv-SE` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-ta-in-preview` | `ta-IN` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-te-in-preview` | `te-IN` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-th-th-preview` | `th-TH` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-tr-tr-preview` | `tr-TR` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-zh-cn-preview` | `zh-CN` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-zh-hk-preview` | `zh-HK` ロケールのコンテナー イメージ。 |
| `2.1.1-amd64-zh-tw-preview` | `zh-TW` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-ar-ae-preview` | `ar-AE` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-ar-eg-preview` | `ar-EG` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-ar-kw-preview` | `ar-KW` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-ar-qa-preview` | `ar-QA` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-ar-sa-preview` | `ar-SA` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-ca-es-preview` | `ca-ES` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-da-dk-preview` | `da-DK` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-de-de-preview` | `de-DE` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-en-au-preview` | `en-AU` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-en-ca-preview` | `en-CA` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-en-gb-preview` | `en-GB` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-en-in-preview` | `en-IN` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-en-nz-preview` | `en-NZ` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-es-es-preview` | `es-ES` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-es-mx-preview` | `es-MX` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-fi-fi-preview` | `fi-FI` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-fr-ca-preview` | `fr-CA` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-fr-fr-preview` | `fr-FR` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-gu-in-preview` | `gu-IN` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-hi-in-preview` | `hi-IN` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-it-it-preview` | `it-IT` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-ko-kr-preview` | `ko-KR` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-mr-in-preview` | `mr-IN` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-nb-no-preview` | `nb-NO` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-nl-nl-preview` | `nl-NL` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-pl-pl-preview` | `pl-PL` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-pt-br-preview` | `pt-BR` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-pt-pt-preview` | `pt-PT` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-ru-ru-preview` | `ru-RU` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-sv-se-preview` | `sv-SE` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-ta-in-preview` | `ta-IN` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-te-in-preview` | `te-IN` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-th-th-preview` | `th-TH` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-tr-tr-preview` | `tr-TR` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-zh-cn-preview` | `zh-CN` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-zh-hk-preview` | `zh-HK` ロケールのコンテナー イメージ。 |
| `2.1.0-amd64-zh-tw-preview` | `zh-TW` ロケールのコンテナー イメージ。 |
| `2.0.3-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-ar-ae-preview` | `ar-AE` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-ar-eg-preview` | `ar-EG` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-ar-kw-preview` | `ar-KW` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-ar-qa-preview` | `ar-QA` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-ar-sa-preview` | `ar-SA` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-ca-es-preview` | `ca-ES` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-da-dk-preview` | `da-DK` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-de-de-preview` | `de-DE` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-en-au-preview` | `en-AU` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-en-ca-preview` | `en-CA` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-en-gb-preview` | `en-GB` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-en-in-preview` | `en-IN` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-en-nz-preview` | `en-NZ` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-es-es-preview` | `es-ES` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-es-mx-preview` | `es-MX` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-fi-fi-preview` | `fi-FI` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-fr-ca-preview` | `fr-CA` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-fr-fr-preview` | `fr-FR` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-gu-in-preview` | `gu-IN` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-hi-in-preview` | `hi-IN` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-it-it-preview` | `it-IT` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-ko-kr-preview` | `ko-KR` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-mr-in-preview` | `mr-IN` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-nb-no-preview` | `nb-NO` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-nl-nl-preview` | `nl-NL` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-pl-pl-preview` | `pl-PL` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-pt-br-preview` | `pt-BR` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-pt-pt-preview` | `pt-PT` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-ru-ru-preview` | `ru-RU` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-sv-se-preview` | `sv-SE` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-ta-in-preview` | `ta-IN` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-te-in-preview` | `te-IN` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-th-th-preview` | `th-TH` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-tr-tr-preview` | `tr-TR` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-zh-cn-preview` | `zh-CN` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-zh-hk-preview` | `zh-HK` ロケールのコンテナー イメージ。 |
| `2.0.2-amd64-zh-tw-preview` | `zh-TW` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-ar-ae-preview` | `ar-AE` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-ar-eg-preview` | `ar-EG` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-ar-kw-preview` | `ar-KW` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-ar-qa-preview` | `ar-QA` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-ar-sa-preview` | `ar-SA` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-ca-es-preview` | `ca-ES` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-da-dk-preview` | `da-DK` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-de-de-preview` | `de-DE` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-en-au-preview` | `en-AU` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-en-ca-preview` | `en-CA` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-en-gb-preview` | `en-GB` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-en-in-preview` | `en-IN` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-en-nz-preview` | `en-NZ` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-es-es-preview` | `es-ES` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-es-mx-preview` | `es-MX` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-fi-fi-preview` | `fi-FI` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-fr-ca-preview` | `fr-CA` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-fr-fr-preview` | `fr-FR` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-gu-in-preview` | `gu-IN` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-hi-in-preview` | `hi-IN` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-it-it-preview` | `it-IT` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-ko-kr-preview` | `ko-KR` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-mr-in-preview` | `mr-IN` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-nb-no-preview` | `nb-NO` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-nl-nl-preview` | `nl-NL` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-pl-pl-preview` | `pl-PL` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-pt-br-preview` | `pt-BR` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-pt-pt-preview` | `pt-PT` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-ru-ru-preview` | `ru-RU` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-sv-se-preview` | `sv-SE` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-ta-in-preview` | `ta-IN` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-te-in-preview` | `te-IN` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-th-th-preview` | `th-TH` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-tr-tr-preview` | `tr-TR` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-zh-cn-preview` | `zh-CN` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-zh-hk-preview` | `zh-HK` ロケールのコンテナー イメージ。 |
| `2.0.1-amd64-zh-tw-preview` | `zh-TW` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-ar-eg-preview` | `ar-EG` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-ca-es-preview` | `ca-ES` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-da-dk-preview` | `da-DK` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-de-de-preview` | `de-DE` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-en-au-preview` | `en-AU` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-en-ca-preview` | `en-CA` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-en-gb-preview` | `en-GB` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-en-in-preview` | `en-IN` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-en-nz-preview` | `en-NZ` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-es-es-preview` | `es-ES` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-es-mx-preview` | `es-MX` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-fi-fi-preview` | `fi-FI` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-fr-ca-preview` | `fr-CA` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-fr-fr-preview` | `fr-FR` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-hi-in-preview` | `hi-IN` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-it-it-preview` | `it-IT` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-ko-kr-preview` | `ko-KR` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-nb-no-preview` | `nb-NO` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-nl-nl-preview` | `nl-NL` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-pl-pl-preview` | `pl-PL` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-pt-br-preview` | `pt-BR` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-pt-pt-preview` | `pt-PT` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-ru-ru-preview` | `ru-RU` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-sv-se-preview` | `sv-SE` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-th-th-preview` | `th-TH` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-tr-tr-preview` | `tr-TR` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-zh-cn-preview` | `zh-CN` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-zh-hk-preview` | `zh-HK` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-zh-tw-preview` | `zh-TW` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-de-de-preview` | `de-DE` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-en-au-preview` | `en-AU` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-en-ca-preview` | `en-CA` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-en-gb-preview` | `en-GB` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-en-in-preview` | `en-IN` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-es-es-preview` | `es-ES` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-es-mx-preview` | `es-MX` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-fr-ca-preview` | `fr-CA` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-fr-fr-preview` | `fr-FR` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-it-it-preview` | `it-IT` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-pt-br-preview` | `pt-BR` ロケールのコンテナー イメージ。 |
| `1.2.0-amd64-zh-cn-preview` | `zh-CN` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-de-de-preview` | `de-DE` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-en-au-preview` | `en-AU` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-en-ca-preview` | `en-CA` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-en-gb-preview` | `en-GB` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-en-in-preview` | `en-IN` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-es-es-preview` | `es-ES` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-es-mx-preview` | `es-MX` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-fr-ca-preview` | `fr-CA` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-fr-fr-preview` | `fr-FR` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-it-it-preview` | `it-IT` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-pt-br-preview` | `pt-BR` ロケールのコンテナー イメージ。 |
| `1.1.3-amd64-zh-cn-preview` | `zh-CN` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-de-de-preview` | `de-DE` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-en-au-preview` | `en-AU` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-en-ca-preview` | `en-CA` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-en-gb-preview` | `en-GB` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-en-in-preview` | `en-IN` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-es-es-preview` | `es-ES` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-es-mx-preview` | `es-MX` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-fr-ca-preview` | `fr-CA` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-fr-fr-preview` | `fr-FR` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-it-it-preview` | `it-IT` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-pt-br-preview` | `pt-BR` ロケールのコンテナー イメージ。 |
| `1.1.2-amd64-zh-cn-preview` | `zh-CN` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-de-de-preview` | `de-DE` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-en-au-preview` | `en-AU` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-en-ca-preview` | `en-CA` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-en-gb-preview` | `en-GB` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-en-in-preview` | `en-IN` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-es-es-preview` | `es-ES` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-es-mx-preview` | `es-MX` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-fr-ca-preview` | `fr-CA` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-fr-fr-preview` | `fr-FR` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-it-it-preview` | `it-IT` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-pt-br-preview` | `pt-BR` ロケールのコンテナー イメージ。 |
| `1.1.1-amd64-zh-cn-preview` | `zh-CN` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-de-de-preview` | `de-DE` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-en-au-preview` | `en-AU` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-en-ca-preview` | `en-CA` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-en-gb-preview` | `en-GB` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-en-in-preview` | `en-IN` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-es-es-preview` | `es-ES` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-es-mx-preview` | `es-MX` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-fr-ca-preview` | `fr-CA` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-fr-fr-preview` | `fr-FR` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-it-it-preview` | `it-IT` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-pt-br-preview` | `pt-BR` ロケールのコンテナー イメージ。 |
| `1.1.0-amd64-zh-cn-preview` | `zh-CN` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-de-de-preview` | `de-DE` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-en-au-preview` | `en-AU` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-en-ca-preview` | `en-CA` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-en-gb-preview` | `en-GB` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-en-in-preview` | `en-IN` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-en-us-preview` | `en-US` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-es-es-preview` | `es-ES` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-es-mx-preview` | `es-MX` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-fr-ca-preview` | `fr-CA` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-fr-fr-preview` | `fr-FR` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-it-it-preview` | `it-IT` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-ja-jp-preview` | `ja-JP` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-pt-br-preview` | `pt-BR` ロケールのコンテナー イメージ。 |
| `1.0.0-amd64-zh-cn-preview` | `zh-CN` ロケールのコンテナー イメージ。 |

## <a name="text-to-speech"></a>テキスト読み上げ

[テキスト読み上げ][sp-tts]コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-text-to-speech` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ                                  | Notes                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | `en-US` ロケールと `en-US-JessaRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | `ar-EG` ロケールと `ar-EG-Hoda` 音声のコンテナー イメージ。            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | `ar-SA` ロケールと `ar-SA-Naayf` 音声のコンテナー イメージ。           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | `bg-BG` ロケールと `bg-BG-Ivan` 音声のコンテナー イメージ。            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | `ca-ES` ロケールと `ca-ES-HerenaRUS` 音声のコンテナー イメージ。       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | `cs-CZ` ロケールと `cs-CZ-Jakub` 音声のコンテナー イメージ。           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | `da-DK` ロケールと `da-DK-HelleRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-de-at-michael-preview`         | `de-AT` ロケールと `de-AT-Michael` 音声のコンテナー イメージ。         |
| `1.3.0-amd64-de-ch-karsten-preview`         | `de-CH` ロケールと `de-CH-Karsten` 音声のコンテナー イメージ。         |
| `1.3.0-amd64-de-de-hedda-preview`           | `de-DE` ロケールと `de-DE-Hedda` 音声のコンテナー イメージ。           |
| `1.3.0-amd64-de-de-heddarus-preview`        | `de-DE` ロケールと `de-DE-Hedda` 音声のコンテナー イメージ。           |
| `1.3.0-amd64-de-de-heddarus-preview`        | `de-DE` ロケールと `de-DE-HeddaRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | `de-DE` ロケールと `de-DE-Stefan-Apollo` 音声のコンテナー イメージ。   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | `el-GR` ロケールと `el-GR-Stefanos` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-en-au-catherine-preview`       | `en-AU` ロケールと `en-AU-Catherine` 音声のコンテナー イメージ。       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | `en-AU` ロケールと `en-AU-HayleyRUS` 音声のコンテナー イメージ。       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | `en-CA` ロケールと `en-CA-HeatherRUS` 音声のコンテナー イメージ。      |
| `1.3.0-amd64-en-ca-linda-preview`           | `en-CA` ロケールと `en-CA-Linda` 音声のコンテナー イメージ。           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | `en-GB` ロケールと `en-GB-George-Apollo` 音声のコンテナー イメージ。   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | `en-GB` ロケールと `en-GB-HazelRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | `en-GB` ロケールと `en-GB-Susan-Apollo` 音声のコンテナー イメージ。    |
| `1.3.0-amd64-en-ie-sean-preview`            | `en-IE` ロケールと `en-IE-Sean` 音声のコンテナー イメージ。            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | `en-IN` ロケールと `en-IN-Heera-Apollo` 音声のコンテナー イメージ。    |
| `1.3.0-amd64-en-in-priyarus-preview`        | `en-IN` ロケールと `en-IN-PriyaRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | `en-IN` ロケールと `en-IN-Ravi-Apollo` 音声のコンテナー イメージ。     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | `en-US` ロケールと `en-US-BenjaminRUS` 音声のコンテナー イメージ。     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | `en-US` ロケールと `en-US-Guy24kRUS` 音声のコンテナー イメージ。       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | `en-US` ロケールと `en-US-Jessa24kRUS` 音声のコンテナー イメージ。     |
| `1.3.0-amd64-en-us-jessarus-preview`        | `en-US` ロケールと `en-US-JessaRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-en-us-zirarus-preview`         | `en-US` ロケールと `en-US-ZiraRUS` 音声のコンテナー イメージ。         |
| `1.3.0-amd64-es-es-helenarus-preview`       | `es-ES` ロケールと `es-ES-HelenaRUS` 音声のコンテナー イメージ。       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | `es-ES` ロケールと `es-ES-Laura-Apollo` 音声のコンテナー イメージ。    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | `es-ES` ロケールと `es-ES-Pablo-Apollo` 音声のコンテナー イメージ。    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | `es-MX` ロケールと `es-MX-HildaRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | `es-MX` ロケールと `es-MX-Raul-Apollo` 音声のコンテナー イメージ。     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | `fi-FI` ロケールと `fi-FI-HeidiRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | `fr-CA` ロケールと `fr-CA-Caroline` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | `fr-CA` ロケールと `fr-CA-HarmonieRUS` 音声のコンテナー イメージ。     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | `fr-CH` ロケールと `fr-CH-Guillaume` 音声のコンテナー イメージ。       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | `fr-FR` ロケールと `fr-FR-HortenseRUS` 音声のコンテナー イメージ。     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | `fr-FR` ロケールと `fr-FR-Julie-Apollo` 音声のコンテナー イメージ。    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | `fr-FR` ロケールと `fr-FR-Paul-Apollo` 音声のコンテナー イメージ。     |
| `1.3.0-amd64-he-il-asaf-preview`            | `he-IL` ロケールと `he-IL-Asaf` 音声のコンテナー イメージ。            |
| `1.3.0-amd64-hi-in-hemant-preview`          | `hi-IN` ロケールと `hi-IN-Hemant` 音声のコンテナー イメージ。          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | `hi-IN` ロケールと `hi-IN-Kalpana-Apollo` 音声のコンテナー イメージ。  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | `hi-IN` ロケールと `hi-IN-Kalpana` 音声のコンテナー イメージ。         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | `hi-IN` ロケールと `hi-IN-Kalpana` 音声のコンテナー イメージ。         |
| `1.3.0-amd64-hr-hr-matej-preview`           | `hr-HR` ロケールと `hr-HR-Matej` 音声のコンテナー イメージ。           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | `hu-HU` ロケールと `hu-HU-Szabolcs` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-id-id-andika-preview`          | `id-ID` ロケールと `id-ID-Andika` 音声のコンテナー イメージ。          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | `it-IT` ロケールと `it-IT-Cosimo-Apollo` 音声のコンテナー イメージ。   |
| `1.3.0-amd64-it-it-luciarus-preview`        | `it-IT` ロケールと `it-IT-LuciaRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | `ja-JP` ロケールと `ja-JP-Ayumi-Apollo` 音声のコンテナー イメージ。    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | `ja-JP` ロケールと `ja-JP-HarukaRUS` 音声のコンテナー イメージ。       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | `ja-JP` ロケールと `ja-JP-Ichiro-Apollo` 音声のコンテナー イメージ。   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | `ko-KR` ロケールと `ko-KR-HeamiRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | `ms-MY` ロケールと `ms-MY-Rizwan` 音声のコンテナー イメージ。          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | `nb-NO` ロケールと `nb-NO-HuldaRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | `nl-NL` ロケールと `nl-NL-HannaRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | `pl-PL` ロケールと `pl-PL-PaulinaRUS` 音声のコンテナー イメージ。      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | `pt-BR` ロケールと `pt-BR-Daniel-Apollo` 音声のコンテナー イメージ。   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | `pt-BR` ロケールと `pt-BR-HeloisaRUS` 音声のコンテナー イメージ。      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | `pt-PT` ロケールと `pt-PT-HeliaRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | `ro-RO` ロケールと `ro-RO-Andrei` 音声のコンテナー イメージ。          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | `ru-RU` ロケールと `ru-RU-EkaterinaRUS` 音声のコンテナー イメージ。    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | `ru-RU` ロケールと `ru-RU-Irina-Apollo` 音声のコンテナー イメージ。    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | `ru-RU` ロケールと `ru-RU-Pavel-Apollo` 音声のコンテナー イメージ。    |
| `1.3.0-amd64-sk-sk-filip-preview`           | `sk-SK` ロケールと `sk-SK-Filip` 音声のコンテナー イメージ。           |
| `1.3.0-amd64-sl-si-lado-preview`            | `sl-SI` ロケールと `sl-SI-Lado` 音声のコンテナー イメージ。            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | `sv-SE` ロケールと `sv-SE-HedvigRUS` 音声のコンテナー イメージ。       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | `ta-IN` ロケールと `ta-IN-Valluvar` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-te-in-chitra-preview`          | `te-IN` ロケールと `te-IN-Chitra` 音声のコンテナー イメージ。          |
| `1.3.0-amd64-th-th-pattara-preview`         | `th-TH` ロケールと `th-TH-Pattara` 音声のコンテナー イメージ。         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | `tr-TR` ロケールと `tr-TR-SedaRUS` 音声のコンテナー イメージ。         |
| `1.3.0-amd64-vi-vn-an-preview`              | `vi-VN` ロケールと `vi-VN-An` 音声のコンテナー イメージ。              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | `zh-CN` ロケールと `zh-CN-HuihuiRUS` 音声のコンテナー イメージ。       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | `zh-CN` ロケールと `zh-CN-Kangkang-Apollo` 音声のコンテナー イメージ。 |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | `zh-CN` ロケールと `zh-CN-Yaoyao-Apollo` 音声のコンテナー イメージ。   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | `zh-HK` ロケールと `zh-HK-Danny-Apollo` 音声のコンテナー イメージ。    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | `zh-HK` ロケールと `zh-HK-Tracy-Apollo` 音声のコンテナー イメージ。    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | `zh-HK` ロケールと `zh-HK-TracyRUS` 音声のコンテナー イメージ。        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | `zh-TW` ロケールと `zh-TW-HanHanRUS` 音声のコンテナー イメージ。       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | `zh-TW` ロケールと `zh-TW-Yating-Apollo` 音声のコンテナー イメージ。   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | `zh-TW` ロケールと `zh-TW-Zhiwei-Apollo` 音声のコンテナー イメージ。   |
| `1.2.0-amd64-de-de-heddarus-preview`        | `de-DE` ロケールと `de-DE-Hedda` 音声のコンテナー イメージ。           |
| `1.2.0-amd64-de-de-heddarus-preview`        | `de-DE` ロケールと `de-DE-HeddaRUS` 音声のコンテナー イメージ。        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | `de-DE` ロケールと `de-DE-Stefan-Apollo` 音声のコンテナー イメージ。   |
| `1.2.0-amd64-en-au-catherine-preview`       | `en-AU` ロケールと `en-AU-Catherine` 音声のコンテナー イメージ。       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | `en-AU` ロケールと `en-AU-HayleyRUS` 音声のコンテナー イメージ。       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | `en-GB` ロケールと `en-GB-George-Apollo` 音声のコンテナー イメージ。   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | `en-GB` ロケールと `en-GB-HazelRUS` 音声のコンテナー イメージ。        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | `en-GB` ロケールと `en-GB-Susan-Apollo` 音声のコンテナー イメージ。    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | `en-IN` ロケールと `en-IN-Heera-Apollo` 音声のコンテナー イメージ。    |
| `1.2.0-amd64-en-in-priyarus-preview`        | `en-IN` ロケールと `en-IN-PriyaRUS` 音声のコンテナー イメージ。        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | `en-IN` ロケールと `en-IN-Ravi-Apollo` 音声のコンテナー イメージ。     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | `en-US` ロケールと `en-US-BenjaminRUS` 音声のコンテナー イメージ。     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | `en-US` ロケールと `en-US-Guy24kRUS` 音声のコンテナー イメージ。       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | `en-US` ロケールと `en-US-Jessa24kRUS` 音声のコンテナー イメージ。     |
| `1.2.0-amd64-en-us-jessarus-preview`        | `en-US` ロケールと `en-US-JessaRUS` 音声のコンテナー イメージ。        |
| `1.2.0-amd64-en-us-zirarus-preview`         | `en-US` ロケールと `en-US-ZiraRUS` 音声のコンテナー イメージ。         |
| `1.2.0-amd64-es-es-helenarus-preview`       | `es-ES` ロケールと `es-ES-HelenaRUS` 音声のコンテナー イメージ。       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | `es-ES` ロケールと `es-ES-Laura-Apollo` 音声のコンテナー イメージ。    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | `es-ES` ロケールと `es-ES-Pablo-Apollo` 音声のコンテナー イメージ。    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | `es-MX` ロケールと `es-MX-HildaRUS` 音声のコンテナー イメージ。        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | `es-MX` ロケールと `es-MX-Raul-Apollo` 音声のコンテナー イメージ。     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | `fr-CA` ロケールと `fr-CA-Caroline` 音声のコンテナー イメージ。        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | `fr-CA` ロケールと `fr-CA-HarmonieRUS` 音声のコンテナー イメージ。     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | `fr-FR` ロケールと `fr-FR-HortenseRUS` 音声のコンテナー イメージ。     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | `fr-FR` ロケールと `fr-FR-Julie-Apollo` 音声のコンテナー イメージ。    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | `fr-FR` ロケールと `fr-FR-Paul-Apollo` 音声のコンテナー イメージ。     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | `it-IT` ロケールと `it-IT-Cosimo-Apollo` 音声のコンテナー イメージ。   |
| `1.2.0-amd64-it-it-luciarus-preview`        | `it-IT` ロケールと `it-IT-LuciaRUS` 音声のコンテナー イメージ。        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | `ja-JP` ロケールと `ja-JP-Ayumi-Apollo` 音声のコンテナー イメージ。    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | `ja-JP` ロケールと `ja-JP-HarukaRUS` 音声のコンテナー イメージ。       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | `ja-JP` ロケールと `ja-JP-Ichiro-Apollo` 音声のコンテナー イメージ。   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | `ko-KR` ロケールと `ko-KR-HeamiRUS` 音声のコンテナー イメージ。        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | `pt-BR` ロケールと `pt-BR-Daniel-Apollo` 音声のコンテナー イメージ。   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | `pt-BR` ロケールと `pt-BR-HeloisaRUS` 音声のコンテナー イメージ。      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | `zh-CN` ロケールと `zh-CN-HuihuiRUS` 音声のコンテナー イメージ。       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | `zh-CN` ロケールと `zh-CN-Kangkang-Apollo` 音声のコンテナー イメージ。 |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | `zh-CN` ロケールと `zh-CN-Yaoyao-Apollo` 音声のコンテナー イメージ。   |
| `1.1.0-amd64-de-de-hedda-preview`           | `de-DE` ロケールと `de-DE-Hedda` 音声のコンテナー イメージ。           |
| `1.1.0-amd64-de-de-heddarus-preview`        | `de-DE` ロケールと `de-DE-Hedda` 音声のコンテナー イメージ。           |
| `1.1.0-amd64-de-de-heddarus-preview`        | `de-DE` ロケールと `de-DE-HeddaRUS` 音声のコンテナー イメージ。        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | `de-DE` ロケールと `de-DE-Stefan-Apollo` 音声のコンテナー イメージ。   |
| `1.1.0-amd64-en-au-catherine-preview`       | `en-AU` ロケールと `en-AU-Catherine` 音声のコンテナー イメージ。       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | `en-AU` ロケールと `en-AU-HayleyRUS` 音声のコンテナー イメージ。       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | `en-GB` ロケールと `en-GB-George-Apollo` 音声のコンテナー イメージ。   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | `en-GB` ロケールと `en-GB-HazelRUS` 音声のコンテナー イメージ。        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | `en-GB` ロケールと `en-GB-Susan-Apollo` 音声のコンテナー イメージ。    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | `en-IN` ロケールと `en-IN-Heera-Apollo` 音声のコンテナー イメージ。    |
| `1.1.0-amd64-en-in-priyarus-preview`        | `en-IN` ロケールと `en-IN-PriyaRUS` 音声のコンテナー イメージ。        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | `en-IN` ロケールと `en-IN-Ravi-Apollo` 音声のコンテナー イメージ。     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | `en-US` ロケールと `en-US-BenjaminRUS` 音声のコンテナー イメージ。     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | `en-US` ロケールと `en-US-Guy24kRUS` 音声のコンテナー イメージ。       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | `en-US` ロケールと `en-US-Jessa24kRUS` 音声のコンテナー イメージ。     |
| `1.1.0-amd64-en-us-jessarus-preview`        | `en-US` ロケールと `en-US-JessaRUS` 音声のコンテナー イメージ。        |
| `1.1.0-amd64-en-us-zirarus-preview`         | `en-US` ロケールと `en-US-ZiraRUS` 音声のコンテナー イメージ。         |
| `1.1.0-amd64-es-es-helenarus-preview`       | `es-ES` ロケールと `es-ES-HelenaRUS` 音声のコンテナー イメージ。       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | `es-ES` ロケールと `es-ES-Laura-Apollo` 音声のコンテナー イメージ。    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | `es-ES` ロケールと `es-ES-Pablo-Apollo` 音声のコンテナー イメージ。    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | `es-MX` ロケールと `es-MX-HildaRUS` 音声のコンテナー イメージ。        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | `es-MX` ロケールと `es-MX-Raul-Apollo` 音声のコンテナー イメージ。     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | `fr-CA` ロケールと `fr-CA-Caroline` 音声のコンテナー イメージ。        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | `fr-CA` ロケールと `fr-CA-HarmonieRUS` 音声のコンテナー イメージ。     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | `fr-FR` ロケールと `fr-FR-HortenseRUS` 音声のコンテナー イメージ。     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | `fr-FR` ロケールと `fr-FR-Julie-Apollo` 音声のコンテナー イメージ。    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | `fr-FR` ロケールと `fr-FR-Paul-Apollo` 音声のコンテナー イメージ。     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | `it-IT` ロケールと `it-IT-Cosimo-Apollo` 音声のコンテナー イメージ。   |
| `1.1.0-amd64-it-it-luciarus-preview`        | `it-IT` ロケールと `it-IT-LuciaRUS` 音声のコンテナー イメージ。        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | `ja-JP` ロケールと `ja-JP-Ayumi-Apollo` 音声のコンテナー イメージ。    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | `ja-JP` ロケールと `ja-JP-HarukaRUS` 音声のコンテナー イメージ。       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | `ja-JP` ロケールと `ja-JP-Ichiro-Apollo` 音声のコンテナー イメージ。   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | `ko-KR` ロケールと `ko-KR-HeamiRUS` 音声のコンテナー イメージ。        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | `pt-BR` ロケールと `pt-BR-Daniel-Apollo` 音声のコンテナー イメージ。   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | `pt-BR` ロケールと `pt-BR-HeloisaRUS` 音声のコンテナー イメージ。      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | `zh-CN` ロケールと `zh-CN-HuihuiRUS` 音声のコンテナー イメージ。       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | `zh-CN` ロケールと `zh-CN-Kangkang-Apollo` 音声のコンテナー イメージ。 |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | `zh-CN` ロケールと `zh-CN-Yaoyao-Apollo` 音声のコンテナー イメージ。   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | `en-US` ロケールと `en-US-BenjaminRUS` 音声のコンテナー イメージ。     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | `en-US` ロケールと `en-US-Guy24kRUS` 音声のコンテナー イメージ。       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | `en-US` ロケールと `en-US-Jessa24kRUS` 音声のコンテナー イメージ。     |
| `1.0.0-amd64-en-us-jessarus-preview`        | `en-US` ロケールと `en-US-JessaRUS` 音声のコンテナー イメージ。        |
| `1.0.0-amd64-en-us-zirarus-preview`         | `en-US` ロケールと `en-US-ZiraRUS` 音声のコンテナー イメージ。         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | `zh-CN` ロケールと `zh-CN-HuihuiRUS` 音声のコンテナー イメージ。       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | `zh-CN` ロケールと `zh-CN-Kangkang-Apollo` 音声のコンテナー イメージ。 |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | `zh-CN` ロケールと `zh-CN-Yaoyao-Apollo` 音声のコンテナー イメージ。   |

## <a name="key-phrase-extraction"></a>キー フレーズ抽出

[キー フレーズ抽出][ta-kp]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services` リポジトリ内にあり、`keyphrase` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/keyphrase` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>言語検出

[言語検出][ta-la]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services` リポジトリ内にあり、`language` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/language` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>感情分析

[感情分析][ta-se]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services` リポジトリ内にあり、`sentiment` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/sentiment` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | Notes                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | 感情分析 v3 (英語)               |
| `3.0-es`   | 感情分析 v3 (スペイン語)               |
| `3.0-fr`   | 感情分析 v3 (フランス語)                |
| `3.0-it`   | 感情分析 v3 (イタリア語)               |
| `3.0-de`   | 感情分析 v3 (ドイツ語)                |
| `3.0-zh`   | 感情分析 v3 (簡体字中国語)  |
| `3.0-zht`  | 感情分析 v3 (繁体字中国語) |
| `3.0-ja`   | 感情分析 v3 (日本語)              |
| `3.0-pt`   | 感情分析 v3 (ポルトガル語)            |
| `3.0-nl`   | 感情分析 v3 (オランダ語)                 |
| `1.1.009301-amd64-preview`    | 感情分析 v2      |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
