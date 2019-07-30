---
title: Speech Devices SDK ドキュメント
titleSuffix: Azure Cognitive Services
description: リリース ノート - 最新リリースの変更点
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: wellsi
ms.openlocfilehash: 1ca6b5265cf97ef551ec0b13b46ac934a372d2c7
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797923"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK のリリース ノート
以下のセクションでは、最新リリースでの変更点の一覧を示します。

## <a name="speech-devices-sdk-160"></a>Speech Devices SDK 1.6.0:

*   Windows および Linux 上で、共通の[サンプル アプリケーション](https://aka.ms/sdsdk-download)を含む [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) がサポートされます。
*   [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) コンポーネントがバージョン 1.6.0 に更新されました。 詳しくは、[リリース ノート](https://aka.ms/csspeech/whatsnew)をご覧ください。

## <a name="speech-devices-sdk-151"></a>Speech Devices SDK 1.5.1:

*   サンプル アプリに[会話の文字起こし](conversation-transcription-service.md)が含まれています。
*   [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) コンポーネントがバージョン 1.5.1 に更新されました。 詳しくは、[リリース ノート](https://aka.ms/csspeech/whatsnew)をご覧ください。

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Cognitive Services Speech Devices SDK 1.5.0: 2019-May リリース

*   Speech Devices SDK の一般提供が開始され、限定的なプレビューではなくなりました。
*   [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) コンポーネントがバージョン 1.5.0 に更新されました。 詳しくは、[リリース ノート](https://aka.ms/csspeech/whatsnew)をご覧ください。
*   新しいウェイク ワード テクノロジにより、品質が大幅に改善されました。「重大な変更」を参照してください。
*   新しいオーディオ処理パイプラインにより、遠距離の認識が向上されました。

**重大な変更**

*   新しいウェイク ワード テクノロジにより、改良されたウェイク ワード ポータルですべてのウェイク ワードを作成し直す必要があります。 デバイスから古いキーワードを完全に削除するには、古いアプリをアンインストールしてください。
    - adb uninstall com.microsoft.coginitiveservices.speech.samples.sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Cognitive Services Speech Devices SDK 1.4.0: 2019-Apr リリース

* [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) コンポーネントがバージョン 1.4.0 に更新されました。 詳しくは、[リリース ノート](https://aka.ms/csspeech/whatsnew)をご覧ください。

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Cognitive Services Speech Devices SDK 1.3.1:2019 年 3 月リリース

* [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) コンポーネントがバージョン 1.3.1 に更新されました。 詳しくは、[リリース ノート](https://aka.ms/csspeech/whatsnew)をご覧ください。
*   ウェイク ワードの処理が更新されました (「重大な変更」を参照してください)。
*   サンプル アプリケーションでは、音声認識と翻訳の両方について、言語の選択肢が追加されました。

**重大な変更**

*   [ウェイク ワードのインストール](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)が簡素化されました。アプリの一部になったので、デバイスに個別にインストールする必要がなくなりました。
*   ウェイク ワードの認識が変更され、2 つのイベントがサポートされました。
    - RecognizingKeyword は、音声認識の結果に (未確認の) キーワード テキストが含まれていることを示します。
    - RecognizedKeyword は、キーワード認識が完了し、特定のキーワードが認識されたことを示します。


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Cognitive Services Speech Devices SDK 1.1.0: 2018-Nov リリース

* [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) コンポーネントがバージョン 1.1.0 に更新されました。 詳しくは、[リリース ノート](https://aka.ms/csspeech/whatsnew)をご覧ください。
* 強化されたオーディオ処理アルゴリズムにより、遠方界の音声認識の精度が向上しました。
* サンプル アプリケーションが新たに中国語の音声認識に対応しました。

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Cognitive Services Speech Devices SDK 1.0.1: 2018-Oct リリース

* [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) コンポーネントがバージョン 1.0.1 に更新されました。 詳しくは、[リリース ノート](https://aka.ms/csspeech/whatsnew)をご覧ください。
* 改良されたオーディオ処理アルゴリズムにより、音声認識の精度が向上します  
* 連続的認識オーディオ セッションの 1 つのバグを修正しました。

**重大な変更**

* このリリースでは、重大な変更がいくつか行われています。 API に関して詳しくは、[こちらのページ](https://aka.ms/csspeech/breakingchanges_1_0_0)をご覧ください。
* KWS モデル ファイルは、Speech Devices SDK 1.0.1 と互換性がありません。 既存のウェイク ワード ファイルは、新しいウェイク ワード ファイルがデバイスに書き込まれた後で削除されます。

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Cognitive Services Speech Devices SDK 0.5.0: 2018-Aug リリース

* 音声処理コードのバグが修正されたことで音声認識の正確性が向上しました。
* [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) コンポーネントがバージョン 0.5.0 に更新されました。 詳しくは、[リリース ノート](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)をご覧ください。

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Cognitive Services Speech Devices SDK 0.2.12733: 2018-May リリース

Cognitive Services Speech Devices SDK の最初のパブリック プレビュー リリースです。
