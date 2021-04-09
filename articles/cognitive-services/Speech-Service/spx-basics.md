---
title: Speech CLI クイックスタート - Speech サービス
titleSuffix: Azure Cognitive Services
description: Azure Speech CLI の概要。 コードを記述しなくても、音声テキスト変換、テキスト読み上げ、音声翻訳などの音声サービスを操作できます。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: trbye
ms.openlocfilehash: 53138a22c58e89ade4af234630e9429a19738a6a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556470"
---
# <a name="get-started-with-the-azure-speech-cli"></a>Azure Speech CLI の概要

この記事では、コードを記述せずに、Speech CLI (コマンドライン インターフェイス) を使用して、音声テキスト変換、テキスト読み上げ、音声翻訳などの音声サービスを利用する方法について説明します。 Speech CLI は実稼働準備が整っており、`.bat` またはシェル スクリプトを使用して、音声サービスの単純なワークフローを自動化するために使用できます。

この記事では、コマンド プロンプト、ターミナル、PowerShell に関する実用的な知識があることを前提としています。

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>基本的な使用方法

このセクションでは、初めてのテストと実験に役立つことが多いいくつかの基本的な SPX コマンドについて説明します。 まずは、次のコマンドを実行して、ツールに組み込まれているヘルプを参照してください。

```console
spx
```

キーワードでヘルプ トピックを検索することができます。 たとえば、次のコマンドを入力すると、Speech CLI の使用例が一覧表示されます。

```console
spx help find --topics "examples"
```

recognize コマンドのオプションを表示するには、次のコマンドを入力します。

```console
spx help recognize
```

右側の欄に、追加の help コマンドが表示されます。 これらのコマンドを入力すると、サブコマンドについての詳しいヘルプを表示できます。

## <a name="speech-to-text-speech-recognition"></a>Speech to Text (音声認識)

次に、Speech CLI を使用して、システムの既定のマイクで音声テキスト変換 (音声認識) を実行しましょう。 コマンドを入力すると、SPX により、現在のアクティブな入力デバイスから音声の聞き取りが開始され、**Enter** キーを押すと停止します。 録音した音声が認識され、コンソール出力でテキストに変換されます。

>[!IMPORTANT]
> Docker コンテナーを使用している場合、`--microphone` は機能しません。

次のコマンドを実行します。

```console
spx recognize --microphone
```

Speech CLI を使用すると、オーディオ ファイルから音声を認識することもできます。

```console
spx recognize --file /path/to/file.wav
```

> [!TIP]
> Docker コンテナー内のオーディオ ファイルから音声を認識している場合、前の手順でマウントしたディレクトリにオーディオ ファイルがあることをご確認ください。

Speech CLI の認識オプションが分からない場合、詳細を調べるには、単に次のように入力します。

```console
spx help recognize
```

## <a name="text-to-speech-speech-synthesis"></a>Text to Speech (音声合成)

次のコマンドを実行すると、テキストが入力として取得され、合成された音声が、現在のアクティブな出力デバイス (コンピューターのスピーカーなど) に出力されます。

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

また、合成された出力をファイルに保存することもできます。 この例では、コマンドの実行ディレクトリに `my-sample.wav` という名前のファイルを作成します。

```console
spx synthesize --text "Enjoy using the Speech CLI." --audio output my-sample.wav
```

これらの例では、英語でテストしていると想定しています。 ただし、音声合成は多くの言語でサポートされています。 このコマンドを使用するか、[言語のサポート](./language-support.md)に関するページにアクセスすることにより、すべての音声の一覧を確認できます。

```console
spx synthesize --voices
```

ここでは、見つけた音声のいずれかを使用する方法を示します。

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

Speech CLI の合成オプションが分からない場合、詳細を調べるには、単に次のように入力します。

```console
spx help synthesize
```

## <a name="speech-to-text-translation"></a>音声テキスト変換の翻訳

Speech CLI を使用すると、音声テキスト変換の翻訳も実行できます。 次のコマンドを実行して、既定のマイクから音声をキャプチャし、翻訳をテキストとして出力します。 `translate` コマンドを使用し、`source` と `target` の各言語を指定する必要があることに注意してください。

```console
spx translate --microphone --source en-US --target ru-RU
```

複数の言語に翻訳する場合は、各言語コードを `;` で区切ります。

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

翻訳の出力を保存する場合は、`--output` フラグを使用します。 この例では、ファイルからの読み取りも行います。

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> サポートされているすべての言語と対応するロケール コードの一覧については、「[音声サービスの言語と音声のサポート](language-support.md)」を参照してください。

Speech CLI の翻訳オプションが分からない場合、詳細を調べるには、単に次のように入力します。

```console
spx help translate
```

## <a name="next-steps"></a>次のステップ

* [Speech CLI の構成オプション](./spx-data-store-configuration.md)
* [Speech CLI を使用したバッチ操作](./spx-batch-operations.md)
