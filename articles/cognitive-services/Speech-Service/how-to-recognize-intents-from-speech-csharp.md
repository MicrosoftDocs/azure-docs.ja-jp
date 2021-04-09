---
title: Speech SDK for C# を使用して音声の意図を認識する方法
titleSuffix: Azure Cognitive Services
description: このガイドでは、Speech SDK for C# を使用して、音声から意図を認識する方法を学習します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 93a3adf00203e317be912e3e72de7a3f7ca666c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001102"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Speech SDK for C# を使用して音声の意図を認識する方法

Cognitive Services [Speech SDK](speech-sdk.md) は [Language Understanding サービス (LUIS)](https://www.luis.ai/home) と統合して **意図認識** 機能を提供します。 意図は、航空機の予約や天気のチェック、あるいは電話を掛けるなどのユーザーが実行したい行動です。 ユーザーは、自然だと思われるどのような用語でも使用できます。 LUIS では機械学習を使用して、定義されている意図にユーザーの要求をマップします。

> [!NOTE]
> LUIS アプリケーションでは、認識する意図およびエンティティを定義します。 これは Speech サービスを使用する C# アプリケーションとは別のものです。 この記事では、「アプリ」は LUIS アプリを意味し、「アプリケーション」は C# のコードを意味します。

このガイドでは、Speech SDK を使用して、デバイスのマイクを通したユーザーの発話から意図を引き出す C# コンソール アプリケーションを開発します。 学習内容は次のとおりです。

> [!div class="checklist"]
>
> - Speech SDK NuGet パッケージを参照する Visual Studio プロジェクトを作成する
> - 音声構成を作成して意図認識エンジンを取得する
> - LUIS アプリのモデルを取得して必要な意図を追加する
> - 音声認識の言語を指定する
> - ファイルから音声を認識する
> - 非同期のイベント ドリブンの継続的な認識を使用する

## <a name="prerequisites"></a>前提条件

このガイドを開始する前に、次の項目を用意する必要があります。

- LUIS アカウント。 [LUIS ポータル](https://www.luis.ai/home)から無料で取得できます。
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (任意のエディション)。

## <a name="luis-and-speech"></a>LUIS および音声認識

LUIS は音声から意図を認識するために Speech サービスと統合しています。 Speech サービスのサブスクリプションは不要で、LUIS だけでかまいません。

LUIS は、次の 3 種類のキーを使用します。

| キーの種類  | 目的                                               |
| --------- | ----------------------------------------------------- |
| Authoring | LUIS アプリをプログラムで作成および変更できる |
| スターター   | テキストのみを使用して LUIS アプリケーションをテストできる   |
| エンドポイント  | 特定の LUIS アプリへのアクセスを承認する            |

このガイドには、エンドポイント タイプのキーが必要です。 このガイドでは、[事前構築済みホーム オートメーション アプリの使用](../luis/luis-get-started-create-app.md)に関するクイックスタートに従って作成できるホーム オートメーション LUIS アプリのサンプルを使用します。 独自の LUIS アプリを作成した場合は、代わりにそれを使用することができます。

LUIS アプリを作成すると、テキスト クエリを使用してアプリをテストできるようにスターター キーが自動的に生成されます。 このキーでは Speech Service との統合が有効にならないため、このガイドでこれを使用することはできません。 Azure ダッシュボードで LUIS リソースを作成して LUIS アプリに割り当ててください。 このガイドでは、無料のサブスクリプション階層を使用することができます。

Azure ダッシュ ボードで LUIS のリソースを作成した後、[LUIS ポータル](https://www.luis.ai/home)にログインし、 **[マイ アプリ]** ページで自分のアプリケーションを選択し、アプリの **[Manage]\(管理\)** ページに切り替えます。 最後に、サイド バーの **[Keys and endpoints]\(キーとエンドポイント\)** を選択します。

![LUIS のポータル キーとエンドポイントの設定](media/sdk/luis-keys-endpoints-page.png)

**[Keys and Endpoint]\(キーとエンドポイント\) の設定** ページで:

1. **[Resources and Keys]\(リソースとキー\)** セクションまでスクロールし、 **[Assign resource]\(リソースの割り当て\)** を選択します。
1. **[Assign a key to your app]\(アプリへのキーの割り当て\)** ダイアログ ボックスに次の変更を加えます。

   - **[Tenant]\(テナント\)** で **[Microsoft]** を選択します。
   - **[Subscription Name]\(サブスクリプション名\)** には、使用したい LUIS リソースが含まれている Azure サブスクリプションを選択します。
   - **[Key]\(キー\)** には、アプリで使用したい LUIS リソースを選択します。

   まもなく、ページの下部にあるテーブルに新しいサブスクリプションが表示されます。

1. キーの横にあるアイコンを選択して、それをクリップボードにコピーします。 (どちらのキーを使用してもかまいません。)

![LUIS アプリのサブスクリプション キー](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Visual Studio での Speech プロジェクトの作成

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>コードの追加

次に、プロジェクトにコードを追加します。

1. **ソリューション エクスプローラー** で **Program.cs** ファイルを開きます。

1. ファイルの先頭にある `using` ステートメントのブロックを次の宣言に置き換えます。

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. 用意されている `Main()` メソッドを、次のように対応する非同期メソッドに置き換えます。

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. 次に示すように空の非同期メソッド `RecognizeIntentAsync()` を作成します。

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. この新しいメソッドの本文に、このコードを追加します。

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. このメソッド内のプレース ホルダーを、次のように LUIS サブスクリプション キー、リージョン、およびアプリ ID で置き換えます。

   | プレースホルダー | 置換後の文字列 |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | LUIS エンドポイント キー。 前と同様に、この項目は Azure ダッシュボードから取得する必要があります ("スターター キー" は使用しません)。 [LUIS ポータル](https://www.luis.ai/home)の、アプリの **キーとエンドポイント** のページ ( **[Manage]\(管理\)** の下) で見つけることができます。 |
   | `YourLanguageUnderstandingServiceRegion` | LUIS サブスクリプションが存在するリージョンの短い識別子で、たとえば米国西部の場合は `westus` です。 [リージョン](regions.md)を参照してください。 |
   | `YourLanguageUnderstandingAppId` | LUIS アプリ ID。 [LUIS ポータル](https://www.luis.ai/home)のアプリの **[Settings]\(設定\)** ページで確認できます。 |

これらの変更を行ってから、アプリケーションをビルド (**Ctrl + Shift + B**) および実行 (**F5**) できます。 入力を求められたら、PC のマイクに向かって "Turn off the lights (照明を消して)" と言ってみてください。 アプリケーションによって、結果がコンソール ウィンドウに表示されます。

次のセクションでには、コードの詳細について説明します。

## <a name="create-an-intent-recognizer"></a>意図認識エンジンを作成する

まず、LUIS のエンドポイント キーとリージョンから音声構成を作成する必要があります。 音声構成は、Speech SDK のさまざまな機能のための認識エンジンを作成するために使用できます。 音声構成では、使用するサブスクリプションを指定する複数の方法があり、ここでは `FromSubscription` を使用します。これは、サブスクリプション キーとリージョンを受け取ります。

> [!NOTE]
> Speech Service サブスクリプションではなく LUIS サブスクリプションのキーおよびリージョンを使用してください。

次に、`new IntentRecognizer(config)` を使用して意図認識エンジンを作成します。 構成で使用するサブスクリプションは既にわかっているため、認識エンジンを作成するときにサブスクリプション キーとエンドポイントをもう一度指定する必要はありません。

## <a name="import-a-luis-model-and-add-intents"></a>LUIS モデルをインポートして意図を追加する

ここで、`LanguageUnderstandingModel.FromAppId()` を使用して LUIS アプリからモデルをインポートし、認識エンジンの `AddIntent()` メソッドを経由して認識する LUIS の意図を追加します。 これら 2 つの手順により、ユーザーが自身の要求で使用すると思われる単語を示すことで音声認識の精度が向上します。 アプリケーション内でそのすべての意図を認識する必要がないのであれば、それらをすべて追加する必要はありません。

意図を追加するには、LUIS モデル (先ほど作成して `model` という名前を付けたもの)、意図名、および意図 ID の 3 つの引数を指定する必要があります。 ID と名前の違いは次のとおりです。

| `AddIntent()`&nbsp;引数 | 目的 |
| --------------------------- | ------- |
| `intentName` | LUIS アプリ内で定義される意図の名前。 この値が LUIS の意図名と正確に一致する必要があります。 |
| `intentID` | Speech SDK によって認識された意図に割り当てられた ID。 この値には任意のものを使用できます。LUIS アプリで定義されている意図名に対応させる必要はありません。 同じコードによって複数の意図が処理される場合、それらに対して同じ ID を使用できます。 |

ホーム オートメーション LUIS アプリには、2 つの意図があります。1 つはデバイスをオンにするための意図、もう 1 つはデバイスをオフにするための意図です。 以下の行はこれらの意図を認識エンジンに追加します。 `RecognizeIntentAsync()` メソッド内の 3 つの `AddIntent` 行をこのコードで置換します。

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

個々の意図を追加する代わりに、`AddAllIntents` メソッドを使用して、モデル内のすべての意図を認識エンジンに追加することもできます。

## <a name="start-recognition"></a>認識を開始する

認識エンジンを作成して意図を追加したら、認識を開始できます。 Speech SDK は、単発の認識および継続的な認識の両方をサポートしています。

| 認識モード | 呼び出すメソッド | 結果 |
| ---------------- | --------------- | ------ |
| 単発 | `RecognizeOnceAsync()` | 1 回の発話後に認識された意図を返します (ある場合)。 |
| 継続的 | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | 複数の発話を認識し、結果が得られた場合はイベント (例: `IntermediateResultReceived`) を出力します。 |

このアプリケーションでは単発モードが使用されるため、`RecognizeOnceAsync()` を呼び出して認識を開始します。 結果は、認識された意図に関する情報を含む `IntentRecognitionResult` オブジェクトです。 LUIS の JSON 応答は、次の式を使用して抽出します。

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

アプリケーションは JSON の結果を解析しません。 JSON テキストをコンソール ウィンドウに表示するだけです。

![単一の LUIS 認識の結果](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>認識言語を指定する

既定では、LUIS は意図を米国英語 (`en-us`) で認識します。 音声構成の `SpeechRecognitionLanguage` プロパティにロケール コードを割り当てることで、意図を他の言語で認識することができます。 たとえば、認識エンジンを作成する前にアプリケーションに `config.SpeechRecognitionLanguage = "de-de";` を追加すると、意図はドイツ語で認識されます。 詳細については、[LUIS の言語サポート](../LUIS/luis-language-support.md#languages-supported)に関するページを参照してください。

## <a name="continuous-recognition-from-a-file"></a>ファイルからの継続的な認識

次のコードは、Speech SDK を使用した意図認識の 2 つの追加機能を示しています。 1 つ目は、前に述べた継続的な認識です。この機能では、結果が使用可能になると認識エンジンがイベントを出力します。 これらのイベントは、指定したイベント ハンドラーによって後で処理できます。 継続的な認識では、`RecognizeOnceAsync()` の代わりに認識エンジンの `StartContinuousRecognitionAsync()` メソッドを呼び出して認識を開始します。

もう 1 つの機能では、処理の対象となる音声を含むオーディオを WAV ファイルから読み取ります。 実装では、意図認識エンジンを作成するときに使用できるオーディオ構成を作成する必要があります。 ファイルはサンプリング速度が 16 kHz の単一チャネル (モノラル) である必要があります。

これらの機能を試すには、`RecognizeIntentAsync()` メソッドの本文を削除するかコメントにし、その位置に次のコードを追加します。

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

コードを修正し、以前と同様に LUIS エンドポイント キー、リージョン、およびアプリ ID を含み、ホーム オートメーションの意図を追加します。 `whatstheweatherlike.wav` を、録音済みのオーディオ ファイルの名前に変更します。 次に、ビルドを実行し、オーディオ ファイルをビルド ディレクトリにコピーして、アプリケーションを実行します。

たとえば、録音済みのオーディオ ファイルで、"Turn off the lights (照明を消して)" と言ってから一呼吸置き、"Turn on the lights (照明を付けて)" と言った場合、次のような結果がコンソールから出力されます。

![オーディオ ファイルに対する LUIS の認識結果](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
この記事のコードを **samples/csharp/sharedcontent/console** フォルダーから探します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クイック スタート: マイクから音声を認識する](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)