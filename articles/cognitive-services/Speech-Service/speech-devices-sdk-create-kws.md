---
title: カスタム ウェイク ワードを作成する - Speech Services
titleSuffix: Azure Cognitive Services
description: デバイスは常にウェイク ワード (またはフレーズ) をリッスンしています。 ユーザーがウェイク ワードを読み上げると、ユーザーが読み上げを止めるまで、デバイスは後続のすべての音声をクラウドに送信します。 ウェイク ワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b5ace2e741f900dd4ab7ba6518d0956284af35f6
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498233"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Speech サービスを使用してカスタム ウェイク ワードを作成する

デバイスは常にウェイク ワード (またはフレーズ) をリッスンしています。 たとえば、"Hey Cortana" は、Cortana アシスタントのウェイク ワードです。 ユーザーがウェイク ワードを読み上げると、ユーザーが読み上げを止めるまで、デバイスは後続のすべての音声をクラウドに送信します。 ウェイク ワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。

この記事では、デバイス用のカスタム ウェイク ワードを作成する方法について説明します。

## <a name="choose-an-effective-wake-word"></a>効果的なウェイク ワードを選択する

ウェイク ワードを選択するときには、以下のガイドラインを考慮に入れます。

* ウェイク ワードは、英語の単語または語句とする必要があります。 2 秒以内に読み上げることができる必要があります。

* 4 音節から 7 音節の単語が最適です。 たとえば、"Hey, Computer" は適切なウェイク ワードです。 "Hey" だけではよくありません。

* ウェイク ワードは、英語の一般的な発音規則に従っている必要があります。

* 英語の一般的な発音規則に従った一意の単語や造語にすると、誤検知が減る可能性があります。 たとえば "computerama" は、適切なウェイク ワードになります。

* 一般的な単語は選択しないでください。 たとえば、"eat" や "go" は、普通の会話の中で頻繁に出てくる単語です。 これらは、デバイスの誤ったトリガーとなる可能性があります。

* 別の発音を持つことがあるウェイク ワードは使わないようにします。 ユーザーは、デバイスが応答するようにするために、”正しい” 発音を知っている必要があります。 たとえば "509" は、"five zero nine"、"five oh nine"、または "five hundred and nine" と発音できます。 "R.E.I."  は、"R E I" または "Ray" として発音できます。 "Live" は、"/Līv/" または "/liv/" と発音できます。

* 特殊な文字、記号、または数字は使用しないでください。 たとえば、"Go#" および "20 + cats" の場合は、適切なウェイク ワードにはなりません。 ただし、"go sharp" や "twenty plus cats" は機能するでしょう。 それでもブランディングにおいて記号を使用して、正しい発音が一般に認知されるように、マーケティングやドキュメントを利用することができます。

> [!NOTE]
> 商標の単語をウェイク ワードとして選択する場合は、あなたがその商標を所有しているか、商標の所有者からその単語の使用を許可されていることを確認してください。 Microsoft は、ウェイク ワードの選択により発生する可能性がある問題については法的責任を負いません。

## <a name="create-your-wake-word"></a>ウェイク ワードを作成する

カスタム ウェイク ワードをデバイスで使用する前に、Microsoft Custom Wake Word Generation サービスを使用してカスタム ウェイク ワードを作成する必要があります。 ウェイク ワードを指定すると、このサービスによってファイルが作成されます。このファイルを開発キットに展開することで、デバイスでウェイク ワードを有効にできます。

1. [Custom Speech Service ポータル](https://cris.ai/)に移動します。

    ![Custom Speech Service ポータル](media/speech-devices-sdk/wake-word-4.png)

1. Azure Active Directory の招待を受信したメール アドレスでサインインします。

1. **[Custom Wake Word]\(カスタム ウェイク ワード)** ページは公開されていないので、このページに直接移動するリンクはありません。 Custom Speech 機能を利用するには Azure のサブスクリプションが必要ですが、カスタム ウェイク ワード機能では不要です。 "**サブスクリプションが見つかりません。**" というエラー メッセージが表示される場合は、 URL 内の **"Subscriptions?errorMessage=No%20Subscriptions%20found"** を "**customkws**" にただ置き換えて、Enter キーを押します。 URL は、ご利用のリージョンに応じて、 https://westus.cris.ai/customkws、 https://eastasia.cris.ai/customkws、または https://northeurope.cris.ai/customkws のいずれかである必要があります。

1. 選択したウェイク ワードを入力して、**[Submit the word]\(ワードを送信する)** を選択します。

    ![ウェイク ワードを入力する](media/speech-devices-sdk/wake-word-5.png)

1. ファイルが生成されるまで、数分かかる場合があります。 ブラウザー ウィンドウに、回転する円が表示されます。 しばらくすると、.zip ファイルをダウンロードするよう指示する情報バーが表示されます。

1. .zip ファイルをコンピューターに保存します。 開発キットにカスタム ウェイク ワードを展開するには、このファイルが必要です。 カスタム ウェイク ワードを展開するには、「[Speech Devices SDK を使ってみる](speech-devices-sdk-qsg.md)」の手順に従います。

1. **[サインアウト]** を選択します。

## <a name="next-steps"></a>次の手順

使い始めるために、[無料の Azure アカウント](https://azure.microsoft.com/free/)を取得し、Speech Devices SDK にサインアップします。

> [!div class="nextstepaction"]
> [Speech Devices SDK にサインアップする](get-speech-devices-sdk.md)
