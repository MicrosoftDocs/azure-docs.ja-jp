---
title: 'クイック スタート: 音声を認識する (Objective-C) - 音声サービス'
titleSuffix: Azure Cognitive Services
description: iOS で Objective-C と Speech SDK を使用して音声を認識する方法について説明します
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 55b442b74847ccbc0dcc944eada7b33ccd368bed
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605058"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>クイック スタート:iOS で Objective-C と Speech SDK を使用して音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、音声を録音したファイルやマイクからの音声をテキストに文字起こしする iOS アプリを、Objective-C と Cognitive Services Speech SDK を使用して作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

開始する前の前提条件の一覧を次に示します。

* 音声サービス用の[サブスクリプション キー](get-started.md)
* macOS コンピューターと [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) 以降
* iOS バージョン 9.3 以降に設定されたターゲット

## <a name="get-the-speech-sdk-for-ios"></a>iOS 用の Speech SDK を取得する

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK の現在のバージョンは `1.6.0` です。

iOS 用の Cognitive Services Speech SDK は、現在は Cocoa フレームワークとして配布されています。
[こちら](https://aka.ms/csspeech/iosbinary)からダウンロードできます。 ファイルをホーム ディレクトリにダウンロードします。

## <a name="create-an-xcode-project"></a>Xcode プロジェクトの作成

Xcode を起動し、 **[File]**  >  **[New]**  >  **[Project]** の順にクリックして新しいプロジェクトを開始します。
テンプレートの選択ダイアログで、"iOS Single View App" テンプレートを選択します。

以降のダイアログで、次のように選択します。

1. [Project Options] ダイアログ
    1. クイック スタート アプリの名前を入力します (例: `helloworld`)。
    1. 既に Apple Developer アカウントをお持ちの場合は、適切な組織名と組織 ID を入力します。 テスト目的の場合は、単に `testorg` のような名前を選択できます。 アプリに署名するためには、適切なプロビジョニング プロファイルが必要です。 詳細については、[Apple の開発者向けサイト](https://developer.apple.com/)を参照してください。
    1. プロジェクトの言語として Objective-C が選択されていることを確認します。
    1. テストとコア データに関するすべてのチェック ボックスを無効にします。
    ![プロジェクトの設定](media/sdk/qs-objectivec-project-settings.png)
1. プロジェクト ディレクトリの選択
    1. ホーム ディレクトリを選択して、プロジェクトを配置します。 これにより、ホーム ディレクトリ内に `helloworld` ディレクトリが作成され、Xcode プロジェクトのすべてのファイルが入れられます。
    1. このサンプル プロジェクトの Git リポジトリの作成を無効にします。
    1. *[Project Settings]* で、SDK へのパスを調整します。
        1. **[Embedded Binaries]\(埋め込みバイナリ\)** ヘッダーの下の **[全般]** タブで、フレームワークとして SDK ライブラリを追加します ( **[Add embedded binaries]\(埋め込みバイナリの追加\)**  >  **[Add other...]\(その他の追加...\)** )。ホーム ディレクトリに移動し、`MicrosoftCognitiveServicesSpeech.framework`ファイルを選択します。 これにより、SDK のライブラリがヘッダー **[Linked Framework and Libraries]** に自動的に追加されます。
        ![追加されたフレームワーク](media/sdk/qs-objectivec-framework.png)
        1. **[Build Settings]** タブに移動し、 **[All]** の設定をアクティブにします。
        1. ディレクトリ `$(SRCROOT)/..` を、 **[Search Paths]** 見出しの下にある *[Framework Search Paths]* に追加します。
        ![[Framework Search Paths] の設定](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>UI の設定

このサンプル アプリの UI はごく単純なものです。音声認識を開始するためのボタンが 2 つ (ファイルから開始するためのボタンとマイク入力から開始するためのボタン) と、結果を表示するためのテキスト ラベルが 1 つあるだけです。
UI はプロジェクトの `Main.storyboard` 部分に設定されます。
プロジェクト ツリーの `Main.storyboard` エントリーを右クリックし、 **[Open As...]**  >  **[Source Code]** の順に選択して、ストーリーボードの XML ビューを開きます。
自動生成された XML をこのコードに置き換えます。

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. リンクを右クリックし、 **[Save target as...]** を選択して、[サンプルの wav ファイル](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)をダウンロードします。Finder ウィンドウからプロジェクト ビューのルート レベルにドラッグすることで、wav ファイルをプロジェクトにリソースとして追加します。
   設定を変更せずに、次のダイアログで **[Finish]** をクリックします。
1. 次の操作によって、自動生成された `ViewController.m` ファイルの内容を置き換えます。

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。
1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。
1. マイクへのアクセスの要求を追加します。 プロジェクト ツリーの `Info.plist` のエントリを右クリックし、 **[Open As...]**  >  **[Source Code]** の順に選択します。 `<dict>` セクションに以下の行を追加してから、ファイルを保存します。
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>サンプルのビルドと実行

1. デバッグ出力が表示されるようにします ( **[View]**  >  **[Debug Area]**  >  **[Activate Console]** )。
1. **[Product]\(製品\)**  >  **[Destination]\(ターゲット\)** メニューの一覧から、お使いの開発マシンに接続された iOS デバイスまたは iOS シミュレーターをアプリのターゲットとして選択します。
1. コード例をビルドし、メニューから **[Product]**  >  **[Run]** の順に選択するか、 **[Play]** ボタンをクリックして、iOS シミュレーターで実行します。
1. アプリの [Recognize (File)]\(認識 (ファイル)\) ボタンをクリックすると、画面の下部にオーディオ ファイルの内容である "What's the weather like?" が表示されます。 表示されます。

   ![シミュレートされた iOS アプリ](media/sdk/qs-objectivec-simulated-app.png)

1. アプリの [Recognize (Microphone)]\(認識 (マイク)\) ボタンをクリックして何か話すと、発話した内容のテキストが画面の下部に表示されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で Objective-C のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
