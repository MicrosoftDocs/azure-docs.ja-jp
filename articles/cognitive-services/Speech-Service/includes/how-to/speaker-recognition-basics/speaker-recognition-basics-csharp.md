---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 35fc646eed802dbff80c82cadfdbba5f96397481
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445059"
---
このクイックスタートでは、Speech SDK を使用した Speaker Recognition の基本的な設計パターンについて学習します。これには次のものが含まれます。

* テキスト依存およびテキスト非依存の認証
* 音声のグループから音声サンプルを識別する話者識別
* 音声プロファイルの削除

Speech Recognition の概念の概要については、[概要](../../../speaker-recognition-overview.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

> [!IMPORTANT]
> 現在、Speaker Recognition は、`westus` リージョンで作成された Azure Speech リソース "*のみ*" でサポートされています。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。 ご利用のプラットフォームに応じて、次の手順を行います。

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin </a>

## <a name="import-dependencies"></a>依存関係のインポート

この記事の例を実行するには、スクリプトの先頭に次の `using` ステートメントを含めます。

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) を作成する必要があります。 この例では、サブスクリプション キーとリージョンを使用して [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) を作成します。 また、この記事の残りの部分で使用する、基本的な定型コードをいくつか作成します。これを変更して、さまざまなカスタマイズを行います。

リージョンが `westus` に設定されていることに注意してください。これは、それがサービスでサポートされている唯一のリージョンであるためです。

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>テキスト依存の認証

Speaker Verification では、話者が既知の、または **登録されている** 音声と一致することを確認します。 最初の手順では、音声プロファイルを **登録** し、サービスで今後の音声サンプルと比較できるようにします。 この例では、**テキスト依存** の戦略を使用してプロファイルを登録します。その場合、登録と認証の両方に使用する特定のパスフレーズが必要になります。 サポートされているパスフレーズのリストについては、[リファレンス ドキュメント](/rest/api/speakerrecognition/)を参照してください。

まず、`Program` クラスに次の関数を作成して、音声プロファイルを登録します。

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

この関数では、`await client.CreateProfileAsync()` で実際に新しい音声プロファイルが作成されます。 作成された後、オーディオ サンプルを入力する方法を指定します。この例では `AudioConfig.FromDefaultMicrophoneInput()` を使用して、既定の入力デバイスからオーディオをキャプチャします。 次に、登録のために、必要な残りのサンプルの数を追跡する `while` ループにオーディオ サンプルを登録します。 各反復では、`client.EnrollProfileAsync(profile, audioInput)` で、マイクに向かってパスフレーズを読み上げるように求められ、サンプルは音声プロファイルに追加されます。

登録が完了した後、`await SpeakerVerify(config, profile, profileMapping)` を呼び出し、先ほど作成したプロファイルに対して認証を行います。 `SpeakerVerify` を定義する別の関数を追加します。

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

この関数では、先ほど作成した `VoiceProfile` オブジェクトを渡して、認証対象のモデルを初期化します。 次に、`await speakerRecognizer.RecognizeOnceAsync(model)` で、パスフレーズをもう一度読み上げるように求められますが、今度は音声プロファイルに対して認証が行われ、0.0 から 1.0 の範囲の類似性スコアが返されます。 `result` オブジェクトでは、パスフレーズが一致するかどうかに基づいて、`Accept` や `Reject` も返されます。

次に、`Main()` 関数を変更し、作成した新しい関数を呼び出します。 さらに、関数呼び出しを介して参照渡しするために、`Dictionary<string, string>` を作成することに注意してください。 その理由は、サービスでは、作成された `VoiceProfile` で人間が判読できる名前を格納できず、プライバシー上の目的で ID 番号のみが格納されるためです。 `VerificationEnroll` 関数では、このディクショナリに、テキスト名と共に、新しく作成された ID を使用してエントリを追加します。 人間が判読できる名前を表示する必要があるアプリケーション開発シナリオでは、**サービスで格納できないどこかにこのマッピングを格納する必要があります**。

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

