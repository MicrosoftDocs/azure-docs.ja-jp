---
title: Speech Devices SDK を取得する
titleSuffix: Azure Cognitive Services
description: Speech Services は、さまざまなデバイスやオーディオ ソースと連携します。 適合するハードウェアやソフトウェアで音声アプリケーションを次のレベルに引き上げることができます。 この記事では、Speech Devices SDK を利用して開発を始める方法について説明します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 3c5874625ee9d1932c401238c1586ad89d5d206d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57856724"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK を取得する

Speech Devices SDK は制限付きのプレビュー段階にあるため、プログラムに登録する必要があります。 現在、Microsoft では、この製品にアクセスする候補として、大規模の企業を優先しています。

## <a name="request-access"></a>アクセスの要求

Speech Devices SDK にアクセスするには:

1. Microsoft Speech Devices SDKの[サインアップ フォーム](https://aka.ms/sdsdk-signup)に移動します。
1. [使用許諾契約書](speech-devices-sdk-license.md)を読みます。
1. この使用許諾契約の条項に同意する場合は、**[同意する]** を選択します。
1. フォームの質問に回答します。
1. フォームを送信します。
1. メール アドレスが Azure Active Directory (Azure AD) に登録されていない場合は、アクセスが承認されたときに、次の例のような招待メールが届きます。 メール アドレスが既に Azure AD に登録されている場合は、アクセスが承認されたときに Microsoft Speech Team からメール メッセージが届き、先に進んで [Speech Devices SDK をダウンロード](#download-the-speech-devices-sdk)できます。

## <a name="approval-e-mail"></a>承認のメール

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com>
Subject: You're invited to the Microsoft organization
```

![メール メッセージ](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>アクセスを受け入れる

登録時に指定したメール アドレスを使用して、以下の手順で Azure AD に参加します。 このプロセスによって、Speech Devices SDK の[ダウンロード サイト](https://shares.datatransfer.microsoft.com/)へのアクセスが許可されます。

1. 受信した電子メール メッセージに記載されている **[開始]** をクリックします。 お客様の組織で既に Office 365 を使用している場合は、サインインするように求められ、手順 7 に進むことができます。

2. 表示されるブラウザー ウィンドウで **[次へ]** を選択します。

    ![認証ウィンドウ](media/speech-devices-sdk/get-sdk-2.png)

3. Microsoft アカウントをまだお持ちでない場合は、作成します。 招待メールの送信先と同じメール アドレスを入力します。

    ![Microsoft アカウントの作成](media/speech-devices-sdk/get-sdk-3.png)

4. **[次へ]** を選択してパスワードを作成します。

5. メールの確認を求められたら、受け取った招待メールから確認コードを取得します。

7. ダイアログ ボックスで、メール メッセージのセキュリティ コードを貼り付けるか、入力します。 この例のセキュリティ コードは **8406** です。 **[次へ]** を選択します。

    ![メールを確認する](media/speech-devices-sdk/get-sdk-6.png)

8. ブラウザーにアクセス パネル アプリケーションが表示される場合、メール アドレスが Azure AD に登録されていることを確認したことになります。 これで、Speech Devices SDK ダウンロード サイトにアクセスできるようになりました。

## <a name="download-the-speech-devices-sdk"></a>Speech Devices SDK をダウンロードする

[Speech Devices SDK ダウンロード サイト](https://shares.datatransfer.microsoft.com/)に移動します。 先ほど作成した Microsoft アカウントを使用してサインインします。

![SDK ダウンロード サイト](media/speech-devices-sdk/get-sdk-7.png)

Speech Devices SDK、関連するサンプル コード、参考資料をダウンロードするには:

1. ブラウザーのメッセージに従って Aspera Connect ツールをダウンロードしてインストールします。

    ![Aspera Connect をダウンロードする](media/speech-devices-sdk/get-sdk-8.png)

1. **[はい]** を選択してアプリを Aspera Connect に切り替えます。

    ![Aspera Connect に切り替える](media/speech-devices-sdk/get-sdk-9.png)

1. **[許可]** を選択して、Aspera Connect を使用してファイルをダウンロードすることを確認します。

    ![Aspera Connect を使用してダウンロードする](media/speech-devices-sdk/get-sdk-10.png)

1. ファイルをダウンロードした後に、Aspera Connect の転送ウィンドウを閉じます。

    ![Aspera Connect の転送ウィンドウ](media/speech-devices-sdk/get-sdk-11.png)

既定では、ファイルは **Downloads** フォルダーにダウンロードされます。 このサイトからすぐにサインアウトできます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech Devices SDK を使ってみる](speech-devices-sdk-qsg.md)
