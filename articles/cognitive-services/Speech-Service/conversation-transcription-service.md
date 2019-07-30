---
title: 会話の文字起こし - Speech Services
titleSuffix: Azure Cognitive Services
description: 会話の文字起こしとは、Speech Services の高度な機能の 1 つであり、リアルタイムの音声認識、話者識別、およびダイアライゼーションを 1 つに結合したものです。 会話の文字起こしは、対面会議の文字起こしに最適です。話者を区別することができるため、だれがいつ何を発言したかを知ることができ、参加者は会議に集中して次のステップにすばやく進むことができます。 この機能により、アクセシビリティも向上します。 文字起こしがあると、聴覚に障碍がある人が積極的に参加できます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 18de3e52d61afd8e1ef20f80c020c8b7d96b9757
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603263"
---
# <a name="what-is-conversation-transcription"></a>会話の文字起こしとは

会話の文字起こしとは、Speech Services の高度な機能の 1 つであり、リアルタイムの音声認識、話者識別、およびダイアライゼーションを 1 つに結合したものです。 会話の文字起こしは、対面会議の文字起こしに最適です。話者を区別することができるため、だれがいつ何を発言したかを知ることができ、参加者は会議に集中して次のステップにすばやく進むことができます。 この機能により、アクセシビリティも向上します。 文字起こしがあると、聴覚に障碍がある人が積極的に参加できます。   

会話の文字起こしは正確な認識が可能であり、音声モデルはカスタマイズ可能であるため、業種や会社に固有の語彙を理解するように調整できます。 さらに、会話の文字起こしと Speech Devices SDK を組み合わせると、マルチマイク デバイスのエクスペリエンスを最適化できます。

>[!NOTE]
> 現時点では、会話の文字起こしをお勧めできるのは小規模な会議です。 会話の文字起こしを大規模な会議に利用したいとお考えの場合は、ご連絡ください。

次の図は、会話の文字起こしと連携するハードウェア、ソフトウェア、およびサービスを示しています。

![会話の文字起こしの入力の図](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> 特定のジオメトリ構成で、7 つのマイクの循環配列が必要です。 仕様とデザインの詳細については、[Microsoft Speech Device SDK マイク](https://aka.ms/cts/microphone)に関するページを参照してください。 開発キットの詳細および購入については、[Microsoft Speech Device SDK の入手](https://aka.ms/cts/getsdk)に関するページを参照してください。

## <a name="get-started-with-conversation-transcription"></a>会話の文字起こしを使ってみる

会話の文字起こしを使用するには、3 つのステップを実行する必要があります。

1. ユーザーから音声サンプルを収集します。
2. ユーザーの音声サンプルを使用して、ユーザー プロファイルを生成します。
3. Speech SDK を使用して、ユーザー (話者) を特定し、音声を文字起こしします。

## <a name="collect-user-voice-samples"></a>ユーザーの音声サンプルを収集する

最初の手順では、各ユーザーからオーディオ録音を収集します。 バックグラウンド ノイズのない静かな環境で、ユーザーの音声を録音する必要があります。 各オーディオ サンプルの推奨される長さは、30 秒から 2 分です。 オーディオ サンプルが長いほど、話者の識別精度が向上します。 オーディオは、16 KHz のサンプル レートのモノラル チャンネルである必要があります。

前述のガイダンス以外に、オーディオの録音方法と保存方法にも配慮してください。安全なデータベースをお勧めします。 次のセクションでは、Speech SDK で話者を認識するために使用されるユーザー プロファイルを生成するために、このオーディオがどのように使用されるかを説明します。

## <a name="generate-user-profiles"></a>ユーザー プロファイルを生成する

次に、収集したオーディオ録音を署名生成サービスに送信して、音声を検証し、ユーザー プロファイルを生成する必要があります。 [署名生成サービス](https://aka.ms/cts/signaturegenservice)は、ユーザー プロファイルの生成と取得を可能にする、REST API のセットです。

ユーザー プロファイルを作成するには、`GenerateVoiceSignature` API を使用する必要があります。 仕様の詳細とサンプル コードについては、以下を参照してください。

> [!NOTE]
> 会話の文字起こしは、現時点では "en-US" と "zh-CN" に対応しており、利用できるリージョンは `centralus` と `eastasia` です。

* [REST の仕様](https://aka.ms/cts/signaturegenservice)
* [会話の文字起こしの使用方法](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>文字起こしと話者の識別を行う

会話の文字起こしは、マルチチャンネル オーディオ ストリームとユーザー プロファイルを入力として文字起こしの生成と話者の識別を行います。 オーディオとユーザー プロファイルのデータは、Speech Devices SDK を使用して会話の文字起こしサービスに送信されます。 既に説明したように、会話の文字起こしを使用するには円形 7 マイク アレイと Speech Devices SDK が必要です。

>[!NOTE]
> 仕様とデザインの詳細については、[Microsoft Speech Device SDK マイク](https://aka.ms/cts/microphone)に関するページを参照してください。 開発キットの詳細および購入については、[Microsoft Speech Device SDK の入手](https://aka.ms/cts/getsdk)に関するページを参照してください。

会話の文字起こしを Speech Devices SDK とともに使用する方法については、[会話の文字起こしの使用方法](https://aka.ms/cts/howto)に関するページを参照してください。


## <a name="quick-start-with-a-sample-app"></a>サンプル アプリを含むクイック スタート

Microsoft Speech Device SDK には、すべてのデバイス関連サンプル用のクイック スタート サンプル アプリがあります。 会話の文字起こしは、その 1 つです。 それは、参照用のサンプル アプリとそのソース コードを含む [Speech Device SDK android クイック スタート](https://aka.ms/sdsdk-quickstart)にあります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech Devices SDK についてさらに詳しく学習する](speech-devices-sdk.md)
