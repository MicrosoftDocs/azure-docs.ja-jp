---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 80bf9247bbb07fa61b7153e321b1991b82a9d616
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246990"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) は、**音声から意図**を認識する方法を提供し、Cognitive Services [Language Understanding Service (LUIS)](https://www.luis.ai/home) によってサポートされています。

1. LUIS サブスクリプション キーと[リージョン](~/articles/cognitive-services/speech-service/regions.md#intent-recognition)をパラメーターとして使用して、音声構成を作成します。 LUIS サブスクリプション キーは、サービス ドキュメント内で**エンドポイント キー**と呼ばれます。 LUIS 作成キーは使用できません  (このセクションの後の方の注を参照してください)。

1. 音声構成から意図認識エンジンを作成します。 既定のマイク以外のソース (たとえば、オーディオ ストリームまたはオーディオ ファイルなど) から認識する場合は、オーディオ構成を指定します。

1. **AppId** に基づいた言語理解モデルを入手します。 必要な意図を追加します。

1. 必要に応じて、非同期操作のイベントを関連付けます。 暫定的結果および最終的結果 (意図を含む) が得られると、認識エンジンによってイベント ハンドラーが呼び出されます。 イベントを関連付けない場合、アプリケーションは、最終的な文字起こしの結果のみを受け取ります。

1. 意図認識を開始します。 コマンドまたはクエリの認識などの単発の認識の場合は、`RecognizeOnceAsync()`メソッドを使用します。 このメソッドは、認識されている最初の発話を返します。 実行時間の長い認識では、`StartContinuousRecognitionAsync()` メソッドを使用します。 非同期認識結果のイベントを関連付けます。

Speech SDK を使用する意図認識のシナリオでは、次のコード スニペットを参照してください。 サンプルの値を、独自の LUIS サブスクリプション キー、[サブスクリプションのリージョン](~/articles/cognitive-services/speech-service/regions.md#intent-recognition)、および意図モデルの **AppId** で置き換えます。

> [!NOTE]
> Speech SDK でサポートされているその他のサービスとは対照的に、意図認識には特定のサブスクリプション キー (LUIS エンドポイント キー) が必要です。 意図認識技術については、[LUIS の Web サイト](https://www.luis.ai)を参照してください。 **エンドポイント キー**を取得する方法については、「[LUIS エンドポイント キーの作成](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription)」を参照してください。
