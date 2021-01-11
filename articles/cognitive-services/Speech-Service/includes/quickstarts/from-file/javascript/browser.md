---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: 34e83fc04942d465f27a5f6f7e1f0ebda46c181b
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406321"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech リソースを作成する <span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md)

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加しましょう。

```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Microsoft Cognitive Services Speech SDK JavaScript Quickstart</title>
    <meta charset="utf-8" />
    </head>
    <body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
    </body>
    </html>
```
## <a name="add-ui-elements"></a>UI 要素を追加する

次に、入力ボックス用の基本的な UI をいくつか追加して、Speech SDK の JavaScript を参照し、承認トークン (利用可能な場合) を取得します。

```html  
  <div id="content" style="display:none">
    <table width="100%">
      <tr>
        <td></td>
        <td><h1 style="font-weight:500;">Microsoft Cognitive Services Speech SDK JavaScript Quickstart</h1></td>
      </tr>
      <tr>
        <td align="right"><a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started" target="_blank">Subscription</a>:</td>
        <td><input id="subscriptionKey" type="text" size="40" value="subscription"></td>
      </tr>
      <tr>
        <td align="right">Region</td>
        <td><input id="serviceRegion" type="text" size="40" value="YourServiceRegion"></td>
      </tr>
      <tr>
        <td align="right">File</td>
        <td><input type="file" id="filePicker" accept=".wav" style="display:none" /></td>
      </tr>
      <tr>
        <td></td>
        <td><button id="startRecognizeOnceAsyncButton">Start recognition</button></td>
      </tr>
      <tr>
        <td align="right" valign="top">Results</td>
        <td><textarea id="phraseDiv" style="display: inline-block;width:500px;height:200px"></textarea></td>
      </tr>
    </table>
  </div>

  <script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>

   <script>
  // Note: Replace the URL with a valid endpoint to retrieve
  //       authorization tokens for your subscription.
  var authorizationEndpoint = "token.php";

  function RequestAuthorizationToken() {
    if (authorizationEndpoint) {
      var a = new XMLHttpRequest();
      a.open("GET", authorizationEndpoint);
      a.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
      a.send("");
      a.onload = function() {
          var token = JSON.parse(atob(this.responseText.split(".")[1]));
          serviceRegion.value = token.region;
          authorizationToken = this.responseText;
          subscriptionKey.disabled = true;
          subscriptionKey.value = "using authorization token (hit F5 to refresh)";
          console.log("Got an authorization token: " + token);
      }
    }
  }
  </script>
  
  <script>
    // status fields and start button in UI
    var phraseDiv;
    var startRecognizeOnceAsyncButton;

    // subscription key and region for speech services.
    var subscriptionKey, serviceRegion;
    var authorizationToken;
    var SpeechSDK;
    var recognizer;
    var filePicker;
    var audioFile;

    document.addEventListener("DOMContentLoaded", function () {
      startRecognizeOnceAsyncButton = document.getElementById("startRecognizeOnceAsyncButton");
      subscriptionKey = document.getElementById("subscriptionKey");
      serviceRegion = document.getElementById("serviceRegion");
      phraseDiv = document.getElementById("phraseDiv");
      filePicker = document.getElementById('filePicker');
      
      filePicker.addEventListener("change", function () {
                audioFile = filePicker.files[0];
            });

      startRecognizeOnceAsyncButton.addEventListener("click", function () {
        startRecognizeOnceAsyncButton.disabled = true;
        phraseDiv.innerHTML = "";

      });

      if (!!window.SpeechSDK) {
        SpeechSDK = window.SpeechSDK;
        startRecognizeOnceAsyncButton.disabled = false;

        document.getElementById('content').style.display = 'block';
        document.getElementById('warning').style.display = 'none';

        // in case we have a function for getting an authorization token, call it.
        if (typeof RequestAuthorizationToken === "function") {
            RequestAuthorizationToken();
        }
      }
    });
  </script>
```
 
## <a name="create-a-speech-configuration"></a>Speech 構成を作成する

`SpeechRecognizer` オブジェクトを初期化するには、サブスクリプション キーとサブスクリプション リージョンを使用する構成を作成する必要があります。 このコードを `startRecognizeOnceAsyncButton.addEventListener()` メソッドに挿入します。

> [!NOTE]
> Speech SDK では、既定で認識される言語が en-us です。ソース言語の選択については、「[音声テキスト変換のソース言語を指定する](../../../../how-to-specify-source-language.md)」を参照してください。


