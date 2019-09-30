---
title: C# デスクトップ ライブラリを使用した Bing Speech Recognition API の利用 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Speech Recognition API を使って音声をテキストに変換する、基本的な Windows アプリケーションを開発します。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3160ccc1c8741d87fcee94a6face48551a79052d
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966900"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>クイック スタート:Windows 上の .NET 用に C&#35; で Bing Speech Recognition API を使用する

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

このページでは、Speech Recognition API を使って音声をテキストに変換する、基本的な Windows アプリケーションの開発方法について説明します。 クライアント ライブラリを使うと、リアルタイム ストリーミングが可能になります。つまり、クライアント アプリケーションがサービスに音声を送信する際に、部分的な認識結果を同時かつ非同期的に受信できるようになります。

開発者は、任意のデバイスで実行されるアプリから音声認識サービスを利用する必要がある場合、C# のデスクトップ ライブラリを使用できます。 ライブラリを使用するには、32 ビット プラットフォーム用の [NuGet パッケージ Microsoft.ProjectOxford.SpeechRecognition-x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) と、64 ビット プラットフォーム用の [NuGet パッケージ Microsoft.ProjectOxford.SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) をインストールします。 クライアント ライブラリ API リファレンスについては、[Microsoft Speech C# デスクトップ ライブラリ](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)をご覧ください。

次のセクションでは、C# デスクトップ ライブラリを使用して、C# のサンプル アプリケーションをインストール、ビルド、実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

### <a name="platform-requirements"></a>プラットフォームの要件

次のサンプルは、[Visual Studio 2015 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs) を使用して、Windows 8 以降と .NET Framework 4.5 以降向けに開発されたものです。

### <a name="get-the-sample-application"></a>サンプル アプリケーションを入手する

