---
title: C# サービス ライブラリを使用した Microsoft Speech Recognition API の利用 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Speech 認識サービス ライブラリを使用して、発話された言語をテキストに変換します。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 71c3e471a8844eb6c6b70921e40c94338a084a8b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965857"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>クイック スタート:.NET Windows 用に C&#35; で Bing Speech 認識サービス ライブラリを使用する

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

このサービス ライブラリは、独自のクラウド サービスを保有していて、それらのサービスから Speech Service を呼び出そうとしている開発者向けのものです。 デバイス バインド アプリから音声認識サービスを呼び出す場合は、この SDK を使わないでください (他のクライアント ライブラリや、それに対応した REST API を使ってください)。

C# サービス ライブラリを使用するには、[NuGet パッケージ Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/) をインストールします。 ライブラリ API リファレンスについては、[Microsoft Speech C# サービス ライブラリ](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)をご覧ください。

次のセクションでは、C# サービス ライブラリを使用して、C# のサンプル アプリケーションをインストール、ビルド、実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

### <a name="platform-requirements"></a>プラットフォームの要件

次のサンプルは、[Visual Studio 2015 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs) を使用して、Windows 8 以降と .NET Framework 4.5 以降向けに開発されたものです。

### <a name="get-the-sample-application"></a>サンプル アプリケーションを入手する

