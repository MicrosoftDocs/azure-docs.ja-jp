---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: 4c685a5d9b2cda5ee7884fb9806f55878653605f
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87404339"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech リソースを作成する <span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md)

## <a name="create-a-new-website-folder"></a>新しい Web サイト フォルダーを作成する

新しい空のフォルダーを作成します。 Web サーバーでサンプルをホストする場合は、Web サーバーがフォルダーにアクセスできることを確認します。

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>JavaScript 用 Speech SDK をフォルダーに解凍する

Speech SDK を [.zip パッケージ](https://aka.ms/csspeech/jsbrowserpackage)としてダウンロードし、新しく作成したフォルダーに解凍します。 これにより、`microsoft.cognitiveservices.speech.sdk.bundle.js` と `microsoft.cognitiveservices.speech.sdk.bundle.js.map` の 2 つのファイルが解凍されます。
後者のファイルはオプションであり、SDK コードのデバッグに有用です。

## <a name="create-an-indexhtml-page"></a>index.html ページを作成する

フォルダーに `index.html` という名前の新しいファイルを作成し、テキスト エディターでこのファイルを開きます。

1. 次の HTML スケルトンを作成します。

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-sts.html)]

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

アプリを起動するには、index.html ファイルをダブルクリックするか、または好みの Web ブラウザーで index.html を開きます。 簡単な GUI が表示されるので、サブスクリプション キーと[リージョン](../../../../regions.md)を入力し、入力音声のテキスト文字起こしをトリガーできます。

## <a name="build-and-run-the-sample-via-a-web-server"></a>Web サーバーからサンプルをビルドして実行する

アプリを起動するには、適切な Web ブラウザーを開き、フォルダーをホストしているパブリック URL にアクセスして、[リージョン](../../../../regions.md)を入力し、入力音声のテキスト文字起こしをトリガーします。 構成した場合、トークン ソースからトークンが取得されます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]