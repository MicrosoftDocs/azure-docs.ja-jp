---
title: Android 上の Java での Microsoft Speech Recognition API の利用 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Microsoft Speech API を使用して、発話された音声をテキストに変換する Android アプリケーションを開発します。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 147042e300e629dd7e354d4e9079cc4855a8146c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547028"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>クイック スタート:Android 上の Java で Bing Speech 認識 API を使用する

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Speech API を使用すると、クラウドベースの Bing Speech サービスを使って音声をテキストに変換する、Android アプリケーションを開発できます。 この API はリアルタイム ストリーミングをサポートしているので、アプリケーションは音声をサービスへ送信するのと同時に、部分的な認識結果を非同期で受け取ることができます。

この記事では、Android 用の Speech クライアント ライブラリを使用し、Android デバイス用の Java で音声認識アプリケーションを開発する方法について、サンプル アプリケーションを使って説明します。

## <a name="prerequisites"></a>前提条件

### <a name="platform-requirements"></a>プラットフォームの要件

サンプルは、Windows 用の [Android Studio](https://developer.android.com/sdk/index.html) を使って Java で開発されました。

### <a name="get-the-client-library-and-sample-application"></a>クライアント ライブラリとサンプル アプリケーションを入手する

Android 用の Speech クライアント ライブラリとサンプルは、[Android 用 Speech クライアント SDK](https://github.com/microsoft/cognitive-speech-stt-android) から入手できます。 ビルド可能なサンプルは、samples/SpeechRecoExample ディレクトリにあります。 アプリに必要な 2 つのライブラリは、armeabi フォルダーと x86 フォルダーの SpeechSDK/libs にあります。 libandroid_platform.so ファイルのサイズは 22 MB ですが、デプロイ時に 4 MB に減ります。

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Speech API をサブスクライブし、無料試用版のサブスクリプション キーを取得する

Speech API は、Cognitive Services (以前の Project Oxford) の一部です。 無料試用版のサブスクリプション キーは、[Cognitive Services サブスクリプション](https://azure.microsoft.com/try/cognitive-services/) ページから取得できます。 Speech API を選択したら、**[Get API Key]**(API キーの取得) を選択してキーを取得します。 プライマリ キーおよびセカンダリ キーを返します。 両方のキーが同じクォータに関連付けられているため、どちらのキーでも使用できます。

"*意図を使った認識機能*" を使用する場合は、[Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) にもサインアップする必要があります。

> [!IMPORTANT]
>* サブスクリプション キーを取得します。 Speech クライアント ライブラリを使用するには、まず[サブスクリプション キー](https://azure.microsoft.com/try/cognitive-services/)を取得する必要があります。
>
>* サブスクリプション キーを使用します。 提供された Android サンプル アプリケーションで、samples/SpeechRecoExample/res/values/strings.xml ファイルをサブスクリプション キーで更新します。 詳しくは、「[サンプルをビルドして実行する](#build-and-run-samples)」をご覧ください。

## <a name="use-the-speech-client-library"></a>Speech クライアント ライブラリを使用する

アプリケーションでクライアント ライブラリを使用するには、こちらの[説明](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library)に従います。

Android 用のクライアント ライブラリ リファレンスは、[Android 用 Speech クライアント SDK](https://github.com/microsoft/cognitive-speech-stt-android) の docs フォルダーにあります。

## <a name="build-and-run-samples"></a>サンプルをビルドして実行する

サンプルをビルドして実行する方法については、こちらの [README ページ](https://github.com/microsoft/cognitive-speech-stt-android#the-sample)をご覧ください。

## <a name="samples-explained"></a>サンプルの説明

### <a name="create-recognition-clients"></a>認識クライアントを作成する

次のサンプル内のコードは、ユーザー シナリオに基づいて認識クライアント クラスを作成する方法を示したものです。

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

クライアント ライブラリには、音声認識の一般的なシナリオに対応した、事前実装済みの認識クライアント クラスが用意されています。

* `DataRecognitionClient`:PCM データ (たとえば、ファイルや音声ソース) を使った音声認識です。 データはバッファーに分割され、各バッファーが Speech Service に送信されます。 バッファーへの変更は加えられないので、ユーザーは必要に応じて独自の無音検出を適用できます。 データが WAV ファイルから提供された場合は、データをファイルから Speech Service に直接送信できます。 生データ (たとえば、Bluetooth 経由で送られる音声など) がある場合は、まず形式ヘッダーを Speech Service に送信し、その後データを送信します。
* `MicrophoneRecognitionClient`:マイクからの音声を使った音声認識です。 マイクの電源が入っていて、マイクからのデータが音声認識サービスに送信されることを確認してください。 マイクのデータが認識サービスへ送信される前に、組み込みの "Silence Detector" がデータに適用されます。
* `DataRecognitionClientWithIntent` と `MicrophoneRecognitionClientWithIntent`: これらのクライアントからは、認識テキストに加えて、話者の意図に関する構造化された情報が返されます。この情報は、アプリケーションでさらなるアクションを実行するために使用できます。 意図検出を使用するには、まず [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) を使用してモデルをトレーニングする必要があります。

### <a name="recognition-language"></a>認識言語

`SpeechRecognitionServiceFactory` を使用してクライアントを作成する際には、言語を選択する必要があります。 Speech Service でサポートされる言語の一覧については、「[Supported languages (サポートされる言語)](../API-Reference-REST/supportedlanguages.md)」をご覧ください。

### `SpeechRecognitionMode`

また、`SpeechRecognitionServiceFactory` を使ってクライアントを作成する際には、`SpeechRecognitionMode` も指定する必要があります。

* `ShortPhrase`:発話時間は最大 15 秒です。 データがサービスに送信されると、クライアントは複数の部分結果と、複数の n-best 選択肢を持った 1 つの最終結果を受け取ります。
* `LongDictation`:発話時間は最大 2 分です。 データがサービスに送信されると、クライアントはサービスが特定した文の区切り位置に基づいて、複数の部分結果と複数の最終結果を受け取ります。

### <a name="attach-event-handlers"></a>イベント ハンドラーをアタッチする

作成したクライアントには、さまざまなイベント ハンドラーをアタッチできます。

* **部分結果イベント**:このイベントは、ユーザーが言おうとしていることを Speech Service が予測するたびに呼び出されます。これは、ユーザーの発話 (`MicrophoneRecognitionClient` を使用した場合) やデータの送信 (`DataRecognitionClient` を使用した場合) が完了する前であっても呼び出されます。
* **エラー イベント**:サービスがエラーを検出したときに呼び出されます。
* **意図イベント**:最終的な認識結果が解析され、構造化された JSON 意図が生成された後、"WithIntent" クライアント (`ShortPhrase` モードの場合のみ) で呼び出されます。
* **結果イベント**:
  * `ShortPhrase` モードでは、このイベントは発話の完了後に呼び出され、n-best 結果を返します。
  * `LongDictation` モードでは、サービスによって特定された文の区切り目に基づいて、イベント ハンドラーが複数回呼び出されます。
  * **各 n-best 選択肢について**、信頼度の値と、複数形式の認識済みテキストが返されます。 詳しくは、[出力形式](../Concepts.md#output-format)に関するページをご覧ください。

## <a name="related-topics"></a>関連トピック

* [Android 用クライアント ライブラリ リファレンス](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [.NET 上の Windows 用 C# での Microsoft Speech API の利用](GetStartedCSharpDesktop.md)
* [iOS 上の Objective-C での Microsoft Speech API の利用](Get-Started-ObjectiveC-iOS.md)
* [JavaScript での Microsoft Speech API の利用](GetStartedJSWebsockets.md)
* [REST を介した Microsoft Speech API の利用](GetStartedREST.md)
