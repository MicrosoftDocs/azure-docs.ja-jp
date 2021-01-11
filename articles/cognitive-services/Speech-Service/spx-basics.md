---
title: Speech CLI の基本
titleSuffix: Azure Cognitive Services
description: Speech CLI コマンド ツールを使用して、コードを記述せずに最小限のセットアップで音声サービスを操作する方法について説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: e859ac13c72ed07d3f57da6e61fd6d9f827f0fca
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854896"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Speech CLI の基本を学ぶ

この記事では、Speech CLI の基本的な使用パターンについて説明します。これは、コードを記述せずに音声サービスを使用できるコマンド ライン ツールです。 開発環境を作成したり、コードを記述したりせずに、音声サービスの主な機能をすばやくテストして、ユースケースが適切に合致しているかどうかを確認できます。 また、Speech CLI は実稼働準備が整っており、`.bat` またはシェル スクリプトを使用して、音声サービスの単純なワークフローを自動化するために使用できます。

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>基本的な使用方法

このセクションでは、初めてのテストと実験に役立つことが多いいくつかの基本的な SPX コマンドについて説明します。 まずは、次のコマンドを実行して、ツールに組み込まれているヘルプを参照してください。

```shell
spx
```

コマンド パラメーターの右側に一覧表示されている **see: (参照)** ヘルプ トピックに注目してください。 これらのコマンドを入力することで、サブコマンドについての詳しいヘルプを表示できます。

キーワードでヘルプ トピックを検索することができます。 たとえば、次のコマンドを入力すると、Speech CLI の使用例が一覧表示されます。

```shell
spx help find --topics "examples"
```

recognize コマンドのオプションを表示するには、次のコマンドを入力します。

```shell
spx help recognize
```

次に、次のコマンドを実行して、Speech サービスを使用して既定のマイクによる音声認識を実行します。

```shell
spx recognize --microphone
```

コマンドを入力すると、SPX により、現在のアクティブな入力デバイスでオーディオのリッスンが開始され、`ENTER` キーを押すと停止します。 録音した音声が認識され、コンソール出力でテキストに変換されます。 Speech CLI を使用すると、テキスト読み上げ合成も簡単に実行できます。 

次のコマンドを実行すると、入力したテキストが入力として取得され、合成された音声が、現在のアクティブな出力デバイスに出力されます。

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Speech CLI を使用すると、音声認識および合成に加えて、音声翻訳を行うこともできます。 上記の音声認識コマンドと同様に、次のコマンドを実行して、既定のマイクからオーディオをキャプチャし、ターゲット言語のテキストに翻訳します。

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

このコマンドでは、ソース (翻訳**元**言語) と、ターゲット (翻訳**先**言語) の両方を指定します。 `--microphone` 引数を使用して、現在のアクティブな入力デバイスでオーディオをリッスンし、`ENTER` キーを押すと停止します。 出力はターゲット言語へのテキスト翻訳で、テキスト ファイルに書き込まれます。

> [!NOTE]
> サポートされているすべての言語と対応するロケール コードの一覧については、「[音声サービスの言語と音声のサポート](language-support.md)」を参照してください。

## <a name="batch-operations"></a>バッチ操作

前のセクションのコマンドは、音声サービスがどのように機能するかをすばやく確認するのに最適です。 ただし、ユースケースが合致しているかどうかを評価する際、さまざまなシナリオがこのサービスによってどのように処理されるかを確認するために、既存の入力範囲に対してバッチ操作の実行が必要になる場合があります。 このセクションでは、次の方法について説明します。

* オーディオ ファイルのディレクトリでバッチ音声認識を実行する
* `.tsv` ファイルを反復処理し、バッチ テキスト読み上げ合成を実行する

## <a name="batch-speech-recognition"></a>バッチ音声認識

オーディオ ファイルのディレクトリがある場合は、Speech CLI を使用して簡単にバッチ音声認識を実行できます。 次のコマンドを実行して、`--files` を使ってディレクトリを指定します。 この例では、ディレクトリに `\*.wav` を追加して、そのディレクトリに存在するすべての `.wav` ファイルを認識します。 さらに、`--threads` 引数を指定して、認識を 10 個の並列スレッドで実行します。

> [!NOTE]
> `--threads` 引数は、次のセクションの `spx synthesize` コマンドでも使用できますが、使用可能なスレッドは CPU とその現在の負荷の割合によって変化します。

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

認識された音声出力は、`--output file` 引数を使用して `speech_output.tsv` に書き込まれます。 出力ファイルの構造の例を次に示します。

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-synthesis"></a>バッチ テキスト読み上げ合成

バッチ テキスト読み上げを実行する最も簡単な方法は、新しい `.tsv` (タブ区切り値) ファイルを作成し、Speech CLI で `--foreach` コマンドを利用することです。 次のファイル `text_synthesis.tsv` を確認してください。

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 次に、`text_synthesis.tsv` を指すコマンドを実行し、各 `text` フィールドで合成を実行し、対応する `audio.output` パスに `.wav` ファイルとして結果を書き込みます。 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

このコマンドは、`.tsv` ファイルの**各**レコードに対して `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` を実行するのと同じです。 注意すべき点がいくつかあります。

* 列ヘッダーの `audio.output` と `text` はそれぞれ、コマンド ライン引数の `--audio output` と `--text` に対応します。 `--audio output` のようなマルチパートのコマンド ライン引数は、スペースなし、先頭ダッシュなし、文字列区切りのピリオド (例: `audio.output`) を使用してファイル内で書式設定する必要があります。 その他の既存のコマンド ライン引数は、このパターンを使用して追加列としてファイルに追加できます。
* ファイルがこのように書式設定されている場合は、追加の引数を `--foreach` に渡す必要はありません。
* 必ず、`.tsv` 内の各値を **タブ**で区切ってください。

ただし、次の例のような `.tsv` ファイルがあり、列ヘッダーがコマンド ライン引数と**一致しない**場合は、次のようになります。

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

次の構文を使用して、`--foreach` 呼び出しでこれらのフィールド名を正しい引数にオーバーライドできます。 これは上記と同じ呼び出しです。

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>次のステップ

* SDK を使用して[音声認識](./quickstarts/speech-to-text-from-microphone.md)または[音声合成](./quickstarts/text-to-speech.md)のクイックスタートを完了します。
