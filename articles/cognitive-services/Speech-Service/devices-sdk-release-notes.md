---
title: Speech Devices SDK ドキュメント
titleSuffix: Azure Cognitive Services
description: リリース ノートには、更新プログラム、拡張機能、バグ修正、および Speech Devices SDK に対する変更のログが記載されています。 この記事は、Speech Devices SDK の各リリースで更新されています。
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 1b2418b248e995b0f64eec82ccb477b15583c81e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562333"
---
# <a name="release-notes-speech-devices-sdk"></a>リリース ノート:Speech Devices SDK

以下のセクションでは、最新リリースでの変更点の一覧を示します。

## <a name="speech-devices-sdk-1150"></a>Speech Devices SDK 1.15.0:

- 新しい Microsoft Audio Stack (MAS) にアップグレードされており、音声のためのビームフォーミングとノイズリダクションが改善されています。
- ターゲットによっては、バイナリ サイズが 70% も縮小されました。
- [Azure Percept Audio](https://docs.microsoft.com/azure/azure-percept/overview-azure-percept-audio) と[バイナリ リリース](https://aka.ms/sdsdk-download-APAudio)のサポート。
- [Speech SDK](./speech-sdk.md) コンポーネントがバージョン 1.15.0 に更新されました。 詳しくは、[リリース ノート](./releasenotes.md)をご覧ください。

## <a name="speech-devices-sdk-1110"></a>Speech Devices SDK 1.11.0:

- [任意のマイク配列ジオメトリ](how-to-devices-microphone-array-configuration.md)と、[構成ファイル](https://aka.ms/sdsdk-micarray-json)を使用した動作する角度の設定のサポート。
- [Urbetter DDK](http://www.urbetter.com/products_56/278.html) のサポート。
- [音声アシスタント サンプル](https://aka.ms/sdsdk-speaker)で使用されている [GGEC Speaker](https://aka.ms/sdsdk-download-speaker) のバイナリがリリースされました。
- [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) と Raspberry Pi および同様のデバイス用の [Linux ARM 64](https://aka.ms/sdsdk-download-linux-arm64) がリリースされました。
- [Speech SDK](./speech-sdk.md) コンポーネントがバージョン 1.11.0 に更新されました。 詳しくは、[リリース ノート](./releasenotes.md)をご覧ください。

## <a name="speech-devices-sdk-190"></a>Speech Devices SDK 1.9.0:

- [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) 対応の初期バイナリ (Linux ARM64) が提供されています。
- Roobo v1 では Speech SDK に Maven が使用されるようになりました
- [Speech SDK](./speech-sdk.md) コンポーネントがバージョン 1.9.0 に更新されました。 詳しくは、[リリース ノート](./releasenotes.md)をご覧ください。

## <a name="speech-devices-sdk-170"></a>Speech Devices SDK 1.7.0:

- Linux ARM がサポートされるようになりました。
- [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) 対応の初期バイナリが提供されています (Linux ARM64)。
- Windows ユーザーは、`AudioConfig.fromDefaultMicrophoneInput()` または`AudioConfig.fromMicrophoneInput(deviceName)` を使用して、使用するマイクを指定できます。
- ライブラリのサイズが最適化されました。
- 同じ音声/意図認識オブジェクトを使用したマルチターン認識のサポート。
- 認識の停止中にプロセスが応答を停止する可能性のある問題を修正しました。
- サンプル アプリに、ファイルの形式を示す participants.properties サンプル ファイルが追加されました。
- [Speech SDK](./speech-sdk.md) コンポーネントがバージョン 1.7.0 に更新されました。 詳しくは、[リリース ノート](./releasenotes.md)をご覧ください。

## <a name="speech-devices-sdk-160"></a>Speech Devices SDK 1.6.0:

- Windows および Linux 上で、共通の[サンプル アプリケーション](./speech-devices-sdk.md)を含む [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) がサポートされます。
- [Speech SDK](./speech-sdk.md) コンポーネントがバージョン 1.6.0 に更新されました。 詳しくは、[リリース ノート](./releasenotes.md)をご覧ください。

## <a name="speech-devices-sdk-151"></a>Speech Devices SDK 1.5.1:

- サンプル アプリに[会話の文字起こし](./conversation-transcription.md)が含まれています。
- [Speech SDK](./speech-sdk.md) コンポーネントがバージョン 1.5.1 に更新されました。 詳しくは、[リリース ノート](./releasenotes.md)をご覧ください。

## <a name="speech-devices-sdk-150-2019-may-release"></a>Speech Devices SDK 1.5.0:2019-May リリース

- Speech Devices SDK の一般提供が開始され、限定的なプレビューではなくなりました。
- [Speech SDK](./speech-sdk.md) コンポーネントがバージョン 1.5.0 に更新されました。 詳しくは、[リリース ノート](./releasenotes.md)をご覧ください。
- 新しいキーワード テクノロジにより、品質が大幅に改善されました。「重大な変更」を参照してください。
- 新しいオーディオ処理パイプラインにより、遠距離の認識が向上されました。

**重大な変更**

- 新しいキーワード テクノロジに伴い、改良されたキーワード ポータルですべてのキーワードを作成し直す必要があります。 デバイスから古いキーワードを完全に削除するには、古いアプリをアンインストールしてください。
  - adb uninstall com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech Devices SDK 1.4.0:2019-Apr リリース

- [Speech SDK](./speech-sdk.md) コンポーネントがバージョン 1.4.0 に更新されました。 詳しくは、[リリース ノート](./releasenotes.md)をご覧ください。

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech Devices SDK 1.3.1:2019 年 3 月リリース

- [Speech SDK](./speech-sdk.md) コンポーネントがバージョン 1.3.1 に更新されました。 詳しくは、[リリース ノート](./releasenotes.md)をご覧ください。
- キーワードの処理が更新されました (「重大な変更」を参照)。
- サンプル アプリケーションでは、音声認識と翻訳の両方について、言語の選択肢が追加されました。

**重大な変更**

- [キーワードのインストール](./custom-keyword-basics.md)が簡素化され、アプリに含まれるようになったため、デバイス上に個別にインストールする必要がなくなりました。
- キーワードの認識が変更され、2 つのイベントがサポートされるようになりました。
  - `RecognizingKeyword,` は、音声認識の結果に (未確認の) キーワード テキストが含まれていることを示します。
  - `RecognizedKeyword` は、キーワード認識が完了し、特定のキーワードが認識されたことを示します。

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech Devices SDK 1.1.0:2018-Nov リリース

- [Speech SDK](./speech-sdk.md) コンポーネントがバージョン 1.1.0 に更新されました。 詳しくは、[リリース ノート](./releasenotes.md)をご覧ください。
- 強化されたオーディオ処理アルゴリズムにより、遠方界の音声認識の精度が向上しました。
- サンプル アプリケーションが新たに中国語の音声認識に対応しました。

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech Devices SDK 1.0.1:2018-Oct リリース

- [Speech SDK](./speech-sdk.md) コンポーネントがバージョン 1.0.1 に更新されました。 詳しくは、[リリース ノート](./releasenotes.md)をご覧ください。
- 改良されたオーディオ処理アルゴリズムにより、音声認識の精度が向上します
- 連続的認識オーディオ セッションの 1 つのバグを修正しました。

**重大な変更**

- このリリースでは、重大な変更がいくつか行われています。 API に関して詳しくは、[こちらのページ](https://aka.ms/csspeech/breakingchanges_1_0_0)をご覧ください。
- KWS モデル ファイルは、Speech Devices SDK 1.0.1 と互換性がありません。 既存のキーワード ファイルは、新しいキーワード ファイルがデバイスに書き込まれると削除されます。

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK 0.5.0:2018-Aug リリース

- 音声処理コードのバグが修正されたことで音声認識の正確性が向上しました。
- [Speech SDK](./speech-sdk.md) コンポーネントがバージョン 0.5.0 に更新されました。 詳しくは、[リリース ノート](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)をご覧ください。

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK 0.2.12733:2018-May リリース

Cognitive Services Speech Devices SDK の最初のパブリック プレビュー リリースです。