---
title: Swagger ドキュメント - 音声サービス
titleSuffix: Azure Cognitive Services
description: Swagger ドキュメントを使用して、さまざまなプログラミング言語の SDK を自動生成できます。 Microsoft のサービスの操作はすべて Swagger によってサポートされています
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: d4369b66bacbe8cff4fc9712ffcd0cb5a370c439
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100636270"
---
# <a name="swagger-documentation"></a>Swagger ドキュメント

音声サービスでは、データのインポート、モデルの作成、モデルの精度のテスト、カスタム エンドポイントの作成、バッチ文字起こしのキューイング、サブスクリプションの管理に使用されるいくつかの REST API を操作するための Swagger 仕様を提供しています。 [Speech Studio の Custom Speech 領域](https://aka.ms/customspeech)を介して利用できる操作のほとんどは、これらの API を使用してプログラムによって実行できます。

> [!NOTE]
> 音声サービスには、[Speech to Text](rest-speech-to-text.md) と [Text to Speech](rest-text-to-speech.md) の REST API がいくつか含まれています。  
>
> ただし、Swagger 仕様には、[Speech to Text REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) と v2.0 のみが記載されています。 他のすべての Speech Services REST API の情報については、前の段落で参照されているドキュメントを参照してください。

## <a name="generating-code-from-the-swagger-specification"></a>Swagger 仕様からコードを生成する

[Swagger 仕様](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)には、さまざまなパスをすばやくテストできるオプションが用意されています。 ただし、すべてのパスのコードを生成し、今後のソリューションの基盤にできる呼び出しの 1 つのライブラリを作成することが望ましい場合があります。 Python ライブラリを生成するプロセスを見てみましょう。

Swagger は、お使いの音声リソースのリージョンに設定する必要があります。 リージョンは、Azure portal の音声リソースの設定の **[概要]** 部分で確認できます。 サポートされているすべてのリージョンの一覧については、[こちら](regions.md#speech-to-text)で確認できます。

1. ブラウザーで、ご利用の[リージョン](regions.md#speech-to-text)の Swagger 仕様に進みます。  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. そのページで **[API]** 定義をクリックし、 **[Swagger]** をクリックします。 表示されたページの URL をコピーします。
1. 新しいブラウザーで [https://editor.swagger.io](https://editor.swagger.io) に移動します。
1. **[ファイル]** をクリックし、 **[URL のインポート]** をクリックし、URL を貼り付け、 **[OK]** をクリックします。
1. **[クライアントの生成]** をクリックし、 **[Python]** を選択します。 クライアント ライブラリがお使いのコンピューターに `.zip` ファイル形式でダウンロードされます。
1. ダウンロード ファイルを解凍し、中身をすべて抽出します。 解凍には `tar -xf` を使用することもあります。
1. 解凍したモジュールを Python 環境にインストールします。  
      `pip install path/to/package/python-client`
1. インストールされるパッケージの名前は `swagger_client` です。 正常にインストールされたことを確認します。  
       `python -c "import swagger_client"`

生成した Python ライブラリは、[GitHub 上の音声サービス サンプル](https://aka.ms/csspeech/samples)で使用できます。

## <a name="reference-documents"></a>リファレンス ドキュメント

* [Swagger: Speech to Text REST API v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [Speech to Text REST API](rest-speech-to-text.md)
* [Text to Speech REST API](rest-text-to-speech.md)

## <a name="next-steps"></a>次のステップ

* [GitHub 上の音声サービス サンプル](https://aka.ms/csspeech/samples)。
* [Speech サービスのサブスクリプション キーを無料で取得する](overview.md#try-the-speech-service-for-free)
