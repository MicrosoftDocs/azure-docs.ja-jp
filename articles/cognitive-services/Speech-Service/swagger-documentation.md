---
title: Swagger ドキュメント - Speech Services
titleSuffix: Azure Cognitive Services
description: Swagger ドキュメントを使用して、さまざまなプログラミング言語の SDK を自動生成できます。 Microsoft のサービスの操作はすべて Swagger によってサポートされています
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 04/12/2019
ms.author: erhopf
ms.openlocfilehash: 29fcbd058651c428b488f5ce1c767105cb7921a6
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59580989"
---
# <a name="swagger-documentation"></a>Swagger ドキュメント

Speech Services では、データのインポート、モデルの作成、モデルの精度のテスト、カスタム エンドポイントの作成、文字起こしのキューへのバッチ配置、およびサブスクリプションの管理に使用されるいくつかの REST API を操作するための Swagger 仕様を提供しています。 Custom Speech ポータルを介して利用できる操作のほとんどは、これらの API を使用してプログラムによって実行できます。 

> [!NOTE]
> 音声変換とテキスト読み上げの両方の操作がサポートされており、REST API として利用可能で、Swagger 仕様に記載されています。

## <a name="generating-code-from-the-swagger-specification"></a>Swagger 仕様からコードを生成する

[Swager 仕様](https://cris.ai/swagger/ui/index)には、さまざまなパスをすばやくテストできるオプションが用意されています。 ただし、すべてのパスのコードを生成し、今後のソリューションの基盤にできる呼び出しの 1 つのライブラリを作成することが望ましい場合があります。 Python ライブラリを生成するプロセスを見てみましょう。

ご利用の Speech Service サブスクリプションと同じリージョンに Swagger を設定する必要があります。 Azure portal 内のご利用の Speech Service リソースの下でご利用のリージョンを確認できます。 サポートされているリージョンの完全な一覧については、[リージョン](regions.md)に関するページをご覧ください。

1. https://editor.swagger.io に移動します
2. **[File]\(ファイル\)**、**[Import]\(インポート\)** の順にクリックします
3. ご利用の Speech Service サブスクリプションのリージョンを含む Swagger URL を入力します `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. **[Generate Client]\(クライアントの生成\)** をクリックし、[Python] を選択します
5. クライアント ライブラリを保存する

生成した Python ライブラリは、[GitHub 上の Speech Services サンプル](https://aka.ms/csspeech/samples)で使用できます。

## <a name="reference-docs"></a>リファレンス ドキュメント

* [REST (Swagger):一括文字起こしとカスタマイズ](https://westus.cris.ai/swagger/ui/index)
* [REST API: 音声テキスト変換](rest-speech-to-text.md)
* [REST API: テキスト読み上げ](rest-text-to-speech.md)

## <a name="next-steps"></a>次の手順

* [GitHub 上の Speech Services サンプル](https://aka.ms/csspeech/samples)
* [Speech Services のサブスクリプション キーを無料で取得する](get-started.md)
