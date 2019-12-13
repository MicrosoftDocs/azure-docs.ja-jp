---
title: クイック スタート:音声を複数の言語に翻訳する - Speech サービス
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980726"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、ある言語の音声を別の言語の音声に対話的に翻訳します。 いくつかの前提条件を満たすと、次の 6 つの手順だけで、音声を複数の言語のテキストに翻訳します。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから ````SpeechTranslationConfig```` オブジェクトを作成します。
> * ````SpeechTranslationConfig```` オブジェクトを更新して、音声認識のソース言語を指定します。
> * ````SpeechTranslationConfig```` オブジェクトを更新して、複数の翻訳ターゲット言語を指定します。
> * 上記の ````SpeechTranslationConfig```` オブジェクトを使用して ````TranslationRecognizer```` オブジェクトを作成します。
> * ````TranslationRecognizer```` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された ````TranslationRecognitionResult```` を検査します。
