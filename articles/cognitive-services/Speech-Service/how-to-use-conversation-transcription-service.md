---
title: Speech SDK で複数参加者の会話を文字起こしする - Speech Services
titleSuffix: Azure Cognitive Services
description: 会話の文字起こしを Speech SDK とともに使用する方法を説明します。 C++、C#、および Java で使用できます。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: jhakulin
ms.openlocfilehash: 215209a5b8e3ed46b25fbfa492c305785a9a0070
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606464"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Speech SDK で複数参加者の会話を文字起こしする

Speech SDK の **ConversationTranscriber** API を使用すると、会議や会話を文字起こしして、`PullStream` または `PushStream` を使用して音声を Speech Services にストリーミングすることによって、参加者を追加、削除、または識別することができます。

## <a name="limitations"></a>制限事項

* 会話の文字起こしは、Windows、Linux、Android 上で C++、C#、および Java 向けにサポートされています。
* ROOBO DevKit は、話者識別のために効率的に利用可能な複数マイクの循環配列を提供するため、会話の文字起こしの作成用にサポートされるハードウェア環境です。 [詳細については、Speech Devices SDK に関する記事を参照してください](speech-devices-sdk.md)。
* Speech SDK による会話の文字起こしのサポートは、16 ビット 16 kHz の PCM 音声の 8 チャネルを使用した、音声のプル モードおよびプッシュ モードでのストリーミングの使用に制限されます。
* 会話の文字起こしは、現時点では "en-US" と "zh-CN" 言語に対応しており、利用できるリージョンは centralus と eastasia です。

## <a name="prerequisites"></a>前提条件

* [Speech SDK で音声テキスト変換を使用する方法を学習します。](quickstart-csharp-dotnet-windows.md)
* [Speech 試用版サブスクリプションを取得します。](https://azure.microsoft.com/try/cognitive-services/)
* Speech SDK バージョン 1.5.1 以降が必要です。

## <a name="create-voice-signatures-for-participants"></a>参加者の声紋を作成する

最初の手順では、会話の参加者の声紋を作成します。 声紋の作成は、効率的な話者識別のために必要です。

### <a name="requirements-for-input-wave-file"></a>入力 wave ファイルの要件

* 声紋を作成するための入力音声 wave ファイルは、16 ビットのサンプル、16 kHz のサンプル レート、およびシングル チャンネル (モノラル) 形式である必要があります。
* 各オーディオ サンプルの推奨される長さは、30 秒から 2 分です。

次の例は、C# から [REST API を使用](https://aka.ms/cts/signaturegenservice)して声紋を作成するための 2 つの異なる方法を示しています。

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>会話の文字起こし

複数の参加者による会話を文字起こしするには、会話セッション用に作成された `AudioConfig` オブジェクトに関連付けられる `ConversationTranscriber` オブジェクトを作成し、`PullAudioInputStream` または `PushAudioInputStream` を使用して音声をストリーミングします。

`MyConversationTranscriber` という ConversationTranscriber クラスがあるとします。 その場合のコードは次のようになります。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
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

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document.
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub でサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
