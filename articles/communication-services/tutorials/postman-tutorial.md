---
title: チュートリアル - Postman で ACS の SMS API に署名して要求を行う
titleSuffix: An Azure Communication Services tutorial
description: ACS に対して、Postman で署名し、SMS メッセージを送信するための要求を行う方法について説明します。
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c21d8696c0c4edd743aab6cbe7866d27ce46a314
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492610"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>チュートリアル: Postman で署名して要求を行う
このチュートリアルでは、Postman を設定して使用することで、HTTP を使って Azure Communication Services (ACS) サービスに対して要求を行います。 このチュートリアルを終了すると、ACS と Postman を使用して SMS メッセージを正常に送信し、Postman で ACS 内の他の API を探索できるようになります。

このチュートリアルでは、次のことを行います。
> [!div class="checklist"]
> * Postman をダウンロードする
> * HTTP 要求に署名するように Postman を設定する
> * ACS の SMS API に対し、メッセージを送信するための要求を行う。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。 無料アカウントでは 200 ドル分の Azure クレジットが提供され、さまざまな組み合わせのサービスをお試しいただけます。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成する方法に関する記事を参照してください](../quickstarts/create-communication-resource.md)。
- SMS メッセージを送信できる ACS 電話番号。電話番号を取得するには、[電話番号の取得](../quickstarts/telephony-sms/get-phone-number.md)に関する記事を参照してください。

## <a name="downloading-and-installing-postman"></a>Postman をダウンロードしてインストールする

