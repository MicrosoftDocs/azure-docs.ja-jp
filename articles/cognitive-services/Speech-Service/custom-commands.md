---
title: カスタム コマンド - 音声サービス
titleSuffix: Azure Cognitive Services
description: 音声アプリケーションを作成するためのソリューションであるカスタム コマンドの特徴、機能、および制限の概要。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: e4ae8a09a554afe232091b68888af17dfa096771
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426471"
---
# <a name="what-is-custom-commands"></a>カスタム コマンドとは

[音声アシスタント](voice-assistants.md)などのアプリケーションでは、ユーザーの音声を聞き取り、応答としてアクションを実行します (多くの場合は、音声で応答します)。 これは、[音声テキスト変換](speech-to-text.md)を使用してユーザーの音声を文字に変換し、そのテキストの自然言語理解に対してアクションを実行します。 このアクションには、多くの場合、[テキスト読み上げ](text-to-speech.md)で生成されたアシスタントからの音声出力が含まれます。 デバイスは、Speech SDK の `DialogServiceConnector` オブジェクトを使用してアシスタントに接続されます。

**カスタム コマンド** を使用すると、音声優先の対話操作を行うために最適化された、リッチな音声コマンド処理アプリを簡単に構築できます。 これにより、統一された作成エクスペリエンス、自動ホスティング モデル、比較的低い複雑性が実現し、音声コマンドのシナリオに最適なソリューションの構築に専念できるようになります。

カスタム コマンドは、タスクの完了やコマンドと制御のシナリオに最適です。特に、モノのインターネット (IoT) デバイスや、アンビエントおよびヘッドレス デバイスに適しています。 例としては、サービス業、小売業、自動車業界向けのソリューションがあります。顧客向けの最適な室内音声コントロール エクスペリエンスの構築、店舗在庫の管理、移動中の車内機能の制御などが実現できます。

> [!TIP]
> Microsoft のランディング ページ ([https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands)) でサンプル デモをご覧ください。

複雑な会話アプリの構築に関心をお持ちの場合は、[仮想アシスタント ソリューション](/azure/bot-service/bot-builder-enterprise-template-overview)を使用して Bot Framework を試すことをお勧めします。 Direct Line Speech を使用すると、任意の Bot Framework ボットに音声を追加できます。

カスタム コマンドの適切な候補には、適切に定義された変数セットを持つ固定の語彙があります。 たとえば、サーモスタットの制御などのホーム オートメーションのタスクが理想的です。

   ![タスク完了シナリオの例](media/voice-assistants/task-completion-examples.png "タスク完了の例")

## <a name="getting-started-with-custom-commands"></a>カスタム コマンドの使用の開始

カスタム コマンドの目的は、認識の負荷を軽減してさまざまなテクノロジを学習し、音声コマンド アプリの構築に専念することです。 カスタム コマンドを使用する最初の手順は、<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech リソースを作成</a>することです。 Speech Studio 上にご自分のカスタム コマンド アプリを作成して発行することができます。その後、デバイス上のアプリケーションが Speech SDK を使用してそれと通信できるようになります。

#### <a name="authoring-flow-for-custom-commands"></a>カスタム コマンドの作成フロー
   ![カスタム コマンドの作成フロー](media/voice-assistants/custom-commands-flow.png "カスタム コマンドの作成フロー")

クイックスタートに従うと、最初のカスタム コマンド アプリの実行コードを 10 分もかからずに作成することができます。

* [カスタム コマンドを使用して音声アシスタントを作成する](quickstart-custom-commands-application.md)

クイック スタートを完了したら、操作方法ガイドを参照して、カスタム コマンド アプリケーションを設計、開発、デバッグ、デプロイ、統合するための詳細な手順を確認してください。

## <a name="building-voice-assistants-with-custom-commands"></a>カスタム コマンドを使用した音声アシスタントの構築
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>次のステップ

* [Speech サービスのサブスクリプション キーを無料で取得する](overview.md#try-the-speech-service-for-free)
* [GitHub で Microsoft の音声アシスタント リポジトリのサンプルを確認する](https://aka.ms/speech/cc-samples)
* [カスタム コマンドを試行するために Speech Studio にアクセスする](https://speech.microsoft.com/customcommands)
* [Speech SDK を取得する](speech-sdk.md)