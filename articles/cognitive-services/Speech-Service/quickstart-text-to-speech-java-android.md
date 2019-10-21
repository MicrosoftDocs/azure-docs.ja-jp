---
title: クイック スタート:音声を合成する、Java (Android) - Speech Service
titleSuffix: Azure Cognitive Services
description: Android 上で Java と Speech SDK を使用して音声を合成する方法について説明します
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 17fb0c1a4ba5495506ceb658eb61d0ac2959caf8
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803763"
---
# <a name="quickstart-synthesize-speech-in-java-on-android-by-using-the-speech-sdk"></a>クイック スタート:Android 上で Java と Speech SDK を使用して音声を合成する

[音声認識](quickstart-java-android.md)と[音声優先仮想アシスタント](quickstart-virtual-assistant-java-android.md)のクイックスタートも利用できます。

この記事では、Cognitive Services Speech SDK を使用してテキストから音声を合成する Android 向け Java アプリケーションを開発する方法について説明します。
このアプリケーションは、Speech SDK Maven パッケージ (バージョン 1.7.0) と Android Studio 3.3 が基になっています。
現在 Speech SDK は、32/64 ビットの ARM プロセッサを搭載した Android デバイスおよび Intel x86/x64 互換のプロセッサを搭載した Android デバイスと互換性があります。

> [!NOTE]
> Speech Devices SDK および Roobo デバイスについては、[Speech Devices SDK](speech-devices-sdk.md) を参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、Speech Services サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳細については、「[Speech Service を無料で試す](get-started.md)」を参照してください。

## <a name="create-and-configure-a-project"></a>プロジェクトの作成と構成

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>ユーザー インターフェイスを作成する

アプリケーション用の基本的なユーザー インターフェイスを作成します。 メイン アクティビティのレイアウト `activity_main.xml` を編集します。 初期状態のレイアウトには、アプリケーションの名前が表示されるタイトル バーと、"Hello World!" というテキストが表示される TextView が含まれています。

1. TextView 要素をクリックします。 右上隅にあるその ID 属性を `outputMessage` に変更し、下の画面にドラッグします。 そのテキストを削除します。

1. `activity_main.xml` ウィンドウの左上のパレットから、テキストの上の何もないスペースにボタンをドラッグします。

1. 右側のボタンの属性で、`onClick` 属性の値に「`onSpeechButtonClicked`」と入力します。 ボタン イベントを処理するこの名前を持つメソッドを記述します。  右上隅にあるその ID 属性を `button` に変更します。

1. [Plain Text]\(プレーンテキスト\) をボタンの上のスペースにドラッグします。その ID 属性を `speakText` に変更し、テキスト属性を `Hi there!` に変更します。

1. レイアウトの制約を推測したい場合、デザイナーの上部にある魔法の杖のアイコンを使用します。


    ![魔法の杖アイコンのスクリーンショット](media/sdk/qs-java-android-10-infer-layout-constraints.png)

この時点で、UI のテキストおよびグラフィカル表現は次のようになります。

![](media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. ソース ファイル `MainActivity.java` を開きます。 このファイル内のすべてのコードを次の内容に置き換えます。

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * メソッド `onSpeechButtonClicked` は上述の通りボタン クリック ハンドラーです。 ボタンを押下すると、音声合成がトリガーされます。

1. 同じファイル内で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. Android デバイスを開発用 PC に接続します。 デバイスで[開発モードと USB デバッグ](https://developer.android.com/studio/debug/dev-options)が有効なことを確認します。 または、[Android エミュレーター](https://developer.android.com/studio/run/emulator)を作成します。

1. アプリケーションをビルドするには、Ctrl + F9 キーを押すか、メニュー バーから **[ビルド]**  >  **[Make Project]\(プロジェクトの作成\)** を選択します。

1. アプリケーションを起動するには、Shift + F10 キーを押すか、 **[実行]**  >  **[Run 'app']\(アプリの実行\)** を選択します。

1. 表示された配置ターゲット ウィンドウで、Android デバイスまたはエミュレーターを選択します。

   ![配置ターゲットの選択ウィンドウのスクリーンショット](media/sdk/qs-java-android-12-deploy.png)

テキストを入力し、アプリケーションのボタンを押して、音声合成セクションを開始します。 合成された音声が既定のスピーカーから聞こえ、画面に `speech synthesis succeeded` の情報が表示されます。

![Android アプリケーションのスクリーンショット](media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で Java のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音声フォントをカスタマイズする](how-to-customize-voice-font.md)
- [音声サンプルを録音する](record-custom-voice-samples.md)
