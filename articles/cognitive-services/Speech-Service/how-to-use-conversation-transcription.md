---
title: リアルタイムでの会話の文字起こしのクイックスタート - Speech サービス
titleSuffix: Azure Cognitive Services
description: リアルタイムでの会話の文字起こしを Speech SDK とともに使用する方法を説明します。 会話の文字起こしを使用すると、会議やその他の会話を文字起こしして、音声を Speech サービスにストリーミングすることによって、複数の参加者を追加、削除、または識別することができます。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: trbye
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 48cd4c7996eabad7293aa2429c76b8943e0ab3da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100368474"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>リアルタイムの会話の文字起こしの概要

Speech SDK の **ConversationTranscriber** API を使用すると、会議やその他の会話を文字起こしして、`PullStream` または `PushStream` を使用し、音声を Speech Service にストリーミングすることによって、複数の参加者を追加、削除、または識別することができます。 まず REST API を使用して各参加者の音声署名を作成し、次に SDK で音声署名を使用して会話を文字起こしします。 詳細については、会話の文字起こしの[概要](conversation-transcription.md)に関するページを参照してください。

## <a name="limitations"></a>制限事項

* 次のサブスクリプション リージョンでのみ使用できます: `centralus`、`eastasia`、`eastus`、`westeurope`
* 7 つのマイクの循環マルチマイク配列が必要です。 このマイク配列は、[Microsoft の仕様](./speech-devices-sdk-microphone.md)を満たす必要があります。
* [Speech Devices SDK](speech-devices-sdk.md) には、会話の文字起こしを実行する適切なデバイスやサンプル アプリが提供されています。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](overview.md#try-the-speech-service-for-free)。

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [非同期での会話の文字起こし](how-to-async-conversation-transcription.md)
> [ROOBO デバイスのサンプル コード](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
> [Azure Kinect Dev Kit のサンプル コード](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)