---
title: クイック スタート:オーディオ ファイルから音声を認識する - Speech サービス
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: trbye
ms.openlocfilehash: a7c91775411f100a92dfcb0a7199dd4b25f6eca4
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400485"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、オーディオ ファイルから音声を認識します。 いくつかの前提条件を満たすと、わずかなステップを実行するだけでファイルの音声が認識されます。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから `SpeechConfig` オブジェクトを作成します。
> * .WAV ファイル名を指定する `AudioConfig` オブジェクトを作成します。
> * 上記の `SpeechConfig` および `AudioConfig` オブジェクトを使用して `SpeechRecognizer` オブジェクトを作成します。
> * `SpeechRecognizer` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された `SpeechRecognitionResult` を検査します。