```JavaScript
        // if we got an authorization token, use the token. Otherwise use the provided subscription key
        var speechConfig;
        if (authorizationToken) {
          speechConfig = SpeechSDK.SpeechConfig.fromAuthorizationToken(authorizationToken, serviceRegion.value);
        } else {
          if (subscriptionKey.value === "" || subscriptionKey.value === "subscription") {
            alert("Please enter your Microsoft Cognitive Services Speech subscription key!");
            return;
          }
          speechConfig = SpeechSDK.SpeechConfig.fromSubscription(subscriptionKey.value, serviceRegion.value);
        }

        speechConfig.speechRecognitionLanguage = "en-US";
```

## <a name="create-an-audio-configuration"></a>オーディオ構成を作成する

ここで、使用するオーディオ ファイルを指し示す `AudioConfig` オブジェクトを作成する必要があります。 このコードを Speech 構成のすぐ下にある `startRecognizeOnceAsyncButton.addEventListener()` メソッドに挿入します。

```JavaScript
        var audioConfig  = SpeechSDK.AudioConfig.fromWavFileInput(audioFile);
```

## <a name="initialize-a-speechrecognizer"></a>SpeechRecognizer を初期化する

ここで、前に作成した `SpeechConfig` オブジェクトと `AudioConfig` オブジェクトを使用して `SpeechRecognizer` オブジェクトを作成しましょう。 このコードを `startRecognizeOnceAsyncButton.addEventListener()` メソッドに挿入します。

```JavaScript
        recognizer = new SpeechSDK.SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-a-phrase"></a>語句を認識する

`SpeechRecognizer` オブジェクトから、`recognizeOnceAsync()` メソッドを呼び出します。 認識の対象として 1 つの語句を送信しようとしていること、また、その語句が識別された後で、音声認識を停止しようとしていることが、このメソッドを通じて Speech サービスに伝えられます。

```JavaScript
recognizer.recognizeOnceAsync(
          function (result) {
            startRecognizeOnceAsyncButton.disabled = false;
            phraseDiv.innerHTML += result.text;
            window.console.log(result);

            recognizer.close();
            recognizer = undefined;
          },
          function (err) {
            startRecognizeOnceAsyncButton.disabled = false;
            phraseDiv.innerHTML += err;
            window.console.log(err);

            recognizer.close();
            recognizer = undefined;
          });
```

## <a name="check-your-code"></a>コードを確認する

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-from-file.html)]

## <a name="create-the-token-source-optional"></a>トークン ソースを作成する (省略可能)

Web サーバーで Web ページをホストする場合は、デモ アプリケーションに対するトークン ソースを必要に応じて指定できます。
これにより、サブスクリプション キーはサーバーに常に存在するようになり、ユーザーは自分で承認コードを入力しなくても音声機能を使用できます。

`token.php` という名前で新しいファイルを作成します。 この例では、Web サーバーが PHP スクリプト言語をサポートし、curl が有効になっているものと想定します。 次のコードを入力します。

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> 承認トークンは有効期間が限られています。
> この簡単な例では、承認トークンを自動更新する方法は示されていません。 ユーザーは、ページを読み込みなおすか、F5 キーを押すことにより、手動で更新できます。

## <a name="build-and-run-the-sample-locally"></a>サンプルをビルドしてローカルに実行する

アプリを起動するには、index.html ファイルをダブルクリックするか、または好みの Web ブラウザーで index.html を開きます。 簡単な GUI が表示されるので、サブスクリプション キーと[リージョン](../../../../regions.md)を入力し、マイクを使用して認識を開始することができます。

> [!NOTE]
> Safari ブラウザーでは、この方法が使用できません。
> Safari では、サンプル Web ページを Web サーバーでホストする必要があります。Safari では、ローカル ファイルから読み込まれた Web サイトにはマイクの使用が許可されません。

## <a name="build-and-run-the-sample-via-a-web-server"></a>Web サーバーからサンプルをビルドして実行する

アプリを起動するには、適当な Web ブラウザーを開き、フォルダーをホストしているパブリック URL にアクセスして、[リージョン](../../../../regions.md)を入力し、マイクを使用して認識を開始します。 構成した場合、トークン ソースからトークンが取得されます。


## <a name="next-steps"></a>次のステップ

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]