Postman は、任意の HTTP API に対して API 要求を行うことができるデスクトップ アプリケーションです。 これは、API のテストと探索用に一般に使用されています。 最新の[デスクトップ バージョンを Postman の Web サイトから](https://www.postman.com/downloads/)をダウンロードします。 Postman には、Windows、Mac、Linux 用のバージョンがあるため、お使いのオペレーティング システムに適したバージョンをダウンロードしてください。 ダウンロードしたら、アプリケーションを開きます。 スタート画面が表示されます。ここで、サインインするか Postman アカウントを作成するかを尋ねられます。 アカウントの作成はオプションであり、[Skip and go to app]\(スキップしてアプリに移動\) リンクをクリックするとスキップできます。 アカウントを作成すると API 要求の設定が Postman に保存され、他のコンピューターでご自身の要求を取得できるようになります。

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="アカウントを作成することもスキップしてアプリに移動することもできることを示す、Postman のスタート画面。":::

アカウントを作成するか作成をスキップすると、Postman のメイン ウィンドウが表示されます。

## <a name="creating-and-configuring-a-postman-collection"></a>Postman コレクションを作成して構成する

Postman では、さまざまな方法で要求を整理できます。 このチュートリアルの目的に沿って、 Postman コレクションを作成します。 これを行うには、アプリケーションの左側にある [Collections]\(コレクション\) ボタンを選択します。

:::image type="content" source="media/postman/collections-tab.png" alt-text="[Collections]\(コレクション\) タブが強調表示された Postman のメイン画面。":::

選択したら、[Create new Collection]\(新しいコレクションの作成\) をクリックして、コレクションの作成プロセスを開始します。 Postman の中央の領域に新しいタブが開きます。 コレクションに任意の名前を指定します。 ここでは、コレクションに "ACS" という名前が付いています。

:::image type="content" source="media/postman/acs-collection.png" alt-text="ACS コレクションが開かれ、コレクションの名前が強調表示された Postman。":::

コレクションを作成し、名前を指定したら、その構成準備ができました。

### <a name="adding-collection-variables"></a>コレクション変数を追加する

より簡単に認証を処理し、要求を行うために、新しく作成された ACS コレクション内に 2 つのコレクション変数を指定します。 これらの変数は、ACS コレクション内のすべての要求で使用できます。 変数の作成を開始するには、コレクションの変数のタブにアクセスします。

:::image type="content" source="media/postman/variable-stab.png" alt-text="ACS コレクションの変数のタブを示す Postman。":::

コレクションのタブを表示したら、次の 2 つの変数を作成します。
- key - この変数は、Azure portal 内の Azure Communication Services のキー ページにあるキーのいずれかでなければなりません。 たとえば、「 `oW...A==` 」のように入力します。
- endpoint - この変数は、キー ページの Azure Communication Services のエンドポイントでなければなりません。 **末尾のスラッシュは必ず削除してください**。 たとえば、「 `https://contoso.communication.azure.com` 」のように入力します。

これらの値を、変数画面の [Initial Value]\(初期値\) 列に入力します。 入力したら、右側のテーブルのすぐ上にある [Persist All]\(すべて保持\) ボタンを押します。 正しく構成されたら、Postman の画面は次のようになります。

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="ACS コレクションの変数が正しく設定された Postman。":::

変数の詳細については、[それに関する Postman のドキュメント](https://learning.postman.com/docs/sending-requests/variables)を参照してください。

### <a name="creating-a-pre-request-script"></a>要求前スクリプトを作成する

次の手順では、Postman 内に要求前スクリプトを作成します。 要求前スクリプトは、Postman の各要求の前に実行され、ユーザーに代わって要求パラメーターを修正または変更できるスクリプトです。 これを使用して HTTP 要求に署名し、ACS のサービスで承認できるようにします。 署名の要件の詳細については、[認証に関するガイドを参照](https://docs.microsoft.com/rest/api/communication/authentication)してください。

このスクリプトをコレクション内に作成することで、コレクション内の任意の要求で実行されるようにします。 これを行うには、コレクションのタブで、[Pre-request Script]\(要求前スクリプト\) サブタブをクリックします。

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="ACS コレクションの [Pre-request Script]\(要求前スクリプト\) サブタブが選択された Postman。":::

このサブタブでは、要求前スクリプトを下のテキスト領域に入力して作成できます。 これは、[Visual Studio Code](https://code.visualstudio.com/) などの高機能のコード エディター内で記述してから、完成時に貼り付ける方が簡単な場合があります。 このチュートリアルでは、スクリプトの各部分について説明します。 単に Postman にコピーして作業を開始する場合は、最後までスキップしてもかまいません。 それではスクリプトの記述を始めます。

### <a name="writing-the-pre-request-script"></a>要求前スクリプトを記述する

まず、協定世界時 (UTC) の文字列を作成し、これを "Date" HTTP ヘッダーに追加します。 また、この文字列を変数に格納し、後で署名時に使用します。

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

次に、SHA 256 を使用して要求本文をハッシュし、`x-ms-content-sha256` ヘッダーに挿入します。 Postman には、グローバルでハッシュと署名を行うための[標準ライブラリ](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries)がいくつか含まれているため、これらをインストールする必要はなく、これらが必要になることもありません。

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

ここでは、前に指定したエンドポイント変数を使用して、HTTP ホスト ヘッダーの値を識別します。 ホスト ヘッダーはこのスクリプトが処理されるまで設定されないため、これを行う必要があります。

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

この情報を作成したら、HTTP 要求に署名する文字列を作成できます。これは、前に作成されたいくつかの値で構成されます。

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

最後に、ACS キーを使用してこの文字列に署名し、それを `Authorization` ヘッダー内で要求に追加する必要があります。

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>最終的な要求前スクリプト

最終的な要求前スクリプトはこちらのようになります。

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

この最終的なスクリプトを、[Pre-request Script]\(要求前スクリプト\) タブ内のテキスト領域に入力するか貼り付けます。

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="ACS コレクションの [Pre-request Script]\(要求前スクリプト\) が入力されている Postman。":::

入力したら、CTRL + S キーを押すか、[Save]\(保存\) ボタンを押します。これで、スクリプトがコレクションに保存されます。 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Postman の要求前スクリプトの [Save]\(保存\) ボタン。":::

## <a name="creating-a-request-in-postman"></a>Postman で要求を作成する

すべてが設定されたので、Postman 内で ACS 要求を作成する準備ができました。 開始するには、ACS コレクションの横にある正符号 (+) アイコンをクリックします。

:::image type="content" source="media/postman/create-request.png" alt-text="Postman の正符号ボタン。":::

これにより、Postman 内に要求のための新しいタブが作成されます。 作成したら、それを構成する必要があります。 SMS Send API に対して要求を行うので、[詳細についてこちらの API のドキュメント](https://docs.microsoft.com/rest/api/communication/sms/send)を参照してください。 それでは Postman の要求を構成します。

まず、要求の種類を `POST` に設定し、要求の URL フィールドに「`{{endpoint}}/sms?api-version=2021-03-07`」と入力します。 この URL では、前に作成した `endpoint` 変数を使用して、ACS リソースに自動的に送信されるようにします。

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="種類が POST に設定され、URL が正しく設定された Postman 要求。":::

次に、要求の [Body]\(本文\) タブを選択し、下にあるオプション ボタンを [raw]\(生\) に変更します。 右側には、"Text" ドロップダウンがあり、これを JSON に変更します。

:::image type="content" source="media/postman/postman-json.png" alt-text="要求本文を [raw]\(生\) および [JSON] に設定する":::

これで、情報が JSON 形式で送受信されるように要求が構成されます。

下のテキスト領域には要求本文を入力する必要があり、次の形式にします。

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

"from" の値については、前述のように ACS ポータルで[電話番号を取得](../quickstarts/telephony-sms/get-phone-number.md)する必要があります。 スペースを含めずに、プレフィックスとして国番号を付けて入力します。 たとえば、`+15555551234` のように指定します。 "message" は送信したい内容であればどのようなものでもかまいませんが、`Hello from ACS` は適例です。 "to" 値は、SMS メッセージを受信できる、ご自身がアクセスできる電話にしてください。 ご自身のモバイルを使用することをお勧めします。

入力したら、前に作成した ACS コレクションにこの要求を保存する必要があります。 これにより、前に作成した変数と要求前スクリプトが確実に取得されます。 これを行うには、要求領域の右上にある [Save]\(保存\) ボタンをクリックします。

:::image type="content" source="media/postman/postman-save.png" alt-text="Postman 要求の [Save]\(保存\) ボタン。":::

これによってダイアログ ウィンドウが表示され、要求の名前とその保存場所を指定するように求められます。 任意の名前を指定できますが、ダイアログ下部で必ずご自身の ACS コレクションを選択してください。

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="ACS コレクションが選択された Postman の要求の保存のダイアログ。":::

## <a name="sending-a-request"></a>要求を送信する

すべてが設定されたので、要求を送信して、ご自身の電話で SMS メッセージを取得できます。 これを行うには、作成した要求が選択されていることを確認し、右側にある [Send]\(送信\) ボタンを押します。

:::image type="content" source="media/postman/postman-send.png" alt-text="[Send]\(送信\) ボタンが強調表示されている Postman 要求。":::

すべて問題なく完了したら、ACS からの応答が表示され、202 という状態コードになっているはずです。

:::image type="content" source="media/postman/postman-202.png" alt-text="正常に送信され、202 の状態コードが表示された Postman 要求。":::

"to" 値に指定した番号を持つ携帯電話も SMS メッセージを受信しているはずです。 これで、ACS のサービスと通信し、SMS メッセージを送信できる、正常に機能する Postman が設定されました。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ACS API を探索する](https://docs.microsoft.com/rest/api/communication/)
> [認証の詳細を参照する](https://docs.microsoft.com/rest/api/communication/authentication)
> [Postman の詳細を参照する](https://learning.postman.com/)

次の記事もご覧ください。

- [チャットをアプリに追加する](../quickstarts/chat/get-started.md)
- [ユーザー アクセス トークンを作成する](../quickstarts/access-tokens.md)
- [クライアントとサーバーのアーキテクチャについて学習する](../concepts/client-and-server-architecture.md)
- [認証について学習する](../concepts/authentication.md)