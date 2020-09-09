---
title: Audio Content Creation - Speech サービス
titleSuffix: Azure Cognitive Services
description: Audio Content Creation は、Microsoft のテキスト読み上げ出力をご使用のアプリや製品に合わせてカスタマイズしたり微調整したりするためのオンライン ツールです。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 72fecbdc173a6174e54a28e48f983965f397ba6a
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224589"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Audio Content Creation ツールを使用して合成を向上させる

[Audio Content Creation](https://aka.ms/audiocontentcreation) は、Microsoft のテキスト読み上げ出力をご使用のアプリやと製品に合わせてカスタマイズしたり微調整したりするためのオンライン ツールです。 このツールを使用すると、パブリックの音声やカスタム音声をより正確で自然な表現にするために微調整したり、クラウドでの出力を管理したりすることができます。

Audio Content Creation ツールは、[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) に基づいています。 カスタマイズとチューニングを簡単にするために、Audio Content Creation を使用して、テキスト読み上げの出力をリアルタイムで視覚的に検査することができます。

Audio Content Creation の[ビデオ チュートリアル](https://www.youtube.com/watch?v=O1wIJ7mts_w)を参照してください。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

次の図に、テキスト読み上げの出力を微調整するための手順を示します。 それぞれのステップの詳細については、下のリンクを使用してください。

![テキスト読み上げの出力を微調整するための手順を示す図。](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. 開始するには、[Azure アカウントと音声リソースを設定](#set-up-your-azure-account-and-speech-resource)します。
2. プレーン テキストまたは SSML スクリプトを使用して、[音声チューニング ファイルを作成](#create-an-audio-tuning-file)します。
3. スクリプトの内容の音声と言語を選択します。 Audio Content Creation には、すべての [Microsoft テキスト読み上げ音声](language-support.md#text-to-speech)が含まれています。 標準、ニューラル、または独自のカスタム音声を使用できます。
   >[!NOTE]
   > カスタム ニューラル音声には、ゲート アクセスを使用できます。これにより、自然な音声と同じような高品位の音声を作成できます。 詳細については、[ゲート プロセス](https://aka.ms/ignite2019/speech/ethics)に関するページを参照してください。

4. 既定の合成出力を確認します。 次に、発音、切れ目、高低、速さ、抑揚、音声スタイルなどを調整して、出力を改善します。 オプションの完全な一覧については、「[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md)」を参照してください。 次の[ビデオ](https://youtu.be/mUvf2NbfuYU)で、Audio Content Creation を使用して音声出力を微調整する方法を示します。 
5. [チューニングした音声を保存してエクスポートします](#export-tuned-audio)。 チューニング トラックをシステムに保存すると、作業を続行して出力を反復処理することができます。 出力に問題がなければ、エクスポート機能を使用して音声作成タスクを作成できます。 エクスポート タスクの状態を監視し、ご使用のアプリや製品で使用するための出力をダウンロードすることができます。

## <a name="set-up-your-azure-account-and-speech-resource"></a>Azure アカウントと音声リソースを設定する

1. Audio Content Creation を操作するには、Azure アカウントが必要です。 Microsoft アカウントを使用して Azure アカウントを作成できます。 次の手順に従って、[Azure アカウントを設定](get-started.md#new-resource)します。 
2. Azure アカウントに対して[音声リソースを作成](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource)します。 価格レベルが **S0** に設定されていることを確認します。 ニューラル音声のいずれかを使用している場合は、必ず[サポートされているリージョン](regions.md#standard-and-neural-voices)でリソースを作成してください。
2. Azure アカウントと音声リソースを取得した後、音声サービスを使用して [Audio Content Creation](https://aka.ms/audiocontentcreation) にアクセスできます。
3. 作業する必要がある音声リソースを選択します。 この時点で新しい音声リソースを作成することもできます。 
4. 上部のナビゲーションにある **[設定]** オプションを使用して、いつでも音声リソースを変更できます。

## <a name="create-an-audio-tuning-file"></a>音声チューニング ファイルを作成する

Audio Content Creation ツールにコンテンツを取り込むには、2 つの方法があります。

**オプション 1:**

1. 新しい音声チューニング ファイルを作成するには、 **[新しいファイル]** をクリックします。
2. 編集ウィンドウに内容を入力するか貼り付けます。 各ファイルの文字数は最大 20,000 文字です。 スクリプトが 20,000 文字を超えている場合は、オプション 2 を使用して、内容を複数のファイルに自動的に分割できます。 
3. 忘れずに保存してください。

**オプション 2:**

1. 1 つまたは複数のテキスト ファイルをインポートするには、 **[アップロード]** をクリックします。 プレーンテキストと SSML の両方がサポートされています。
2. スクリプト ファイルが 20,000 文字を超えている場合は、段落、文字、または正規表現によってファイルを分割してください。 
3. テキスト ファイルをアップロードするときは、ファイルが以下の要件を満たしていることを確認してください。

   | プロパティ | 値/備考 |
   |----------|---------------|
   | ファイル形式 | プレーン テキスト (.txt)<br/> SSML テキスト (.txt)<br/> ZIP ファイルはサポートされていません |
   | エンコード形式 | UTF-8 |
   | ファイル名 | 各ファイルには一意の名前が必要です。 重複はサポートされていません。 |
   | テキストの長さ | テキスト ファイルは 20,000 文字を超えないようにしてください。 |
   | SSML の制限 | 各 SSML ファイルには、単一の SSML のみを含めることができます。 |

### <a name="plain-text-example"></a>プレーン テキストの例

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>SSML テキストの例

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>チューニングした音声をエクスポートする

音声出力を確認し、チューニングと調整に問題がなければ、音声をエクスポートできます。

1. 音声作成タスクを作成するには、 **[エクスポート]** をクリックします。 長時間のオーディオ出力と完全なオーディオ出力エクスペリエンスがサポートされているため、**オーディオ ライブラリにエクスポートする**ことをお勧めします。 ローカル ディスクに音声を直接ダウンロードすることもできますが、使用できるのは最初の 10 分のみです。 
2. チューニングした音声の出力形式を選択します。 サポートされている形式とサンプル レートの一覧については、以下を参照してください。
3. タスクの状態は **[Export task]\(エクスポート タスク\)** タブで確認できます。タスクが失敗した場合は、詳細情報のページで詳細なレポートを確認してください。
4. タスクが完了すると、 **[Audio Library]\(音声ライブラリ\)** タブから音声をダウンロードできるようになります。
5. **[Download]** をクリックします。 これで、カスタムのチューニングされた音声をご使用のアプリや製品で使用する準備ができました。

### <a name="supported-audio-formats"></a>サポートされている音声形式

| Format | 16 kHz サンプル レート | 24 kHz サンプル レート |
|--------|--------------------|--------------------|
| wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>関連項目

* [Long Audio API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
