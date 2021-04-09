---
title: カスタム キーワード - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech Software Development Kit (SDK) を使用したカスタム キーワードの特徴、機能、制限の概要。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: b2763372cc63637f73610e1936e3ea54a4524e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91356606"
---
# <a name="what-is-a-keyword"></a>キーワードとは

キーワードは、音声で製品を有効できるようにする単語または短い語句です。 たとえば、"コルタナさん" は、Cortana アシスタントのキーワードです。 ボイス アクティベーションを使用すると、ユーザーがキーワードを話すだけで、完全にハンドフリーで製品の操作を開始することができます。 製品ではキーワードが継続的にリッスンされているため、データを可能な限りプライベートに保つために、すべての音声は検出が行われるまで、ユーザーのデバイスでローカルに処理されます。

## <a name="core-features-of-custom-keyword"></a>カスタム キーワードのコア機能

カスタム キーワードのカスタマイズ、パフォーマンス、および統合機能を使用すると、お客様の製品のビジョンとユーザーのニーズに合わせてボイス アクティベーションを調整できます。

| 機能 | 説明 |
|----------|----------|
| キーワードのカスタマイズ | ブランドの延長として、キーワードは、お客様と築き上げてきたブランド エクイティを強化します。 Speech Studio のカスタム キーワード ポータルでは、ブランドを最もよく表す単語または短い語句を指定できます。 適切な発音を選択してキーワードをさらにカスタマイズでき、これは生成されたキーワード モデルで採用されます。
| キーワード検証 | ローカルで検出されたキーワードの信頼度が高い場合は、ユーザーがキーワードを発したことを確認するために、音声がクラウドに送信されます。 キーワード検証では、不適切なローカル検出による影響を軽減し、ユーザーのプライバシーを保護することで、セキュリティを強化することができます。
| 音声アシスタントと Speech SDK の統合 | Speech Studio のカスタム キーワードで生成されたキーワードは、Speech SDK を使用してデバイスまたはアプリケーション内に簡単に統合できます。 SDK を Speech Studio で提供されているキーワード モデルにポイントするだけで、キーワード検証を使用して音声で製品を有効にできるようになります。 独自の[音声アシスタント](voice-assistants.md)を作成して、製品の音声エクスペリエンスを完成させることができます。

## <a name="get-started-with-custom-keywords"></a>カスタム キーワードの概要

* 基本的な使用方法と設計パターンについては、「[カスタム キーワードの基本](custom-keyword-basics.md)」を参照してください。
* [C# と Speech SDK を使用して音声で製品を有効にする](tutorial-voice-enable-your-bot-speech-sdk.md)方法

## <a name="choose-an-effective-keyword"></a>有効なキーワードを選択する

有効なキーワードを作成することは、デバイスによって一貫して正確な応答が行われることを保証するうえで不可欠です。 キーワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。 キーワードを選択するときには、以下のガイドラインを考慮に入れます。

> [!div class="checklist"]
> * キーワードは、英語の単語または語句にする必要があります。
> * 2 秒以内に読み上げることができる必要があります。
> * 4 音節から 7 音節の単語が最適です。 たとえば、"Hey, Computer" は適切なキーワードです。 "Hey" だけではよくありません。
> * キーワードは、英語の一般的な発音規則に従っている必要があります。
> * 英語の一般的な発音規則に従った一意の単語や造語にすると、誤検知が減る可能性があります。 たとえば "computerama" が適切なキーワードになる可能性があります。
> * 一般的な単語は選択しないでください。 たとえば、"eat" や "go" は、普通の会話の中で頻繁に出てくる単語です。 これらは、デバイスの誤ったトリガーとなる可能性があります。
> * 別の発音をする可能性があるキーワードは使用しないようにします。 ユーザーは、デバイスが応答するようにするために、”正しい” 発音を知っている必要があります。 たとえば "509" は、"five zero nine"、"five oh nine"、または "five hundred and nine" と発音できます。 "R.E.I."  は、"R E I" または "Ray" として発音できます。 "Live" は、"/Līv/" または "/liv/" と発音できます。
> * 特殊な文字、記号、または数字は使用しないでください。 たとえば、"Go#" や "20 + cats" は、問題を引き起こすキーワードになる可能性があります。 ただし、"go sharp" や "twenty plus cats" は機能するでしょう。 それでもブランディングにおいて記号を使用して、正しい発音が一般に認知されるように、マーケティングやドキュメントを利用することができます。

> [!NOTE]
> 商標登録された単語をキーワードとして選択する場合は、自身でその商標を所有しているか、商標の所有者からその単語の使用を許可されている必要があります。 Microsoft は、キーワードの選択によって発生する可能性がある法律上のどんな問題に対しても一切の責任を負いません。

## <a name="see-samples-on-github"></a>GitHub 上のサンプルを参照してください

* [ユニバーサル Windows プラットフォームで C# と Speech SDK を使用してキーワードを認識する](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [Android で Java と Speech SDK を使用してキーワードを認識する](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>次のステップ

* [Speech サービスのサブスクリプション キーを無料で取得する](overview.md#try-the-speech-service-for-free)
* [Speech SDK を取得する](speech-sdk.md)
* [音声アシスタントの詳細情報を確認する](voice-assistants.md)
