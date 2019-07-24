---
title: チュートリアル:Translator Speech API (C#)
titleSuffix: Azure Cognitive Services
description: Translator Speech API を使用して、テキストをリアルタイムに翻訳します。
services: cognitive-services
author: v-jerkin
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: tutorial
ms.date: 3/5/2018
ms.author: v-jerkin
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 81ee3c7da43aa4d81838c90f4e8f41d114da72b5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57900830"
---
# <a name="tutorial-translator-speech-application-in-c"></a>チュートリアル:C# での Translator Speech アプリケーション

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

このチュートリアルでは、Translator Speech API (Azure Cognitive Services の一部) を使用した、対話型音声翻訳ツールについて説明します。 学習内容:

> [!div class="checklist"]
> * サービスでサポートされている言語の一覧を要求する
> * 音声をキャプチャし、サービスに転送する
> * 音声の翻訳を受信し、テキストとして表示する
> * 必要に応じて、翻訳の音声 (テキスト読み上げ) バージョンを再生する

このアプリケーションの Visual Studio ソリューション ファイルは、[GitHub から入手できます](https://github.com/MicrosoftTranslator/SpeechTranslator)。

## <a name="prerequisites"></a>前提条件

このチュートリアルを行うには、Community Edition を含む Visual Studio 2017 のいずれかのエディションが必要です。

Visual Studio ソリューションでは、アプリケーションのインストーラーもビルドされます。 この機能をサポートするには、[WiX Toolset](http://wixtoolset.org/) と [WiX Toolset Visual Studio Extension](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) が必要です。

また、Translator Speech サービスのサブスクリプション キーも必要です (Microsoft Azure ダッシュ ボードから取得できます)。 ユーザーは、毎月最大 10 時間の発話を無料で翻訳できる、無料価格レベルを利用できます。 このチュートリアルでは、この価格レベルで十分です。

また、サード パーティの [JSON.NET ライブラリ](https://www.newtonsoft.com/json) (提供元は Newtonsoft) も必要になります。 このアセンブリは、Visual Studio のオプションで [パッケージの復元] チェック ボックスが両方とも有効になっている場合、NuGet によって自動的にインストールされます。

## <a name="trying-the-translation-app"></a>翻訳アプリの使用

Visual Studio で Speech Translator ソリューション (`SpeechTranslator.sln`) を開いたら、F5 キーを押してアプリケーションをビルドし、起動します。  プログラムのメイン ウィンドウが表示されます。

![[Speech Translator のメイン ウィンドウ]](media/speech-translator-main-window.png)

初回実行時には、**[設定]** メニューから **[アカウント設定]** を選択して、次のようなウィンドウを開きます。

![[Speech Translator のメイン ウィンドウ]](media/speech-translator-settings-window.png)

このウィンドウで、Translator Speech のサブスクリプション キーを貼り付け、**[保存]** をクリックします。 キーはアプリの実行ごとに保存されます。

メイン ウィンドウに戻り、使用する音声入出力デバイスを選択して、どの言語からどの言語に翻訳するかを選択します。 翻訳の音声を再生する場合は、**[TTS]**(テキスト読み上げ) オプションをオンにします。 発話中に部分的な予測翻訳を表示するには、**[Partial Results]\(部分結果\)** オプションをオンにします。

最後に、**[開始]** をクリックして翻訳を開始します。 翻訳する言葉を発話し、認識されたテキストとその翻訳がウィンドウに表示されるのを確認します。 TTS オプションをオンにした場合は、翻訳の音声も再生されます。

## <a name="obtaining-supported-languages"></a>サポートされている言語の取得

このドキュメントの作成時点で、Translator Speech サービスではテキスト翻訳の対象として 60 以上の言語をサポートしています。 音声翻訳については、サポートされている言語の数はこれより少なくなっています。 これらの言語については、トランスクリプション (音声認識) とテキスト読み上げ (音声合成) の両方のサポートが必要となります。

つまり、音声翻訳の場合は、トランスクリプション用にサポートされている言語をソース言語にする必要があります。 出力言語は、結果をテキストで出力するのであれば、テキスト翻訳用にサポートされている任意の言語を指定できます。 音声出力する場合は、テキスト読み上げ用にサポートされている言語しか翻訳できません。

Microsoft では、新しい言語のサポートを不定期に追加する場合があります。 そのため、サポートされている言語のナレッジをアプリケーションでハード コードすることはしないでください。 代わりに、Translator Speech API では、サポートされている言語を実行時に取得できる、Languages エンドポイントが提供されます。 ユーザーは、次の中から 1 つ以上の言語一覧を受信できます。

| | |
|-|-|
|`speech`|音声トランスクリプション用にサポートされていてい言語です。 音声翻訳のソース言語として指定できます。|
|`text`|テキストからテキストへの翻訳用にサポートされる言語です。 テキスト出力が使用される場合に、音声翻訳のターゲット言語として指定できます。|
|`tts`|音声合成用にサポートされている音声です (それぞれ特定の言語に関連付けられています)。 テキスト読み上げが使用される場合に、音声翻訳のターゲット言語として指定できます。 1 つの言語が複数の音声によってサポートされる場合もあります。|

Languages エンドポイントにはサブスクリプション キーは必要なく、その使用はクォータに対してカウントされません。 URI は `https://dev.microsofttranslator.com/languages` で、結果は JSON 形式で返されます。

`MainWindow.xaml.cs` 内の `UpdateLanguageSettingsAsync()` メソッド (下記) は、Languages エンドポイントを呼び出して、サポートされている言語の一覧を取得します。

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

このメソッドは、まず Languages エンドポイントへの HTTP 要求を構築し、3 つの言語一覧 (`text`、 `speech`、および `tts`) をすべて要求します。

Languages エンドポイントは、要求の `Accept-Languages` ヘッダーを使って、言語名の表示言語を決定します。 たとえば、英語の話者が "German" と呼ぶ言語は、ドイツ語では "Deutsch"、スペイン語では "Alemán" と呼ばれますが、これらの違いが言語の一覧に反映されます。 このヘッダーにはシステムの既定の言語が使用されます。

要求が送信され、JSON の応答が受信されると、応答が解析され、内部データ構造が生成されます。 これらの構造体は、ソース言語とターゲット言語を選択するメニューの作成に使用されます。

使用可能な音声はユーザーが選択した言語によって決まるため、この時点では、まだ音声メニューを設定することはできません。 代わりに、各言語に対して使用可能な音声が、後の使用のために格納されます。 `ToLanguage_SelectionChanged` ハンドラー (同じソース ファイル内にあります) は、後でユーザーがターゲット言語を選択したときに、`UpdateVoiceComboBox()` を呼び出して音声メニューを更新します。

なお、ユーザーがアプリケーションを初めて実行する際には、ターゲット言語がランダムに選択されます (メニュー設定はセッションごとに格納されます)。

## <a name="authenticating-requests"></a>要求の認証

Microsoft Translator Speech サービスに対する認証を行うには、接続要求内の `Ocp-Apim-Subscription-Key` の値として、ヘッダー内の Azure サブスクリプション キーを送信する必要があります。

## <a name="translation-overview"></a>翻訳の概要

Translate API (Websocket エンドポイント `wss://dev.microsofttranslator.com/speech/translate`) は、翻訳する音声を、モノラル、16 kHz、16 ビット符号付きの WAVE 形式で受け付けます。 サービスは、認識されたテキストと翻訳されたテキストの両方を含んだ、1 つ以上の JSON 応答を返します。 テキスト読み上げが要求された場合は、音声ファイルが送信されます。

ユーザーは、[Microphone/File Input]\(マイク/ファイル入力\) メニューを使って音声ソースを選択します。 音声は、音声デバイス (マイクなど) または `.WAV` ファイルから取得できます。

`StartListening_Click` メソッドは、ユーザーが [開始] ボタンをクリックしたときに呼び出されます。 呼び出されると、このイベント ハンドラーが `Connect()` を呼び出して、サービス API エンドポイントに音声を送信するプロセスを開始します。 `Connect()` メソッドは、次のタスクを実行します。


> [!div class="checklist"]
> * メイン ウィンドウからユーザーの設定を取得し、それらを検証する
> * 音声の入力ストリームと出力ストリームを初期化する
> * `ConnectAsync()` を呼び出して、残りの作業を処理する

`ConnectAsync()` は次の作業を処理します。

> [!div class="checklist"]
> * ヘッダー `Ocp-Apim-Subscription-Key` 内の Azure サブスクリプション キーを使用して認証を行う
> * `SpeechClient` インスタンス (`SpeechClient.cs` 内にあります) を作成して、サービスと通信する
> * `TextMessageDecoder` インスタンスと `BinaryMessageDecoder` インスタンス (`SpeechResponseDecoder.cs` を参照) を初期化して、応答を処理する
> * `SpeechClient` インスタンスを通じて、Translator Speech サービスに音声を送信する
> * 翻訳の結果を受信し、処理する

`SpeechClient` では、次の作業のみが処理されます。

> [!div class="checklist"]
> * Translator Speech サービスへの WebSocket 接続を確立する
> * ソケットを通じて音声データを送信し、応答を受信する

## <a name="a-closer-look"></a>詳細

翻訳要求を実行するために、アプリケーションの各部がどのように連携するのかがわかってきたところで、 次は、特に重要な箇所に焦点を当てながら、一部のコードを見ていきましょう。

次に示すのは、音声ストリームの設定に関連する、`Connect()` の一部です。

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };

    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

`Connect()` の大部分は、翻訳のオプションを保持するための、`SpeechClientOptions` インスタンス (`SpeechClientOptions.cs` を参照) の作成に関連しています。 オプションには、サービスへの接続に必要な情報 (認証キーやホスト名など) や、翻訳に使用する機能が含まれます。 ここにあるフィールドは、 [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference) によって公開される、ヘッダー フィールドと HTTP パラメーターにマップされます。

また、`Connect()` では、翻訳する音声のソースとなる音声入力デバイス (変数 `sampleProvider`) も作成され、初期化されます。 このデバイスは、マイクなどのハードウェア入力デバイスか、WAVE 音声データを含んだファイルのいずれかになります。

次に示すのは、`speechClient` クラスをインスタンス化し、テキストとサービスからバイナリ応答を処理するための匿名関数をフックアップする、`ConnectAsync()` メソッドです。

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);

    TextMessageDecoder textDecoder;

    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);

    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected.
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

認証の後、このメソッドは `SpeechClient` インスタンスを作成します。 `SpeechClient` クラス (`SpeechClient.cs` 内) は、バイナリ データとテキスト データを受信するとイベント ハンドラーを呼び出します。 接続が失敗するか切断された場合には、追加のハンドラーが呼び出されます。

バイナリ データは、TTS がオンの場合にサービスによって送信される音声 (テキスト読み上げ出力) です。 テキスト データは、発話されたテキストの翻訳 (一部または全体) です。 インスタンス化の後、メソッドはそれらのメッセージを処理するための関数をフックアップします (音声の場合は後で再生できるように音声を格納し、テキストの場合はウィンドウにテキストを表示します)。

## <a name="next-steps"></a>次の手順

このコード サンプルは、Translator Speech API の使用方法を示すための高機能アプリケーションです。 そのため、理解するべき可変要素が多数存在します。 ここでは、特に重要な部分について説明しました。 その他の部分については、Visual Studio でいくつかのブレークポイントを設定し、翻訳プロセスのしくみを見ていくことで効果的に理解できます。 サンプル アプリケーションについて理解できたら、自分のアプリケーションで実際に Translator Speech サービスを利用してみましょう。

> [!div class="nextstepaction"]
> [Microsoft Translator Speech API リファレンス](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
