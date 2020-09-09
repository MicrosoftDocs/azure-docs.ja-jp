---
title: リアルタイムでの会話の文字起こし (プレビュー) - Speech Service
titleSuffix: Azure Cognitive Services
description: リアルタイムでの会話の文字起こしを Speech SDK とともに使用する方法を説明します。 C++、C#、および Java で使用できます。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.custom: devx-track-csharp
ms.openlocfilehash: a425c75dfd57f0d5f9c98b73d8b351972b70703a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918657"
---
# <a name="real-time-conversation-transcription-preview"></a>リアルタイムでの会話の文字起こし (プレビュー)

Speech SDK の **ConversationTranscriber** API を使用すると、会議やその他の会話を文字起こしして、`PullStream` または `PushStream` を使用し、音声を Speech Service にストリーミングすることによって、複数の参加者を追加、削除、または識別することができます。 このトピックでは、Speech SDK (バージョン 1.8.0 以降) で音声テキスト変換を使用する方法について理解している必要があります。 詳細については、「[Speech Service とは](overview.md)」を参照してください。

## <a name="limitations"></a>制限事項

- ConversationTranscriber API は、Windows、Linux、Android 上で C++、C#、および Java 向けにサポートされています。
- 現時点では、"en-US" と "zh-CN" 言語に対応しており、_centralus_ および _eastasia_ のリージョンで利用できます。
- 再生参照ストリームを使用する、7 つのマイクの循環マルチマイク配列が必要です。 このマイク配列は、[Microsoft の仕様](https://aka.ms/sdsdk-microphone)を満たす必要があります。
- [Speech Devices SDK](speech-devices-sdk.md) には、会話の文字起こしを実行する適切なデバイスやサンプル アプリが提供されています。

## <a name="optional-sample-code-resources"></a>オプションのサンプル コード リソース

Speech Device SDK には、8 チャネルを使用したリアルタイム オーディオ キャプチャ用の Java のサンプル コードが提供されています。

- [ROOBO デバイス サンプル コード](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
- [Azure Kinect Dev Kit サンプル コード](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>前提条件

Speech Service のサブスクリプション。 [無料の Azure アカウント](https://azure.microsoft.com/free/cognitive-services/)をお持ちでない場合は、作成することができます。

## <a name="create-voice-signatures"></a>声紋を作成する

最初の手順では、話者識別のために、会話の参加者の声紋を作成します。

### <a name="audio-input-requirements"></a>オーディオ入力の要件

- 声紋を作成するための入力音声 wave ファイルは、16 ビットのサンプル、16 kHz のサンプル レート、およびシングル チャンネル (モノラル) 形式である必要があります。
- 各オーディオ サンプルの推奨される長さは、30 秒間から 2 分間です。

### <a name="sample-code"></a>サンプル コード

次の例は、C# で [REST API を使用](https://aka.ms/cts/signaturegenservice)して声紋を作成するための 2 つの異なる方法を示しています。 "YourSubscriptionKey" を実際の情報に、"speakerVoice.wav" を wave ファイル名に、`{region}` と "YourServiceRegion" (_centralus_ または _eastasia_) をご自分のリージョンに置き換える必要がある点にご注意ください。

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>会話の文字起こし

次のサンプル コードは、3 人の話者の会話をリアルタイムで文字起こしする方法を示しています。 各話者の声紋が上記のように既に作成されていることを前提としています。 SpeechConfig オブジェクトの作成時に、"YourSubscriptionKey" および "YourServiceRegion" を実際の情報に置き換えます。

サンプル コードの特徴を次に示します。

- `Guid.NewGuid()` を使用して作成された会議の識別子を使用して `SpeechConfig` オブジェクトから `Conversation` オブジェクトを作成する
- `ConversationTranscriber` オブジェクトを作成し、`JoinConversationAsync()` を使用して会話に参加して文字起こしを開始する
- 関心のあるイベントを登録する
- Conversation オブジェクトを使用して、参加者を会話に追加または削除する
- オーディオのストリーミング
- Speech SDK バージョン 1.9.0 以降では、`int` と `string` の値型は両方とも、音声署名のバージョン フィールドでサポートされています。

文字起こしと話者の識別子は、登録されたイベントで返されます。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber(audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [非同期での会話の文字起こし](how-to-async-conversation-transcription.md)
