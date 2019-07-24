---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 31482cc7850c574b952c454021af729da324ba15
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978810"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) では、アプリケーションのすべての機能と共に**音声テキスト変換**を使用する最も簡単な方法を提供しています。

1. 音声構成を作成し、音声サービス サブスクリプション キー (または認証トークン) と[リージョン](~/articles/cognitive-services/speech-service/regions.md)をパラメーターとして指定します。 必要に応じて構成を変更します。 たとえば、非標準サービス エンドポイントを指定するカスタム エンドポイントを提供したり、音声入力言語または出力形式を選択します。

1. 音声構成から音声認識エンジンを作成します。 既定のマイク以外のソース (たとえば、オーディオ ストリームまたはオーディオ ファイルなど) から認識する場合は、オーディオ構成を指定します。

1. 必要に応じて、非同期操作のイベントを関連付けます。 暫定的結果および最終的結果が得られると、認識エンジンによってイベント ハンドラーが呼び出されます。 それ以外の場合、アプリケーションは、最終的な文字起こしの結果のみを受け取ります。

1. 認識を開始します。 コマンドまたはクエリの認識などの単発の認識の場合は、`RecognizeOnceAsync()` メソッド (または言語ごとの同等のメソッド) を使用します。 このメソッドは、認識されている最初の発話を返します。 文字起こしのような実行時間の長い認識では、`StartContinuousRecognitionAsync()` メソッド (または言語ごとの同等のメソッド) を使用します。 非同期認識結果のイベントを関連付けます。

Speech SDK を使用する音声認識のシナリオでは、次のコード スニペットを参照してください。

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
