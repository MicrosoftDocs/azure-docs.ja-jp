---
title: クイック スタート:音声を翻訳する、Java (Windows、Linux) - Speech Services
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、ユーザーの音声をキャプチャし、別の言語に変換してコマンド ラインにテキストを出力する、単純な Java アプリケーションを作成します。 このガイドは、Windows および Linux ユーザー向けに設計されています。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 36eaaeabcf888aac10bcf9b8a27e3590d21079ec
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897106"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>クイック スタート:Speech SDK for Java を使用して音声を翻訳する

このクイック スタートでは、コンピューターのマイクからユーザーの音声をキャプチャし、その音声を翻訳して、翻訳されたテキストをコマンド ラインにリアルタイムで表示する、単純な Java アプリケーションを作成します。 このアプリケーションは、64 ビット Windows または 64 ビット Ubuntu Linux 16.04/18.04 上で動作するように設計されており、Speech SDK Maven パッケージおよび Eclipse Java IDE を使用してビルドされています。

音声翻訳が可能な言語の完全な一覧については、[言語サポート](language-support.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* オペレーティング システム:64 ビットの Windows または 64 ビットの Ubuntu Linux 16.04/18.04
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) または [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Speech Service 用の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。

Ubuntu 16.04/18.04 を実行している場合は、Eclipse を開始する前に、これらの依存関係がインストールされていることを確認してください。

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

> [!NOTE]
> Speech Devices SDK および Roobo デバイスについては、[Speech Devices SDK](speech-devices-sdk.md) を参照してください。

## <a name="create-and-configure-project"></a>プロジェクトの作成と構成

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. 新しい空のクラスを Java プロジェクトに追加するために、**[File]**  >  **[New]**  >  **[Class]** の順に選択します。

1. **[New Java Class]** ウィンドウで、**[Package]** フィールドに **speechsdk.quickstart** と入力し、**[Name]** フィールドに **Main** と入力します。

   ![[New Java Class] ウィンドウのスクリーンショット](media/sdk/qs-java-jre-06-create-main-java.png)

1. `Main.java` のすべてのコードを次のスニペットに置き換えます。

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

F11 キーを押すか、**[Run]**  >  **[Debug]** の順に選択します。

マイクからの音声入力は、ドイツ語で文字起こしされ、コンソール ウィンドウにログ記録されます。 音声のキャプチャを停止するには、Enter キーを押します。

![認識が成功した後のコンソール出力のスクリーンショット](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>次の手順

オーディオ ファイルから音声を読み取る方法や、翻訳されたテキストを合成音声として出力する方法など、追加のサンプルは GitHub で入手できます。

> [!div class="nextstepaction"]
> [GitHub で Java のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [クイック スタート:音声を認識する、Java (Windows、Linux)](quickstart-java-jre.md)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