スクリプトを実行すると、登録のために 3 回、さらに認証のために 1 回、*My voice is my passport, verify me* というフレーズを読み上げるように求められます。 その結果、類似性スコアが返されます。これを使用して、認証用に独自のカスタムしきい値を作成することができます。

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>テキストに依存しない認証

**テキストに依存する** 認証とは対照的に、**テキストに依存しない** 認証は次のようになります。

* 特定のパスフレーズを読み上げる必要はなく、何と話しかけてもかまいません
* 3 つのオーディオ サンプルは必要ありませんが、合計 20 秒のオーディオが "*必要*" になります

`VerificationEnroll` 関数にいくつかの単純な変更を加え、**テキストに依存しない** 認証に切り替えます。 まず、認証の種類を `VoiceProfileType.TextIndependentVerification` に変更します。 次に、`result.RemainingEnrollmentsSpeechLength` を追跡するために `while` ループを変更します。その場合、20 秒のオーディオがキャプチャされるまで引き続き話しかけるように求められます。

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

プログラムをもう一度実行します。パスフレーズは不要であるため、認証フェーズでは何か話しかけます。 ここでも、類似スコアが返されます。

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>話者識別

話者識別を使用して、登録されている音声の特定のグループの **誰** が話しているのかを判別します。 このプロセスは **テキストに依存しない認証** と非常によく似ています。主な違いは、1 つのプロファイルに対して認証するのではなく、一度に複数の音声プロファイルに対して認証できることです。

複数の音声プロファイルを登録するための関数 `IdentificationEnroll` を作成します。 各プロファイルの登録プロセスは **テキストに依存しない認証** の登録プロセスと同じであり、プロファイルごとに 20 秒のオーディオが必要です。 この関数では `profileNames` という文字列のリストが受け入れられ、リストの名前ごとに新しい音声プロファイルが作成されます。 関数からは `VoiceProfile` オブジェクトのリストが返されます。これは、次の関数で話者を識別するために使用します。

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

次の関数 `SpeakerIdentification` を作成して、識別要求を送信します。 **話者認証** 要求と比較した場合のこの関数の主な違いは、`VoiceProfile` オブジェクトのリストを受け入れる、`SpeakerIdentificationModel.FromProfiles()` を使用することです。 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similar voice profile is {profileMapping[result.ProfileId]} with similarity score {result.Score}");
}
```

`Main()` 関数を次のように変更します。 `IdentificationEnroll()` 関数に渡す、`profileNames` という文字列のリストを作成します。 この場合、さらに名前を追加して、友人や同僚用に追加のプロファイルを作成できるように、このリストの名前ごとに新しい音声プロファイルを作成するように求められます。

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

スクリプトを実行すると、最初のプロファイル用の音声サンプルを登録するために話しかけるように求められます。 登録が完了した後、`profileNames` リストの各名前に対して、このプロセスを繰り返すように求められます。 各登録が完了した後、**誰か** に話しかけてもらうように求められ、サービスでは登録されている音声プロファイルの中からその誰かを識別しようとします。

この例では、最も近い一致のみが返され、これは類似性スコアですが、`SpeakerIdentification` 関数に `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` を追加することで、上位 5 つの類似性スコアを含む完全な応答を得ることができます。

## <a name="changing-audio-input-type"></a>オーディオ入力の種類の変更

この記事の例では、オーディオ サンプルの入力として既定のデバイス マイクを使用しています。 しかし、マイク入力の代わりにオーディオ ファイルを使用する必要があるシナリオでは、ファイル入力に切り替えるために `AudioConfig.FromDefaultMicrophoneInput()` の任意のインスタンスを `AudioConfig.FromWavFileInput(path/to/your/file.wav)` に変更するだけです。 入力を混在させることもできます。たとえば、登録用にはマイクを、認証用にはファイルを使用します。

## <a name="deleting-voice-profile-enrollments"></a>音声プロファイル登録の削除

登録されているプロファイルを削除するには、`VoiceProfileClient` オブジェクトで `DeleteProfileAsync()` 関数を使用します。 次の関数例では、既知の音声プロファイル ID から音声プロファイルを削除する方法を示します。

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```
