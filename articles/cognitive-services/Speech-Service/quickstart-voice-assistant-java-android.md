---
title: クイック スタート:カスタム音声アシスタント、Java (Android) - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用して Android 上で Java を使用して音声アシスタント アプリケーションを作成する方法について説明します
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: travisw
ms.openlocfilehash: 70031513d6f120e539e80d6befad4bf9320fb9c4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975960"
---
# <a name="quickstart-create-a-voice-assistant-in-java-on-android-by-using-the-speech-sdk"></a>クイック スタート:Speech SDK を使用して Android 上で Java を使用して音声アシスタントを作成する

[音声変換](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)と[テキスト読み上げ](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android)のクイックスタートも使用できます。

この記事では、[Speech SDK](speech-sdk.md) を使用して Java for Android で音声アシスタントを構築します。 このアプリケーションは、[Direct Line Speech チャネル](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)で既に作成および構成されたボットに接続します。 その後、ボットに音声要求を送信し、音声に対応した応答アクティビティを提供します。

このアプリケーションは、Speech SDK Maven パッケージと Android Studio 3.3 を使用して構築されています。 現在 Speech SDK は、32/64 ビットの ARM プロセッサを搭載した Android デバイスおよび Intel x86/x64 互換のプロセッサを搭載した Android デバイスと互換性があります。

> [!NOTE]
> Speech Devices SDK および Roobo デバイスについては、[Speech Devices SDK](speech-devices-sdk.md) を参照してください。

## <a name="prerequisites"></a>前提条件

- Speech サービス用の Azure サブスクリプション キー。 [無料で入手する](get-started.md)か、[Azure portal](https://portal.azure.com) 上に作成します。
- [Direct Line Speech チャネル](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)で構成された、以前に作成済みのボット
- [Android Studio](https://developer.android.com/studio/) v3.3 以降

    > [!NOTE]
    > [音声アシスタントをサポートしているリージョンの一覧](regions.md#voice-assistants)を参照し、ご使用のリソースがそれらのリージョンのいずれかにデプロイされていることを確認します。

## <a name="create-and-configure-a-project"></a>プロジェクトの作成と構成

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>ユーザー インターフェイスを作成する

このセクションでは、アプリケーション用の基本的なユーザー インターフェイス (UI) を作成します。 メイン アクティビティ `activity_main.xml` を開いて開始します。 基本的なテンプレートには、アプリケーションの名前を示すタイトル バーと、メッセージ "Hello world!" を示す `TextView` が含まれています。

次に、`activity_main.xml` の内容を次のコードに置き換えます。

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

この XML は、ボットと対話するシンプルな UI を定義します。

- `button` 要素は、クリックされると対話を開始し、`onBotButtonClicked` メソッドを呼び出します。
- `recoText` 要素は、ユーザーがボットに話したときの音声テキスト変換の結果を表示します。
- `activityText` 要素は、ボットからの最新の Bot Framework アクティビティについての JSON ペイロードを表示します。

この時点で、UI のテキストおよびグラフィカル表現は次のようになります。

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>サンプル コードを追加する

1. `MainActivity.java` を開き、内容を次のコードに置き換えます。

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from speech subscription information
            BotFrameworkConfig config = BotFrameworkConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * `onCreate` メソッドには、マイクとインターネットのアクセス許可を要求するコードが含まれています。

   * メソッド `onBotButtonClicked` は上述の通りボタン クリック ハンドラーです。 ボタンを押すと、ボットとの 1 回の対話 ("ターン") がトリガーされます。

   * `registerEventListeners` メソッドは、`DialogServiceConnector` によって使用されるイベントと、受信アクティビティの基本的な処理を示します。

1. 同じファイルで、リソースに一致するように構成文字列を置き換えます。

    * `YourChannelSecret` を、ボットの Direct Line Speech チャネルのシークレットに置き換えます。

    * `YourSpeechSubscriptionKey` は、実際のサブスクリプション キーで置き換えてください。

    * `YourServiceRegion` を、サブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。Direct Line Speech では Speech サービス リージョンの一部のみが現在サポートされています。 詳細については、[リージョン](regions.md#voice-assistants)に関するページを参照してください。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. Android デバイスを開発用 PC に接続します。 デバイスで[開発モードと USB デバッグ](https://developer.android.com/studio/debug/dev-options)が有効なことを確認します。

1. アプリケーションをビルドするには、Ctrl + F9 キーを押すか、メニュー バーから **[ビルド]**  >  **[Make Project]\(プロジェクトの作成\)** を選択します。

1. アプリケーションを起動するには、Shift + F10 キーを押すか、 **[実行]**  >  **[Run 'app']\(アプリの実行\)** を選択します。

1. 表示された配置ターゲット ウィンドウで、Android デバイスを選択します。

   ![配置ターゲットの選択ウィンドウのスクリーンショット](media/sdk/qs-java-android-12-deploy.png)

アプリケーションとそのアクティビティを起動したら、ボタンをクリックしてボットとの対話を開始します。 対話中に変換されたテキストが表示され、ボットから受信した最新のアクティビティは、受信したときに表示されます。 ボットが音声による応答を提供するように構成されている場合、音声テキスト変換により自動的に再生されます。

![Android アプリケーションのスクリーンショット](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [基本ボットの作成とデプロイ](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>関連項目
- [音声アシスタントについて](voice-assistants.md)
- [Speech サービスのサブスクリプション キーを無料で取得する](get-started.md)
- [カスタム キーワード](speech-devices-sdk-create-kws.md)
- [Direct Line Speech をボットに接続する](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [GitHub で Java のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
