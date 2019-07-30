---
title: クイック スタート:Android 上で Speech Devices SDK を実行する - Speech Services
titleSuffix: Azure Cognitive Services
description: Android Speech Devices SDK を使用するための前提条件と手順です。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 7eea978456ed565f8fc58647dc548d1a7bc76b27
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606359"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>クイック スタート:Android 上で Speech Devices SDK サンプル アプリを実行する

このクイックスタートでは、Speech Devices SDK for Android を使用して音声対応製品を構築するか、またはそれを[会話の文字起こし](conversation-transcription-service.md)デバイスとして使用する方法について説明します。

このガイドでは、Speech Services リソースがある [Azure Cognitive Services](get-started.md) アカウントが必要になります。 アカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/try/cognitive-services/)を使用してサブスクリプション キーを取得できます。

サンプル アプリケーションのソース コードは Speech Devices SDK に付属しています。 [GitHub で入手する](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)こともできます。

## <a name="prerequisites"></a>前提条件

Speech Devices SDK の使用を開始する前に、次のことを行う必要があります。

* [開発キット](get-speech-devices-sdk.md)に付属している手順に従って、デバイスの電源を入れます。

* [Speech Devices SDK](https://aka.ms/sdsdk-download) の最新バージョンをダウンロードし、.zip を作業ディレクトリに解凍します。
   > [!NOTE]
   > Android-Sample-Release.zip ファイルには、Android サンプル アプリが含まれており、このクイックスタートでは、アプリが C:\SDSDK\Android-Sample-Release に抽出されることを前提としています

* [Speech Services 用の Azure サブスクリプション キー](get-started.md)を取得します。

* 会話の文字起こしを使用する予定がある場合は、[円形マイク デバイス](get-speech-devices-sdk.md)を使用する 必要があります。この機能は現在、"centralus" および "eastasia" リージョンの "en-US" と "zh-CN" でのみ使用できます。 会話の文字起こしを使用するには、それらのいずれかのリージョンの Speech キーが必要です。

* Speech Services を使用してユーザーの発話から意図 (またはアクション) を識別する場合は、[Language Understanding Service (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) サブスクリプションが必要です。 LUIS と意図認識の詳細については、 「[LUIS、C# を使って音声から意図を認識する](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)」 を参照してください。

    [LUIS の単純なモデルを作成](https://docs.microsoft.com/azure/cognitive-services/luis/)またはサンプルの LUIS モデルである LUIS-example.json. を使用できます。 サンプルの LUIS モデルは、[Speech Devices SDK のウンロード サイト](https://aka.ms/sdsdk-luis)から入手できます。 モデルの JSON ファイルを [LUIS ポータル](https://www.luis.ai/home)にアップロードするには、 **[新しいアプリのインポート]** を選択し、その JSON ファイルを選択します。

* [Android Studio](https://developer.android.com/studio/) と [Vysor](https://vysor.io/download/) を PC にインストールします。

## <a name="set-up-the-device"></a>デバイスを設定する

1. コンピューターで Vysor を起動します。

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. お使いのデバイスが **[Choose a device]** \(デバイスを選択する\) の下に表示されます。 デバイスの横にある **[View]\(ビュー\)** ボタンを選択します。

1. フォルダー アイコンを選択した後、 **[Settings]\(設定\)**  >  **[WLAN]** の順に選択して、ワイヤレス ネットワークに接続します。

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Wi-Fi システムへのデバイスの接続に関するポリシーが会社にある場合は、MAC アドレスを取得し、会社の Wi-Fi への接続方法を IT 部門に問い合わせる必要があります。
    >
    > 開発キットの MAC アドレスを検索するには、開発キットのデスクトップ上のファイル フォルダー アイコンを選択します。
    >
    >  ![Vysor ファイル フォルダー](media/speech-devices-sdk/qsg-10.png)
    >
    > **[設定]** を選択します。 "mac address" を検索し、 **[Mac アドレス]**  >  **[Advanced WLAN]** (高度な WLAN) の順に選択します。 ダイアログ ボックスの下部近くに表示される MAC アドレスを書き留めます。
    >
    > ![Vysor の MAC アドレス](media/speech-devices-sdk/qsg-11.png)
    >
    > 会社によっては、デバイスを Wi-Fi システムに接続しておくことができる時間が制限されている場合があります。 一定の日数が経過した後、Wi-Fi システムへの開発キットの登録を延長することが必要な場合があります。

## <a name="run-the-sample-application"></a>サンプル アプリケーションの実行

開発キットのセットアップを検証するには、サンプル アプリケーションをビルドしてインストールします。

1. Android Studio を起動します。

1. **[Open an existing Android Studio project]\(既存の Android Studio プロジェクトを開く\)** を選択します。

   ![Android Studio - 既存のプロジェクトを開く](media/speech-devices-sdk/qsg-5.png)

1. C:\SDSDK\Android-Sample-Release\example に移動します。 **[OK]** を選択してサンプル プロジェクトを開きます。

1. ソース コードに Speech サブスクリプション キーを追加します。 意図認識を試す場合は、[Language Understanding Service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) のサブスクリプション キーとアプリケーション ID も追加します。

   Speech および LUIS の場合、ユーザーの情報が MainActivity.java に配置されます。

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    会話の文字起こしを使用している場合は、conversation.java に Speech キーとリージョン情報も必要です。

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. 既定のウェイク ワード (キーワード) は "Computer" です。 用意されている別のウェイク ワード ("Machine"、"Assistant" など) を試すこともできます。 これらの代替ウェイク ワード用のリソース ファイルは、Speech Devices SDK の keyword フォルダーにあります。 たとえば、C:\SDSDK\Android-Sample-Release\keyword\Computer には、ウェイク ワード "Computer" に使用されるファイルが含まれています。

   > [!TIP]
   > [カスタム ウェイク ワードを作成する](speech-devices-sdk-create-kws.md)こともできます。

    新しいウェイク ワードを使用するには、`MainActivity.java` で下記の 2 行を更新し、ウェイク ワード パッケージをお使いのアプリにコピーします。 たとえば、ウェイク ワード パッケージ kws-machine.zip からウェイク ワード "Machine" を使用するには、次の操作を行います。

   * ウェイク ワード パッケージを、"C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\" フォルダーにコピーします。
   * 次に示すように、`MainActivity.java` でキーワードとパッケージ名を更新します。

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. マイク配列ジオメトリ設定が含まれる次の行を更新します。

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   次の表に、サポートされている値を示します。

   |変数|意味|使用できる値|
   |--------|-------|----------------|
   |`DeviceGeometry`|物理的なマイクの構成|円形開発キットでは `Circular6+1` |
   |||直線開発キットでは `Linear4`|
   |`SelectedGeometry`|ソフトウェアのマイクの構成|すべてのマイクを使用する円形開発キットでは `Circular6+1`|
   |||4 つのマイクを使用する円形開発キットでは `Circular3+1`|
   |||すべてのマイクを使用する線形開発キットでは `Linear4`|
   |||2 つのマイクを使用する線形開発キットでは `Linear2`|

1. アプリケーションをビルドするには、 **[実行]** メニューで **[アプリの実行]** を選択します。 **[Select Deployment Target]** \(配置ターゲットの選択\) ダイアログ ボックスが表示されます。

1. デバイスを選択し、 **[OK]** を選択してアプリケーションをデバイスに配置します。

    ![[Select Deployment Target]\(配置ターゲットの選択\) ダイアログ ボックス](media/speech-devices-sdk/qsg-7.png)

1. Speech Devices SDK のサンプル アプリケーションが起動し、次のオプションが表示されます。

   ![Speech Devices SDK のサンプル アプリケーションとオプション](media/speech-devices-sdk/qsg-8.png)

1. 新しい会話の文字起こしのデモをお試しください。 'セッションの開始' で文字起こしを開始します。 既定では、すべてのユーザーがゲストになります。 ただし、参加者の声紋がある場合は、デバイス上のファイル `/video/participants.properties` に入れることができます。 声紋を生成するには、[会話の文字起こし (SDK)](how-to-use-conversation-transcription-service.md) に関するページを参照してください。

   ![会話の文字起こしアプリケーションのデモ](media/speech-devices-sdk/qsg-15.png)

1. 実験

## <a name="troubleshooting"></a>トラブルシューティング

   Speech Device に接続できない場合。 コマンド プロンプト ウィンドウで次のコマンドを入力します。 これにより、デバイスの一覧が返されます。

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > このコマンドでは、Android Studio のインストールの一部である Android Debug Bridge `adb.exe` を使用します。 このツールは C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools にあります。 このディレクトリをパスに追加して、`adb` を呼び出しやすくすることができます。 それ以外の場合は、`adb` を呼び出すすべてのコマンドで adb.exe の完全なインストール パスを指定する必要があります。
   >
   > エラー `no devices/emulators found` が発生した場合は、USB ケーブルが接続されていること、および高品質のケーブルが使用されていることを確認してください。
   >

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [リリース ノートを確認します](devices-sdk-release-notes.md)
