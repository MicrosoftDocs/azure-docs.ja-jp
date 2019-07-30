---
title: クイック スタート:音声を認識する、Java (Windows、Linux) - Speech Services
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、コンピューターのマイクからユーザーの音声をキャプチャしてテキストに文字起こしする単純な Java アプリケーションの作成について説明します。
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: dce0a1b2adf20b2301402f37307e7ee1284c9aee
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605166"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>クイック スタート:Speech SDK for Java を使用して音声を認識する

クイック スタートは[音声から音声への翻訳](quickstart-translate-speech-java-jre.md)と[音声優先仮想アシスタント](quickstart-virtual-assistant-java-jre.md)にも使用できます。

必要に応じて、別のプログラミング言語や環境を選択します。<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、[Speech SDK](speech-sdk.md) を使用して、Java コンソール アプリケーションを作成 します。 PC のマイクからリアルタイムで音声をテキストに変換します。 アプリケーションは、Speech SDK Maven パッケージと、64 ビット Windows、64-bit Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9)、または macOS 10.13 以降の Eclipse Java IDE (v4.8) を使用してビルドされます。 これは、64 ビットの Java 8 のランタイム環境 (JRE) で実行されます。

> [!NOTE]
> Speech Devices SDK および Roobo デバイスについては、[Speech Devices SDK](speech-devices-sdk.md) を参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* オペレーティング システム:64 ビット Windows、64 ビット Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9)、または macOS 10.13 以降
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) または [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Speech Service 用の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。

Linux を実行している場合は、Eclipse を開始する前に、これらの依存関係がインストールされていることを確認してください。

* Ubuntu の場合:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Debian 9 の場合:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Windows (64 ビット版) を実行している場合は、お使いのプラットフォーム用の Microsoft Visual C++ 再頒布可能パッケージがインストールされていることを確認してください。
* [Visual Studio 2019 の Microsoft Visual C++ 再頒布可能パッケージをダウンロードする](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>プロジェクトの作成と構成

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. 新しい空のクラスを Java プロジェクトに追加するために、 **[File]**  >  **[New]**  >  **[Class]** の順に選択します。

1. **[New Java Class]** ウィンドウで、 **[Package]** フィールドに **speechsdk.quickstart** と入力し、 **[Name]** フィールドに **Main** と入力します。

   ![[New Java Class] ウィンドウのスクリーンショット](media/sdk/qs-java-jre-06-create-main-java.png)

1. `Main.java` のすべてのコードを次のスニペットに置き換えます。

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

F11 キーを押すか、 **[Run]**  >  **[Debug]** の順に選択します。
その後 15 秒間、マイクからの音声入力が認識され、コンソール ウィンドウにログが記録されます。

![認識が成功した後のコンソール出力のスクリーンショット](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>次の手順

オーディオ ファイルから音声を読み取る方法など、追加のサンプルは GitHub で入手できます。

> [!div class="nextstepaction"]
> [GitHub で Java のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [クイック スタート:音声を翻訳する、Java (Windows、Linux)](quickstart-translate-speech-java-jre.md)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
