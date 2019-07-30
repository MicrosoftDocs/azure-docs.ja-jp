---
title: テキスト読み上げの出力を微調整する - Speech Services
titleSuffix: Azure Cognitive Services
description: Speech SDK のログを有効にします。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 94b58279b1a9fd4d9acdb4183f59b0a8579c17fd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606456"
---
# <a name="fine-tune-text-to-speech-output"></a>テキスト読み上げの出力を微調整する

Azure Speech Services では、[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) を使用して、テキスト読み上げの出力の速さ、発音、音量、音程、および音調を調整できます。 SSML は XML ベースのマークアップ言語であり、タグを使用して、どの機能でチューニングが必要かをサービスに知らせます。 その後、SSML メッセージは、テキスト読み上げサービスへの各要求の本文で送信されます。 カスタマイズ プロセスを簡素化するために、Speech Services では [Voice Tuning](https://aka.ms/voicetuning) ツールが提供されるようになりました。これを使用すると、テキスト読み上げの出力をリアルタイムで視覚的に調査したり微調整したりすることができます。

Voice Tuning ツールでは、Microsoft の[標準](language-support.md#standard-voices)、[ニューラル](language-support.md#text-to-speech)、および[カスタム音声](how-to-customize-voice-font.md)がサポートされています。

## <a name="get-started-with-the-voice-tuning-tool"></a>Voice Tuning ツールの使用

Voice Tuning ツールを使用してテキスト読み上げの出力の微調整を始める前に、以下の手順を完了する必要があります。

1. [無料の Microsoft アカウント](https://account.microsoft.com/account)を、まだお持ちでない場合は、作成します。
2. [無料の Azure アカウント](https://azure.microsoft.com/free/)を、まだお持ちでない場合は、作成します。 **[無料で始める]** をクリックし、お使いの Microsoft アカウントを使用して新しい Azure アカウントを作成します。

3. Azure portal で Speech Services サブスクリプションを作成します。 [Speech リソースを作成する方法](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)の詳細な手順を参照できます。
   >[!NOTE]
   >Azure portal で Speech リソースを作成するときに、Azure の場所情報は TTS 音声リージョンと一致する必要があります。 ニューラル TTS 音声では、Azure の場所のサブ セットがサポートされています。 サポートの完全な一覧については、[リージョン](regions.md#text-to-speech)に関するページを参照してください。

   >[!NOTE]
   >サービスを使用する前に、Azure portal で F0 キーまたは S0 キーを作成しておく必要があります。 Voice Tuning では、[30 日間の無料試用キー](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial)はサポートされて**いません**。

4. [Voice Tuning](https://aka.ms/voicetuning) ポータルにサインインし、自分の Speech Services サブスクリプションに接続します。 1 つの Speech Services サブスクリプションを選択し、プロジェクトを作成します。
5. **[New Tuning]\(新しいチューニング\)** を選択します。 その後、次の手順に従います。

   * **[すべてのサブスクリプション]** を探して選択します。  
   * **[Connect Existing Subscription]\(既存のサブスクリプションに接続\)** を選択します。  
     ![既存のサブスクリプションに接続する](./media/custom-voice/custom-voice-connect-subscription.png)
   * Azure Speech Services サブスクリプション キーを入力し、 **[追加]** を選択します。 サブスクリプション キーは、[サブスクリプション ページ](https://go.microsoft.com/fwlink/?linkid=2090458)の Speech のカスタマイズ ポータルにあります。 [Azure portal](https://portal.azure.com/) の [リソースの管理] ウィンドウからキーを取得することもできます。
   * 使用する予定の Speech Services サブスクリプションが複数ある場合は、サブスクリプションごとにこれらの手順を繰り返します。

## <a name="customize-the-text-to-speech-output"></a>テキスト読み上げの出力のカスタマイズ

アカウントを作成し、サブスクリプションをリンクしたので、テキスト読み上げの出力のチューニングを開始できるようになりました。

1. 音声を選択します。
2. 編集するテキストを入力します。
3. 編集を始める前に、オーディオを再生して、出力の感触をつかみます。
4. 調整する単語または文を選択して、さまざまな SSML ベースの機能を試してみます。

>[!TIP]
> SSML の調整と音声出力のチューニングの詳細については、「[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md)」を参照してください。

## <a name="limitations"></a>制限事項

ニューラル音声のチューニングは、標準およびカスタムの音声のチューニングとは少し異なります。

* ニューラル音声では、イントネーションはサポートされていません。
* 音程および音量の機能は、完全な文でのみ動作します。 これらの機能は、単語レベルでは使用できません。
* 速度については、一部のニューラル音声では単語に基づいてチューニングできますが、他の音声では文全体を選択する必要があります。

> [!TIP]
> Voice Tuning ツールでは、機能とチューニングに関するコンテキスト情報が提供されます。

## <a name="next-steps"></a>次の手順
* [Azure で Speech リソースを作成する](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [音声のチューニングを開始する](https://speech.microsoft.com/app.html#/VoiceTuning)
* [音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md)
