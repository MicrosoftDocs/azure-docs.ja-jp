---
title: テナント モデルを作成する (プレビュー) - Speech サービス
titleSuffix: Azure Cognitive Services
description: Office 365 データを使用する、セキュリティとコンプライアンスが確保されたテナント モデル (Custom Speech with Office 365 data) を自動的に生成し、組織固有の用語に最適な音声認識を提供します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 52e4271fca02dc9b0eab45ca98581ecd85119b59
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934482"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>チュートリアル:テナント モデルを作成する (プレビュー)

テナント モデル (Custom Speech with Office 365 data) は、組織の Office 365 データからカスタム音声認識モデルを自動的に生成する Office365 企業ユーザー向けのオプトイン サービスです。 このモデルは、専門用語と業界用語、人名に関して、すべてセキュリティとコンプライアンスに配慮した方法で最適化されます。

> [!IMPORTANT]
> テナント モデル サービスを使用して登録した場合、Speech Service は、貴社の言語モデルにアクセスすることができます。 このモデルは、組織内のだれでも見ることができる Office 365 パブリック グループのメールやドキュメントから生成されます。 組織の Office 365 管理者は、Office 365 管理ポータルから組織全体の言語モデルの使用をオンにしたりオフにしたりすることができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Microsoft 365 管理センターを使用してテナント モデルに登録する
> * 音声のサブスクリプション キーを取得する
> * テナント モデルを作成する
> * テナント モデルをデプロイする
> * Speech SDK でテナント モデルを使用する

## <a name="enroll-in-the-tenant-model-service"></a>テナント モデル サービスに登録する

テナント モデルをデプロイする前に、まずテナント モデル サービスに登録する必要があります。 登録作業は Microsoft 365 管理センターで行います。この作業を実行できるのは Microsoft 365 管理者だけです。

1. [Microsoft 365 管理センター](https://admin.microsoft.com)にサインインします。

1. 左側のペインで、 **[設定]** を選択し、入れ子になったメニューから **[設定]** を選択します。次に、メイン ウィンドウから **[Azure Speech Services]** を選択します。

   ![[サービスとアドイン] ペイン](media/tenant-language-model/tenant-language-model-enrollment.png)

1. **[組織全体での言語モデルを許可する]** チェック ボックスをオンにし、 **[変更の保存]** を選択します。

   ![[Azure Speech Services] ペイン](media/tenant-language-model/tenant-language-model-enrollment-2.png)

テナント モデル インスタンスをオフにするには、次の手順に従います。
1. 前出の手順 1. と 2. を繰り返します。
1. **[組織全体での言語モデルを許可する]** チェック ボックスをオフにし、 **[変更の保存]** を選択します。

## <a name="get-a-speech-subscription-key"></a>音声のサブスクリプション キーを取得する

Speech SDK でテナント モデルを使用するには、音声リソースとそれに関連付けられたサブスクリプション キーが必要です。

1. [Azure portal](https://aka.ms/azureportal) にサインインします。
1. **[リソースの作成]** を選択します。
1. **検索**ボックスに「**Speech**」と入力します。
1. 結果の一覧で、 **[Speech]** 、 **[作成]** の順に選択します。
1. 画面の指示に従って、リソースを作成します。 次のことを確認してください。
   * **[場所]** が **[eastus]** または **[westus]** のいずれかに設定されている。
   * **[価格レベル]** が **[S0]** に設定されている。
1. **［作成］** を選択します

   数分後、リソースが作成されます。 サブスクリプション キーは、リソースの **[概要]** セクションにあります。

## <a name="create-a-language-model"></a>言語モデルを作成する

管理者が組織のテナント モデルを有効にした後は、Office 365 データに基づいて言語モデルを作成できます。

1. [Speech Studio](https://speech.microsoft.com/) にサインインします。
1. 右上の **[設定]** (歯車アイコン) を選択し、 **[Tenant Model settings]\(テナント モデルの設定\)** を選択します。

   ![[Tenant Model settings]\(テナント モデルの設定\) リンク](media/tenant-language-model/tenant-language-settings.png)

   テナント モデルを作成する資格があるかどうかを伝えるメッセージが Speech Studio に表示されます。

   > [!NOTE]
   > 北米の Office 365 企業ユーザーのお客様は、テナント モデル (英語) を作成することができます。 カスタマー ロックボックス、カスタマー キー、Office 365 Government のお客様は、この機能をご利用いただけません。 お客様がカスタマー ロックボックスまたはカスタマー キーのユーザーかどうかを判断するには、次のページを参照してください。
   > * [カスタマー ロックボックス](/microsoft-365/compliance/customer-lockbox-requests)
   > * [カスタマー キー](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365 Government](https://www.microsoft.com/microsoft-365/government)

1. **[オプトイン]** を選択します。

   テナント モデルの準備が完了すると、その後の手順と共に確認のメール メッセージが送信されます。

## <a name="deploy-your-tenant-model"></a>テナント モデルをデプロイする

テナント モデル インスタンスの準備が完了したら、次の手順に従ってデプロイします。

1. 確認のメール メッセージで **[View model]\(モデルの表示\)** ボタンを選択します。 または [Speech Studio](https://speech.microsoft.com/) にサインインします。
1. 右上の **[設定]** (歯車アイコン) を選択し、 **[Tenant Model settings]\(テナント モデルの設定\)** を選択します。

   ![[Tenant Model settings]\(テナント モデルの設定\) リンク](media/tenant-language-model/tenant-language-settings.png)

1. **[デプロイ]** を選択します。

   モデルがデプロイされると、状態は *[Deployed]\(デプロイ済み\)* に変わります。

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Speech SDK でテナント モデルを使用する

モデルのデプロイが完了したので、Speech SDK でそれを使用できます。 このセクションでは、サンプル コードから Azure Active Directory (Azure AD) 認証を使用して Speech サービスを呼び出します。

C# で Speech SDK を呼び出すために使用するコードを見てみましょう。 この例では、テナント モデルを使用して音声認識を実行します。 このガイドは、プラットフォームが既に設定されていることを前提としています。 セットアップについてご不明な点がある場合は、[C# (.NET Core) での音声認識に関するクイックスタート](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)を参照してください。

このコードをプロジェクトにコピーします。

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

次に、コマンド ラインからプロジェクトをリビルドして実行する必要があります。 コマンドを実行する前に、次の手順に従っていくつかのパラメーターを更新します。

1. `<Username>` と `<Password>` を、有効なテナント ユーザーの値に置き換えます。
1. `<Subscription-Key>` を音声リソースのサブスクリプション キーに置き換えます。 この値は、[Azure portal](https://aka.ms/azureportal) 内の音声リソースの **[概要]** セクションにあります。
1. `<Endpoint-Uri>` を次のエンドポイントに置き換えます。 `{your region}` は、必ず音声リソースが作成されたリージョンに置き換えてください。 サポートされているリージョンは、`westus`、`westus2`、および `eastus` です。 リージョン情報は、[Azure portal](https://aka.ms/azureportal) 内の音声リソースの **[概要]** セクションで確認できます。
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. 次のコマンドを実行します。

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

このチュートリアルでは、Office 365 データを使用してカスタム音声認識モデルを作成し、それをデプロイして、Speech SDK で使用する方法を説明しました。

## <a name="next-steps"></a>次のステップ

* [Speech Studio](https://speech.microsoft.com/)
* [Speech SDK](speech-sdk.md)