[Speech C# デスクトップ ライブラリ サンプル](https://github.com/microsoft/cognitive-speech-stt-windows)のリポジトリから、サンプルを複製します。

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Speech Recognition API をサブスクライブし、無料試用版のサブスクリプション キーを取得する

Speech API は、Cognitive Services (以前の Project Oxford) の一部です。 無料試用版のサブスクリプション キーは、[Cognitive Services サブスクリプション](https://azure.microsoft.com/try/cognitive-services/) ページから取得できます。 Speech API を選択したら、 **[Get API Key]** (API キーの取得) を選択してキーを取得します。 プライマリ キーおよびセカンダリ キーを返します。 両方のキーが同じクォータに関連付けられているため、どちらのキーでも使用できます。

> [!IMPORTANT]
> * サブスクリプション キーを取得します。 Speech クライアント ライブラリを使用するには、まず[サブスクリプション キー](https://azure.microsoft.com/try/cognitive-services/)を取得する必要があります。
>
> * サブスクリプション キーを使用します。 提供された C# デスクトップ サンプル アプリケーションで、サンプルを実行する際、テキスト ボックスにサブスクリプション キーを貼り付けます。 詳しくは、「[サンプル アプリケーションを実行する](#step-3-run-the-sample-application)」をご覧ください。

## <a name="step-1-install-the-sample-application"></a>手順 1:サンプル アプリケーションをインストールする

1. Visual Studio 2015 を起動し、 **[ファイル]**  >  **[開く]**  >  **[プロジェクト/ソリューション]** を選択します。

2. ダウンロードした Speech Recognition API ファイルを保存したフォルダーを参照します。 **[Speech]**  >  **[Windows]** を選択し、Sample-WP フォルダーを選択します。

3. SpeechToText-WPF-Samples.sln という名前の Visual Studio 2015 ソリューション (.sln) ファイルをダブルクリックして開きます。 Visual Studio でソリューションが開きます。

## <a name="step-2-build-the-sample-application"></a>手順 2:サンプル アプリケーションを構築する

1. *インテントを使った認識機能*を使用する場合は、最初に [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) にサインアップする必要があります。 その後、LUIS アプリのエンドポイント URL を使用して、samples/SpeechRecognitionServiceExample フォルダーの app.config ファイル内にあるキー `LuisEndpointUrl` の値を設定します。 LUIS アプリのエンドポイント URL について詳しくは、[アプリの発行](../../luis/luis-get-started-create-app.md#publish-your-app)に関する記事をご覧ください。

   > [!TIP]
   > LUIS エンドポイント URL の `&` 文字を `&amp;` に置き換えて、URL が XML パーサーによって正しく解釈されるようにしてください。

2. Ctrl キーと Shift キーを押しながら B キーを押すか、リボン メニューで **[ビルド]** を選択します。 その後、 **[ソリューションのビルド]** を選択します。

## <a name="step-3-run-the-sample-application"></a>手順 3:サンプル アプリケーションの実行

1. ビルドが完了したら、F5 キーを押すか、リボン メニューの **[開始]** を選択してサンプルを実行します。

2. **[Project Oxford Speech to Text Sample]\(Project Oxford Speech to Text サンプル\)** ウィンドウに移動します。 「**Paste your subscription key here to start**」と書かれてあるテキスト ボックスに、サブスクリプション キーを貼り付けます。 PC やラップトップにサブスクリプション キーを保持するには、 **[Save Key]\(キーの保存\)** を選択します。 サブスクリプション キーをシステムから削除するには、 **[Delete Key]\(キーの削除\)** を選択して PC またはラップトップから削除します。

   ![音声認識のキーの貼り付け](../Images/SpeechRecog_paste_key.PNG)

3. **[Speech Recognition Source]\(音声認識ソース\)** で、6 つの音声ソースのいずれかを選択します。これらは主に、次の 2 つの入力カテゴリに分類されます。

   * コンピューターのマイクか、接続されたマイクを使用して音声をキャプチャする。
   * 音声ファイルを再生する。

   各カテゴリには、次の 3 つの認識モードがあります。

    * **ShortPhrase モード**:発話時間は最大 15 秒です。 データがサーバーに送信されると、クライアントは複数の部分結果と、複数の n-best 選択肢を持った 1 つの最終結果を受け取ります。
    * **LongDictation モード**:発話時間は最大 2 分です。 データがサーバーに送信されると、クライアントはサーバーが指定した文の区切り位置に基づいて、複数の部分結果と複数の最終結果を受け取ります。
    * **インテント検出**:サーバーから、音声入力に関する追加の構造化情報が返されます。 インテント検出を使用するには、まず [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) を使用してモデルをトレーニングする必要があります。

このサンプル アプリケーションでは、サンプルの音声ファイルを使用します。 このファイルは、このサンプルと一緒にダウンロードしたリポジトリ内 (samples/SpeechRecognitionServiceExample フォルダー) にあります。 これらのサンプル音声ファイルは、音声入力として **[Use wav file for Shortphrase mode]\(Shortphrase モードに wav ファイルを使用する\)** または **[Use wav file for Longdictation mode]\(Longdictation モードに wav ファイルを使用する\)** を選択する際、他のファイルが選択されていなければ、自動的に実行されます。 現在のところ、サポートされているのは WAV 音声形式のみです。

![Speech to Text のインターフェイス](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>サンプルの説明

### <a name="recognition-events"></a>認識イベント

* **部分結果イベント**:このイベントは、ユーザーが言おうとしていることを Speech Service が予測するたびに呼び出されます。これは、ユーザーの発話 (`MicrophoneRecognitionClient` を使用した場合) やデータの送信 (`DataRecognitionClient` を使用した場合) が完了する前であっても呼び出されます。
* **エラー イベント**:サービスがエラーを検出したときに呼び出されます。
* **意図イベント**:最終的な認識結果が解析され、構造化された JSON 意図が生成された後、"WithIntent" クライアント (ShortPhrase モードの場合のみ) で呼び出されます。
* **結果イベント**:
  * `ShortPhrase` モードでは、このイベントは発話の完了後に呼び出され、n-best 結果を返します。
  * `LongDictation` モードでは、サービスによって特定された文の区切り目に基づいて、イベント ハンドラーが複数回呼び出されます。
  * **各 n-best 選択肢について**、信頼度の値と、複数形式の認識済みテキストが返されます。 詳しくは、[出力形式](../Concepts.md#output-format)に関するページをご覧ください。

イベント ハンドラーについては、コード内のコード コメントで既に説明しています。

## <a name="related-topics"></a>関連トピック

* [Microsoft Speech デスクトップ ライブラリ リファレンス](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Android 上の Java での Microsoft Speech Recognition API の利用](GetStartedJavaAndroid.md)
* [iOS 上の Objective-C での Microsoft Speech Recognition API の利用](Get-Started-ObjectiveC-iOS.md)
* [JavaScript での Microsoft Speech Recognition API の利用](GetStartedJSWebsockets.md)
* [REST を介した Microsoft Speech Recognition API の利用](GetStartedREST.md)
