---
title: クイック スタート:カスタムの音声優先仮想アシスタント (プレビュー)、Java (Windows、Linux) - Speech Services
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Cognitive Services Speech ソフトウェア開発キット (SDK) を Java コンソール アプリケーションで使用する方法を学習します。 クライアント アプリケーションを、Direct Line Speech チャネルを使用するように構成されている作成済みの Bot Framework ボットに接続して、音声優先仮想アシスタント エクスペリエンスを有効にする方法を学習します。
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: bidishac
ms.openlocfilehash: 78e80b276a13ee6e27fdf0515f2901fdeaa20c5d
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604921"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>クイック スタート:Speech SDK (Java) を使用して音声優先仮想アシスタントを作成する

クイック スタートは[音声変換](quickstart-java-jre.md)と[音声翻訳](quickstart-translate-speech-java-jre.md)にも使用できます。

この記事では、[Cognitive Services Speech SDK](speech-sdk.md) を使用して、Java コンソール アプリケーションを作成します。 アプリケーションは、Direct Line Speech チャネルを使用するように構成されている作成済みのボットに接続し、音声要求を送信し、音声応答アクティビティ (構成されている場合) を返します。 アプリケーションは、Speech SDK Maven パッケージと、Windows、Ubuntu Linux、または macOS 上の Eclipse Java IDE を使用して構築されます。 これは、64 ビットの Java 8 のランタイム環境 (JRE) で実行されます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* オペレーティング システム:Windows (64 ビット)、Ubuntu Linux 16.04/18.04 (64 ビット)、または macOS 10.13 以降
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) または [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Speech Services 用の Azure サブスクリプション キー。 [無料で入手する](get-started.md)か、[Azure portal](https://portal.azure.com) 上に作成します。
* Bot Framework バージョン 4.2 以上を使用して作成された事前構成済みのボット。 ボットは、音声入力を受信するために新しい "Direct Line Speech" チャネルに登録する必要があります。

    > [!NOTE]
    > Direct Line Speech (プレビュー) は、Speech Services のリージョンのサブセットでのみ現在利用できます。 [音声優先仮想アシスタントをサポートしているリージョンの一覧](regions.md#Voice-first virtual assistants)を参照し、ご使用のリソースがそれらのリージョンのいずれかにデプロイされていることを確認します。

Ubuntu 16.04 または 18.04 を実行している場合は、Eclipse を起動する前に、これらの依存関係がインストールされていることを確認してください。

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Windows (64 ビット版) を実行している場合は、お使いのプラットフォーム用の Microsoft Visual C++ 再頒布可能パッケージがインストールされていることを確認してください。
* [Visual Studio 2017 の Microsoft Visual C++ 再頒布可能パッケージをダウンロードする](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>省略可能:すぐに開始

このクイック スタートでは、音声対応のボットに接続する単純なクライアント アプリケーションを作成する方法を順を追って説明します。 このクイック スタートに使用されている、すぐにコンパイルできる完全なソース コードは、[Speech SDK のサンプル](https://aka.ms/csspeech/samples)のページの `quickstart` フォルダーに含まれています。

## <a name="create-and-configure-project"></a>プロジェクトの作成と構成

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

さらに、ログ記録を有効にするには、**pom.xml** ファイルを更新して次の依存関係を含めます。

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>サンプル コードを追加する

1. 新しい空のクラスを Java プロジェクトに追加するために、 **[File]**  >  **[New]**  >  **[Class]** の順に選択します。

1. **[New Java Class]** ウィンドウで、 **[Package]** フィールドに **speechsdk.quickstart** と入力し、 **[Name]** フィールドに **Main** と入力します。

   ![[New Java Class] ウィンドウのスクリーンショット](media/sdk/qs-java-jre-06-create-main-java.png)

1. 新しく作成された **Main** クラスを開き、`Main.java` ファイルの内容を次の開始コードに置き換えます。

    ```java
    package speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;
    import java.io.InputStream;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }

        private void playAudioStream(PullAudioOutputStream audio) {
            ActivityAudioStream stream = new ActivityAudioStream(audio);
            final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
            final AudioFormat format = new AudioFormat(
                    AudioFormat.Encoding.PCM_SIGNED,
                    audioFormat.getSamplesPerSecond(),
                    audioFormat.getBitsPerSample(),
                    audioFormat.getChannels(),
                    audioFormat.getFrameSize(),
                    audioFormat.getSamplesPerSecond(),
                    false);
            try {
                int bufferSize = format.getFrameSize();
                final byte[] data = new byte[bufferSize];

                SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
                SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
                line.open(format);

                if (line != null) {
                    line.start();
                    int nBytesRead = 0;
                    while (nBytesRead != -1) {
                        nBytesRead = stream.read(data);
                        if (nBytesRead != -1) {
                            line.write(data, 0, nBytesRead);
                        }
                    }
                    line.drain();
                    line.stop();
                    line.close();
                }
                stream.close();

            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }
    ```

1. **main** メソッドでは、最初に `DialogServiceConfig` を構成し、それを使用して `DialogServiceConnector` インスタンスを作成します。 これにより、ボットと対話するために Direct Line Speech チャネルに接続されます。 `AudioConfig` インスタンスは、音声入力のソースを指定するためにも使用されます。 この例では、`AudioConfig.fromDefaultMicrophoneInput()` により既定のマイクが使用されます。

    * 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。サブスクリプション キーは、[こちら](get-started.md)から入手できます。
    * 文字列 `YourServiceRegion` を、自分のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。
    * 文字列 `YourChannelSecret` を、Direct Line Speech チャネルのシークレットに置き換えます。

    > [!NOTE]
    > Direct Line Speech (プレビュー) は、Speech Services のリージョンのサブセットでのみ現在利用できます。 [音声優先仮想アシスタントをサポートしているリージョンの一覧](regions.md#voice-first-virtual-assistants)を参照し、ご使用のリソースがそれらのリージョンのいずれかにデプロイされていることを確認します。

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
    final String region = "YourServiceRegion"; // Your speech subscription service region. Note: only a subset of regions are currently supported
    final DialogServiceConfig botConfig = DialogServiceConfig.fromBotSecret(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a DialogServiceConnector instance
    final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
    ```

1. `DialogServiceConnector` は、ボットのアクティビティ、音声認識の結果、およびその他の情報を伝達するために、いくつかのイベントに依存しています。 次に、これらのイベント リスナーを追加します。

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        connector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
    connector.activityReceived.addEventListener((o, activityEventArgs) -> {
        final String act = activityEventArgs.getActivity().serialize();
            log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
            if (activityEventArgs.hasAudio()) {
                playAudioStream(activityEventArgs.getAudio());
            }
        });
    ```

1. `connectAsync()` メソッドを呼び出して、`DialogServiceConnector` を Direct Line Speech に接続します。 ボットをテストするには、`listenOnceAsync` メソッドを呼び出してマイクから音声入力を送信します。 さらに、`sendActivityAsync` メソッドを使用して、カスタム アクティビティをシリアル化された文字列として送信することもできます。 これらのカスタム アクティビティを使用すると、ボットが会話に使用する追加データを提供できます。

    ```java
    connector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    connector.listenOnceAsync();

    // connector.sendActivityAsync(...)
    ```

1. 変更を `Main` ファイルに保存します。

1. 応答の再生をサポートするために、getAudio() API から返される PullAudioOutputStream オブジェクトを処理しやすいように java InputStream に変換するクラスを追加します。 この ActivityAudioStream は、"Direct Line Speech チャネル" からの音声応答を処理する特別なクラスです。 再生の処理に必要なオーディオ形式の情報を取得するアクセサーが提供されます。そのために、 **[ファイル]**  >  **[新規]**  >  **[クラス]** を選択します。

1. **[New Java Class]\(新しい Java クラス\)** ウィンドウで、 **[パッケージ]** フィールドに **speechsdk.quickstart** と入力し、 **[名前]** フィールドに **ActivityAudioStream** と入力します。

1. 新しく作成した **ActivityAudioStream** クラスを開き、以下に示すコードに置き換えます。

    ```java
    package com.speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

    import java.io.IOException;
    import java.io.InputStream;


    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second. (16 kHz)
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format. (16 bits)
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample and the # channels
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

    ```

1. 変更を `ActivityAudioStream` ファイルに保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

F11 キーを押すか、 **[Run]**  >  **[Debug]** の順に選択します。
コンソールに "Say something" というメッセージが表示されます。この時点で、ボットが理解できる英語の語句や文を話しかけることができます。 音声は Direct Line Speech チャネルを介してボットに送信されます。ボットによって音声が認識および処理された後、その応答がアクティビティとして返されます。 ボットが応答として音声を返す場合、音声は `AudioPlayer` クラスを使用して再生されます。

![認識が成功した後のコンソール出力のスクリーンショット](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>次の手順

オーディオ ファイルから音声を読み取る方法など、追加のサンプルは GitHub で入手できます。

> [!div class="nextstepaction"]
> [基本ボットの作成とデプロイ](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>関連項目

- [音声優先仮想アシスタントの概要](voice-first-virtual-assistants.md)
- [Speech Services のサブスクリプション キーを無料で取得する](get-started.md)
- [カスタム ウェイク ワード](speech-devices-sdk-create-kws.md)
- [ボットを Direct Line Speech に接続する](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [GitHub で Java のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
