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
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f43cbedc633b26a3a7fcbfb5f6a75da514bf0c45
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604862"
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

* 別の発音を持つことがあるウェイク ワードは使わないようにします。 ユーザーは、デバイスが応答するようにするために、”正しい” 発音を知っている必要があります。 たとえば "509" は、"five zero nine"、"five oh nine"、または "five hundred and nine" と発音できます。 "R.E.I." は、"R E I" または "Ray" として発音できます。 "Live" は、"/Līv/" または "/liv/" と発音できます。

* 特殊な文字、記号、または数字は使用しないでください。 たとえば、"Go#" および "20 + cats" の場合は、適切なウェイク ワードにはなりません。 ただし、"go sharp" や "twenty plus cats" は機能するでしょう。 それでもブランディングにおいて記号を使用して、正しい発音が一般に認知されるように、マーケティングやドキュメントを利用することができます。

> [!NOTE]
> 商標の単語をウェイク ワードとして選択する場合は、あなたがその商標を所有しているか、商標の所有者からその単語の使用を許可されていることを確認してください。 Microsoft は、ウェイク ワードの選択により発生する可能性がある問題については法的責任を負いません。

## <a name="create-your-wake-word"></a>ウェイク ワードを作成する

カスタム ウェイク ワードをデバイスで使用する前に、Microsoft Custom Wake Word Generation サービスを使用してウェイク ワードを作成する必要があります。 ウェイク ワードを指定すると、このサービスによってファイルが作成されます。このファイルを開発キットに展開することで、デバイスでウェイク ワードを有効にできます。

1. [Custom Speech Service ポータル](https://aka.ms/sdsdk-speechportal)に移動して**サインイン**します。音声サブスクリプションを持っていない場合は、[ **[サブスクリプションを作成する]** ](https://go.microsoft.com/fwlink/?linkid=2086754) を選択します。

    ![Custom Speech Service ポータル](media/speech-devices-sdk/wake-word-4.png)

1. [[Custom Wake Word]\(カスタム ウェイク ワード\)](https://aka.ms/sdsdk-wakewordportal) ページに選択したウェイク ワードを入力し、 **[Add wake word]\(ウェイク ワードの追加\)** をクリックします。 効果的なキーワードを選択するために役立つ[ガイドライン](#choose-an-effective-wake-word)がいくつかあります。 現在は、en-US 言語のみがサポートされています。

    ![ウェイク ワードを入力する](media/speech-devices-sdk/wake-word-5.png)

1. ウェイク ワードの 3 つの代替発音が作成されます。 必要な発音をすべて選択することができます。 次に **[送信]** を選択してウェイク ワードを生成します。 ウェイク ワードを変更する場合は、まず既存のウェイク ワードを削除します。発音行にマウス ポインターを移動すると、削除アイコンが表示されます。

    ![ウェイク ワードを確認する](media/speech-devices-sdk/wake-word-6.png)

1. モデルが生成されるまでに最大で 1 分かかる場合があります。 ファイルをダウンロードするように求められます。

    ![ウェイク ワードをダウンロードする](media/speech-devices-sdk/wake-word-7.png)

1. .zip ファイルをコンピューターに保存します。 開発キットにカスタム ウェイク ワードをデプロイするには、このファイルが必要です。

## <a name="next-steps"></a>次の手順

カスタム ウェイク ワードを [Speech Devices SDK クイックスタート](https://aka.ms/sdsdk-quickstart)でテストします。
