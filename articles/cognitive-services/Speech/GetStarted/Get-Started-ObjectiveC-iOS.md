---
title: iOS 上の Objective-C での Bing Speech Recogniton API の利用 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Speech Recognition API を使って、音声をテキストに変換する iOS アプリケーションを開発します。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 09b7e8961e59bd6fad49408c28e9ee9a4a209cae
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669433"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>クイック スタート:iOS 上の Objective-C で Bing Speech Recognition API を使用する

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Speech Recognition API を使用すると、クラウドベースの Speech Service を使って音声をテキストに変換する、iOS アプリケーションを開発できます。 この API はリアルタイム ストリーミングをサポートしているので、アプリケーションは音声をサービスへ送信するのと同時に、部分的な認識結果を非同期で受け取ることができます。

この記事では、サンプル アプリケーションを使って Speech Recognition API の利用を開始して iOS アプリケーションを開発するための基本について説明します。 詳細な API リファレンスについては、[Speech SDK クライアント ライブラリ リファレンス](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)をご覧ください。

## <a name="prerequisites"></a>前提条件

### <a name="platform-requirements"></a>プラットフォームの要件

Mac XCode IDE がインストールされていることを確認してください。

### <a name="get-the-client-library-and-examples"></a>クライアント ライブラリと例を入手する

iOS 用の Speech クライアント ライブラリとサンプルは、[iOS 用 Speech クライアント SDK](https://github.com/microsoft/cognitive-speech-stt-ios) から入手できます。

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Speech Recognition API をサブスクライブし、無料試用版のサブスクリプション キーを取得する

Speech API は、Cognitive Services (以前の Project Oxford) の一部です。 無料試用版のサブスクリプション キーは、[Cognitive Services サブスクリプション](https://azure.microsoft.com/try/cognitive-services/) ページから取得できます。 Speech API を選択したら、**[Get API Key]**(API キーの取得) を選択してキーを取得します。 プライマリ キーおよびセカンダリ キーを返します。 両方のキーが同じクォータに関連付けられているため、どちらのキーでも使用できます。

"*意図を使った認識機能*" を使用する場合は、[Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) にもサインアップする必要があります。

> [!IMPORTANT]
> * サブスクリプション キーを取得します。 Speech クライアント ライブラリを使用するには、まず[サブスクリプション キー](https://azure.microsoft.com/try/cognitive-services/)を取得する必要があります。
>
> * サブスクリプション キーを使用します。 指定された iOS サンプル アプリケーションを使って、ファイル Samples/SpeechRecognitionServerExample/settings.plist をサブスクリプション キーに更新する必要があります。 詳しくは、「[サンプルをビルドして実行する](#build-and-run-samples)」をご覧ください。

## <a name="use-the-speech-client-library"></a>Speech クライアント ライブラリを使用する

クライアント ライブラリを XCode プロジェクトに追加するには、こちらの[指示](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library)に従います。

iOS 用のクライアント ライブラリ リファレンスを見つけるには、この [Web ページ](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)を参照してください。

## <a name="build-and-run-samples"></a>サンプルをビルドして実行する

サンプルをビルドして実行する方法については、こちらの [README ページ](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample)をご覧ください。

## <a name="samples-explained"></a>サンプルの説明

### <a name="create-recognition-clients"></a>認識クライアントを作成する

サンプル内の次のコードは、ユーザー シナリオに基づいて認識クライアント クラスを作成する方法を示したものです。

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

クライアント ライブラリには、音声認識の一般的なシナリオに対応した、事前実装済みの認識クライアント クラスが用意されています。

* `DataRecognitionClient`:PCM データ (たとえば、ファイルや音声ソース) を使った音声認識です。 データはバッファーに分割され、各バッファーが Speech Service に送信されます。 バッファーへの変更は加えられないので、ユーザーは必要に応じて独自の無音検出を適用できます。 データが WAV ファイルから提供された場合は、データをファイルからサーバーに直接送信できます。 生データ (たとえば、Bluetooth 経由で送られる音声など) がある場合は、まず形式ヘッダーをサーバーに送信し、その後データを送信します。
* `MicrophoneRecognitionClient`:マイクからの音声を使った音声認識です。 マイクの電源が入っていて、マイクからのデータが音声認識サービスに送信されることを確認してください。 マイクのデータが認識サービスへ送信される前に、組み込みの "Silence Detector" がデータに適用されます。
* `DataRecognitionClientWithIntent` と `MicrophoneRecognitionClientWithIntent`: これらのクライアントからは、認識テキストに加えて、話者の意図に関する構造化された情報が返されます。この情報は、アプリケーションでさらなるアクションを実行するために使用できます。 意図検出を使用するには、まず [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) を使用してモデルをトレーニングする必要があります。

### <a name="recognition-language"></a>認識言語

`SpeechRecognitionServiceFactory` を使用してクライアントを作成する際には、言語を選択する必要があります。 Speech Service でサポートされる言語の一覧については、「[Supported languages (サポートされる言語)](../API-Reference-REST/supportedlanguages.md)」をご覧ください。

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

また、`SpeechRecognitionServiceFactory` を使ってクライアントを作成する際には、`SpeechRecognitionMode` も指定する必要があります。

* `SpeechRecognitionMode_ShortPhrase`:発話時間は最大 15 秒です。 データがサービスに送信されると、クライアントは複数の部分結果と、複数の n-best 選択肢を持った 1 つの最終結果を受け取ります。
* `SpeechRecognitionMode_LongDictation`:発話時間は最大 2 分です。 データがサービスに送信されると、クライアントはサーバーが特定した文の区切り位置に基づいて、複数の部分結果と複数の最終結果を受け取ります。

### <a name="attach-event-handlers"></a>イベント ハンドラーをアタッチする

作成したクライアントには、さまざまなイベント ハンドラーをアタッチできます。

* **部分結果イベント**:このイベントは、ユーザーが言おうとしていることを Speech Service が予測するたびに呼び出されます。これは、ユーザーの発話 (`MicrophoneRecognitionClient` を使用した場合) やデータの送信 (`DataRecognitionClient` を使用した場合) が完了する前であっても呼び出されます。
* **エラー イベント**:サービスがエラーを検出したときに呼び出されます。
* **意図イベント**:最終的な認識結果が解析され、構造化された JSON 意図が生成された後、"WithIntent" クライアント (ShortPhrase モードの場合のみ) で呼び出されます。
* **結果イベント**:
  * `SpeechRecognitionMode_ShortPhrase` モードでは、このイベントは発話の完了後に呼び出され、n-best 結果を返します。
  * `SpeechRecognitionMode_LongDictation` モードでは、サービスによって特定された文の区切り目に基づいて、イベント ハンドラーが複数回呼び出されます。
  * **各 n-best 選択肢について**、信頼度の値と、複数形式の認識済みテキストが返されます。 詳しくは、[出力形式](../Concepts.md#output-format)に関するページをご覧ください。

## <a name="related-topics"></a>関連トピック

* [iOS 用クライアント ライブラリ リファレンス](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Android 上の Java での Microsoft 音声認識または意図の利用](GetStartedJavaAndroid.md)
* [JavaScript での Microsoft Speech API の利用](GetStartedJSWebsockets.md)
* [REST を介した Microsoft Speech API の利用](GetStartedREST.md)
