---
title: クイック スタート:Speech SDK for JavaScript (ブラウザー) プラットフォームの設定 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech サービス SDK を使用して JavaScript (ブラウザー) 用にプラットフォームを設定するには、このガイドを使用します。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: 54fe96ecee99f3de37667d255b57b89c34aecc14
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406422"
---
このガイドでは、Web ページで使用する JavaScript 用の [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>新しい Web サイト フォルダーを作成する

新しい空のフォルダーを作成します。 Web サーバーでサンプルをホストする場合は、Web サーバーがフォルダーにアクセスできることを確認します。

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>JavaScript 用 Speech SDK をフォルダーに解凍する

Speech SDK を [.zip パッケージ](https://aka.ms/csspeech/jsbrowserpackage)としてダウンロードし、新しく作成したフォルダーに解凍します。 次の 5 つのファイルが解凍されます。
* `microsoft.cognitiveservices.speech.sdk.bundle.js` 人が判読できるバージョンの Speech SDK。
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` SDK コードのデバッグに使用されるマップ ファイル。
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` TypeScript で使用するオブジェクトの定義。
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` Speech SDK の縮小版。
* `speech-processor.js` 一部のブラウザーでパフォーマンスを改善するコード。

## <a name="create-an-indexhtml-page"></a>index.html ページを作成する

フォルダーに `index.html` という名前の新しいファイルを作成し、テキスト エディターでこのファイルを開きます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [windows](../quickstart-list.md)]