[Speech C# サービス ライブラリ サンプル](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary)のリポジトリから、サンプルを複製します。

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Speech Recognition API をサブスクライブし、無料試用版のサブスクリプション キーを取得する

Speech API は、Cognitive Services (以前の Project Oxford) の一部です。 無料試用版のサブスクリプション キーは、[Cognitive Services サブスクリプション](https://azure.microsoft.com/try/cognitive-services/) ページから取得できます。 Speech API を選択したら、 **[Get API Key]** (API キーの取得) を選択してキーを取得します。 プライマリ キーおよびセカンダリ キーを返します。 両方のキーが同じクォータに関連付けられているため、どちらのキーでも使用できます。

> [!IMPORTANT]
> * サブスクリプション キーを取得します。 Speech クライアント ライブラリを使用するには、まず[サブスクリプション キー](https://azure.microsoft.com/try/cognitive-services/)を取得する必要があります。
>
> * サブスクリプション キーを使用します。 提供された C# サービス ライブラリ サンプル アプリケーションで、コマンドライン パラメーターの 1 つとして、サブスクリプション キーを指定する必要があります。 詳しくは、「[サンプル アプリケーションを実行する](#step-3-run-the-sample-application)」をご覧ください。

## <a name="step-1-install-the-sample-application"></a>手順 1:サンプル アプリケーションをインストールする

1. Visual Studio 2015 を起動し、 **[ファイル]**  >  **[開く]**  >  **[プロジェクト/ソリューション]** を選択します。

2. SpeechClient.sln という名前の Visual Studio 2015 ソリューション (.sln) ファイルをダブルクリックして開きます。 Visual Studio でソリューションが開きます。

## <a name="step-2-build-the-sample-application"></a>手順 2:サンプル アプリケーションを構築する

Ctrl キーと Shift キーを押しながら B キーを押すか、リボン メニューで **[ビルド]** を選択します。 その後、 **[ソリューションのビルド]** を選択します。

## <a name="step-3-run-the-sample-application"></a>手順 3:サンプル アプリケーションの実行

1. ビルドが完了したら、F5 キーを押すか、リボン メニューの **[開始]** を選択してサンプルを実行します。

2. サンプルの出力ディレクトリを開きます (たとえば、SpeechClientSample\bin\Debug)。 Shift キーを押しながら右クリックし、 **[Open command window here]\(ここにコマンド ウィンドウを開く\)** を選択します。

3. 次の引数を指定して `SpeechClientSample.exe` を実行します。

   * Arg[0]:入力音声 WAV ファイルを指定します。
   * Arg[1]:音声のロケールを指定します。
   * Arg[2]:認識モードを指定します。`ShortPhrase` モードの場合は *Short*、`LongDictation` モードの場合は *Long* です。
   * Arg[3]:音声認識サービスにアクセスするためのサブスクリプション キーを指定します。

## <a name="samples-explained"></a>サンプルの説明

### <a name="recognition-modes"></a>認識モード

* `ShortPhrase` モード:発話時間は最大 15 秒です。 データがサーバーに送信されると、クライアントは複数の部分結果と、最終的な最善の結果 (1 つ) を受け取ります。
* `LongDictation` モード:発話時間は最大 10 分です。 データがサーバーに送信されると、クライアントはサーバーが指定した文の区切り位置に基づいて、複数の部分結果と複数の最終結果を受け取ります。

### <a name="supported-audio-formats"></a>サポートされている音声形式

Speech API では、次のコーデックを使用した音声/WAV がサポートされています。

* PCM 単一チャネル
* Siren
* SirenSR

### <a name="preferences"></a>ユーザー設定

SpeechClient を作成するには、まず Preferences オブジェクトを作成する必要があります。 Preferences オブジェクトとは、音声認識サービスの動作を構成する一連のパラメーターのことです。 次のフィールドで構成されます。

* `SpeechLanguage`:音声認識サービスに送信される音声のロケールです。
* `ServiceUri`:音声認識サービスの呼び出しに使用されるエンドポイントです。
* `AuthorizationProvider`:音声認識サービスにアクセスするためのトークンを取得するために使用される IAuthorizationProvider 実装です。 このサンプルでは Cognitive Services 承認プロバイダーが提供されていますが、トークンのキャッシュを処理するための独自の実装を作成することを強くお勧めします。
* `EnableAudioBuffering`:高度なオプションです。 [接続管理](#connection-management)に関する記事をご覧ください。

### <a name="speech-input"></a>音声入力

SpeechInput オブジェクトは、次の 2 つのフィールドで構成されます。

* **オーディオ**:SDK が音声を取得する際の取得元となる、任意のストリーム実装です。 読み取りをサポートする、任意の[ストリーム](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx)を指定できます。
   > [!NOTE]
   > SDK は、読み取り時にストリームが **0** を返したときに、ストリームの終端を検出します。

* **RequestMetadata**:音声認識要求に関するメタデータです。 詳しくは、[リファレンス](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)をご覧ください。

### <a name="speech-request"></a>音声認識要求

SpeechClient オブジェクトと SpeechInput オブジェクトをインスタンス化したら、RecognizeAsync を使用して Speech Service への要求を行います。

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

要求が完了すると、RecognizeAsync によって返されたタスクが完了します。 最後の RecognitionResult が返されると、認識が完了します。 サービスまたは SDK が予期せず失敗した場合には、タスクが失敗することがあります。

### <a name="speech-recognition-events"></a>音声認識イベント

#### <a name="partial-results-event"></a>部分結果イベント

このイベントは、ユーザーが言おうとしていることを Speech Service が予測するたびに呼び出されます。これは、ユーザーの発話 (`MicrophoneRecognitionClient` を使用した場合) やデータの送信 (`DataRecognitionClient` を使用した場合) が完了する前であっても呼び出されます。 イベントは、`SpeechClient.SubscribeToPartialResult()` を使用してサブスクライブできます。 または、汎用イベント サブスクリプション メソッド `SpeechClient.SubscribeTo<RecognitionPartialResult>()` を使用することもできます。

**戻り値の形式** | 説明 |
------|------
**LexicalForm** | この形式は、未処理の音声認識結果 (生データ) を必要とするアプリケーションで使用するのに適しています。
**DisplayText** | 認識された語句です。逆テキスト正規化、大文字/小文字、句読点、および不適切表現マスクが適用されます。 不適切表現は最初の文字だけ表示され、残りはアスタリスクでマスクされます (たとえば、"d***")。 この形式は、音声認識結果をユーザーに表示するアプリケーションで使用するのに適しています。
**Confidence** | 音声認識サーバーで定義された関連する音声に対する、認識済み語句の信頼度レベルです。
**MediaTime** | 音声ストリームの開始点を基準とした現在時刻です (100 ナノ秒単位)。
**MediaDuration** | 音声ストリームに対する、現在の語句の相対的な継続時刻/長さです (100 ナノ秒単位)。

#### <a name="result-event"></a>結果イベント
(`ShortPhrase` モードで) 発話が完了すると、このイベントが呼び出されます。 ユーザーには、結果の n-best 選択肢が提供されます。 `LongDictation` モードでは、サーバーによって示された文の区切り目に基づいて、イベントが複数回呼び出される可能性があります。 イベントは、`SpeechClient.SubscribeToRecognitionResult()` を使用してサブスクライブできます。 または、汎用イベント サブスクリプション メソッド `SpeechClient.SubscribeTo<RecognitionResult>()` を使用することもできます。

**戻り値の形式** | 説明 |
------|------|
**RecognitionStatus** | 認識結果がどのように生成されのたかを示すステータスです (たとえば、正常に認識された結果として生成されたのか、それとも接続がキャンセルされた結果として生成されたのかなど)。
**Phrases** | n-best の認識済み語句セットと、認識の信頼度です。

認識結果について詳しくは、「[出力形式](../Concepts.md#output-format)」をご覧ください。

### <a name="speech-recognition-response"></a>音声認識の応答

音声認識の応答例
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High)
```

## <a name="connection-management"></a>接続管理

この API では、要求ごとに 1 つの WebSocket 接続が使用されます。 最適なユーザー エクスペリエンスを得るため、SDK は Speech Service に再接続し、受信した最後の RecognitionResult から認識を開始しようとします。 たとえば、音声要求が 2 分間の場合に、SDK が 1 分のマークで RecognitionEvent を受信し、その 5 秒後にネットワーク障害が発生した場合、SDK は 1 分のマークから開始される新しい接続を開始します。

>[!NOTE]
>ストリームがシークをサポートしていない場合もあるため、SDK は 1 分のマークにシーク バックすることはしません。 代わりに、SDK は音声をバッファーするための内部バッファーを保持し、RecognitionResult イベントを受信したときにバッファーをクリアします。

## <a name="buffering-behavior"></a>バッファリングの動作

既定では、SDK は音声をバッファーし、ネットワーク割り込みが発生した場合に回復できるようにします。 ネットワーク切断中に失われた音声を破棄し、接続を再開するほうが望ましい場合は、Preferences オブジェクトの `EnableAudioBuffering` を `false` に設定して、音声バッファリングを無効にすることをお勧めします。

## <a name="related-topics"></a>関連トピック

[Microsoft Speech C# サービス ライブラリ リファレンス](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
